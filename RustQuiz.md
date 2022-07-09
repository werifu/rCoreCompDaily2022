# Solutions for Rust Quiz

## #1

https://dtolnay.github.io/rust-quiz/1

What output

```rust
macro_rules! m {
    ($($s:stmt)*) => {
        $(
            { stringify!($s); 1 }
        )<<*
    };
}

fn main() {
    print!(
        "{}{}{}",
        m! { return || true },
        m! { (return) || true },
        m! { {return} || true },
    );
}
```

解答：（原文的很详细，推荐）

1. 根据官方文档，stmt的解释是

   > a [*Statement*](https://doc.rust-lang.org/reference/statements.html) without the trailing semicolon (except for item statements that require semicolons)
   > 没有__后接分号__的statement

   题目解释里也给了另一个例子

   ```rust
   // Items are statements.
   struct S { x: u64 }
   
   // Let-bindings are statements.
   let mut s = S { x: 1 }
   
   // Expressions are statements.
   s.x + 1
   ```

   一个语句少了分号就是stmt，语法块也算其中一种，因为不需要加分号（比如for {}, if {}）

2. stringify!的作用纯粹是把里面的内容套上双引号，不会有语法报错而已

3. `<<*`代表使用<<来连接多个匹配到的stmt，比如我匹配`1;2;3`在这个宏里会被转换成`1<<2<<3`，更常见的用法是`,*`，在很多宏里能看到

4. 分析题目里的各个stmt

   1. `return || true`：这个很坑，乍一眼觉得很语法错误，但实际上是返回了个闭包，类似`return ()=>true`，那么这就是一个stmt，因为这是一个完整的陈述/语句少了个分号，因此被展开为`{stringify!(return || true);1}`，然后这个代码块会忽略掉分号前的内容，等价于`{1}`，因此会打印1

   2. `(return) || true`：中间的`||`是逻辑或，`(return)`是一个语句，返回发散type`!`，因此这是一个完整的statement

   3. `{return} || true`：这也是闭包，`{return}`却自己是一个statement，这个代码等价于

      ```rust
      {
        return
      }
      || true
      ```

      因此展开，简化掉stringify，成为`{1}<<{1}`，就是2

5. 所以返回112

## #16

https://dtolnay.github.io/rust-quiz/16

What output

```rust
fn main() {
    let mut x = 4;
    --x;
    print!("{}{}", --x, --x);
}
```

解答：

1. __Rust并没有像C的自增/自减操作__
2. `--x`代表负负x，所以负负得正……
3. 打印44

## #21

https://dtolnay.github.io/rust-quiz/21

What output

```rust
trait Trait {
    fn f(&self);
}

impl<F: FnOnce() -> bool> Trait for F {
    fn f(&self) {
        print!("1");
    }
}

impl Trait for () {
    fn f(&self) {
        print!("2");
    }
}

fn main() {
    let x = || { (return) || true; };
    x().f();

    let x = loop { (break) || true; };
    x.f();

    let x = || { return (|| true); };
    x().f();

    let x = loop { break (|| true); };
    x.f();

    let x = || { return || true; };
    x().f();

    let x = loop { break || true; };
    x.f();
}
```

解答：

1. 考察的是逻辑或以及闭包的区分
2. 打印2。x是一个闭包，返回()（`(return)`表示`return ()`）
3. 打印2。`(break)`的类型也是never type `!`，所以x类型是`()`
4. 打印1。x是返回闭包的闭包。
5. 打印1。这是个__break-with-value__的表达式，value是break时作为loop表达式的值，因此x是`|| ture`
6. 打印1。同4.
7. 打印1。在Rust1.19前解析为`(break) || true`，在Rust1.19后__非故意地__解析成了`break (|| true)`
8. 答案是221111

## #26

https://dtolnay.github.io/rust-quiz/26

What output

```rust
fn main() {
    let input = vec![1, 2, 3];

    let parity = input
        .iter()
        .map(|x| {
            print!("{}", x);
            x % 2
        });

    for p in parity {
        print!("{}", p);
    }
}
```

解答：

1. map返回的是一个迭代器，是lazy的，只有在被迭代上的时候才会执行里面的函数
2. 在迭代的时候是对一个结果一个结果进行操作的，因此打印顺序是1, 1%2, 2, 2%2, 3, 3%3
3. 结果为112031

## #27

https://dtolnay.github.io/rust-quiz/27

What output

```rust
trait Base {
    fn method(&self) {
        print!("1");
    }
}

trait Derived: Base {
    fn method(&self) {
        print!("2");
    }
}

struct BothTraits;
impl Base for BothTraits {}
impl Derived for BothTraits {}

fn dynamic_dispatch(x: &dyn Base) {
    x.method();
}

fn static_dispatch<T: Base>(x: T) {
    x.method();
}

fn main() {
    dynamic_dispatch(&BothTraits);
    static_dispatch(BothTraits);
}
```

解答：（答案详尽，推荐）

这个考察特征的方法重叠时如何判断，分为静态和动态两种情况（即泛型/ dyn Trait）

1. 调用时肯定调用trait的默认方法实现
2. Derived: Base表示Base作为Derived的supertrait，实现Derived的必须实现了Base，supertraits并不是继承关系，只是一种约束。
3. 实际上Derived跟Base并没有关系。
4. 函数签名里的参数都是Base，因此在编译期都会解析成调用Base的方法。

