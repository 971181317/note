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

  如果字符串小1mb，扩容加倍当前的空间，大于1mb，每次扩容1mb

```c
// sds.c
#define SDS_MAX_PREALLOC (1024*1024) // 1mb
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

支持两端插入、弹出数据，可以充当stack和queue

### 2.1 ziplist

redis使用链表实现，下面是一种常见的链表实现方式adlist.h：

```c
/* Node, List, and Iterator are the only data structures used currently. */
//Node、List 和 Iterator 是目前唯一使用的数据结构。 

typedef struct listNode {
    struct listNode *prev;
    struct listNode *next;
    void *value;
} listNode;

// 迭代器
typedef struct listIter {
    listNode *next;
    int direction;
} listIter;

typedef struct list {
    listNode *head;
    listNode *tail;
    void *(*dup)(void *ptr);
    void (*free)(void *ptr);
    int (*match)(void *ptr, void *key);
    unsigned long len;
} list;
```

redis内部使用该链表保存数据，如主服务下所有的服务器信息

但redis并不使用该链表保存用户列表数据，对内存管理不友好

1. 每个节点都是独立的内存块，导致内存碎片过多
2. 链表节点中前后节点占用过多的额外内存

申请一整块内存，然后在这块内存存放链表数据，这是ziplist的设计思想

#### 2.1.1 定义(ziplist.c，注释中详细解释了结构)

1. ziplist总体布局：

   ```shell
   <zlbytes> <zltail> <zllen> <entry> <entry> ... <entry> <zlend>
   ```

   * zlbytes：`uint32_t`，记录整个ziplist占用的字节数，包括zlbytes占用的4个字节

   * zltail：`uint32_t`，记录从ziplist起始位置到最后一个节点的偏移量，用于支持链表从尾部弹出或反向（从尾到头）遍历链表

   * zllen：`uint16_t`，记录节点数量，如果超过2^16^-2 = 65534个节点，则这个值设置为2^16^-1 = 65535，这是需要遍历整个ziplist获取真正的节点数量。超过阈值会溢出，所以设置最大值，如果读取到最大值会遍历整个链表。

   * zlend：`uint8_t`，一个特殊的标志节点，等于255，标志ziplist结尾。其他数据不会以255开头

     > 问：zlen不是说255是结束标志吗，为什么zllen会大于254个呢？
     >
     > 这会涉及到quicklist的实现，它是由多个ziplist所实现的，这些我们后面会讲到

   英文原文：

   ```txt
   ZIPLIST OVERALL LAYOUT
   ======================
   
   The general layout of the ziplist is as follows:
   
   ziplist的大体布局如下 :
   
   <zlbytes> <zltail> <zllen> <entry> <entry> ... <entry> <zlend>
    
   NOTE: all fields are stored in little endian, if not specified otherwise.
   
   注意：如果没有另外指定，所有字段都以小端存储。
   
   <uint32_t zlbytes> is an unsigned integer to hold the number of bytes that the ziplist occupies, including the four bytes of the zlbytes field itself. This value needs to be stored to be able to resize the entire structure without the need to traverse it first.
   
   <uint32_t zlbytes> 是一个无符号整数，用于保存 ziplist 占用的字节数，包括 zlbytes 字段本身的四个字节。 需要存储此值才能调整整个结构的大小，而无需先遍历它。
   
   <uint32_t zltail> is the offset to the last entry in the list. This allows  a pop operation on the far side of the list without the need for full traversal.
   
   <uint32_t zltail> 是到列表中最后一个entry的偏移量。 这允许在列表的远端进行弹出操作而无需完全遍历。
   
   <uint16_t zllen> is the number of entries. When there are more than 2^16-2 entries, this value is set to 2^16-1 and we need to traverse the entire list to know how many items it holds.
   
   <uint16_t zllen> 是entry数。 当entry超过 2^16-2 时，该值设置为 2^16-1，我们需要遍历整个列表才能知道它包含多少项。
   
   <uint8_t zlend> is a special entry representing the end of the ziplist. Is encoded as a single byte equal to 255. No other normal entry starts with a byte set to the value of 255.
   
   <uint8_t zlend> 是一个特殊的entry，表示 ziplist 的结尾。 编码为等于 255 的单个字节。没有其他正常entry以设置为值 255 的字节开始。 
   ```

2. entry的结构：

   ```shell
   <prelen> <encoding> <entry-data>
   ```

   * entry-data：该节点元素，即节点储存的数据

   * prevlen：记录前驱节点的长度，单位为字节，该属性长度为1字节或5字节

     * 如果前驱节点长度小于254，则使用1字节储存前驱长度
     * 否则使用5字节，并且第一个字节固定为254，剩下4个字节储存前驱节点

   * encoding：代表当前节点元素的编码格式，包含类型和节点长度。一个ziplist中，不同的节点元素的编码格式可以不同。编码格式如下：

     > encoding字段取决于entry的内容。当entry是字符串时，encoding第一个字节的前2位将保存用于存储字符串长度的编码类型，后面是字符串的实际长度。当entry是整数时，前 2 位都设置为 1。接下来的 2 位用于指定将在此标头后存储哪种整数。不同类型和编码的概述如下。第一个字节总是足以确定entry的类型。

     1. 00pppppp(pppppp代表encoding的低六位，下同)：字符串编码，长度小于等于63（2^6^ - 1），长度存放在encoding的低六位
     2. 01pppppp：字符串编码，长度小于16383（2^14^ - 1）,长度存放在encoding的后6位和encoding后1字节中
     3. 10000000：字符串编码，长度大于16383（2^14^ - 1），长度存放在encoding后4个字节中
     4. 11000000：数值编码，类型为int16_t，占用2字节
     5. 11010000：数值编码，类型为int32_t，占用4字节
     6. 11100000：数值编码，类型为int64_t，占用8字节
     7. 11110000：数值编码，使用3字节保存一个整数
     8. 11111110：数值编码，使用1字节保存一个整数
     9. 1111xxxx：使用encoding第四位储存一个整数，存储数值范围为 0 ~ 12.改编码下encoding第四位可用范围0001 ~ 1101，encoding第四位为实际储存的值。（超过1101只有，1110和1111，就和8，10中的编码冲突了）
     10. 11111111：255，ziplist结束标志

   * 2,3编码除了encoding，还需要额外的空间储存节点元素长度。9将节点元素直接放在了encoding属性上，这是对小叔子的优化，entry-data为空。

   英文原文：

   ```txt
   ZIPLIST ENTRIES
   ===============
   
   Every entry in the ziplist is prefixed by metadata that contains two pieces  of information. First, the length of the previous entry is stored to be able to traverse the list from back to front. Second, the entry encoding is provided. It represents the entry type, integer or string, and in the case of strings it also represents the length of the string payload.
   So a complete entry is stored like this:
   
   ziplist 中的每个entry都以包含两条信息的元数据为前缀。首先，存储前一个entry的长度，以便能够从后向前遍历列表。其次，提供了entry encoding。它表示entry类型，整数或字符串，在字符串的情况下，它还表示字符串有效负载的长度。
   所以一个完整的entry是这样存储的：
   
   <prevlen> <encoding> <entry-data>
   
   Sometimes the encoding represents the entry itself, like for small integers as we'll see later. In such a case the <entry-data> part is missing, and we could have just:
   
   有时encoding代表entry本身，例如我们稍后将看到的小整数。在这种情况下， <entry-data> 部分丢失了，我们可以：
   
   <prevlen> <encoding>
    
   The length of the previous entry, <prevlen>, is encoded in the following way: If this length is smaller than 254 bytes, it will only consume a single byte representing the length as an unsigned 8 bit integer. When the length is greater than or equal to 254, it will consume 5 bytes. The first byte is set to 254 (FE) to indicate a larger value is following. The remaining 4 bytes take the length of the previous entry as value.
   
   前一个entry的长度 <prevlen> 的编码方式如下：如果该长度小于 254 字节，它将只消耗一个字节，表示长度为无符号 8 位整数。当长度大于等于254时，会消耗5个字节。第一个字节设置为 254 (FE) 以指示后面有更大的值。剩余的 4 个字节取前一个entry的长度作为值。
   
   So practically an entry is encoded in the following way:
   
   所以实际上一个entry是按以下方式编码的：
   
    <prevlen from 0 to 253> <encoding> <entry>
   
   Or alternatively if the previous entry length is greater than 253 bytes the following encoding is used: 
   0xFE <4 bytes unsigned little endian prevlen> <encoding> <entry>
   
   或者，如果前一个entry长度大于 253 字节，则使用以下编码：
   0xFE <4 bytes unsigned little endian prevlen> <encoding> <entry>
   
   The encoding field of the entry depends on the content of the entry. When the entry is a string, the first 2 bits of the encoding first byte will hold the type of encoding used to store the length of the string, followed by the actual length of the string. When the entry is an integer the first 2 bits are both set to 1. The following 2 bits are used to specify what kind of integer will be stored after this header. An overview of the different types and encodings is as follows. The first byte is always enough to determine the kind of entry.
   
   entry的encoding字段取决于entry的内容。当entry是字符串时，encoding第一个字节的前2位将保存用于存储字符串长度的编码类型，后面是字符串的实际长度。当entry是整数时，前 2 位都设置为 1。接下来的 2 位用于指定将在此标头后存储哪种整数。不同类型和编码的概述如下。第一个字节总是足以确定entry的类型。
   
   |00pppppp| - 1 byte
        String value with length less than or equal to 63 bytes (6 bits).
        "pppppp" represents the unsigned 6 bit length.
        长度小于或等于 63 字节（6 位）的字符串值。
        “pppppp”表示无符号的 6 位长度。
        
   |01pppppp|qqqqqqqq| - 2 bytes
        String value with length less than or equal to 16383 bytes (14 bits).
        IMPORTANT: The 14 bit number is stored in big endian.
        长度小于或等于 16383 字节（14 位）的字符串值。
        重要提示：14 位数字以大端序存储。
        
   |10000000|qqqqqqqq|rrrrrrrr|ssssssss|tttttttt| - 5 bytes
        String value with length greater than or equal to 16384 bytes.
        Only the 4 bytes following the first byte represents the length up to 2^32-1. The 6 lower bits of the first byte are not used and are set to zero.
        IMPORTANT: The 32 bit number is stored in big endian.
        长度大于或等于 16384 字节的字符串值。
        只有第一个字节后面的 4 个字节代表长度可达 2^32-1。不使用第一个字节的 6 个低位并设置为零。
        重要提示：32 位数字以大端存储。
        
   |11000000| - 3 bytes
        Integer encoded as int16_t (2 bytes).
        整数编码为 int16_t（2 字节）。
        
   |11010000| - 5 bytes
        Integer encoded as int32_t (4 bytes).
        整数编码为 int32_t（4 字节）。
        
   |11100000| - 9 bytes
        Integer encoded as int64_t (8 bytes).
        整数编码为 int64_t（8 字节）。
        
   |11110000| - 4 bytes
        Integer encoded as 24 bit signed (3 bytes).
        24 位有符号整数编码为（3 个字节）。
        
   |11111110| - 2 bytes
        Integer encoded as 8 bit signed (1 byte).
        8 位有符号整数编码为（1 字节）。
        
   |1111xxxx| - (with xxxx between 0001 and 1101) immediate 4 bit integer.
   （xxxx 在 0001 和 1101 之间）立即数 4 位整数。
       Unsigned integer from 0 to 12. The encoded value is actually from 1 to 13 because 0000 and 1111 can not be used, so 1 should be subtracted from the encoded 4 bit value to obtain the right value.
       0到12的无符号整数。编码后的值实际上是1到13，因为0000和1111不能用，所以要从编码后的4位值中减去1才能得到正确的值。
       
   |11111111| - End of ziplist special entry.
   |11111111| - ziplist 特殊entry结束。
   
   Like for the ziplist header, all the integers are represented in little endian byte order, even when this code is compiled in big endian systems.
   与 ziplist 标头一样，所有整数都以小端字节序表示，即使此代码是在大端系统中编译的。 
   ```

#### 2.1.2 字节序(ziplist.c，注释中详细解释了结构)

encoding属性使用多个字节储存节点元素长度，这种多字节数据存储在计算机内存中或者在网络传输时的顺序为字节序。

* 大端字节序：低字节数据保存在内存高地址位置，高字节数据保存在内存低地址位置
* 小端字节序：低字节数据保存在内存低地址位置，高字节数据保存在内存高地址位置

储存0X44332211：

![image-20211214170025373](https://dxytoll-img-1304942391.cos.ap-nanjing.myqcloud.com/img/image-20211214170025373.png)

使用小端，cpu可以优先处理低位字节，执行计算的错位、进位操作时效率更高。大端则更符合人们的读写习惯。

```txt
[0f 00 00 00] [0c 00 00 00] [02 00] [00 f3] [02 f6] [ff]
      |             |          |       |       |     |
   zlbytes        zltail      zllen   "2"     "5"   end
