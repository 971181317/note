# redis

## 第一章 字符串

### 1.1 redisObject

Redis中数据对象 server.h/redisObject 是对内部储存数据定义的抽象类型

```c
//server.h/redisObject
#define LRU_BITS 24
typedef struct redisObject {
    unsigned type:4; 
    unsigned encoding:4; 
    unsigned lru:LRU_BITS; /* LRU time (relative to global lru_clock) or
                            * LFU data (least significant 8 bits frequency
                            * and most significant 16 bits access time). */
    int refcount;
    void *ptr;
} robj;
```

* type：数据类型
* encoding：编码格式，即储存数据使用的数据结构。同一个类型的数据，Redis会根据数据量、占用内存等情况使用不同的编码，最大限度的节省内存
* refcount：应用计数，为了节省内存，Redis会在多出应用同一个redisObject
* ptr：指向实际的数据结构，如sds，真正的数据储存在该数据结构中
* lru：24位，LRU时间戳或LFU计数

redisObject负责装载所有的键和值，redisObject.ptr指向真正储存数据的数据结构，redisObject.refcount、redisObject.lru等属性则用于管理数据（数据共享，数据过期）

### 1.2 sds

redis扩展了c语言的字符串，定义了sds（Simple Dynamic String）。redis所有的key都是string

#### 1.2.1 定义

```c
// sds.h
typedef char *sds;

/* Note: sdshdr5 is never used, we just access the flags byte directly.
 * However is here to document the layout of type 5 SDS strings. */
/*注意：sdshdr5 从未使用过，我们只是直接访问标志字节。
  然而，这里是为了记录类型 5 SDS 字符串的布局。  */
struct __attribute__ ((__packed__)) sdshdr5 {
    unsigned char flags; /* 3 lsb of type, and 5 msb of string length */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr8 {
    uint8_t len; /* used */
    uint8_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr16 {
    uint16_t len; /* used */
    uint16_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr32 {
    uint32_t len; /* used */
    uint32_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr64 {
    uint64_t len; /* used */
    uint64_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
```

sdshdr针对不同的长度，定义不同的len和alloc，最大可能的节省内存

* len：已使用字节长度，即字符串长度。sdshdr n 可以储存 2^n的数据。最大长度为512MB。获取字符长度为o(1)
* alloc：申请的长度，空闲时间为`len- alloc`
* flag：低3位表示sdshdr的类型，高5位只在sdshdr5中使用，表示字符串的长度，所以sdshdr5没有len属性。另外，Redis对sdshdr5定义是常量字符串，不支持扩容，不存在alloc属性。
* buf：字符串内容，sds祖先c语言字符串规范，保存一个空字符作为buf结尾，并不计入len、alloc属性中。可以直接使用strcmp和strcpy直接操作sds

#### 1.2.2 操作分析

* 构建函数sdsnewlen

```c
sds _sdsnewlen(const void *init, size_t initlen, int trymalloc) {
    void *sh;//sdshdr
    sds s;//实际是sdshdr.buf
    // 获取初始化的长度数据的type
    char type = sdsReqType(initlen);
    /* Empty strings are usually created in order to append. Use type 8
     * since type 5 is not good at this. */
    // 长度为0的sdshdr5可能要扩容，需要转为sdshdr8
    if (type == SDS_TYPE_5 && initlen == 0) type = SDS_TYPE_8;
    int hdrlen = sdsHdrSize(type);//sdshdr.len
    unsigned char *fp; /* flags pointer. */
    size_t usable;//sdshdr.alloc
	
    assert(initlen + hdrlen + 1 > initlen); /* Catch size_t overflow */
    // 分配内存，需要多分配一个元素，储存结尾
    sh = trymalloc?
        s_trymalloc_usable(hdrlen+initlen+1, &usable) :
        s_malloc_usable(hdrlen+initlen+1, &usable);
    if (sh == NULL) return NULL;
    if (init==SDS_NOINIT)
        init = NULL;
    else if (!init)
        memset(sh, 0, hdrlen+initlen+1);
    s = (char*)sh+hdrlen;
    fp = ((unsigned char*)s)-1;
    usable = usable-hdrlen-1;
    if (usable > sdsTypeMaxSize(type))
        usable = sdsTypeMaxSize(type);
    switch(type) {
        case SDS_TYPE_5: {
            // fp第三位储存内容，高5位储存长度
            *fp = type | (initlen << SDS_TYPE_BITS);
            break;
        }
        case SDS_TYPE_8: {
            //SDS_HDR_VAR 宏，按长度转换为对应的struct
            SDS_HDR_VAR(8,s);
            sh->len = initlen;
            sh->alloc = usable;
            *fp = type;
            break;
        }
        case SDS_TYPE_16: {
            SDS_HDR_VAR(16,s);
            sh->len = initlen;
            sh->alloc = usable;
            *fp = type;
            break;
        }
        case SDS_TYPE_32: {
            SDS_HDR_VAR(32,s);
            sh->len = initlen;
            sh->alloc = usable;
            *fp = type;
            break;
        }
        case SDS_TYPE_64: {
            SDS_HDR_VAR(64,s);
            sh->len = initlen;
            sh->alloc = usable;
            *fp = type;
            break;
        }
    }
    if (initlen && init)
        memcpy(s, init, initlen);
    // 结尾符号
    s[initlen] = '\0';
    // 返回实际储存的 sdshdr.buf
    return s;
}

sds sdsnewlen(const void *init, size_t initlen) {
    return _sdsnewlen(init, initlen, 0);
}
```

