# ARTS Week 02
## Algorithm
### 2. Add Two Numbers(java)
You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

Example:
```
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.
```
### My Answer
``` java
/**
 * @description:
 * @author: liyaguang
 * @create: 2018-10-15 19:21
 **/
public class S2AddTwoNumbers {

    public static ListNode addTwoNumbers(ListNode l1, ListNode l2) {

        ListNode result = new ListNode(0);

        int carry = 0;
        ListNode p = l1, q = l2, current = result;

        while (p != null || q != null) {
            int x = p != null ? p.val : 0;
            int y = q != null ? q.val : 0;
            int sum = x + y + carry;

            current.next = new ListNode(sum % 10);
            carry = sum / 10;

            current= current.next;

            p = p != null ? p.next : null;
            q = q != null ? q.next : null;
        }

        if (carry > 0){
            current.next = new ListNode(carry);
        }

        return result.next;
    }

    public static ListNode revertListNode(ListNode ln) {
        ListNode pre = null;
        ListNode cur = ln;
        ListNode next = ln.next;

        while (cur != null) {
            cur.next = pre;

            pre = cur;
            cur = next;
            next = cur != null ? next.next : null;
        }

        return pre;
    }

    public static void main(String[] args) {
        ListNode l11 = new ListNode(2);
        ListNode l12 = new ListNode(4);
        ListNode l13 = new ListNode(3);

        l11.next = l12;
        l12.next = l13;

        ListNode l21 = new ListNode(5);
        ListNode l22 = new ListNode(6);
        ListNode l23 = new ListNode(4);

        l21.next = l22;
        l22.next = l23;

        ListNode res = addTwoNumbers(l11, l21);
        while (res != null) {
            System.out.println(res.val + " ");
            res = res.next;
        }
    }
}

class ListNode {

    int val;
    ListNode next;

    ListNode(int x) {
        val = x;
    }
}
```

## Review

关于日志的那些事儿！
日志文件是系统开发人员和管理员的标配工具。作为系统”卫的角色，记录着系统访问过程中哲学性的三个问题：你是谁，你要去哪，你要做什么？

#### Syslog
在计算机中，Syslog是消息记录的标准，主要用于记录网络设备的状态日志，可用于管理员进行系统管理和安全审计。许多设备（诸如打印机、路由器等）都使用Syslog标准，许多操作系统平台都提供了Syslog的实现。

1. 组成部分
[Syslog](https://en.wikipedia.org/wiki/Syslog)消息的发送端提供设备编码和严重性级别，在将消息传递到接收器之前，Syslog软件会将一些额外的信息添加到消息头当中，主要包括发送端进程id、时间戳以及设备的主机名或ip地址。可用设备类型、日志安全级别分别对应一个标准列表。在不同的操作系统和syslog实现中，设备的code与关键字有可能不一致。

日志安全级别中，除了Emergency和Debug级别外，都与应用程序有关，根据不同的应用场景设定。

另外，还有一个Message部分，新标准成其包含消息的TAG、MSG，TAG属于header中的一部分，不是单个属性，而是分隔成了APP-NAME, PROCID和MSGID。流行的Rsyslog工具符合了这个新的标准。

一个Syslog消息示例：

一个关于Syslog的入门简单介绍视频：
   https://www.youtube.com/watch?v=2jDYd5RkAl8

2. Logger
生成的日志消息可以定向到很多目标，包括控制台、文件、远程syslog服务器、或者是中继器。大多数实现都提供了命令行工具（通常称为logger）以及软件库（用于将消息发送到日志）。一些实现提供了报告程序，用于过滤和展示程序。

3. 网络协议
当进行网络操作时，syslog实现了client-server应用架构，server端在已知或已注册端口监听来自client端的请求。通常用于网络日志传输层协议的是514端口。由于UDP缺乏阻塞控制机制，因此一般应用传输控制层端口6514来支持传输层安全性
未完，待续……

## Tip
工作中，遇到记录用户准实时活跃信息的需求，使用了Apache Storm框架，与Kafka集成，消费埋点日志，筛选出活跃信息后，存入Redis。另外一个定时任务读取Redis数据后存入Mysql。检索服务再将其同步到ES中，供业务端调用。

## share
总结了一下《Java8实战》中Lambda表达式、StreamAPI部分的内容，整理到博客：https://blog.csdn.net/lfsfxytgb9_lyg/article/details/84591967
