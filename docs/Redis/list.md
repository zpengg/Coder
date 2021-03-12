# list 链表

## 分类
按发展时间，有不同版本的链表
 - [[adlist]]/list 早期的双端链表
 - [[ziplist]] 其他数据结构时使用到的**通过编码技巧压缩**的**小**列表
 - [[quicklist]] 后期结合 ziplist 和 adlist 的新版本双端链表

## 简单回顾
### adlist 
![list 结构](http://zpengg.oss-cn-shenzhen.aliyuncs.com/img/16113878972b443a.png)

### ziplist
<div align="center"> <img src="http://zpengg.oss-cn-shenzhen.aliyuncs.com/img/7ffb02c911e8745ec6341106c98f7aea.png"/> </div>


## quicklist 
quicklist 结构是在 redis 3.2 版本中新加的数据结构，用在列表的底层实现。
adlist(linked list) 可以说是没有出场的余地了

## 结构（ 双端 套ziplist）
```C 
typedef struct quicklistNode {
    struct quicklistNode *prev;
    struct quicklistNode *next;
    unsigned char *zl;
    unsigned int sz;             /* ziplist size in bytes */
    unsigned int count : 16;     /* count of items in ziplist */
    unsigned int encoding : 2;   /* RAW==1 or LZF==2 */
    unsigned int container : 2;  /* NONE==1 or ZIPLIST==2 */
    unsigned int recompress : 1; /* was this node previous compressed? */
    unsigned int attempted_compress : 1; /* node can't compress; too small */
    unsigned int extra : 10; /* more bits to steal for future usage */
} quicklistNode;
```

*zl 数据指针，如果没有被压缩，就指向 ziplist 结构，反之指向 quicklistLZF 结构 

```C
typedef struct quicklistLZF {
    unsigned int sz; /* LZF size in bytes*/
    char compressed[];
} quicklistLZF;
```

quicklistLZF 结构表示一个被压缩过的 ziplist。其中：

sz: 表示压缩后的 ziplist 大小。
compressed: 是个柔性数组（flexible array member），存放压缩后的 ziplist 字节数组。

```C
typedef struct quicklist {
    quicklistNode *head;
    quicklistNode *tail;
    unsigned long count;        /* total count of all entries in all ziplists */
    unsigned int len;           /* number of quicklistNodes */
    int fill : 16;              /* fill factor for individual nodes */
    unsigned int compress : 16; /* depth of end nodes not to compress;0=off */
} quicklist;
```

fill: 16bit，ziplist 大小设置，存放 list-max-ziplist-size 参数的值。
compress: 16bit，节点压缩深度设置，存放 list-compress-depth 参数的值。

会影响插入时，是否需要创建节点或者直接从 zlist 中插入

<div align="center"> <img src="http://zpengg.oss-cn-shenzhen.aliyuncs.com/img/fcdb2c189d2caf83b7450c0c914b6244.png"/> </div>
