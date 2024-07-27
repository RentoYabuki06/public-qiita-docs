---
title: 【Atcoder】巡回セールスマン問題3問解いてみた
tags:
  - C++
  - AtCoder
  - 巡回セールスマン問題
private: false
updated_at: '2024-07-27T15:35:20+09:00'
id: 4d6c22e422944b1f69a2
organization_url_name: null
slide: false
ignorePublish: false
---
# 巡回セールスマン問題3問解いてみた

巡回セールスマン問題とは何か、簡単に説明すると、全ての都市を一度だけ訪れ、最後に出発地に戻るような経路の中で最短の経路を求める問題。

基本的にはbitDPを使用し、dp[i][j]としてiの状態で都市jにいるとき（最後に訪れた都市がj）の最短経路を求める。iの状態をビットで表現し、iのjビット目が立っているとき都市jに訪れたことを表す。

## [B23 - Traveling Salesman Problem](https://atcoder.jp/contests/tessoku-book/tasks/tessoku_book_cv)

競技プログラミングの鉄則で紹介されている基本的な巡回セールスマン問題。

全ての都市の座標が2次元座標上の点として与えられるので、全ての都市を一度だけ訪れ、最後に出発地に戻るような経路の中で最短の経路を求める。

```cpp
ll N;
ll X[1009];
ll Y[1009];
float dp[34009][19];

float ft_dist(ll xa, ll ya, ll xb, ll yb)
{
	return sqrt(pow(xa - xb, 2) + pow(ya - yb, 2));
}

int main()
{
	cin >> N;
	for (int i = 0; i < N; i++) cin >> X[i] >> Y[i];
	// 配列の初期化
	for (int i = 0; i < (1 << N); i++)
	{
		for (int j = 0; j < N; j++)
		{
			dp[i][j] = 100000;
		}
	}
	// 最初は都市1からスタート
	dp[1][0] = 0;

	// スタートのみから全都市制覇までbitDP
	for (int i = 1; i < (1 << N); i++)
	{
		// 最後の都市をjとする
		for (int j = 0; j < N; j++)
		{
			if (!(i & (1 << j))) continue;
			// 次に行く都市をkとする
			for (int k = 1; k < N; k++)
			{
				if (i & (1 << k)) continue;
				dp[i | (1 << k)][k] = min(dp[i | (1 << k)][k], dp[i][j] + ft_dist(X[j], Y[j], X[k], Y[k]));
			}
		}
	}
	// 出力
	float ans = 100000;
	for (int k = 1; k < N; k++)
	{
		ans = min(ans, dp[(1 << N) - 1][k] + ft_dist(X[k], Y[k], X[0], Y[0]));
	}
	cout << ans << endl;
	return 0;
}

```

## [DPL_2_A - 巡回セールスマン問題](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=DPL_2_A&lang=ja)

次に会津大のAIZU ONLINE JUDGEで出題されている巡回セールスマン問題。こちらも基礎問題。

先ほどはX,Y座標からそれぞれの都市の距離を求めたが、こちらは都市間の距離が与えられる。

```cpp
ll V, E;
ll dp[34009][19];
const ll INF = 100000;
vector<pair<ll, ll>> Graph[19];

int main()
{
    cin >> V >> E;
    for (int i = 0; i < E; i++)
    {
        ll s, t, d;
        cin >> s >> t >> d;
        Graph[s].push_back(make_pair(t, d));
    }
    
    // 配列の初期化
    for (int i = 0; i < (1 << V); i++)
    {
        for (int j = 0; j < V; j++)
        {
            dp[i][j] = INF;
        }
    }

    // 最初は都市0からスタート
    dp[1][0] = 0;

    // スタートのみから全都市制覇までbitDP
    for (int i = 1; i < (1 << V); i++)
    {
        // 最後の都市をjとする
        for (int j = 0; j < V; j++)
        {
            if (!(i & (1 << j))) continue;
            // 次に行く都市をkとする
            for (const auto& edge : Graph[j])
            {
                ll nex = edge.first;
                ll dist = edge.second;
                if (i & (1 << nex)) continue;
                dp[i | (1 << nex)][nex] = min(dp[i | (1 << nex)][nex], dp[i][j] + dist);
            }
        }
    }

    // 出力
    ll ans = INF;
    for (int k = 1; k < V; k++)
    {
        for (const auto& edge : Graph[k])
        {
            if (edge.first == 0)
            {
                ans = min(ans, dp[(1 << V) - 1][k] + edge.second);
            }
        }
    }

	if (ans == INF) cout << -1 << endl;
	else cout << ans << endl;
    return 0;
}
```