```

* [0f 00 00 00] ：zlbyte为15，代表ziplist总共占用15个字节，用小端储存
* [0c 00 00 00] ：zltail为12，代表从ziplist起始位置到最后一个节点（[02 f6]）的偏移量
* [02 00] ：zllen为2，代表ziplist中有两个节点
* [00 f3] ： 00表示前一个节点长度，f3（11110011）使用了9的编码方式，表示2
* [02 f6] ：02带边起一个节点的长度，f5（11110101）同上，表示5
* [ff] ： 结束位置节点

英文原文：

```txt
EXAMPLES OF ACTUAL ZIPLISTS
===========================

The following is a ziplist containing the two elements representing the strings "2" and "5". It is composed of 15 bytes, that we visually split into sections:

以下是一个包含代表字符串“2”和“5”的两个元素的 ziplist。它由 15 个字节组成，我们在视觉上将它们分成几个部分：

[0f 00 00 00] [0c 00 00 00] [02 00] [00 f3] [02 f6] [ff]
      |             |          |       |       |     |
   zlbytes        zltail    entries   "2"     "5"   end

The first 4 bytes represent the number 15, that is the number of bytes the whole ziplist is composed of. The second 4 bytes are the offset at which the last ziplist entry is found, that is 12, in fact the last entry, that is "5", is at offset 12 inside the ziplist. The next 16 bit integer represents the number of elements inside the ziplist, its value is 2 since there are just two elements inside.Finally "00 f3" is the first entry representing the number 2. It is composed of the previous entry length, which is zero because this is our first entry, and the byte F3 which corresponds to the encoding |1111xxxx| with xxxx between 0001 and 1101. We need to remove the "F" higher order bits 1111, and subtract 1 from the "3", so the entry value is "2". The next entry has a prevlen of 02, since the first entry is composed of exactly two bytes. The entry itself, F6, is encoded exactly like the first entry, and 6-1 = 5, so the value of the entry is 5. Finally the special entry FF signals the end of the ziplist.

