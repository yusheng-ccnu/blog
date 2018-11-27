---
title: 年会算法题目
date: 2018-09-05 17:29:15
tags: 
- 算法
- 招商银行
- 年会游戏
categories:
- 面试算法
---
### 公司年会算法题
#### 题目描述
> N个人参与游戏，站成一排来抢工作人抛来的M个小玩偶。为了增加游戏的趣味和难度，规则规定，参与游戏的人抢到的礼物不能比左右两边的人多两个或以上，否则会受到一定的惩罚。游戏结束时拥有玩偶最多的人将获得一份大奖。
假设大家都想赢得这份大奖，请问站在第K个位置的小招在赢得游戏时，最多能拥有几个玩偶？

#### 题目分析

![](20180905-174315.png)

如图所示，假设站在k位置的小招最多拥有p个玩偶，非阴影部分为M，阴影部分为s，两部分加起来就是np，假设三角形的边长为x，而玩偶的数目为f(x)，可得**f(x) = f(x - 1) + x **(图形的垂直高度对应着玩偶的数量)，所以分别计算两个三角形表示的玩偶数目可得到如下的公式：
```
s = (k-1)k/2 + (n-k+1)(n-k)/2
```
考虑可能多余i个玩偶，则从玩偶数量最少的人选择i个发玩偶，则可以这样计算p
```
p = (m + s - i) / n
```

#### 编码实现
用java实现该算法的代码

```Java
import java.util.Scanner;

public class Demo {
    public static void main(String []args){
        Scanner scanner = new Scanner(System.in);
        //参与游戏的人数量
        int n = scanner.nextInt();
        //发放的玩偶数
        int m = scanner.nextInt();
        //小招站的位置
        int k = scanner.nextInt();

        if (n <= 0 || k <=0 || m <= 0 || k > n){
            System.out.println(0);
            return;
        }

        if (n > 0 && k >= 1 && m >= 1) {
            //计算s的值
            int s = ((k - 1) * k >> 1) + ((n - k + 1) * (n - k) >> 1);
            for (int i = 0 ; i < n ; i++) {
                //迭代求解p值
                if ((m + s - i) % n == 0) {
                    System.out.println((m + s - i) / n);
                    break;
                }
            }
        }
    }
}
```