## [G - Revenge of Traveling Salesman Problem](https://atcoder.jp/contests/s8pc-1/tasks/s8pc_1_g)

最後に少し難易度の高そうな問題。G問題と言われるとグッとくるものがある。

とは言ってもGの中では（多分）簡単な方の問題で、以下の二つの条件が加わる。

 - 最短経路の距離だけでなく、最短経路となる巡回パターンがいくつあるかも数えて出力
 - 特定の道路は $time_i$ までに通過しきらなければ、通れなくなってしまう。（1移動時間に対して1距離1進むことができる）

```cpp
ll N, M;
ll dp[1 << 16][16];
ll cnt[1 << 16][16];
const ll INF = 1e18;

struct Edge
{
    ll to;
    ll dist;
    ll time;
    Edge (ll to, ll dist, ll time) : to(to), dist(dist), time(time) {}
};

int main()
{
    cin >> N >> M;
    vector<Edge> Graph[16];
    for (int i = 0; i < M; i++)
    {
        ll s, t, d, time;
        cin >> s >> t >> d >> time;
        --s;
        --t;
        Graph[s].emplace_back(t, d, time);
        Graph[t].emplace_back(s, d, time);
    }

    // 配列の初期化
    for (int i = 0; i < (1 << N); i++)
    {
        for (int j = 0; j < N; j++)
        {
            dp[i][j] = INF;
            cnt[i][j] = 0;
        }
    }

    // 最初は都市0からスタート
    dp[1][0] = 0;
    cnt[1][0] = 1;

    // スタートのみから全都市制覇までbitDP
    for (int i = 1; i < (1 << N); i++)
    {
        // 最後の都市をjとする
        for (int j = 0; j < N; j++)
        {
            if (!(i & (1 << j))) continue;
            // 次に行く都市をkとする
            for (const auto& city : Graph[j])
            {
                ll nex = city.to;
                ll dist = city.dist;
                ll time = city.time;
                if (i & (1 << nex)) continue;
                if (dp[i][j] + dist > time) continue;
                if (dp[i | (1 << nex)][nex] > dp[i][j] + dist)
                {
                    dp[i | (1 << nex)][nex] = dp[i][j] + dist;
                    cnt[i | (1 << nex)][nex] = cnt[i][j];
                }
                // もし最短経路が複数ある場合は追加
                else if (dp[i | (1 << nex)][nex] == dp[i][j] + dist)
                {
                    cnt[i | (1 << nex)][nex] += cnt[i][j];
                }
            }
        }
    }

    // 出力
    ll ans = INF;
    ll count = 0;
    for (const auto& city : Graph[0])
    {
        ll k = city.to;
        ll dist = city.dist;
        if (dp[(1 << N) - 1][k] + dist <= city.time)
        {
            if (ans > dp[(1 << N) - 1][k] + dist)
            {
                ans = dp[(1 << N) - 1][k] + dist;
                count = cnt[(1 << N) - 1][k];
            }
            else if (ans == dp[(1 << N) - 1][k] + dist)
            {
                count += cnt[(1 << N) - 1][k];
            }
        }
    }

    if (ans == INF) cout << "IMPOSSIBLE" << endl;
    else cout << ans << " " << count << endl;
    return 0;
}

```
