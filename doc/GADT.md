# 10分でできる魔法：一般的代数的データ型
### By 「玩火」 
#### Translated By 「sonka」

> 前提知識：JAVAの基礎、ADT

 ADTで次のような型を構築することができる：

 ```java
//コンストラクターは省略
interface Expr {}
class IVal implements Expr {
    Integer value;
}
class BVal implements Expr {
    Boolean value;
}
class Add implements Expr {
    Expr e1, e2;
}
class Eq implements Expr {
    Expr e1, e2;
}
 ```
 
 しかしここは問題点がある。`Bval`上は加法が定義されないが、ここでの実装はこうした状況を防ぐことができない。従って、この実装は不十分（型安全ではない）である。

 それを解決するため、`Expr`に型パラメータを付け添えることができる。つまり、
```java
//コンストラクターは省略
interface Expr<T> {}
class IVal implements Expr<Integer> {
    Integer value;
}
class BVal implements Expr<Boolean> {
    Boolean value;
}
class Add implements Expr<Integer> {
    Expr<Integer> e1, e2;
}
class Eq<T> implements Expr<Boolean> {
    Expr<T> e1, e2;
}
```
こうした手法で`Boolean`の間の加法を避けることができ、型安全になった。

ここで注意すべきなのは、classの親クラスは`Expr<T>`じゃなくて、パラメータを含む`Expr`のことである。これはADTとは違うので、一般的代数的データ型(Generalized Algebraic Data Type, GADT）と呼ばれる。




