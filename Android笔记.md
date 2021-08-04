## **布局**

### 布局文件

布局文件是res/layout文件夹下的xml文件

### java代码实现布局

一般不使用该方法，还是使用xml文件进行修改

```java
public class MainActivity extends AppCompatActivity {
    //Activity:可视化界面
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //setContentView设置内容视图
        //R：为每一个资源文件按类别分配一个索引
        //使程序员可以通过R.类别名.资源名去操作对应的资源
        //setContentView(R.layout.activity_main);

        //代码实现设置布局
        //1.根布局为线性布局
        LinearLayout ll = new LinearLayout(this);
        //2.设置宽高
        //第一个参数为宽，第二个参数为高
        ll.setLayoutParams(new LinearLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT,
                ViewGroup.LayoutParams.MATCH_PARENT));
        //3.背景设为红色
        ll.setBackgroundColor(Color.RED);
        //4.指定此Activity内容为线性布局
        setContentView(ll);
    }
}
```

### 线性布局(LinearLayout)

1. `android:layout_margin`:外边距
2. `android:padding`：内边距
3. `android:orientation`:方向
   * vertical :垂直的
   * horizontal：水平的
4. `android:layout_weight`:权重
   1. 先分配没有该属性的元素，然后在分配该属性
   2. 水平状态下，将宽度设为0dp后，按比例划分，宽度不随内容而改变
5. `android:layout_gravity`:相关与父容器的重力

### 相对布局(RelativeLayout)

1. 默认左上
2. 相对于父容器居中
   `android:layout_centerInParent`
3. 相对于父容器居左，右，上，下
   `android:layout_alignParentLeft`
   `android:layout_alignParentRight`
   `android:layout_alignParentTop`
   `android:layout_alignParentBottom`
4. 居中
   `android:layout_centerHorizontal`      水平居中
   `android:layout_centerVertical `     垂直居中
5. 在参照的那一边
   在谁的左边，右边，上边，下边
   `android:layout_toLeftOf`
   `android:layout_toRightOf`
   `android:layout_above`
   `android:layout_below`
6. 和参照物某边线对其
   `layout_alignTop`
   `layout_alignBottom`
   `layout_alignLeft`
   `layout_alignRight`
   
### 约束布局(ConstraintLayout)

`app:layout_constraintHorizontal_bias`   水平偏移量
`app:layout_constraintVertical_bias`   竖直偏移量

偏移量是百分比，取值0~1，0.2对应20%，无该属性，默认居中（0.5）

`app:layout_constraintBottom_toBottomOf`   前一个bottom是控件底部，后一个bottom是参照物的底部

### 帧布局(FrameLayout)

`android:foreground`   前景设置
`android:foregroundGravity`   前景重力设置

### 表格布局(TableLayout)

`android:stretchColumns="0,1,2,3"` TableLayout的属性，表示那些列需要自动加宽，若需要所有列等分，则传入`*`

`android:shrinkColumns`TableLayout的属性，表示那些列需要自动收缩（控件数量过多挤不下是）

`android:collapseColumns`TableLayout的属性，表示那些列需要自动隐藏

如果直接在TableLayout中添加控件，那么控件和父容器等宽
如果想让控件出现在同一行，必须添加一个`<TableRow>`标签
在`<TableRow>`中的控件，宽度都是默认的`wrap_content`

### 网格布局(GridLayout)

`android:columnCount`  规定行
`android:rowCount`  规定列

也有`orientation`属性，自动排列，表示先行后列还是先列后行

`android:layout_columnSpan`内部组件属性，表示跨越几列
`android:layout_row`内部组件属性，表示跨越几行

`android:layout_gravity`内部组件属性，使用fill可以填充跨越的行列

## 清单文件：AndroidManifest.xml

清单文件，每创建一个`Activity`，就要在清单文件中使用`activity`标签进行声明

## ui基础控件

### 通用属性

1. `android:layout width` 和`android:layout_ height`

   `match_ parent`    填充整个父容器
   `wrap_ content`    根据所含内容确定
   正整数单位dp    精确大小

2. `android:id`

   `@id/valName`   使用已存在id
   `@ + id/valName`   添加新的id

