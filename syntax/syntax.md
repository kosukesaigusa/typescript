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

## 変数末尾のエクスクラメーション

TypeScript において `foo` という変数に対して直後にエクスクラメーションマークを付加して `foo!` と記述するとき、このエクスクラメーションマーク `!` を "Non-null assertion operator" といい、プログラマがコンパイラに対して、この変数は `undefined` や `null` になることはないということを示すための記述である。なお、この記述は TypeScript が Javascript に変換された後のコードからは削除されるため、コード実行時に違反していても例外などのエラーは発生しない。つまり、コード実行時に `foo` の値が `undefined` や `null` でもエラーは発生しない（ただし、コンパイラから見て `foo` に `undefined` が入ることが明らな場合には、コンパイル時にはエラーになる）。
