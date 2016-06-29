# m2t

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-generate-toc again -->
**Table of Contents**

- [m2t](#m2t)
    - [历史与未来](#历史与未来)
        - [BT 1.0 时代](#bt-10-时代)
        - [BT 2.0 时代](#bt-20-时代)
            - [DHT](#dht)
            - [PEX](#pex)
            - [Magnet Link](#magnet-link)
            - [DHT + PEX + Magnet Link 解决的问题](#dht--pex--magnet-link-解决的问题)
            - [Bootrap Node](#bootrap-node)
    - [开工](#开工)
        - [Magnet Link 转 Torrent](#magnet-link-转-torrent)
        - [来吧，开始写 m2t](#来吧开始写-m2t)

<!-- markdown-toc end -->

## 历史与未来
### BT 1.0 时代
1. 获取 Torrent 文件
2. 使用 BitTorrent 客户端软件打开获取到的 Torrent 文件；客户端根据 Torrent 文件中包含的 Tracker 信息来连接 Tracker 服务器，从它那里获取到正在下载该文件的人的地址名单；与名单上的地址一一取得联系，从它们那里获取文件的片段
3. 等待文件下载完成

可以看出，Tracker 服务器是一个中央节点，任何客户端都需要连接到 Tracker 服务器。

> 只要有中央节点，网络就会被控制。

2009 年，MPAA（美国电影协会）和 RIAA（美国唱片行业协会）开始通过法律手段打压 Tracker 服务器的提供者，The Pirate Bay 和 Mininova 相继倒地。

MPAA 和 RIAA 赢了，它们除掉了 BT 的心脏 —— Tracker 服务器。

BT 1.0 时代终结。

### BT 2.0 时代
DHT、PEX 和 Magnet Link，它们将是未来 BT 的核心。
#### DHT
2002年，纽约大学的两个教授 Petar Maymounkov 和 David Mazières 发表了一篇论文，提出了一种真正去中心化的“点对点”下载模型，
他们将其称为 Kademlia 方法。2005年，BT 软件开始引入这种技术，在 BT 中被称为 DHT 协议（Distributed HashTable，分布式哈希表）。
DHT 是一种分布式存储方法，它的作用是找到那些与本机正在下载/上传相同文件的对端主机（Peer）。

在 DHT 网络中，每个客户端负责一个小范围的路由，并负责存储一小部分数据，从而实现整个 DHT 网络的寻址和存储。
这种信息获取方式保证了整个网络没有单个的中心，即使一个节点下线，依然可以通过其他节点来获取文件，因此也就不需要 Tracker 服务器来告诉你，其他节点在什么地方。

#### PEX
PEX 是 Peer Exchange 的简写，可以将其理解为“节点信息交换”。

虽然DHT解决了去中心化的问题，但要在没有 Tracker 的情况下实现高效寻址，就要借助 PEX。
PEX 所提供的功能有点类似于以前的 Tracker 服务器，但工作方式却非常不同，我们可以打个比方来说明：

> 小赵在 A 班，她不认识 B 班的小何，也不认识 C 班的小温，但小赵认识同班的小王，而小王认识 B 班的小何，也可能还认识 C 班的小温，或者小王仅认识 B 班的小何，但小何认识 C 班的小温，而小温又认识同班的所有同学，结果就是小赵可以“无限”地延伸自己的关系网，不管怎样，总有一条沟通途径可以将这些同学联系在一起，待小赵“认识”了小温后，他们就可以直接沟通了。

#### Magnet Link
DHT 和 PEX 解决了 BT 寻址的问题，但是如何告诉 BT 客户端要找什么又是另外一个问题。

在 BT 1.0 时代，Torrent 文件中包含着文件信息，它告诉客户端要获取什么内容。但 BT 2.0 时代，文件信息不再存放于 Torrent 文件中，而被放在了
Mangent Link 中。

#### DHT + PEX + Magnet Link 解决的问题

* 网络的可靠性得到了极大的增强
* 不存在所谓的中央节点，审查将变得更加困难
* Magnet Link 只是一个字符串，容易传播，根本无法禁止

#### Bootrap Node

BT 2.0 中存在一个问题 —— BT 客户端的如何迈出第一步。

套用在介绍 PEX 时使用的例子，那就是小赵是怎么加入A班的呢？

解决这个问题依然需要一台服务器 —— Bootstrap Node，不过这台服务器所起的作用与 Tracker 不同，它只负责接纳小赵进入 A 班，当小赵与 A 班中的同学取得联系后，
这台服务器就没有什么用处了。Bootstrap Node 可以是不同 BT 客户端厂商独立运营的，也可以是几家联合共用，总之，它是分散的，只要在客户端软件中内置一张表单，那客户端就将有非常多的入口可以选择。


## 开工
### Magnet Link 转 Torrent

原理：根据 Magnet Link 获取的目标文件的 info hash，即 Magnet Link 中的 `urn:btin` 字段，然后使用这个 info hash 到各大 Torrent 库中下载对应的 Torrent 文件。

比如拥有一个 Magnet Link：

    magnet:?xt=urn:btih:66b106b04f931da3485282c43cf66f6bd795c8c4

分析得出，info hash 为 `66b106b04f931da3485282c43cf66f6bd795c8c4`，然后到各大 Torrent 库下载 Torrent。

* 迅雷 Torrent 库
  * 地址：`http://bt.box.n0808.com/<info hash 头两位>/<info hash 后两位>/<info hash>.torrent`
  * 举例：`http://bt.box.n0808.com/66/C4/66B106B04F931DA3485282C43CF66F6BD795C8C4.torrent`
* TorCache Torrent 库
  * 地址：`https://torcache.net/torrent/<info hash>.torrent`
  * 举例：`https://torcache.net/torrent/66B106B04F931DA3485282C43CF66F6BD795C8C4.torrent`
* TorRage Torrent 库
  * 地址：`https://torrage.net/torrent/<info hash>.torrent`
  * 举例：`http://torrage.com/torrent/66B106B04F931DA3485282C43CF66F6BD795C8C4.torrent`


### 来吧，开始写 m2t

原理明白了，就懒得写了。