前4个字节代表数字15，即整个ziplist组成的字节数。
第二个 4 字节是找到最后一个 ziplist entry的偏移量，即 12，实际上最后一个entry，即“5”，位于 ziplist 内的偏移量 12。
下一个 16 位整数表示 ziplist 中元素的数量，它的值为 2，因为里面只有两个元素。
最后“00 f3”是表示数字2的第一个entry。它由前一个entry长度（由于这是我们的第一个entry而为零）和对应于编码|1111xxxx|的字节F3组成。 xxxx在0001和1101之间。我们需要把“F”高位1111去掉，把“3”减1，所以入口值为“2”。
下一个entry的 prevlen 为 02，因为第一个entry正好由两个字节组成。entry本身 F6 的编码与第一个entry完全相同，并且 6-1 = 5，因此entry的值为 5。
最后，特殊entry FF 表示 ziplist 的结束。

Adding another element to the above string with the value "Hello World" allows us to show how the ziplist encodes small strings. We'll just show the hex dump of the entry itself. Imagine the bytes as following the entry that stores "5" in the ziplist above:

向上述字符串中添加另一个值为“Hello World”的元素允许我们展示 ziplist 如何编码小字符串。我们将只显示entry本身的十六进制转储。想象在上面的 ziplist 中存储“5”的entry后面的字节：

