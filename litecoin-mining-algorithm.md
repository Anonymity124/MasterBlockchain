# 莱特币挖矿算法

## 1. Scrypt算法简介

莱特币采用的挖矿算法是Scrypt算法。第一个使用Scrypt算法的数字货币是Tenebrix，而后该算法被莱特币使用。莱特币创始人在莱特币创世帖中介绍了莱特币采用的共识机制，挖矿算法，发行总量，挖矿难度等相关重要信息。该帖中，李启威说明了莱特币所使用的挖矿算法为数字货币Tenebrix所使用的Scrypt算法，这也是一种符合PoW共识机制的算法。Scrypt算法过程中也需要计算哈希值，但是，Scrypt计算过程中需要使用较多的内存资源。

其它使用Scrypt算法的数字货币还有数码币（DigitalCoin）、狗狗币（DogeCoin）、幸运币（LuckyCoin）、世界币（WorldCoin）等。

## 2. Scrypt算法过程

Scrypt算法使用的几个函数环环相扣，本节按函数从里到外的调用顺序描述。

### 2.1. Salsa20/8

```c
#define R(a,b) (((a) << (b)) | ((a) >> (32 - (b))))
void salsa20_word_specification(uint32 out[16],uint32 in[16])
{
    int i;
    uint32 x[16];
    for (i = 0;i < 16;++i) x[i] = in[i];
    for (i = 8;i > 0;i -= 2) {
        x[ 4] ^= R(x[ 0]+x[12], 7); x[ 8] ^= R(x[ 4]+x[ 0], 9);
        x[12] ^= R(x[ 8]+x[ 4],13); x[ 0] ^= R(x[12]+x[ 8],18);
        x[ 9] ^= R(x[ 5]+x[ 1], 7); x[13] ^= R(x[ 9]+x[ 5], 9);
        x[ 1] ^= R(x[13]+x[ 9],13); x[ 5] ^= R(x[ 1]+x[13],18);
        x[14] ^= R(x[10]+x[ 6], 7); x[ 2] ^= R(x[14]+x[10], 9);
        x[ 6] ^= R(x[ 2]+x[14],13); x[10] ^= R(x[ 6]+x[ 2],18);
        x[ 3] ^= R(x[15]+x[11], 7); x[ 7] ^= R(x[ 3]+x[15], 9);
        x[11] ^= R(x[ 7]+x[ 3],13); x[15] ^= R(x[11]+x[ 7],18);
        x[ 1] ^= R(x[ 0]+x[ 3], 7); x[ 2] ^= R(x[ 1]+x[ 0], 9);
        x[ 3] ^= R(x[ 2]+x[ 1],13); x[ 0] ^= R(x[ 3]+x[ 2],18);
        x[ 6] ^= R(x[ 5]+x[ 4], 7); x[ 7] ^= R(x[ 6]+x[ 5], 9);
        x[ 4] ^= R(x[ 7]+x[ 6],13); x[ 5] ^= R(x[ 4]+x[ 7],18);
        x[11] ^= R(x[10]+x[ 9], 7); x[ 8] ^= R(x[11]+x[10], 9);
        x[ 9] ^= R(x[ 8]+x[11],13); x[10] ^= R(x[ 9]+x[ 8],18);
        x[12] ^= R(x[15]+x[14], 7); x[13] ^= R(x[12]+x[15], 9);
        x[14] ^= R(x[13]+x[12],13); x[15] ^= R(x[14]+x[13],18);
    }
    for (i = 0;i < 16;++i) out[i] = x[i] + in[i];
}
```

### 2.2. scryptBlockMix

```
   Parameters:
            r Block size parameter.
   Input:
            B[0] || B[1] || ... || B[2 * r - 1]
                   Input octet string (of size 128 * r octets),
                   treated as 2 * r 64-octet blocks,
                   where each element in B is a 64-octet block.
   Output:
            B'[0] || B'[1] || ... || B'[2 * r - 1]
                   Output octet string.
   Steps:
     1. X = B[2 * r - 1]
     2. for i = 0 to 2 * r - 1 do
          T = X xor B[i]
          X = Salsa (T)
          Y[i] = X
        end for
     3. B' = (Y[0], Y[2], ..., Y[2 * r - 2],
              Y[1], Y[3], ..., Y[2 * r - 1])
```