3. `android:layout_ margin`

   正整数单位dp    和相邻控件或边缘的距离

4. `android:padding`

   正整数单位dp    控件内容距离控件边缘的距离

5. `android:background`

   十六进制的颜色值     颜色作为背景
   @mipmap/resourceId     图片作为背景
   
6. `android:layout gravity`和`android:gravity`

   前者是相对于父容器的重力方向，后者是自身内部的重力方向
   `center_ horizontal` 水平居中
   `center vertical`垂直居中
   `center`水平垂直居中
   `left`居左
   `right`居右
   `top`居顶
   `bottom`居底


7. `android:visibility`

   `visible`可见状态
   `invisible`不可见状态，但保留控件位置
   `gone`不可见状态，也不保留位置

### TextView

可以实现对长文本的显示处理，支持Html代码，内容有样式，链接效果

* `android:text`：设置字体大小
* `android:textColor`:设置字体颜色
* `android:lineSpacingMultiplier`:倍距，行距的倍数
* `android:lineSpacingExtra`:行距
* `android:singleLine`:只设置一行
* `android:ellipsize`：设置省略号位置
* `android:focusable`:设置可以获取焦点
* `android:focusableInTouchMode`：设置触摸式获取焦点
* `android:marqueeRepeatLimit`：设置跑马灯重复次数



如果是长文本，可以使用`res/values/strings.xml`文件放置字符串，在调用时使用`@string/名称`



长文本太长，`ScrollView`标签添加滚动条，但里面只能放一个直接控件,还可以使用上面后三个属性设置为跑马灯

### EditView

继承于TextView

* `android:inputType`:输入类型
   * `textPassword`:密码
   * `number` ：只能有整数
   * `numberSigned`：整数
   * `numberDecimal`：小数

* `android:maxLength`:最大长度

* `android:hint`：背景提示文字

### Button

继承于TextView

* `android:id`：为button添加id，可以被java代码获取到

Button注册点击事件的方法

实现注册点击事件，就是使用id获取到button对象后，使用`setOnClickListener(View.OnClickListener l)`方法绑定，主要是实现`View.OnClickListener`接口，然后重写其中的方法`onClick(View view)`

1. 自定义内部类

   创建一个内部类，实现`View.OnClickListener`接口

2. 匿名内部类

   创建一个匿名内部类，实现`View.OnClickListener`接口

3. 当前Activity去实现事件接口

   用当前的`Activity`类直接实现`View.OnClickListener`接口

4. 在布局文件中添加点击事件属性（xml文件中）

   在`xml`文件中的`button`标签添加`android:onClick="名称"`，然后在对应的`Activity`类创建名称对应的方法，不需要使用`setOnClickListener(View.OnClickListener l)`方法即可绑定
   
```java
package com.example.myapplication;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;

public class ButtonActivity extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_button);
        //1.获取按钮
        Button btn1 = findViewById(R.id.btn1);
        //点击事件：被点击时触发的事件
        MyClickListener mcl = new MyClickListener();
        //2.为按钮注册点击事件监听器
        btn1.setOnClickListener(mcl);


        Button btn2 = findViewById(R.id.btn2);
        //创建了匿名内部类
        btn2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Log.e("TAG","匿名内部类");
            }
        });

        //使用本类实现的View.OnClickListener接口
        Button btn3 = findViewById(R.id.btn3);
        btn3.setOnClickListener(this);
    }

    //本类实现了View.OnClickListener接口
    @Override
    public void onClick(View view) {
        Log.e("TAG","用本类实现了OnClickListener");
    }

    //创建了内部类
    class MyClickListener implements View.OnClickListener{

        @Override
        public void onClick(View view) {
            //在控制台输出一条语句
            Log.e("TAG","刚刚点击按钮是注册了内部类监听器对象");
        }
    }


    //xml文件中绑定的方法
    //参数，被点击的对象
    public void myClick(View v){
        switch (v.getId()){
            case R.id.btn4:
                Log.e("TAG","btn4通过XML绑定的点击事件");
                break;
            case R.id.btn5:
                Log.e("TAG","btn5通过XML绑定的点击事件");
        }
    }
}
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".ButtonActivity"
        android:orientation="vertical">

    <Button
            android:id="@+id/btn1"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="通过自定义内部类实现点击事件" />

    <Button
            android:id="@+id/btn2"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="通过匿名内部类实现点击事件" />

    <Button
            android:id="@+id/btn3"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="通过当前Activity实现点击事件" />

    <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="在XML文件实现点击事件"
            android:onClick="myClick"/>

    <Button
            android:id="@+id/btn5"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:onClick="myClick"
            android:text="在XML文件实现点击事件2" />
</LinearLayout>
```