[02] [0b] [48 65 6c 6c 6f 20 57 6f 72 6c 64]

The first byte, 02, is the length of the previous entry. The next  byte represents the encoding in the pattern |00pppppp| that means that the entry is a string of length <pppppp>, so 0B means that an 11 bytes string follows. From the third byte (48) to the last (64) there are just the ASCII characters for "Hello World".

第一个字节 02 是前一个entry的长度。
下一个字节表示模式中的编码 |00pppppp|这意味着该entry是一个长度为 <pppppp> 的字符串，因此 0B 意味着后面跟着一个 11 字节的字符串。
从第三个字节 (48) 到最后一个 (64)，只有“Hello World”的 ASCII 字符。 
```

#### 2.1.3 操作分析

* 查找元素

```c
// ziplist.c
#define ZIP_END 255         /* Special "end of ziplist" entry. */

/* We use this function to receive information about a ziplist entry.
 * Note that this is not how the data is actually encoded, is just what we
 * get filled by a function in order to operate more easily. */
// zlentry:我们使用此函数接收有关 ziplist entry的信息。 请注意，这并不是数据的实际编码方式，而是我们通过函数填充的内容，以便更轻松地操作。
typedef struct zlentry {
    // 用于编码前一个entry 长度的字节（entry.prelen的长度，可能为1字节或5字节）
    unsigned int prevrawlensize; /* Bytes used to encode the previous entry len*/
    
    // 上一个entry 长度。
    unsigned int prevrawlen;     /* Previous entry len. */
    
    // 用于对此entry 类型/长度进行编码的字节。 例如，字符串具有 1、2 或 5 个字节的标头。 整数总是使用单个字节。
    unsigned int lensize;        /* Bytes used to encode this entry type/len.
                                    For example strings have a 1, 2 or 5 bytes
                                    header. Integers always use a single byte.*/
    
    // 用于表示实际entry 的字节。 对于字符串，这只是字符串长度，而对于整数，它是 1、2、3、4、8 或 0（对于 4 位立即数），具体取决于数字范围。
    unsigned int len;            /* Bytes used to represent the actual entry.
                                    For strings this is just the string length
                                    while for integers it is 1, 2, 3, 4, 8 or
                                    0 (for 4 bit immediate) depending on the
                                    number range. */
    // prevrawlensize + lensize.
    unsigned int headersize;     /* prevrawlensize + lensize. */
    
    // 根据entry编码设置为 ZIP_STR_* 或 ZIP_INT_*。 然而，对于 4 位立即数，这可以假设一个值范围并且必须进行范围检查。
    unsigned char encoding;      /* Set to ZIP_STR_* or ZIP_INT_* depending on
                                    the entry encoding. However for 4 bits
                                    immediate integers this can assume a range
                                    of values and must be range-checked. */
    
    // 指向entry最开始的指针，即 this 指向 prev-entry-len 字段。
    unsigned char *p;            /* Pointer to the very start of the entry, that
                                    is, this points to prev-entry-len field. */
} zlentry;

