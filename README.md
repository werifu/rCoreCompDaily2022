## 简介
参加2022 rCore Comp的学习日志

## 目录

#### 7.05

* 一边阅读[《Rust语言圣经》](https://course.rs/about-book.html) 一边做rustlings（接近40道）
* 一些新的收获：
  * match 模式匹配是一个非常有魅力的设定，能像switch一样区分出各种各样可预见的值，还能进行匹配的赋值，在语义上有极其强大的表达能力；此外，rust中区分表达式和语句使得match这个行为本身也能有一个值，像是三目运算符的加强版
  * 实际上模式匹配无处不在，解构也是一种匹配，在js里非常常见而且优雅，@绑定给match的项增加了表达能力（可以用于进一步约束）
  * [给enum添加方法，如何匹配自身？](https://stackoverflow.com/questions/33925232/how-to-match-over-self-in-an-enum) match *self
  * rust在泛型&特征方面提供了巨多语法糖，<T: SomeTrait>、where、impl SomeTrait，看上去很方便，不过怀疑在实践中会带来一定的混乱。
  * 特征对象其实就是一个实现了某些trait的对象，但是编译期无法确定具体对象类型，因此只能在运行时创建，使用& / Box\<dyn Trait\>用智能指针来创建（特征对象的动态分发）

#### 7.06

* 一边阅读圣经一边做rustlings，进度53/84

  * 在error6卡到了一下，对map_err的使用有了更深的理解，rust的数据处理方式感觉跟js真的很像

* 收获：

  * vector的引用：

    对一个元素有不可变引用后，不能拥有该vector的可变引用（即不能同时push又同时修改其中某个元素），虽然看起来风牛马不相及，安全问题在哪？

    > 原因在于：数组的大小是可变的，当旧数组的大小不够用时，Rust 会重新分配一块更大的内存空间，然后把旧数组拷贝过来。这种情况下，之前的引用显然会指向一块无效的内存，这非常 rusty —— 对用户进行严格的教育

    这个点确实没想到

* to_string, to_owned, into()的区别

* 语法糖?会让Err的情况下一直返回错误，而main返回()，所以main里不能用func()?吗
  ：错误的，main也可以返回Result

  * 比Go文明许多的设计，代替了if err != nil return err这样的句子，让链式调用更方便

  