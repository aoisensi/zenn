---
title: "GodotのAnimationPlayer/Treeとは何か"
emoji: "🤖"
type: "tech"
topics: ["godot", "godot4"]
published: true
---

両方、`Node`を継承した`AnimationMixer`の子クラスだ
自分はこれを最初触っているときにお互いがどのような目的で使われ、なぜ2つに分かれているのかよくわかっていなかった
なのでメモとしてまとめておく

# Animation
最初から違うものの説明だ
`Animation`は単一のアニメーションを管理する`Resource`
これは`Dictionary[NodePath, Dictionary[float, Variant]]`のような構造になっている
一つのアニメーションデータが、複数のパラメーターを時間あたりでどう変化させるかなどを保持している

# AnimationLibrary
複数の`Animation`を含んだ`Resource`
つまり`Dictionary[StringName, Animation]`のような構造になっている

# AnimationMixer
これは`Node`で、`abstract`なので直接使うことはない
`Dictionary[StringName, AnimationLibrary]`を持っており、複数のアニメーションセットを保持できる
また、`root_node`プロパティを持っており、`Animation`が参照する`NodePath`のルートノードを指定できる
これ単体には再生機能は多分ない

# AnimationPlayer
`AnimationMixer`を継承している
基本的には単一のアニメーションを再生する目的で使用する

# AnimationTree
`AnimationPlayer`と同じく`AnimationMixer`を継承している
これ単体で使用することはできず、`anim_player`プロパティから`AnimationPlayer`を指定する必要がある
つまり、これを使うということは`AnimationPlayer`も配置する必要がある
これが何をするかというと、`AnimationPlayer`と連携し、それに設定されたアニメーションを複数同時に、つまりブレンドして再生することができる
また、`AnimationPlayer`単体だと外部からこれに対し手続き的に再生するアニメーションを処理しないといけないが、
`AnimationTree`を使えば宣言的にどういう条件のときにどのアニメーションを再生するかを処理できる
UEでいうAnimBPにあたる機能はこれ

# Godotのアニメーションは強力
最初自分は3Dモデルのボーンアニメーションにしか使えないと勘違いしていた
実際には`Animation`が保持するのは任意の`NodePath`と`Variant`
つまり、ノードが保持する全てのプロパティをこの機能を使ってアニメーションさせることができる
3Dモデルのボーンアニメーションはもちろん、Live2Dのような表現、`AnimatedSprite`のフレームやUIアニメーションなんかにも使える
任意のタイミングでメソッドを呼び出すことができるので、アニメーションに合わせたロジックを含むエフェクトの再生なんかもできる
