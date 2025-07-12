---
title: ""
emoji: "😎"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

## はじめに

## state はどこにある？

まず state を正確に理解したいと思います。  
そのための例として、次のようなコンポーネントを用意してみます。

```
export function TestButton() {
  const [text, setText] = useState("Test Button");
  const handleClick = () => {
    setText(text + "+");
  };
  return <button onClick={handleClick}>{text}</button>;
}
```

ボタンを押した回数だけ、`"+"`がテキストに追加されていく単純なボタンです。

上記のコードにおいて、state はどこに存在するかを考えてみましょう。  
このコードの `text` が state だと考えてしまいがちですが、実は正確に言うとこれは誤りです。  
なぜなら `text` 自体はただの文字列変数であり、現在の state の値を取得したスナップショットにすぎないためです。

実はこのコード上に state そのものは存在しません。  
state は React によって内部的に保持、管理されています。

## state 更新で何度もコンポーネントは作り直される

ここで、先程のコンポーネントのコードを再掲します。

```
export function TestButton() {
  const [text, setText] = useState("Test Button");
  const handleClick = () => {
    setText(text + "+");
  };
  return <button onClick={handleClick}>{text}</button>;
}
```

このボタンを押下した時に起こることは、新しい state の値を View に反映させるための再レンダリングです。  
再レンダリングされるということは、TestButton 関数を呼び出し再実行することを意味します。

普段実装している時は、state の更新関数を呼び出すと state オブジェクトを更新してくれると考えがちですが実際は違います。  
TestButton 関数を再度呼び出しているのだから、text も setText 関数も再生成されるというのが正しいのです。

ここまでを図にして表してみると次のようになります。

![lifecycle](./images/lifecycle.png)

## おわりに