### ImageView和ImageButton

ImageView用来显示和控制图像的空间，可以对它进行放大缩小，旋转等操作
ImageButton图片按钮，无text属性，以下属性都有

* `android：src` 指定前景图片资源  保证自身比例不变
* `android:background `设置背景，自身随标签大小变化

### ProgressBar

进度条，默认情况下是圆形，没有刻度，只是一个不断旋转的动画效果。通过设置style，可以显示传统的水平带刻度进度条

`style`       设置风格 `progressBarStyleHorizontal`水平进度条样式
`android:progress`  设置进度
`android:max`   设置最大值，默认100
`android:indeterminate`   设置进度条不停的滚动

```java
final ProgressBar progressBar = findViewById(R.id.progress);
//setProgress()设置进度条长度
progressBar.setProgress(80);
//android中，4.0之后不能在线程中操作空间
//进度条是个特例
new Thread(){
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            progressBar.setProgress(i);
            try {
                Thread.sleep(30);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}.start();
```

### CheckBox

多选框
```java
CheckBox checkBox = findViewById(R.id.checkBox);

//设置是否选中（设置状态）
checkBox.setChecked(false);
//获取状态，是否选中
final boolean ischecked = checkBox.isChecked();

checkBox.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
@Override
public void onCheckedChanged(CompoundButton compoundButton, boolean b) {
//当状态被改变的时候，可以处理很多的数据和UI
Log.d("tag",""+b);
}
});
```

### RadioButton

单选框

可以和RadioGroup一起使用，只能使用一个

和CheckBox的区别：
1. 通过点击无法变为未选中
2. 一组RadioButton，只能同时选中一个
3. 大部分ui空间都用圆形，CheckBox为方形

```xml
<RadioGroup
            android:layout_width="match_parent"
            android:layout_height="wrap_content">

        <RadioButton
                android:id="@+id/radioButton4"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:text="RadioButton" />

        <RadioButton
                android:id="@+id/radioButton5"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:text="RadioButton" />

        <RadioButton
                android:id="@+id/radioButton6"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:text="RadioButton" />
    </RadioGroup>
```

### TagButton

1. 切换程序中的状态
2. 两种状态
   * android:textOn
   * android:textOff
   * setChecked(boolean)
3. setOnCheckedChangeListener  设置监听

### SeekBar

滚动条

`max`最大值
`prograss`当前值


## Activity

一个类继承了`AppCompatActivity`才会成为Activity，并且必须正在`AndroidManifest.xml`声明

```xml
<activity android:name=".声明类名称"
    android:label="名称">
</activity>
```

`activity`标签拥有以下代码是启动入口，如果有两个以上`activity`标签，那么程序会有两个入口（应用列表有两个图标）

```xml
<intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <categoryandroid:name="android.intent.category.LAUNCHER"/>
</intent-filter>
```

在java文件中使用`onCreate`方法将`activity`和`layout`相关联

```java
@Override
protected void onCreate(@Nullable Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	//activity与layout关联
	setContentView(R.layout.activity_text);
}
```

### `Activity`的跳转

`intent`对象，意图，构造方法：从前一个activity跳转到后一个activity的意图
`Intent intent = new Intent(TextActivity.this, NewActivity.class);`

通过`startActivity(Intent)`方法进行跳转；
```java
button.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
    	//为textView动态设置文本
    	textView.setText(getString(R.string.app_name));
    	//跳转到一个NewActivity
    	//intent,意图，构造方法：从前一个activity跳转到后一个activity的意图
    	//在AndroidManifest.xml声明之后才可以跳转
    	Intent intent = new Intent(TextActivity.this, NewActivity.class);
    	startActivity(intent);
    	}
    });
}
```

### `Activity`启动模式