* 扩容sdsMakeRoomFor

```c
// sds.c
#define SDS_MAX_PREALLOC (1024*1024)
...
sds _sdsMakeRoomFor(sds s, size_t addlen, int greedy) {
    // sds就是buf的首元素
    void *sh, *newsh;
    size_t avail = sdsavail(s); // 算出空余字符
    size_t len, newlen, reqlen;
    // 结构体每个成员相对结构体首地址的偏移都是成员大小的整数倍，如不满足，对前一个成员填充字节以满足。
    // 因为sdshdr使用了__attribute__ ((__packed__))，取消了字节对齐，这里的s是sdshdr.buf所以这里s[-1]实际是指前一个元素sdshdr.flag
    char type, oldtype = s[-1] & SDS_TYPE_MASK;
    int hdrlen;
    size_t usable;

    /* Return ASAP if there is enough space left. */
    // 如果有足够的空间，则不扩容
    if (avail >= addlen) return s;

    len = sdslen(s);
    // 通过sdshdr.buf反推sdshdr，因为sdshdr.buf首元素储存在结构体中，所以buf的首地址就是结构体的结尾
    sh = (char*)s-sdsHdrSize(oldtype);
    reqlen = newlen = (len+addlen);
    assert(newlen > len);   /* Catch size_t overflow */
    //greedy为1的时候，扩大的需要更多，减少下次重新分配
    //greedy为0的时候，扩大到足以让addlen有可用空间
    if (greedy == 1) {
        // 小于SDS_MAX_PREALLOC，扩容两倍
        if (newlen < SDS_MAX_PREALLOC)
            newlen *= 2;
        // 大于SDS_MAX_PREALLOC，每次扩容SDS_MAX_PREALLOC
        else
            newlen += SDS_MAX_PREALLOC;
    }
	
    // 通过newlen计算出新的type
    type = sdsReqType(newlen);

    /* Don't use type 5: the user is appending to the string and type 5 is
     * not able to remember empty space, so sdsMakeRoomFor() must be called
     * at every appending operation. */
    // 机翻：不要使用类型 5：用户附加到字符串并且类型 5 无法记住空白的空间，因此必须在每次附加操作时调用 sdsMakeRoomFor()。 
    // 将sdshdr5转换为sdshdr8，sdshdr5不支持扩容
    if (type == SDS_TYPE_5) type = SDS_TYPE_8;

    // 新长度
    hdrlen = sdsHdrSize(type);
    assert(hdrlen + newlen + 1 > reqlen);  /* Catch size_t overflow */
    if (oldtype==type) {
        // 新type等于老type，尝试在原有的基础上分配新内存
        newsh = s_realloc_usable(sh, hdrlen+newlen+1, &usable);
        if (newsh == NULL) return NULL;
        s = (char*)newsh+hdrlen;// 通过sdrhdr和hdrlen反推buf，修改指针的偏移量，偏移到buf开始的地方
    } else {
        /* Since the header size changes, need to move the string forward,
         * and can't use realloc */
        // 因为type发生了变化，需要分配新的内存空间，然后将将老数据迁移
        newsh = s_malloc_usable(hdrlen+newlen+1, &usable);
        if (newsh == NULL) return NULL;
        memcpy((char*)newsh+hdrlen, s, len+1);//复制内容
        s_free(sh);//释放老数据空间
        s = (char*)newsh+hdrlen;// 通过sdrhdr和hdrlen反推buf，修改指针的偏移量，偏移到buf开始的地方
        s[-1] = type;// 修改flag
        sdssetlen(s, len);//设置长度
    }
    // 计算使用的长度，sdshdr.alloc
    usable = usable-hdrlen-1;
    if (usable > sdsTypeMaxSize(type))
        usable = sdsTypeMaxSize(type);
    sdssetalloc(s, usable);
    return s;
}

/* Enlarge the free space at the end of the sds string more than needed,
 * This is useful to avoid repeated re-allocations when repeatedly appending to the sds. */
sds sdsMakeRoomFor(sds s, size_t addlen) {
    return _sdsMakeRoomFor(s, addlen, 1);
}

/* Unlike sdsMakeRoomFor(), this one just grows to the necessary size. */
sds sdsMakeRoomForNonGreedy(sds s, size_t addlen) {
    return _sdsMakeRoomFor(s, addlen, 0);
}
```