### 2.3. scryptROMix

```
   Input:
            r Block size parameter.
            B Input octet vector of length 128 * r octets.
            N CPU/Memory cost parameter, must be larger than 1,
                    a power of 2, and less than 2^(128 * r / 8).
   Output:
            B' Output octet vector of length 128 * r octets.
   Steps:
     1. X = B
     2. for i = 0 to N - 1 do
          V[i] = X
          X = scryptBlockMix (X)
        end for
     3. for i = 0 to N - 1 do
          j = Integerify (X) mod N
                 where Integerify (B[0] ... B[2 * r - 1]) is defined
                 as the result of interpreting B[2 * r - 1] as a
                 little-endian integer.
          T = X xor V[j]
          X = scryptBlockMix (T)
        end for
     4. B' = X
```

### 2.4. scrypt

```bash
   Input:
            P Passphrase, an octet string.
            S Salt, an octet string.
            N CPU/Memory cost parameter, must be larger than 1,
                    a power of 2, and less than 2^(128 * r / 8).
            r Block size parameter.
            p Parallelization parameter, a positive integer
                    less than or equal to ((2^32-1) * hLen) / MFLen
                    where hLen is 32 and MFlen is 128 * r.
            dkLen Intended output length in octets of the derived
                    key; a positive integer less than or equal to
                    (2^32 - 1) * hLen where hLen is 32.
   Output:
            DK Derived key, of length dkLen octets.
   Steps:
    1. Initialize an array B consisting of p blocks of 128 * r octets
       each:
        B[0] || B[1] || ... || B[p - 1] =
          PBKDF2-HMAC-SHA256 (P, S, 1, p * 128 * r)
    2. for i = 0 to p - 1 do
          B[i] = scryptROMix (r, B[i], N)
        end for
    3. DK = PBKDF2-HMAC-SHA256 (P, B[0] || B[1] || ... || B[p - 1], 
                                        1, dkLen)
```

在莱特币的挖矿算法中，选取的参数为：

- P：区块头；
- S：区块头；
- N：固定为1024；
- r：固定为1；
- p：固定为1；
- dkLen：固定为32，即输出长度为32个字节。

因此，莱特币的区块头哈希值为powhash = scrypt(blockheader, blockheader, 1024, 1, 1, 32)。可以参考莱特币获取区块头哈希的Go语言版本[实现](https://github.com/ltcsuite/ltcd/blob/master/wire/blockheader.go#L69)。

## 3. 参考资料

- [Litecoin - a lite version of Bitcoin. Launched!](https://bitcointalk.org/index.php?topic=47417.3400)：莱特币创始人发表在bitcointalk.org上的帖子，因为莱特币没有白皮书，则莱特币创始人李启威（英文名字为Charlie lee）发表在bitcointalk.org上的帖子姑且算是个白皮书。
- [Tenebrix, a CPU-friendly, GPU-hostile cryptocurrency](https://bitcointalk.org/index.php?topic=45667.msg544675#msg544675)：介绍Tenebrix的帖子。
- [http://www.tarsnap.com/scrypt.html](http://www.tarsnap.com/scrypt.html)：介绍Scrypt算法的主页。
- [https://datatracker.ietf.org/doc/rfc7914/?include_text=1](https://datatracker.ietf.org/doc/rfc7914/?include_text=1)：Scrypt算法的标准文档。
- [crypto/scrypt/scrypt.go](https://github.com/golang/crypto/blob/master/scrypt/scrypt.go)：Scrypt算法的Go语言实现代码。
- [scrypt基于密码的密钥派生函数(译)](http://rossihwang.farbox.com/post/2014-03-25)
- [Scrypt-wikipedia](https://en.wikipedia.org/wiki/Scrypt)：Scrypt算法的维基百科主页。