---
title: Fermat_little_theorem
tags:
  - fermat
private: false
updated_at: '2024-06-09T14:13:12+09:00'
id: b717499b06646b47b824
organization_url_name: null
slide: false
ignorePublish: false
---
# フェルマーの小定理の証明 (個人的備忘録)

## フェルマーの小定理
証明する式はこちら。

pが素数、かつaがpの倍数でない整数の時、以下の式が成り立つ。

$$
a^{p-1} \equiv 1 \;(mod \; p)
$$

## フェルマーの小定理の別表現

$$
a^p \equiv a \; (mod \; p)
$$

:::note info
別表現の場合、aの制約が緩くなっていることに注意してください。
:::

別表現で"pの倍数でない"という制約が取り払われているのは、aがpの倍数である場合のことを考えれば理解できます。

もしaがpの倍数であれば、$0 \equiv 0$となり、式が成り立つので問題ありません。

## フェルマーの小定理の証明①
フェルマーの小定理の証明の方法はいくつかあるようですが、今回は[ヨビノリたくみさんのYoutube動画](https://www.youtube.com/watch?v=-4QXGVyYVq8)で採用されていた方法で証明していきます。（全ての合同式で mod p を省略していることに注意）

### step 1
まず、$a, 2a, 3a, ..., (p-1)a$を $p$ で割った余りはどれも異なることを背理法により示す。

$$
\begin{equation}
1 \leqq 1 < j \leqq p - 1　\pmod{n}
\tag{1}

\end{equation}
$$ 

となる $i,j$を仮定したときに、$ia \equiv ja$が存在すると仮定すると、aとpは互いに素なことより、両辺からaを割って$i \equiv j$が成り立つ。iもjも1以上p未満より、$i = j$ が成り立つ。しかし、それは(1)の仮定に反してしまうため存在しない。

つまり、$a, 2a, 3a, ..., (p-1)a$を $p$ で割った余りはどれも異なることが示された。

### step 2
step1より、以下の式が成り立つ。

$$
a+ 2a+ 3a+ ...+ (p-1)a \equiv (p-1)!
$$

左辺を整理すると、
$$
a^{p-1}× (p-1)! \equiv (p-1)!
$$

ここで、$(p-1)!$と$p$は互いに素であるので、

$$
a^{p-1} \equiv 1
$$

となり、証明が完了する。