/* Find pointer to the entry equal to the specified entry. Skip 'skip' entries
 * between every comparison. Returns NULL when the field could not be found. */
// 查找指向与指定entry相等的entry的指针。 在每次比较之间跳过 “skip” entry 。 找不到该字段时返回 NULL。 
unsigned char *ziplistFind(unsigned char *zl, unsigned char *p, unsigned char *vstr, unsigned int vlen, unsigned int skip) {
    // p:从哪里开始找
    // vstr,vlen：待查找元素的内容和长度
    // skip：间隔多少次才执行一次元素对比
    
    int skipcnt = 0;
    unsigned char vencoding = 0; // 如果是数字编码会设置为非0
    long long vll = 0; // 如果是数字编码，当前的entry的数值
    size_t zlbytes = ziplistBlobLen(zl); //以字节为单位返回 ziplist blob 大小。 

    while (p[0] != ZIP_END) {
        struct zlentry e;
        unsigned char *q;

        assert(zipEntrySafe(zl, zlbytes, p, &e, 1));
        
        // 偏移prelen和encoding的长度，实际就是找到data存储的地方
        q = p + e.prevrawlensize + e.lensize;

        if (skipcnt == 0) {
            //已经跳过了skip个元素，这时开始比较
            
            // 将当前entry与指定entry进行比较 
            /* Compare current entry with specified entry */
            if (ZIP_IS_STR(e.encoding)) {
                // 字符串
                
                // 长度相同，内容相等
                if (e.len == vlen && memcmp(q, vstr, vlen) == 0) {
                    return p;
                }
            } else {
                // 数字编码
                
                /* Find out if the searched field can be encoded. Note that
                 * we do it only the first time, once done vencoding is set
                 * to non-zero and vll is set to the integer value. */
                // 找出是否可以对搜索的字段进行编码。 请注意，我们只在第一次这样做，一旦完成，vencoding 设置为非零，vll 设置为整数值。 
                if (vencoding == 0) {
                    // vencoding是标志位，0的话表示从未编码过，本次只要编码了，之后一直可以编码结果
                    if (!zipTryEncoding(vstr, vlen, &vll, &vencoding)) {
                        /* If the entry can't be encoded we set it to
                         * UCHAR_MAX so that we don't retry again the next
                         * time. */
                        // 如果无法对条目进行编码，我们将其设置为 UCHAR_MAX，以便下次不再重试。 
                        vencoding = UCHAR_MAX;
                    }
                    /* Must be non-zero by now */
                    // 断言 vecoding非0，如果为0的话，每次进入都需要encoding
                    assert(vencoding);
                }

                /* Compare current entry with specified entry, do it only
                 * if vencoding != UCHAR_MAX because if there is no encoding
                 * possible for the field it can't be a valid integer. */
                // 将当前条目与指定条目进行比较，仅当 vencoding != UCHAR_MAX 时才执行此操作，因为如果该字段没有可能的编码，则它不能是有效整数。 
                if (vencoding != UCHAR_MAX) {
                    // 编码为整形
                    long long ll = zipLoadInteger(q, e.encoding);
                    if (ll == vll) {
                        return p;
                    }
                }
            }

            /* Reset skip count */
            // 重置skip
            skipcnt = skip;
        } else {
            /* Skip entry */
            // 跳过entry
            skipcnt--;
        }

        /* Move to next entry */
        // 偏移元素到下个entry
        p = q + e.len;
    }

    return NULL;
}
```

