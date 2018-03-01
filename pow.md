#  POW共识机制

<!-- TOC -->

- [POW共识机制](#pow%E5%85%B1%E8%AF%86%E6%9C%BA%E5%88%B6)
    - [1. POW起源](#1-pow%E8%B5%B7%E6%BA%90)
    - [2. POW原理](#2-pow%E5%8E%9F%E7%90%86)
    - [3. POW特点](#3-pow%E7%89%B9%E7%82%B9)
    - [4. POW挖矿算法种类](#4-pow%E6%8C%96%E7%9F%BF%E7%AE%97%E6%B3%95%E7%A7%8D%E7%B1%BB)
    - [5. 参考资料](#5-%E5%8F%82%E8%80%83%E8%B5%84%E6%96%99)

<!-- /TOC -->

Proof of Work（工作量证明，以下简称POW），是区块链发展早期使用最多的一种共识机制。基于POW的系统大致过程如下：

1. 对于发送者而言，需要完成特定的工作量，这个工作量一般是解决一个数学难题，得到一个难题的解。一般这个过程时间较长。之后将自己得到的解发送给接收者。
2. 接受者收到发送者的解之后，能够简单快速的验证这个解是否正确。如果正确，则认为发送者提出的请求合法，然后与之进行下一步的数据交互过程。如果错误，则认为发送者的请求不合法，拒绝与之数据交互。

POW的两个过程中，解决数学难题需要消耗一定的计算机资源，这样发送者需要发起服务请求的时候就必须付出一点代价。对于合法的用户来说，这个代价可以接受，因为他只需要数量有限的服务请求，需要消耗的计算机资源也不多。但是对于攻击者而言，例如发送大量垃圾邮件的攻击者需要提前准备大量的满足条件的解，这就要消耗大量的计算机资源。

## 1. POW起源

1993年，Cynthia Dwork和Moni NaorNaor在论文《Pricing via Processing or
Combatting Junk Mail》中首次提出使用代价函数对抗垃圾邮件的攻击。而在1999年，Markus Jakobsson和Ari Juels才在论文中正式提出“Proof of Work”这个词。

比特币白皮书中的POW算法灵感来自于Hashcash。Hashcash的主要目的也是为了预防垃圾邮件和DDOS攻击。

## 2. POW原理

## 3. POW特点

POW的特点：产生特定的数据结果困难，但是验证结果简单。

## 4. POW挖矿算法种类

- CPU消耗型：SHA256
- 内存消耗型：Scrypt，Ethash

## 5. 参考资料

- [Proof-of-work system](https://en.wikipedia.org/wiki/Proof-of-work_system):维基百科中有关POW的介绍。
- [Proof of work](https://en.bitcoin.it/wiki/Proof_of_work):比特币百科中关于POW的介绍。