* 常用函数

| func                                  | 作用                                                  |
| ------------------------------------- | ----------------------------------------------------- |
| sdsnew，sdsempty                      | 创建sds                                               |
| sdsfree，sdsclear，sdsRemoveFreeSpace | 释放sds，清空sds中的字符串的内容，移除sds剩余可用空间 |
| sdslen                                | 计算sds长度                                           |
| sdsdup                                | 将指定字符串复制sds中，覆盖原字符串                   |
| sdscat                                | 给定内容拼接在sds之后                                 |
| sdscmp                                | 比较是否相同                                          |
| sdsrange                              | 获取子字符串，并释放其他空间                          |

#### 1.2.3 编码

字符串由3类编码(server.h)

```c
#define OBJ_ENCODING_RAW 0     /* Raw representation */
#define OBJ_ENCODING_INT 1     /* Encoded as integer */
#define OBJ_ENCODING_EMBSTR 8  /* Embedded sds string encoding */
```

* `OBJ_ENCODING_EMBSTR`：长度小于`OBJ_ENCODING_EMBSTR_SIZE_LIMIT` (44字节，object.c)字符串
  * redisObject、sds在**连续**的内存块中![image-20211212005026253](https://dxytoll-img-1304942391.cos.ap-nanjing.myqcloud.com/img/image-20211212005026253.png)
  * `OBJ_ENCODING_EMBSTR`是redis对字符串的优化
* `OBJ_ENCODING_RAW`：长度大于`OBJ_ENCODING_EMBSTR_SIZE_LIMIT` (44字节，object.c)字符串
  * redisObject、sds在**不连续**的内存块中
* `OBJ_ENCODING_INT`：将数值型字符串转换为整形，可以答复降低内存空间。
  * e.g.：“123456789012”占用12字节，会自动转化为long long，只占用8字节

给redis发请求后，redis会自动解析报文，并将命令参数转换为redisObject。object.c/createStringObject函数完成

```c
#define OBJ_ENCODING_EMBSTR_SIZE_LIMIT 44
robj *createStringObject(const char *ptr, size_t len) {
    if (len <= OBJ_ENCODING_EMBSTR_SIZE_LIMIT)
        return createEmbeddedStringObject(ptr,len);
    else
        return createRawStringObject(ptr,len);
}
```

redis将key和value都转换为redisObject之后，再将数据存入数据库

```shell
> Set Introduction "Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache and message broker. "
```

![image-20211212011748127](https://dxytoll-img-1304942391.cos.ap-nanjing.myqcloud.com/img/image-20211212011748127.png)

redis会尝试将准换为`OBJ_ENCODING_INT`，object.c/tryObjectEncoding

```c
robj *tryObjectEncoding(robj *o) {
    // robj: redisObject
    long value;
    sds s = o->ptr;
    size_t len;

    /* Make sure this is a string object, the only type we encode
     * in this function. Other types use encoded memory efficient
     * representations but are handled by the commands implementing
     * the type. */
    // 机翻：确保这是一个字符串对象，我们在这个函数中编码的唯一类型。 其他类型使用编码内存高效表示，但由实现该类型的命令处理。 
    serverAssertWithInfo(NULL,o,o->type == OBJ_STRING);

    /* We try some specialized encoding only for objects that are
     * RAW or EMBSTR encoded, in other words objects that are still
     * in represented by an actually array of chars. */
    // 机翻：我们仅针对 RAW 或 EMBSTR 编码的对象尝试一些专门的编码，换句话说，仍然由实际的字符数组表示的对象。 
    // 人话：不对处理int做特殊处理
    if (!sdsEncodedObject(o)) return o;

    /* It's not safe to encode shared objects: shared objects can be shared
     * everywhere in the "object space" of Redis and may end in places where
     * they are not handled. We handle them only as values in the keyspace. */
    // 机翻：对共享对象进行编码是不安全的：共享对象可以在 Redis 的“对象空间”中的任何地方共享，并且可能在未处理它们的地方结束。 我们仅将它们作为键空间中的值来处理。 
    // 人话：很多地方引用的话别处理，可能影响其他地方
     if (o->refcount > 1) return o;

    /* Check if we can represent this string as a long integer.
     * Note that we are sure that a string larger than 20 chars is not
     * representable as a 32 nor 64 bit integer. */
    // 机翻：检查我们是否可以将此字符串表示为长整数。 请注意，我们确信大于 20 个字符的字符串不能表示为 32 位或 64 位整数。 
    // 人话：判断是不是数字，转化后能否储存
    len = sdslen(s);
    if (len <= 20 && string2l(s,len,&value)) {
        /* This object is encodable as a long. Try to use a shared object.
         * Note that we avoid using shared integers when maxmemory is used
         * because every object needs to have a private LRU field for the LRU
         * algorithm to work well. */
        // 机翻：此对象可编码为 long。 尝试使用共享对象。 请注意，当使用 maxmemory 时，我们避免使用共享整数，因为每个对象都需要有一个私有的 LRU 字段才能使 LRU 算法正常工作。 
        //如果配置了server.maxmemory，并使用了不支持共享数据的淘汰算法（LRU，LFU），不能使用共享数据，。只有每个数据都存在edisObject.lru属性才可以计算。
        if ((server.maxmemory == 0 ||
            !(server.maxmemory_policy & MAXMEMORY_FLAG_NO_SHARED_INTEGERS)) &&
            value >= 0 &&
            value < OBJ_SHARED_INTEGERS)
        {
            // 减少o的引用
            decrRefCount(o);
            // shared.integers是redis共享数据，保存0~9999，启动时创建
            // 增加shard的引用
            incrRefCount(shared.integers[value]);
            return shared.integers[value];
        } else {
            if (o->encoding == OBJ_ENCODING_RAW) {
                //OBJ_ENCODING_RAW redisObject和sdshdr分开储存，可以直接替换值
                sdsfree(o->ptr);//释放指针
                o->encoding = OBJ_ENCODING_INT;// 修改encoding
                o->ptr = (void*) value;//写入新值
                return o;
            } else if (o->encoding == OBJ_ENCODING_EMBSTR) {
                // 减少o引用，返回一个新的创建的值，encoding==OBJ_ENCODING_INT，并且ptr指向long long或long
                decrRefCount(o);
                return createStringObjectFromLongLongForValue(value);
            }
        }
    }

    /* If the string is small and is still RAW encoded,
     * try the EMBSTR encoding which is more efficient.
     * In this representation the object and the SDS string are allocated
     * in the same chunk of memory to save space and cache misses. */
    //如果字符串很小并且仍然是 RAW 编码，请尝试更有效的 EMBSTR 编码。 在这种表示中，对象和 SDS 字符串被分配在同一块内存中，以节省空间和缓存未命中。 
    //人话：将分块内存改为占用小的连续内存
    if (len <= OBJ_ENCODING_EMBSTR_SIZE_LIMIT) {
        robj *emb;

        if (o->encoding == OBJ_ENCODING_EMBSTR) return o;
        emb = createEmbeddedStringObject(s,sdslen(s));
        decrRefCount(o);
        return emb;
    }

    /* We can't encode the object...
     *
     * Do the last try, and at least optimize the SDS string inside
     * the string object to require little space, in case there
     * is more than 10% of free space at the end of the SDS string.
     *
     * We do that only for relatively large strings as this branch
     * is only entered if the length of the string is greater than
     * OBJ_ENCODING_EMBSTR_SIZE_LIMIT. */
    // 我们无法对对象进行编码...做最后一次尝试，至少优化字符串对象内部的 SDS 字符串，使其占用空间很小，以防 SDS 字符串末尾有超过 10% 的可用空间。我们仅对相对较大的字符串执行此操作，因为仅当字符串的长度大于 OBJ_ENCODING_EMBSTR_SIZE_LIMIT 时才会进入此分支。 
    // 人话：歇逼了，现在依然是raw的编码，想不出其他方法再转换，只能试试减少sdshdr内存占用
    trimStringObjectIfNeeded(o);

    /* Return the original object. */
    return o;
}
```

string的实现：t_string.c

redis命令映射关系：server.c/redisConmmandTable

```shell

# 不同编码的字符串
localhost:6379> SET msg "hello world"
OK
localhost:6379> Set Introduction "Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache and message broker. "
OK
localhost:6379> SET page 1
OK
localhost:6379> TYPE msg
string
localhost:6379> Type Introduction
string
localhost:6379> TYPE page
string
localhost:6379> OBJECT ENCODING Introduction
"raw"
localhost:6379> OBJECT ENCODING msg
"embstr"
localhost:6379> OBJECT ENCODING page
"int"
```

## 第二章 列表

### 2.1 ziplist