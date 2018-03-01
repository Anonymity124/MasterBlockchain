#  POW共识机制

<!-- TOC -->

- [POW共识机制](#pow%E5%85%B1%E8%AF%86%E6%9C%BA%E5%88%B6)
    - [1. POW起源](#1-pow%E8%B5%B7%E6%BA%90)
    - [2. POW特点](#2-pow%E7%89%B9%E7%82%B9)
    - [3. POW挖矿算法种类](#3-pow%E6%8C%96%E7%9F%BF%E7%AE%97%E6%B3%95%E7%A7%8D%E7%B1%BB)
    - [4. 参考资料](#4-%E5%8F%82%E8%80%83%E8%B5%84%E6%96%99)

<!-- /TOC -->

Proof of Work（以下简称POW），是区块链发展早期使用最多的一种共识机制。

## 1. POW起源

1993年，Cynthia Dwork和Moni NaorNaor在论文《Pricing via Processing or
Combatting Junk Mail》中首次提出使用代价函数对抗垃圾邮件的攻击。而在1999年，Markus Jakobsson和Ari Juels才在论文中正式提出“Proof of Work”这个词。

比特币白皮书中的POW算法灵感来自于Hashcash。Hashcash的主要目的也是为了预防垃圾邮件，或是预防DDOS攻击。

## 2. POW特点

POW的特点：产生特定的数据结果困难，但是验证结果简单。

## 3. POW挖矿算法种类

- CPU消耗型：SHA256
- 内存消耗型：Scrypt，Ethash

## 4. 参考资料

- [Proof-of-work system](https://en.wikipedia.org/wiki/Proof-of-work_system):维基百科中有关POW的介绍。
- [Proof of work](https://en.bitcoin.it/wiki/Proof_of_work):比特币百科中关于POW的介绍。