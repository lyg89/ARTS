# ARTS Week 01
## Alogrithm
### 1. Two Sum(java)

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```
### My Answer

```java
import java.util.HashMap;
import java.util.Map;

/**
 * @description:
 * @author: liyaguang
 * @create: 2018-09-21 13:10
 **/
public class S1TwoSum {

    public static void main(String[] args) {
        int[] r = new S1TwoSum().twoSum(new int[]{11, 15, 2, 7}, 9);
        for (int i = 0; i < r.length; i++) {
            System.out.println(r[i]);
        }
    }

    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> mapNums = new HashMap<>(nums.length);
        int[] results = new int[2];
        for (int i = 0; i < nums.length; i++) {
            if (mapNums.keySet().contains(target - nums[i])) {
                results[0] = mapNums.get(target - nums[i]);
                results[1] = i;
                return results;
            }
            mapNums.put(nums[i], i);
        }
        return results;
    }
}
```

## Review
1. Youtube上订阅了皓哥推荐的一些频道，浏览到一个数学题，感觉挺有趣，后来才知道这种证明方式是不严谨的。原视频地址：https://youtu.be/w-I6XTVZXww

S = 1 + 2 + 3 + … = ？

解：

 S1=1 - 1 + 1 - 1 + 1 - 1 …

 => 1 − S1 = 1 − (1 − 1 + 1 − 1 + ...) = 1 − 1 + 1 − 1 + ... = S1

 => 1 − S1 = S1

 => S1 = 1/2

 ***

 S2=1 - 2 + 3 - 4 + 5 - 6 … 

 =>  S2 + S2

     =1 - 2 + 3 - 4 + 5 - 6 … 

     + [1 - 2 + 3 - 4 + 5 - 6 … ]

     =1-1+1-1+1…

     =1/2 

 => S2=1/4

 ***

 S-S2 

 =1 + 2 + 3 + … 

  -[1 - 2 + 3 - 4 … ]

 =4+8+12+…=4*(1+2+3+4+…)=4*S

 => > 3*S=-S2

 => S=-1/12

2. 公司用到的kafka，之前对其了解的不多，前一阵子翻看了官方文档，简单翻译了介绍的章节，姑且贴到这里：
> Kafka-note-intro
> * 分布式流式平台
> 1. 一个流式平台有三个特点：
> * 发布、订阅流式记录，类似于消息队列或企业级消息系统
> * 以容错的持久化方式存储记录流
> * 记录发生时处理流

> 2. 通常被用于两大类应用：

> * 构建可在系统或应用程序之间可靠获取数据的实时流数据管道
> * 构建转换或响应数据流的实时流应用程序

> 3. 几个核心概念：

> * kafka作为一个集群运行在一台或多台能够跨越多个数据中心的服务器上
> * kafka集群存储记录流的类别称为topics
> * 每条记录包含一个key、value、timestamp

> 4. 四个核心apis
> * 应用程序可通过Producer API发送一个数据流到一或多个Kafka topics
> * 应用程序可通过Consumer API订阅一或多个topics，处理生成给对应topic的数据流
> * 应用程序可通过Stream API扮演一个流处理器角色，消费来自一个或多个主题的输入流并产生到一个或多个输出主题的输出流，有效地将输入流转换为输出流。
> * 通过Connector API可实现构建、运行可重复使用的生产者或消费者，连接Kafka topics到已存在应用或数据系统。例如，关系数据库的连接器能够捕获对表的每个更改

> 客户端与服务端的通信基于简单的、高性能的、开发语言透明的TCP协议，此协议已版本化并保持与旧版本的向后兼容性，我们为Kafka提供Java客户端，其他语言也有很多客户端。
> ### Topics and Logs
> 首先深入kafka为流式数据提供的核心概念——topic
> topic是指发布记录的类别或订阅源名称。topic总是有多个订阅者，也就是一个topic可以有0个、一个或多个消费者订阅写入它的数据。
> 对于每一个topic，Kafka集群维护了一个分区日志，如下图所示

> 每个分区是一个有序的、不可变的记录序列，不断被追加的一个结构化提交日志。在分区中的每一条记录都被分配了一个有序的id号，称作offset，作为在分区中每条记录的唯一标识
> Kafka使用一个可配置的保存期限，持久化存储所有发布的记录，无论他们是否被消费。例如，如果保存策略设置为了2天，然后，在记录发布的两天内，它可供消费，之后他将被丢弃以释放空间。Kafka的性能在数据大小方面实际上是恒定的，因此长时间存储数据不是问题。

> 实际上，基于每个消费者保留的唯一元数据是该消费者在日志中的偏移或位置。该偏移由消费者控制：一般消费者在读取记录时会线性的提高其偏移量。但实际上，由于位点由消费者控制，因此可以按任意的顺序消费记录。例如，一个消费者可以重置到一个旧的offset，以重新处理过去的数据或者跳到最近的记录并从“现在”开始消费。

> 这些的功能组合意味着Kafka消费者非常轻量，他们可以在对集群和其他消费者没有太大影响的情况下加入或移除。例如，你能够使用我们的命令行工具来追加任意topic的内容，无需修改现有消费者消费的内容。

> 日志中的分区有多种用途：首先，它们允许日志扩展到超出适合单个服务器的大小。每个单独的分区必须适合托管它的服务器，但主题可能有许多分区，因此它可以处理任意数量的数据。其次，它们充当了并行性的单位 - 这个用途更重要一些。

> * Distribution
> 日志的分区分布在Kafka集群中的服务器上，每个服务器处理数据并请求分区的共享。每个分区都在可配置数量的服务器上进行复制，以实现容错。

> 每个分区都有一个服务器充当“leader”，零个或多个服务器充当“followers”。leader处理分区的所有读取和写入请求，而followers被动地复制领导者。如果领导者失败，其中一个followers将自动成为新的leader。每个服务器都充当其某些分区的领导者和其他服务器的追随者，因此负载在群集中得到很好的平衡。

> * Geo-Replication
> Kafka MirrorMaker为你的集群提供了镜像复制支持，通过它消息被复制到多个数据中心或云区域。你能使用他在主/备方案中实现备份和恢复；或者在主/主方案中将数据放在里用户较近的位置，或支持数据位置请求。

> * Producers
> 生产者发送数据到他们选中的topics。生产者负责选择哪条数据被分配到哪个分区。这可以通过循环方式完成，只是为了平衡负载，或者可以根据一些语义分区功能（比如基于记录中的某些键）来完成。

> * Consumers
> 消费者使用consumer group名称标记自己，发布到topic的每条记录被传递到每个订阅者消费组中的一个消费者实例。消费者实例可以在不同进程中，也可以在不同机器。

> 如果所有的消费者实例都拥有同样的consumer group，则记录将被有效的在消费者实例上进行负载均衡。
> 如果所有的消费者实例有不同的consumer group，则每条记录将被广播到所有消费者进程。


> 拥有两台服务器的kafka集群，托管了四个分区（P0-P3），包含了两个consumer groups，consumer group A有两个消费者实例，B有4个实例。

> 然而，更常见的是，我们发现主题具有少量的消费者群体，每个“逻辑订户”一个。每个组由许多用于可伸缩性和容错的消费者实例组成。这只不过是发布 - 订阅语义，其中订阅者是消费者群集而不是单个进程。

> Kafka中实现消费的方式是在日志中划分分区覆盖消费者实例，这样每个实例都是任何时间点分区中“公平份额”的唯一消费者。维护组中成员资格的过程由Kafka协议动态处理。如果新实例加入该组，他将从该组其他成员接管一些分区；如果实例死亡，其分区将分发给其余实例。

> Kafka仅提供partition内记录的总排序，而不是topic中不同分区之间的记录。对于大多数应用程序而言，按分区排序与按键分区数据的能力相结合就足够了。但是，如果您需要对记录进行总排序，则可以使用仅包含一的主题来实现，但这意味着每个消费组只有一个消费进程。

## Tips
1. linux命令：服务器间传递文件：nc命令
2. Java远程调试：配置JVM启动参数即可
3. 在调试慢sql的时候，注意到执行计划Extra列出现Using MRR的标记，查了下[资料](http://mysql.taobao.org/monthly/2016/01/04/)：MRR 的全称是 Multi-Range Read Optimization，是优化器将随机IO转化为顺序IO以降低查询过程中IO开销的一种手段

## Share
[JVM动态内存划分与垃圾回收算法——《深入理解Java虚拟机》读书总结](https://blog.csdn.net/lfsfxytgb9_lyg/article/details/80837046)
之前阅读了《深入理解Java虚拟机》中的主要章节，总结了几张导图和说明，其中一些参数、细节并没有涉及。本文仅对Java虚拟机运行时内存进行概念模型的详细介绍，并针对堆内存中的对象创建流程、对象数据结构、访问定位做了详细说明。最后针对垃圾回收算法，围绕着回收哪些对象、何时回收、怎么回收展开描述，经过整理这些内容，对JVM内存结构、垃圾回收有了理论上的认识。
