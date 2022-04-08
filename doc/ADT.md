# 10分でできる魔法：代数的データ型
### By 「玩火」 
#### Translated By 「sonka」

> 前提知識：JAVAの基礎

## 直積型(Product type)

直積型とは、同時に複数の値を持つ型である。例えば、Javaのクラスはいつくかのフィールドを持つ：

```java
final class Student {
    String name;
    int id;
}
```

上記のコートでは、Studentという型はStringとintを同時に持っている。つまり　`String * int` Stringとintの「積」の形で表せる。


### 直和型

直和型とは、いつくかの型中の一つを表す型である。Javaでは、継承で表せる。

```java
interface SchoolPerson {}
final class Student implements SchoolPerson {
    String name;
    int id;
}
final class Teacher implements SchoolPerson {
    String name;
    String  office;
}
```

SchoolPersonはStudentとTeacherのどちらかの一つで、
StudentとTeacherの「和」として理解できる。つまり、`String * int + String * String` と表せる。さらに、
`instanceof`でSchoolPersonの型を知ることができる。

## 代数的データ型

直和型と直積型で作れるデータ型は代数的データ型と呼ばれる。ここでの「代数」とは、和と積のことである。

直和型での列挙と直積型での結合で、Javaの基本的なデータ型を作ることができる。例えば、ブール型の場合では、

```java
interface Bool {}
final class True implements Bool {}
final class False implements Bool {}
```
ここで、`t instanceof True` でtの値を評価することできる。

さらに、下のように自然数を表現：

```java
interface Nat {}
final class Z implements Nat {}
final class S implements Nat {
    Nat value;
    
    S(Nat v) { value = v; }
}
```

ここでは、自然数のペアノの公理が使われた。自然数は、0（上記のZ）か、前の自然数プラス1（上記のS）である。例えば3は、
 `new S(new S(new S(new Z))`の形で表せる。

 連結リストは：

 ```java
interface List<T> {}
final class Nil<T> implements List<T> {}
final class Cons<T> implements List<T> {
    T value;
    List<T> next;
    
    Cons(T v, List<T> n) {
        value = v;
        next = n;
    }
}
```
`[1, 3, 4]` は `new Cons(1, new Cons(3, new Cons(4, new Nil)))`の形で表現できる。

さらに面白いのは、代数的データ型はデータ型の可能なインスタンスの数に対応する。

直積型は明らに各フィールドの場合の数の積で、直和型は場合の数の和と理解できる。

例えば，Bool型は`1 + 1` なので、TrueとFalseの二つのインスタンスを持っている。一方で、Nat型は`1+1+1+...`で、各1は自然数に対応する。さらに、List型は`1+x(1+x(...))`　で、つまり`1+x^2+x^3...`なので、実はList内のオブジェクトの数に対応する。

### 応用

ADTは木構造の実装に合う。例えばJSONの解析では、次のようなデータ構造を構築することができる。

```java
interface JsonValue {}
final class JsonBool implements JsonValue {
    boolean value;
}
final class JsonInt implements JsonValue {
    int value;
}
final class JsonString implements JsonValue {
    String value;
}
final class JsonArray implements JsonValue {
    List<JsonValue> value;
}
final class JsonMap implements JsonValue {
    Map<String, JsonValue> value;
}
```