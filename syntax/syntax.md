# 各種文法

## 参考

- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
- [TypeScript Deep Dive](https://basarat.gitbook.io/typescript/)
- [TypeScript Deep Dive 日本語版](https://typescript-jp.gitbook.io/deep-dive/)
- [仕事ですぐに使える TypeScript](https://future-architect.github.io/typescript-guide/async.html)
- [Qiita TypeScript の変数の末尾のエクスクラメーション（感嘆符）の意味](https://qiita.com/zigenin/items/364264a6cf635b962542)
- [TypeScript for the New Programmer](https://www.typescriptlang.org/docs/handbook/typescript-from-scratch.html)

## `var` と `let`

JavaScript において、`var` 変数は関数スコープ (functional scope) と呼ばれ、C# や Java などの変数がブロックスコープ (blocked scope) である言語と異なっている。

下記の JavaScript のコードを、もしブロックスコープの考え方で見てみると、出力されるのは `123` であると考えてしまうかもしれないが、JavaScript では `{}` が新しい変数スコープ (variable scope) を作成しないため、変数 `foo` は `if` ブロックの内側にあっても外側にあっても同一のものを参照しているということである。

```javascript
var foo = 123;
if (true) {
  var foo = 456;
}
console.log(foo); // 456
```

これがエラーの原因となることがあるので、TypeScript では `let` キーワードを導入して、ブロックスコープ (blocked scope) の変数を定義できるようにしている。つまり、`var` を使う代わりに `let` を使うことで、ブロックの外側で定義されているかもしれない変数とは異なるものであることを保証して、その変数にアクセスすることができるということである。

```typescript
let foo = 123;
if (true) {
  let foo = 456;
}
console.log(foo); // 123
```

例えば下記のような例では、ループ処理の外側の `index` 変数は、ループ内の `let` キーワードを付けられた `index` 円数とは区別されるので、そのループを出たあとでも、`console.log(index)` によって出力されるのは `0` であるということである。

```typescript
var index = 0;
var array = [1, 2, 3];
for (let index = 0; index < array.length; index++) {
  console.log(array[index]);
}
console.log(index); // 0
```

よって、TypeScript を書く原則として、コードの可読性を大きく改善することになるので、可能なときにはいつも `var` ではなく `let` や `const` を使うことが推奨される。

JavaScript にコンパイルされた後は、`let` によって定義されていた変数と同名の変数が周囲のスコープに存在しているときには、一方の変数名がリネームされることになる（賢い仕様だ...）。

つまり、次の TypeScript が

```typescript
var foo = "123";
if (true) {
  let foo = 123;
}
```

次のような JavaScript となる

```javascript
var foo = "123";
if (true) {
  var _foo = 123; // リネームされている
}
```

また当然ではあるが、TypeScript では JavaScript と同様に、関数は新たならスコープを作成することも覚えておく。

## `const`

`const` は、変数をイミュータブル (immutable)、つまり変更不可能・再代入不可能にするためのキーワードである。`var` に比較して、可読性を高めるだけでなく、実行面でも優れているので、変更されることのない変数については、`var` の代わりに単に `const` を使うようにすれば良い。

たとえば下記のような例では、マジックリテラル（謎の定数・マジックナンバー）を使うことを避けることにもつながる。

```typescript
// 可読性が低いコード
if (x > 10) {
}

// ベターな書き方
const maxRows = 10;
if (x > maxRows) {
}
```

`const` キーワードを用いて変数を宣言する際には、初期化する必要があることや、当然代入の左辺には記述できないことは知っておく。

```typescript
const foo; // ERROR: constの宣言は値を初期化する必要がある
```

```typescript
const foo = 123;
foo = 456; // ERROR: 代入の左側に定数を置くことはできない
```

また、`const` は `let` と同様にブロックスコープである。

immutability（不変性）の深さの範囲として、下記の例はエラーとなること

```typescript
const foo = { bar: 123 };
foo = { bar: 456 }; // ERROR: 代入の左側に定数を置くことはできない
```

下記の例では、オブジェクト内部のプロパティを変更することは可能であること

```typescript
const foo = { bar: 123 };
foo.bar = 456; // オブジェクト内部のプロパティの変更は可能である
console.log(foo); // { bar: 456 }
```

も把握しておく。

`var` , `let` と `const` に関するまとめとして、**後で変数の初期化を行う予定や、再代入する予定が無いのであれば `const` を使、再代入する場合は `let` を使うべきで、`var` は極力使用すべきでない**ということを確認しておく。

## 分割代入

分割代入とは、配列とオブジェクトを分解して、要素とプロパティの値をひとつひとつ変数に分解するための構文である。

たとえば、分割代入をせずにある配列の各要素を新たな変数に格納するとすれば、下記のように書く必要がある。

```typescript
var list = [10, 20, 30, 40, 50];
var item0 = list[0];
var item1 = list[1];
var item2 = list[2];
var item3 = list[3];
var item4 = list[4];
```

しかし、分割代入では、下記のような記述が可能となっている。

```typescript
var list = [10, 20, 30, 40, 50];
var [item0, item1, item2, item3, item4] = list;
```

オブジェクトについても、下記のようにして分割代入を行うことができる。

```typescript
var rect = { x: 0, y: 10, width: 15, height: 20 };

// 分割代入
var { x, y, width, height } = rect;
console.log(x, y, width, height); // 0, 10, 15, 20

rect.x = 0;
// 全体をカッコで囲むことで、既存の変数に代入することもできる
({ x, y, width, height } = rect);
console.log(x, y, width, height); // 10,10,15,20
```

また、展開した変数を新しい変数名に割り当てることもできる。

```typescript
// オブジェクトの生成
const obj = { "some property": "some value" };

// 分割
const { "some property": someProperty } = obj; // const someProperty = obj["some property"] と同義
console.log(someProperty === "some value"); // true
```

さらに、分割を使用して、楮帯の深いデータを取得することもできる。

```typescript
var foo = { bar: { baz: 123 } };
// var baz = foo.bar.baz と同義
var {
  bar: { baz },
} = foo;
```

## オブジェクトの分割とスプレッド演算子（Rest パラメータ）

あるオブジェクトから任意の数の要素を取得して、残った要素をスプレッド演算子を使って取得することができる。

```typescript
var { w, x, ...remaining } = { w: 1, x: 2, y: 3, z: 4 };
console.log(w, x, remaining); // 1, 2, {y: 3, z: 4}
```

このようなオブジェクトの分割とスプレッド演算子を使用するケースは、下記のように特定のプロパティを無視するようなときにある。

```typescript
function goto(point2D: {x: number, y: number}){
  // 余計なものが含まれたオブジェクトが渡されたら
  // 正しく動作しないようなコードを想定する
}

// どこかから取得した Point のオブジェクト
const point3D = {x: 1, y: 2, z: 3};
// 余計なプロパティを取り除くための、面白いスプレッド演算子の使い方
const {z: ...point2D} = point3D;
goto(point2D);
```

## 変数末尾のエクスクラメーション

TypeScript において `foo` という変数に対して直後にエクスクラメーションマークを付加して `foo!` と記述するとき、このエクスクラメーションマーク `!` を "Non-null assertion operator" といい、プログラマがコンパイラに対して、この変数は `undefined` や `null` になることはないということを示すための記述である。なお、この記述は TypeScript が Javascript に変換された後のコードからは削除されるため、コード実行時に違反していても例外などのエラーは発生しない。つまり、コード実行時に `foo` の値が `undefined` や `null` でもエラーは発生しない（ただし、コンパイラから見て `foo` に `undefined` が入ることが明らな場合には、コンパイル時にはエラーになる）。
