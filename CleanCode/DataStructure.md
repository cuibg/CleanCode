## 对象和数据结构

> 将变量设置为私有有一个理由：我们不想让其他人依赖这些变量，为什么还有人给对象添加赋值器和取值器呢

### 数据抽象

1. 隐藏实现关乎抽象，类并不简单的采用取值器和赋值器将其作为变量推向外间，而是暴露接口，以便用户无需了解数据的实现就能操作数据本体
2. 以最好的方式呈现某个对象包含的数据，需要做严肃的思考，傻乐着乱加取值器和赋值器，是最坏的选择

### 数据、对象的反对称性

1. 对象把数据隐藏于抽象之后，暴露操作数据的函数
2. 数据结构暴露其数据，没有提供有意义的函数
3. 对象与数据结构

	* 过程式编码便于在不改动既有数据结构的前提下添加新函数，面向对象代码便于在不改动既有函数的前提下添加新类
	* 过程式代码难以添加新的数据结构，因为必须修改所有函数。面向对象代码难以添加新函数，因为必须修改所有类

### 得墨忒耳律

> 对象不应该了解他所操作对象的内部结构

#### 类C的方法f只应该调用以下对象的方法

1. C的方法
2. C的实体变量持有的对象
3. 由f所创建的对象
4. 作为参数传给f的对象

#### 火车失事事件

* 不要使用哪种得到一个对象接着又得到另外一个对象的方法

	```
	ctxt.getOptions().getScratchDir().getAbsolutePath();
	```

#### 混杂

1. 代码中最好不要混杂，既有过程编码又有面向对象编码

#### 隐藏结构

### 数据传送对象

1. 最为精炼的数据结构，是一个只有公共变量，没有函数的类，这种结构有时被称为数据传送对象

2. bean类看起来oo舒服些，但是没有任何好处

### 总结

1. 对象暴露行为，隐藏数据。便于添加新对象类型和无需修改行为，同时也难以在既有的对象中添加新行为。
2. 数据结构暴露数据，没有明显的行为