在`mainfest`中的`activity`标签中设置
`android:launchMode="standard"`

1. standard(标准模式)
多个`Activity`之间的跳转，使用了栈栈，跳转后新`Activity`进栈，返回后`Activity`出栈
多次调用同一个`Activity`，多次进栈
例：栈：a，b，c     再次跳转c，栈：a，b，c，c
2. singleTop(顶部复用模式)

如果发现顶部还有相同的`Activity`，复用
例：
栈：a，b，c     再次跳转c，栈：a，b，c
栈：a，b，c     再次跳转b，栈：a，b，c，b
3. singleTask(删除已存在栈之后的元素)

如果发现栈里有`Activity`，先将栈里**该`Activity`**上的元素清除，然后复用
例：栈：a，b，c，d    再次跳转b，栈：a，b
4. singleInstance(独自占用一个栈)

例：栈：a，b，c，d    再次跳转e，栈1：a，b，c，d    栈2：e
从栈2返回，直接退出程序


使用`intend`对象的`addFlag`,可以对栈进行操作
```java
intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);//清除栈
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);//开新栈
```



## Menu

在`res`文件夹下创建`menu`资源文件夹，然后新建`xml`文件

### 选项菜单(OptionMenu)

右上角的菜单
使用时要先在java文件中重写`onCreateOptionsMenu`方法

`app:showAsAction` 显示属性
   `always`:在操作栏中直接显示,有图片时直接显示图片，无该属性图片不显示
   `withTex`:图片文本一起显示
   `never`:不在标题栏显示
   `ifroom`:有空间就显示
   `collapseActionView`:折叠试图，结合ifroom属性

`onOptionsItemSelected`OptingMenu菜单项选中的方法

**`onOptionsItemSelected`必须返回true，否则菜单不显示**

子菜单最多有两层

```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto">
    <item
            android:id="@+id/save"
            android:icon="@mipmap/ic_launcher"
            android:title="保存"
            app:showAsAction="withText" />
    <item
            android:id="@+id/setting"
            android:title="设置" />
    <item android:title="更多设置">
        <menu>
            <item
                    android:id="@+id/exit"
                    android:title="退出" />
            <item android:title="子菜单2" />
            <item android:title="子菜单3" />
        </menu>
    </item>
</menu>
```
```java
//创建OptingMenu
@Override
public boolean onCreateOptionsMenu(Menu menu) {
	//加载菜单资源
	getMenuInflater().inflate(R.menu.option, menu);
    //必须返回true
	return true;
}
//OptingMenu菜单项选中的方法
@Override
public boolean onOptionsItemSelected(@NonNull MenuItem item) {
    switch (item.getItemId()) {
        case R.id.save:
        	Toast.makeText(this, "保存", Toast.LENGTH_SHORT).show();
        	break;
        case R.id.setting:
        	Toast.makeText(this, "设置", Toast.LENGTH_SHORT).show();
        	break;
        case R.id.exit:
        	finish();//退出程序
        	break;
    }
    return super.onOptionsItemSelected(item);
}
```

纯java代码实现
```java
	//创建OptingMenu
    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        //加载菜单资源
        //纯java代码设计
        /*
        设置
        更多
            添加
            删除
         */
        //参数1：组id, 参数2：菜单项id，参数3：序号
        menu.add(1, 1, 1, "设置");
        SubMenu sub = menu.addSubMenu(1, 2, 2, "更多");
        sub.add(2, 3, 1, "添加");
        sub.add(2, 4, 2, "删除");
        //必须返回true
        return true;
    }

    //OptingMenu菜单项选中的方法
    @Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
        switch (item.getItemId()) {
            case 1:
                Toast.makeText(this, "设置", Toast.LENGTH_SHORT).show();
                break;
            case 2:
                Toast.makeText(this, "更多", Toast.LENGTH_SHORT).show();
                break;
            case 3:
                Toast.makeText(this, "添加", Toast.LENGTH_SHORT).show();
                break;
            case 4:
                Toast.makeText(this, "删除", Toast.LENGTH_SHORT).show();
                break;
        }

        return super.onOptionsItemSelected(item);
    }
```

### 上下文菜单（ContextMenu）

