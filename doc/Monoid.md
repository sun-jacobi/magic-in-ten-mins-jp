# 10分でできる魔法：モノイド（単系）
### By 「玩火」 
#### Translated By 「sonka」

> 前提知識：JAVAの基礎

##　半群(Semigroup)

半群とは、集合とその上の結合的な二項演算からなる代数構造である。二項演算とは、集合`A` 上の、`A`の二つの元を`A`自身に写す演算のことで、`<> : (A, A) -> A​`と表せる。さらに、「結合」とは、`(a <> b) <> c == a <> (b <> c)`のような性質である。この時、代数構造`{<>, A}`は半群と呼ばれる。

例えば、自然数集合とその上の加法、または乗法は半群となる。そのほかに、文字列とその上の連接からなる構造も半群と考える。

## モナイド（Monoid）

モナイドは、単位元をもつ半群である。半群`{<>, A}`で、ある元 `a`存在して、`A`の全ての元`x`に対して、`x <> a` と `a <> x` が `x`に等しいなら、`a`は`{<>, A}`の単位元と呼ばれる。

例えば、{+, 自然数}`の単位元は0で、`{*, 自然数}`の単位元は1である。文字列の例`{+, 文字列からなる集合}`に対しても、空文字列が単位元である。

Javaで書くと、
```java
interface Monoid<T> {
    T empty();
    T append(T a, T b);
    default T appends(Stream<T> x) {
        return x.reduce(empty(), this::append);
    }
}
```
となる。

##　応用：Optional

Java8では、`Optional`という、「値がnullであるかもしれない」を表すクラスがある。その上でモノイドを定義することができ、

```java
class OptionalM<T> implements Monoid<Optional<T>> {
    public Optional<T> empty() {
        return Optional<T>.empty();
    }
    public Optional<T> 
    append(Optional<T> a, Optional<T> b) {
        if (a.isPresent()) return a;
        else return b;
    }
}
```
これでいつくかのOptionalの中での初めて空でない値が得られる。
例えば、
```java
new OptionalM<int>.appends(Stream.of(try1(), try2(), try3(), try4()))
```

## 応用: Ordering
`Comparable`のインターフェースの上で、
```java
class OrderingM implements Monoid<int> {
    public int empty() { return 0; }
    public int append(int a, int b) {
        if (a == 0) return b;
        else return a;
    }
}
```
これで、複数の比較操作の優先順位を表現でき、

```java
class Student implements Comparable {
    String name;
    String sex;
    Date birthday;
    String from;
    public int compareTo(Object o) {
        Student s = (Student)(o);
        return new OrderingM.appends(Stream.of(
            name.compareTo(s.name),
            sex.compareTo(s.sex),
            birthday.compareTo(s.birthday),
            from.compareTo(s.from)
        ));
    }
}
```
雑な`if-else`文よりはだいぶ綺麗になってきた。

## 拡張
Monoidインターフェースの中にdefault
メソッドを加えると:

```java
interface Monoid<T> {
    //...
    default T when(boolean c, T then) {
        if (c) return then;
        else return empty();
    }
    default T cond(boolean c, T then, T els) {
        if (c) return then;
        else return els;
    }
}

class Todo implements Monoid<Runnable> {
    public Runnable empty() {
        return () -> {};
    }
    public Runnable 
    append(Runnable a, Runnable b) {
        return () -> { a(); b(); };
    }
}
```
これで次のような機能が実現できる
```java
new Todo.appends(Stream.of(
    logic1,
    () -> { logic2(); },
    Todo.when(condition1, logic3)
))
```



