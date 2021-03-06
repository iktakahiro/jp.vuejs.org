---
title: よくある初心者の落とし穴
date: 2016-02-06 10:00:00
---

Vue.js を使い始めたユーザーからたびたび幾つかの種類の質問があります。それらの質問の回答はガイドでどこかに全て言及していますが、落とし穴にはまったときそれらはあまり目ただず、そして見つけるのは難しいです。そこで、この記事では、うまくいけば、無駄な時間を節約できるよう、それらの落とし穴についてまとめたものを紹介します！

<!-- more -->

### なぜ DOM 更新をしないのですか？

ほとんどの場合、あなたが Vue インスタンスのデータを変更するとき、view を更新します。しかし、2 つの稀なケースがあります:

1. データが監視されるときに、あなたが追加する**新しいプロパティ**が存在しない場合。ES5 の制限とブラウザ間で一貫性のある動作を確保するために、Vue.js はプロパティの追加/削除を検出することはできません。ベストプラクティスは前もってリアクティブにする必要があるプロパティを常に宣言します。あなたが実行時にプロパティを追加または削除する必要がある場合は、グローバルな [`Vue.set`](/api/#Vue-set) または [`Vue.delete`](/api/#Vue-delete) メソッドを使用します。

2. あなたが直接インデックス(例: `arr[0] = val`) を設定、または `length` プロパティを変更することによって配列を変更する場合。同様に、Vue.js はこれらの変更をピックアップすることはできません。常に配列のインスタンスメソッドを使用することよって配列を変更、またはそれを完全に置き換えます。Vue は `arr.splice(index, 1, value)` に対するまさにシンタックスシュガーである便利メソッド `arr.$set(index, value)` を提供します。

参考文献: [リアクティブの探求](/guide/reactivity.html) と [配列の変化を検出](/guide/list.html#配列の変化を検出)

### いつ DOM が更新されますか？

Vue.js はまとめて DOM を更新するために非同期キューを使用します。これはあなたがいくつかのデータを変更するとき、DOM の更新は瞬時に発生しないことを意味します。それらはキューがフラッシュされたとき非同期に適用されます。そこで、あなたは DOM が更新されたときにどうやって知るのでしょうか？あなたがデータを変更後、`Vue.nextTick` を使用するのが正解です。キューがフラッシュされた後、あなたが渡すそのコールバック関数が一度だけ呼ばれます。

参考文献: [非同期更新キュー](/guide/reactivity.html#非同期更新キュー)

### なぜ `data` は関数として必要なのですか？

基本的な例では、`data` は直接プレーンなオブジェクトとして宣言しています。これは、`new Vue()` によって単一のインスタンスだけが作成されるためです。しかしながら、**コンポーネント**を定義するときは、`data` は初期データオブジェクトを返す関数として宣言されなければなりません。なぜでしょうか？同じ定義を使用して作成された多くのインスタンスがあるからです。まだ `data` に対してプレーンなオブジェクトを使用している場合、同じオブジェクトが作成された全てのインスタンス全体を横断して**参照によって共有**されます！`data` 関数を提供することによって、新しいインスタンスが作成される度に、単にそれは初期データの新しいコピーを返すための関数として呼び出すことができます。

参考文献: [コンポーネントオプションの注意事項](/guide/components.html#コンポーネントオプションの注意事項)

### HTML は小文字・大文字を区別しない

全ての Vue.js テンプレートは有効で、解析可能な HTML マークアップ、そして Vue.js はそのテンプレートを処理するために仕様に準拠するパーサーに依存しています。しかしながら、標準で指定された、HTML はタグと属性名がマッチする小文字・大文字を区別しません。これは `:myProp="123"` のようなキャメルケース属性は`:myprop="123"`としてマッチされます。経験則として、あなたは JavaScript ではキャメルケースを使用し、テンプレートでは、ケバブケースを使用すべきです。例えば、`myProp` として JavaScript で定義する prop は `:my-prop` としてテンプレートでバウンドされるべきです。

参考文献: [キャメルケース 対 ケバブケース](/guide/components.html#キャメルケース_対_ケバブケース)

小文字、大文字を区別しない作法において props そして コンポーネントを解決することによって、この矛盾を解消する可能性を議論しています。[ここ](https://github.com/vuejs/vue/issues/2308)の会話に参加しましょう。
