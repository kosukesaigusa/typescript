# TypeScript の基礎

## 参考

- [TypeScript Deep Dive 日本語版](https://typescript-jp.gitbook.io/deep-dive/)

## 基礎事項

TypeScript は、最終的には JavaScript にコンパイルされ、実際に実行されるのは JavaScript である。つまり、開発するときには TypeScript を書いても、ブラウザで実行する時には（またはサーバーサイドで Node.js を扱う時にも）、コンパイルされた JavaScript を実行するということである。そのため、TypeScript を利用するには、TypeScript のコンパイラ（NPM のパッケージとして提供されている）と TypeScript のエディタ次のものが必要となる。

## TypeScript の環境構築

ターミナルで下記のコマンドを実行すると、TypeScript をインストールすることができる。また、`npm` コマンドは、Node.js をインストールすると利用できるようになる。

```
npm install -g typescript
```

TypeScript のインストールを済ませると、`tsc` コマンドを利用できるようになる。`tsc` は TypeScript のコンパイラを起動するコマンドで、`tsc app.ts` のように実行して、TypeScript のファイルを JavaScript のファイルへとコンパイルすることができる。

## TypeScript を使う理由

TypeScript を用いる主な目的は次の 2 つである。

- JavaScript に任意の型システムを追加する（型を利用するかどうかは開発者の自由ではある）
- JavaScript の将来のバージョンで計画されている機能を、現在の（モダンではない）JavaScript 環境でも使えるようにする

型はコードの品質と読みやすさを高めることが実証されており、Google, Microsoft, Facebook などの大規模なチームでは、その効果は顕著である。

たとえば、型があることによって、ランタイム（実行時）ではなく、コードを書いている時点でエラーに気づくことができるようになるため開発中の早い段階でエラーに気付けることになったり、型自体がコードの読みやすさを高めるドキュメントのような役割を果たしたりするなどの効果がある。

つまり、TypeScript は、JavaScript へのコンパイル時の型安全性を提供するものであり、それが "Type"（つまり「型」）スクリプトという名前を付けられた所以であるということである。また、TypeScript ではこの「型」を利用するかどうかが完全に任意であるため、例えば何らかの JavaScript で書かれたコードの `.js` ファイルをそのまま `.ts` ファイルとして移植してコンパイルしてたとしても、TypeScript コンパイラは元の JavaScript ファイルと同等の、正常に動作する `.js` ファイルを出力するということで、TypeScript は、JavaScript の上位互換というこができる、任意の型チェックの仕組みを持ったプログラミング言語であるということである。

## 暗黙的な型推論 (Type interface)

TypeScript では、コーディングの生産性に対する影響をなるべく小さく抑えながら方の安全性を提供するために、可能化な限り暗黙的な型推論を行う。つまり、下記の例では、2 行目にエラーが表示されるということである。

```typescript
var foo = 123;
foo = "456"; // エラー: `string` を `number` に代入できません
```

## 明示的な型指定 (Type annotation)

上記の通り、TypeScript では、安全に行える限りはできる限り型を推論するようになっているが、型推論の結果が正しくない場合には、開発者が目地的にコード上で型を指定する（つまり、型アノテーションを書く）ことができる。型アノテーションを書くということは、

1. コンパイラの理解を助けること、かつ、自身が書いたコードを次に読まなくてはならない開発者（自分かもしれない）にとっての有用なドキュメントになる
2. コンパイラがどのようにコードを理解するか、ということを強制することとなる。つまり、コードに対する開発者の正しい理解を、コンパイラの型チェックのアルゴリズムに反映することができる

というメリットを受けられることになる。

TypeScript では、「末尾型アノテーション」といって、対象の後ろにコロンを付けて型を指定する。方が一致していない場合にはコンパイラはエラーを出力し、VSCode などのエディタによっては、エディタ上でエラーを発見することもできる。

```typescript
var foo: number = 123;
```

```typescript
var foo: number = "123"; // エラー: `string` を `number` に代入できません
```

また、デフォルトの設定では、TypeScript にコンパイルエラーがあったとしても、下記のように有効な JavaScript が出力されることも覚えておく必要がある。

```typescript
var foo = 123;
foo = "456"; // エラー: `string` を `number` に代入できません
```

```javascript
var foo = 123;
foo = "456";
```