# Dockerを使ったtc-qdiscの実験環境

ネットワークシミュレーションの実験環境です。Docker containerを立て直すだけで環境をリセットできるので安全に何度でも再試行できます。

## 1) Run `docker-compose up` in a terminal]

```console
1> docker-compose up
```

## 2) See the network latency in another terminal

with `ping`:

```console
2> docker exec client ping server
PING server (172.18.0.3) 56(84) bytes of data.
64 bytes from server.example-docker-tc-qdisc_default (172.18.0.3): icmp_seq=1 ttl=64 time=0.140 ms
64 bytes from server.example-docker-tc-qdisc_default (172.18.0.3): icmp_seq=2 ttl=64 time=0.150 ms
...
```

with `tc qdisc`:

```console
2> docker exec client tc qdisc
qdisc noqueue 0: dev lo root refcnt 2
qdisc noqueue 0: dev eth0 root refcnt 2
```

## 3) Run `tc qdisc` for the client container

```console
2> docker exec client tc qdisc add dev eth0 root netem delay 100ms
```

## 4) See the server latency again

with `ping`:

```console
2> docker exec client ping server
PING server (172.18.0.3) 56(84) bytes of data.
64 bytes from server.example-docker-tc-qdisc_default (172.18.0.3): icmp_seq=1 ttl=64 time=101 ms
64 bytes from server.example-docker-tc-qdisc_default (172.18.0.3): icmp_seq=2 ttl=64 time=101 ms
...
```

with `tc qdisc`:


```console
2> docker exec client tc qdisc
qdisc noqueue 0: dev lo root refcnt 2
qdisc netem 8004: dev eth0 root refcnt 2 limit 1000 delay 100.0ms
```

## See Also

* "Linux TC (帯域制御、帯域保証) 設定ガイドライン" https://labs.gree.jp/blog/2014/10/11288/
* "tcコマンドの使い方" https://qiita.com/hana_shin/items/d9ba818b49aca87b2314
* "tcコマンドとDockerコンテナを用いて遅いネットワークをシミュレートする" https://blog.kkty.jp/entry/2019/08/29/211431 (in Japanese)
* netem(8) https://www.man7.org/linux/man-pages/man8/tc-netem.8.html
