## 简介
参加2022 rCore Comp的学习日志

## 目录

#### 7.05

* 一边阅读[《Rust语言圣经》](https://course.rs/about-book.html) 一边做rustlings（接近40道）
* 一些新的收获：
  * match 模式匹配是一个非常有魅力的设定，能像switch一样区分出各种各样可预见的值，还能进行匹配的赋值，在语义上有极其强大的表达能力；此外，rust中区分表达式和语句使得match这个行为本身也能有一个值，像是三目运算符的加强版
  * 实际上模式匹配无处不在，解构也是一种匹配，在js里非常常见而且优雅，@绑定给match的项增加了表达能力（可以用于进一步约束）
  * [给enum添加方法，如何匹配自身？](https://stackoverflow.com/questions/33925232/how-to-match-over-self-in-an-enum)
  * rust在泛型&特征方面提供了巨多语法糖，<T: SomeTrait>、where、impl SomeTrait，看上去很方便，不过怀疑在实践中会带来一定的混乱。
  * 特征对象其实就是一个实现了某些trait的对象，但是编译期无法确定具体对象类型，因此只能在运行时创建，使用& / Box\<dyn Trait\>用智能指针来创建（特征对象的动态分发）