长按出现的菜单

先绑定，然后操作

1. 注册
2. 创建 覆盖`onCreateContextMenu`
3. 菜单项的操作 覆盖`onContextItemSelected`

```java
	@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //ctx_button:延时ContextMenu
        //1.注册
        registerForContextMenu(findViewById(R.id.ctx_button));
        //2.创建 覆盖onCreateContextMenu

        //3.菜单项的操作 覆盖onContextItemSelected
        //4.为按钮设置上下文操作模式
        //4.1实现接口ActionMode.CallBack
        //4.2在View的长按事件中去启动上下文操作模式
    }

    @Override
    public void onCreateContextMenu(ContextMenu menu, View v, ContextMenu.ContextMenuInfo menuInfo) {
        getMenuInflater().inflate(R.menu.context, menu);
    }

    @Override
    public boolean onContextItemSelected(@NonNull MenuItem item) {
        switch (item.getItemId()){
            case R.id.delete:
                Toast.makeText(this, "删除", Toast.LENGTH_SHORT).show();
                break;
            case R.id.opear1:
                Toast.makeText(this, "操作1", Toast.LENGTH_SHORT).show();
                break;
            case R.id.opear2:
                Toast.makeText(this, "操作2", Toast.LENGTH_SHORT).show();
                break;
        }
        return super.onContextItemSelected(item);
    }
```

4. 为按钮设置上下文操作模式
   1. 实现接口ActionModeCallBack
   2. 在View的长按事件中去启动上下文操作模式


```java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //4.为按钮设置上下文操作模式
        //4.1实现接口ActionMode.CallBack
        //4.2在View的长按事件中去启动上下文操作模式
        findViewById(R.id.ctx_button).setOnLongClickListener(new View.OnLongClickListener() {
            @Override
            public boolean onLongClick(View v) {
                startActionMode(cb);
                return false;
            }
        });
    }

    ActionMode.Callback cb = new ActionMode.Callback() {
        //创建，在启动上下文操作（startActionMode(Callback)）模式时调用
        @Override
        public boolean onCreateActionMode(ActionMode mode, Menu menu) {
            Log.e("TAG", "创建");
            getMenuInflater().inflate(R.menu.context, menu);
            return false;
        }

        //在创建方法后进行调用
        @Override
        public boolean onPrepareActionMode(ActionMode mode, Menu menu) {
            Log.e("TAG", "准备");
            return false;
        }

        @Override
        public boolean onActionItemClicked(ActionMode mode, MenuItem item) {
            Log.e("TAG", "点击");
            switch (item.getItemId()) {
                case R.id.delete:
                    Toast.makeText(MainActivity.this, "删除", Toast.LENGTH_SHORT).show();
                    break;
                case R.id.opear1:
                    Toast.makeText(MainActivity.this, "操作1", Toast.LENGTH_SHORT).show();
                    break;
                case R.id.opear2:
                    Toast.makeText(MainActivity.this, "操作2", Toast.LENGTH_SHORT).show();
                    break;
            }
            return true;
        }

        //上下文模式结束时被调用
        @Override
        public void onDestroyActionMode(ActionMode mode) {
            Log.e("TAG", "结束");
        }
    };
```

### 弹出菜单（PopupMenu）

1. 实例化PopupMenu对象(参数2：出现在谁的下方)
2. 加载菜单资源：利用MenuInflater将mean资源加载到PopupMenu.getMenu()所返回的Menu对象中,将R.id.xx对于的菜单资源加载到弹出式菜单中
3. 为PopupMenu设置点击监听器
4. 显示PopupMenu
```java
	@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //popup_btn:演示PopupMenu
        final Button popupBtn = findViewById(R.id.popup_button);
        popupBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //1.实例化PopupMenu对象(参数2：出现在谁的下方)
                PopupMenu menu = new PopupMenu(MainActivity.this, popupBtn);
                //2.加载菜单资源：利用MenuInflater将mean资源加载到PopupMenu.getMenu()所返回的Menu对象中
                //将R.id.xx对于的菜单资源加载到弹出式菜单中
                menu.getMenuInflater().inflate(R.menu.popup, menu.getMenu());
                //3.为PopupMenu设置点击监听器
                menu.setOnMenuItemClickListener(new PopupMenu.OnMenuItemClickListener() {
                    @Override
                    public boolean onMenuItemClick(MenuItem item) {
                        switch (item.getItemId()){
                            case R.id.copy:
                                Toast.makeText(MainActivity.this,"复制",Toast.LENGTH_SHORT).show();
                                break;
                            case R.id.paste:
                                Toast.makeText(MainActivity.this,"粘贴",Toast.LENGTH_SHORT).show();
                                break;
                        }
                        return false;
                    }
                });
                //4.显示PopupMenu
                menu.show();
            }
        });
```


