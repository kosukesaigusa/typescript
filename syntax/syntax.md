# 各種文法

## 参考

- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
- [仕事ですぐに使える TypeScript](https://future-architect.github.io/typescript-guide/async.html)
- [Qiita TypeScript の変数の末尾のエクスクラメーション（感嘆符）の意味](https://qiita.com/zigenin/items/364264a6cf635b962542)
- [TypeScript for the New Programmer](https://www.typescriptlang.org/docs/handbook/typescript-from-scratch.html)

## 変数末尾のエクスクラメーション

TypeScript において `foo` という変数に対して直後にエクスクラメーションマークを付加して `foo!` と記述するとき、このエクスクラメーションマーク `!` を "Non-null assertion operator" といい、プログラマがコンパイラに対して、この変数は `undefined` や `null` になることはないということを示すための記述である。なお、この記述は TypeScript が Javascript に変換された後のコードからは削除されるため、コード実行時に違反していても例外などのエラーは発生しない。つまり、コード実行時に `foo` の値が `undefined` や `null` でもエラーは発生しない（ただし、コンパイラから見て `foo` に `undefined` が入ることが明らな場合には、コンパイル時にはエラーになる）。
