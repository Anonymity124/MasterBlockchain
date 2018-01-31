# FNV哈希算法

FNV算法属于非密码学哈希函数，它最初由Glenn Fowler和Kiem-Phong Vo于1991年在IEEE POSIX P1003.2上首先提出，最后由Landon Curt Noll 完善，故该算法以三人姓的首字母命名。

FNV算法目前有三种，分别是FNV-1，FNV-1a和FNV-0，但是FNV-0算法已经被丢弃了。FNV算法的哈希结果有32、64、128、256、512和1024位等长度。如果需要哈希结果长度不属于以上任意一种，也可以采用根据[Changing the FNV hash size - xor-folding](http://www.isthe.com/chongo/tech/comp/fnv/#xor-fold)上面的指导进行变换得到。

## 算法过程

### FNV-1

FNV-1算法过程如下：

```
hash = offset_basis
for each octet_of_data to be hashed
    hash = hash * FNV_prime
    hash = hash xor octet_of_data
return hash
```

参数说明（以32位结果为例，其它长度同理）：

- 所有的参数，除了octet_of_data之外，都是32位无符号整型，即hash、offset_basis、FNV_prime类型都是32位无符号整型；
- octet_of_data的类型是8位无符号整型；
- 32位的offset_basis值为2166136261=0x811c9dc5，FNV_prime值为2^24 + 2^8 + 0x93 = 16777619，其它参数可以查看FNV的[维基百科主页](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function)；
- 算法循环的次数等于输入的字节长度；
- 算法的相乘部分，因为hash类型是32位无符号整型，故相乘结果需要mod 2^32；
- 算法的异或部分，octet_of_data为32位值的低8位，其它三个字节不进行异或运算。

举例：

输入“V”，对应的十六进制值为0x56，输出32位的FNV hash值为0x050c5d49。可以使用[在线工具](https://fnvhash.github.io/fnv-calculator-online/)得到结果。

计算过程：

1. 初始化：hash = 2166136261
2. 进入循环，因为要求的数据长度只有一个字节长度，故循环只有一次。因为hash结果为32位无符号整型，故需要舍弃高位，保留低32位：hash = (2166136261 * 16777619) mod 2^32 = 0x050c5d1f
3. 进行异或运算，首先将0x56转化为32位的值0x00000056，然后才能进行异或运算：hash = 0x050c5d1f xor 0x00000056 = 0x050c5d49

也可以点击参考资料中的FNV函数Go代码更加深刻地理解上述计算过程。

### FNV-1a

FNV-1a算法过程如下：

```
hash = offset_basis
for each octet_of_data to be hashed
    hash = hash xor octet_of_data
    hash = hash * FNV_prime
return hash
```

可以发现，FNV-1a算法只是将相乘和异或运算进行了顺序调换，其它过程和参数与FNV-1相同。

### FNV-0

FNV-0算法过程如下：

```
hash = 0
for each octet_of_data to be hashed
    hash = hash * FNV_prime
    hash = hash XOR octet_of_data
return hash
```

## 参考资料

- [FNV Hash](http://www.isthe.com/chongo/tech/comp/fnv/)：详细介绍FNV算法的网站。在该网站上可以找到该算法的历史，应用以及源码等资料。
- [Fowler–Noll–Vo hash function](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function)：FNV算法的维基百科主页。
- [Source file src/hash/fnv/fnv.go](https://golang.org/src/hash/fnv/fnv.go)：FNV函数的Go语言实现代码，可以更加深入理解FNV算法过程。