## 对话框

### AlertDialog

使用方法：
1. 创建构建器
   `AlertDialog`构造方法`protected`，所以用构建器
   `AlertDialog.Builder builder = new AlertDialog.Builder(this);`
2. 设置标题信息
3. 设置按钮
4. 创建并展示



第一种方法,在其他View的事件中使用
创建按钮有三个方法，决定按钮的摆放位置

1. `setPositiveButton`确定性质按钮
2. `setNegativeButton`取消性质按钮
3. `setNeutralButton`中立性质按钮
```java
//1.实例化一个Builder
//AlertDialog构造方法protected，所以用构建器
AlertDialog.Builder builder = new AlertDialog.Builder(this);
//2.设置对话框样式
builder.setTitle("提示");
builder.setMessage("您确定退出程序吗");
//3.设置按钮
//传一个点击事件
builder.setPositiveButton("确定", new DialogInterface.OnClickListener() {
	@Override
	public void onClick(DialogInterface dialog, int which) {
		finish();
	}
});
builder.setNegativeButton("取消",null);
//4.显示对话框
builder.show();
//builder.show();等价于
//AlertDialog dialog = builder.create();
//dialog.show();
```

第二种方法（不推荐）
在设置按钮时，如果不想进行操作，不能出入null
```java
public void showNormalDialog(){
        AlertDialog dialog = new AlertDialog.Builder(this).create();
        dialog.setTitle("提示");
        dialog.setMessage("您确定退出程序吗");
        dialog.setButton(DialogInterface.BUTTON_POSITIVE, "确定", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                //setButton什么都不做，但必须传参数，所以有这个匿名内部类
            }
        });
        dialog.show();
}
```

### 自定义对话框

1. 设置样式，`layout`中新建`xml`
2. 设计style(style.xml)
```xml
<style name="myDialog" parent="android:style/Theme.Dialog">
        <!--无标题-->
        <item name="android:windowNoTitle">true</item>
        <!--透明色背景-->
        <item name="android:windowBackground">@android:color/transparent</item>
</style>
```

3. 将第一步布局应用到自定义对话框（创建新类继承Dialog），重写构造方法，在参数二传入style

```java
package com.example.myapplication;

import android.app.Dialog;
import android.content.Context;
import android.view.View;

import androidx.annotation.NonNull;

public class MyDialog extends Dialog {
    //构造方法默认style
    public MyDialog(@NonNull Context context) {
        super(context);
        //为对话框设置布局
        setContentView(R.layout.dialog_layout);
    }

    //传递自己的style
    public MyDialog(@NonNull Context context, int themeResId) {
        super(context, themeResId);
        //为对话框设置布局
        setContentView(R.layout.dialog_layout);

        findViewById(R.id.yes_btn).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                System.exit(0);//退出
            }
        });

        findViewById(R.id.no_btn).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //对话框消失
                dismiss();
            }
        });
    }
}
```

4. 实例化自己的Dialog，并展示

```java
MyDialog md = new MyDialog(this, R.style.myDialog);
md.show();
```

### PopupWindow

1. 实例化一个弹窗
2. 设置（背景，动画）
3. 显示

