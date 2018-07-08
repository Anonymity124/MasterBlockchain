以太坊挖矿算法
============

- [1. 基础数据](#1-基础数据)
- [2. 挖矿算法](#2-挖矿算法)
    - [2.1. 以太坊Ethash过程](#21-以太坊ethash过程)
- [3. 以太坊使用的散列函数说明](#3-以太坊使用的散列函数说明)
- [4. 参考资料](#4-参考资料)

## 1. 基础数据

新区块生成周期：约12秒。
挖矿难度调整周期：约12秒，即每生成一个新区块就动态调整一次难度值。
挖矿奖励：以太坊的挖矿奖励来源于三部分：
每个区块固定奖励5个以太币。
用户在进行交易时支付的手续费，具体取决于\(gas*gasprice\)。
每个叔伯块包含1/32的奖励。

## 2. 挖矿算法

挖矿参考算法：挖矿算法为Ethash，该算法由Dagger-Hashimoto算法改进而来。运行该算法时，需要每30000个区块（大约100个小时，该时期叫做epoch，即以100个小时为一个窗口期）生成一个初始大小约为1GB的数据集，这个数据集就叫做DAG（directed acyclic graph，有向无环图）。DAG的大小不是固定不变的，以太坊规定每个窗口期增长8MB。Ethash工作量证明依赖的是大量的内存资源，这就使得使用ASIC矿机进行挖矿变得不太可能。在挖矿过程中，该工作量证明机制要求选择依赖随机数和区块头的固定资源的子集。由于DAG只依赖区块高度，因此能够提前生成这个资源集合。每个窗口期都需要重新生成DAG，而生成DAG需要耗费的时间较多，则最好的方法就是提前在缓存中存储DAG。否则，在每个窗口期的开始区块中需要等待很长的时间。值得注意的是，在验证工作量的时候无需生成DAG，这样就能保证那些使用低速CPU和较小内存的设备也能正常验证。

以太坊挖矿算法过程如下：

1. 对于每一个块(block)，先计算出一个种子(seed)。种子的计算只依赖于当前块的信息，例如block number以及block headers。
2. 使用种子产生32MB的伪随机数据集，称为cache。轻客户端需要保存cache。
3. 基于cache再生成一个1GB大小的数据集，称为the DAG。这个数据集中的每一个元素都只依赖cache中的某几个元素，换句话说，只要有cache就可以快速计算出DAG中指定位置的元素。完整的可挖矿的客户端需要保存DAG。
4. 挖矿可以概括为从DAG中随机选择元素然后对其进行hash的过程。验证的过程也是一样，只不过不是从DAG里面选择元素，而是基于cache计算得到指定位置的元素，然后验证这个元素集合的hash结果小于某个值。由于cache很小, 而且指定位置的DAG元素很容易计算，因此验证过程只需要普通CPU和普通内存即可完成。
5. cache和DAG每一个周期更新一次，一个周期的长度是1000个块。也就是说这1000个块产生的cache和DAG是完全一样的，因此挖矿的主要工作在于从DAG中读取数据，而不是更新cache和DAG。DAG的大小随时间的推移线性增长，从1GB开始，每年增加大约7GB – 因此到2015年12月大约是8GB, 到2016年12月大约15GB。

### 2.1. 以太坊Ethash过程

1. 生成32个字节的种子。以太坊规定每30000个区块是一个窗口，在同一个窗口期中，种子是相同的。种子的生成过程是这样的：第一个窗口期的种子是将32字节的0值做一次Keccak256运算，得到一个32字节的种子。而以后每个窗口期的种子生成方式就是将前一个窗口期的种子做一次Keccak256运算。
2. 生成不定长度的缓存。缓存的生成过程是这样的：每个缓存单元的大小为64字节，即512位。第一个缓存单元是当前窗口的种子值做Keccak512运算得到的。之后每个缓存单元都是前一个缓存单元的Keccak512值。而每个窗口期的缓存大小随着窗口期的增加而线性增大。初始大小为16MB，之后每个窗口期增加不到128KB。之后将初步得到的缓存做3个轮次的RandMemoHash运算。RandMemoHash算法将缓存的各个单元进行混淆。
3. 生成不定长度的数据集合。首先从生成的缓存中随机找出256个缓存单元，然后合并做哈希运算。这样得到的数据集初始大小为1GB，而后每个窗口期增加不到8MB。注意，在验证区块的过程中，也是同样的操作。这样就需要将缓存和数据集保存到内存中，以方便挖矿或者验证区块的时候频繁的读取数据消耗过多的时间。
4. 矿工之后就通过PoW机制进行挖矿操作。但是因为每个缓存和数据集生成时间需要消耗大量的时间，则为了防止在下一个窗口期到来的时候影响出块速度，则鼓励矿工提前计算好缓存值和数据集。

## 3. 以太坊使用的散列函数说明

以太坊中采用的哈希算法为Keccak，该算法与NIST于2015年发布的SHA3标准有一点不同，而SHA-3 在2015年8月5日由 NIST 通过 FIPS 202 正式发表。
使用不同的哈希算法对单词`testing`进行计算，得到结果如下：

- Ethereum SHA3 function in Solidity = `5f16f4c7f149ac4f9510d9cf8cf384038ad348b3bcdc01915f95de12df9d1b02`
- Keccak-256 = `5f16f4c7f149ac4f9510d9cf8cf384038ad348b3bcdc01915f95de12df9d1b02`
- SHA3-256 (NIST Standard) = `7f5979fb78f082e8b1c676635db8795c4ac6faba03525fb708cb5fd68fd40c5e`

以太坊挖矿算法总结，以太坊使用的哈希算法sha256与标准的nist公布的SHA3算法有一点不同。有关资料可以查看：

- https://ethereum.stackexchange.com/questions/550/which-cryptographic-hash-function-does-ethereum-use
- https://bitcoin.stackexchange.com/questions/42055/what-is-the-approach-to-calculate-an-ethereum-address-from-a-256-bit-private-key/42057#42057
- https://github.com/ethereum/EIPs/issues/59

## 4. 参考资料

- [以太坊黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf )：介绍以太坊挖矿算法最权威最详细的说明。Ethash的说明在本书的最后一部分。本书是了解以太坊挖矿算法最精确的资料。
- [Ethash](https://github.com/ethereum/wiki/wiki/Ethash)：本文是了解Ethash算法具体实现的最好资料。以太坊黄皮书主要侧重理论概念的描述，而此文则含有大量的实际代码。文章开头介绍了Ethash算法的一般过程，后面介绍了使用Python实现的算法过程，结合以太坊黄皮书和该wiki介绍，能够深入了解以太坊挖矿算法的基本原理。
- [以太坊的挖矿机制是怎样的？](http://8btc.cn/article-1916-1.html)
- [Ethash DAG](https://github.com/ethereum/wiki/wiki/Ethash-DAG)：介绍了DAG在客户端的存储位置和格式。
- [Mining](https://github.com/ethereum/wiki/wiki/Mining)：介绍了以太坊挖矿的相关内容。
- [Dagger-Hashimoto](https://github.com/ethereum/wiki/blob/master/Dagger-Hashimoto.md)：以太坊wiki中介绍有关Dagger-Hashimoto算法文章。但是这篇文章介绍的算法是较为早的一个版本，现在的算法对此有多个特性的改进。