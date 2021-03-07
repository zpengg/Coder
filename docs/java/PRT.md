# PRT
RSet内部使用**Per Region Table(PRT)**记录分区的引用情况。
由于RSet的记录要占用分区的空间，如果一个分区非常"受欢迎"，那么RSet占用的空间会上升，从而降低分区的可用空间。[[G1]] 采用了**改变RSet的密度**的方式，在PRT中将会以三种模式记录引用：
 - 稀少：直接记录引用对象的卡片索引
 - 细粒度：记录引用对象的分区索引 
 - 粗粒度：只记录引用情况，每个分区对应一个比特位

[[RSet]] 