```java
//设置popupWindow方法
private void showPopupWindow(View view) {
    //准备弹窗所需要的的试视图对象
    View v = LayoutInflater.from(this).inflate(R.layout.popop_layout, null);

    //1.实例化对象
    //参数1：用在弹窗中的view
    //参数2,3：弹窗的宽高
    //参数4：能否获取焦点
    final PopupWindow window = new PopupWindow(v, 550, 120, true);

    //2.设置(背景动画)
    //设置背景(透明色)
    window.setBackgroundDrawable(new ColorDrawable(Color.TRANSPARENT));
    //设置能响应外部的点击事件
    window.setOutsideTouchable(true);
    //设置弹窗能响应点击事件
    window.setTouchable(true);
    //设置动画
    //1)创建动画资源(res/anim)
    //2)创建style应用动画资源(style.xml)
    //3)对当前弹窗的动画风格设置为第二部的资源索引
    window.setAnimationStyle(R.style.translate_anim);

    //3.显示
    //参数1（anchor）：参照物，DropDown下方
    //参数2，3：相对于anchor在x，y方向上的偏移量
    window.showAsDropDown(view, -550, 50);

    //为弹窗中的文本添加点击事件
    //按钮在pupop——layout中，也就是v中
    v.findViewById(R.id.choose).setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            Toast.makeText(MainActivity.this, "点击了选择", Toast.LENGTH_SHORT).show();
            window.dismiss();
        }
    });
    v.findViewById(R.id.copy).setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            Toast.makeText(MainActivity.this, "点击了复制", Toast.LENGTH_SHORT).show();
            window.dismiss();
        }
    });
    v.findViewById(R.id.chooseAll).setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            Toast.makeText(MainActivity.this, "点击了全选", Toast.LENGTH_SHORT).show();
            window.dismiss();
        }
    });
}
```

### ArrayAdapter(文本适配器)

## Fragment

必须绑定在`Activity`中，一个`Activity`可以有多个`Fragment`

### 静态加载（xml）

   * 写好类继承`Fragment`，并绑定视图
   * 创建`Fragment`类对应的layout文件
   * 在要调用的`Activity`的layout文件中调用`Fragment标签`，布局文件里写好后增加name属性，指向类

```java
//绑定视图
//创建视图
    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        //参数1 布局文件 参数2：当前的ViewGroup 参数3：是否绑定根布局
        View view = inflater.inflate(R.layout.fragment_list,container,false);

        TextView textView = view.findViewById(R.id.textView);
        textView.setText("llll");
        return view;
    }
```

### 动态加载（java code）

   * 容器，在xml文件里设置好容器
   * 创建fragment
   * 把fragment放进容器里

```java
//动态加载fragment
//1.容器，在xml文件里设置好容器
//2.创建fragment
ListFragment fragment = new ListFragment();
//3.把fragment放进容器里
//获取fragment事物对象，然后开启事物，在对应容器里加载加入fragment
getSupportFragmentManager()
        .beginTransaction()
        .add(R.id.listFragment, fragment)
        .commit();
//一个Fragment对象只能用一次
//add增加，remove移除，replace替换
getSupportFragmentManager()
        .beginTransaction()
        .add(R.id.detailFragment, new ListFragment())
        .commit();
```

### Activity->Fragment传值

setArguments方法，参数为bundle，里面储存数据
可以在Fragment里创建一个方法，传值为数据，然后返回一个Fragment对象，这样就不需要构造方法了
之后可以使用getArguments获取相关数据、

```java
public static final String BUNDLE_TITLE = "bundle_title";

public static ListFragment newInstance(String title) {
    ListFragment fragment = new ListFragment();
    Bundle bundle = new Bundle();
    bundle.putString(BUNDLE_TITLE, title);
    fragment.setArguments(bundle);
    return fragment;
}
```

### Fragment->Activity传值

1. 在`Fragment`创建一个监听器接口，然后对接口设置`set`方法，然后设置全局变量
2. 在`Activity`里实现接口，在`Fragment`里调用接口方法时就把值传到`Activity`里了

### ViewPage

1. 创建视图列表
2. 将列表输入到PagerAdapter
3. 创建ViewPage对象，并传入PagerAdapter

## 网络

### 从服务器获取数据

1. 实例化一个URL对象
2. 获取HttpURLConnection对象

   GET获取数据
   POST提交数据
3. 设置请求连接属性
4. 获取响应码，判断连接结果码
5. 读取输入流，解析


安卓9.0使用http必须创建安全配置文件，
* 在`res`文件夹下创建`xml/network-security-config`
* 增加cleartextTrafficPermitted属性
* mainfest文件中申明