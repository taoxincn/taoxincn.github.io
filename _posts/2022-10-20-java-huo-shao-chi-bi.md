---
title:  一道Java考题，火烧赤壁
date:   2022-10-20 22:17:00 +0800
categories: [开发, 后端]
tags: [Java]
---

# 题目描述
公元208年，曹操发兵攻打荆州，刘备派诸葛亮到东吴，说服了孙权。孙、刘两家联合起来共同抵抗曹军。这年冬天，孙刘大军在赤壁与曹军隔江对峙。

曹军将领常年在北方生活，不习惯坐船，许多人因为晕船根本没法打仗。于是曹操就下令将战船用铁链连在一起，上面铺上木板，这样在上面行走的时候，就好像走在平地上一样，不会晕船了。吴军将领黄盖观察到这个情况后，就对大都督周瑜献计道，曹军的船只连在一起。我们可以采取火攻。周瑜听后点了点头，也觉得此计可行。于是黄盖先给曹操写了一封信，假称要投降曹操。

曹操信以为真，便和黄盖约定了投降的时间和暗号。到了约定的这天，黄盖带着十艘轻便的小船载满浇了油的柴草，外面用帷幕挡上，插上惊奇旌旗前往曹营。当时刮的是东南风，十艘船，顺风而行。曹操以为这是来投降的，所以毫无防备，等到离曹营只有二里左右的时候，黄盖命令士兵点燃柴草。

黄盖下令士兵，将同行同列或同一斜行的三艘船点燃借助风势就可以将所有的曹军船只点燃，然而曹操也下令士兵开始阻碍火船的前进，同理，曹操如果阻碍了同行同列或同一斜行的三艘船着火，黄盖将被生擒，如果双方都没有完成，则相互撤军，已知黄盖每次只能下令点燃一艘曹军船只，曹操每次只能指挥阻碍一艘火船的前进，现在我们用九个数字代表曹军船只的位置
1 2 3
4 5 6
7 8 9

前方的斥候给刘孙大军带回来一封密函交给诸葛先生，这封密函是一串数字代表黄将军和曹操对弈的顺序，现在让诸葛先生你来判断这场战斗究竟是谁赢了。

# 输入描述
一行，一串数字，表示黄盖点火和曹军阻碍的地点，黄盖总是先手。

# 输出描述
一行，如果黄盖赢，输出`huang wins.`。如果曹操赢，输出`cao wins.`。如果平局，输出`drew.`

# 示例
#### 示例1
输入      `5237649`
输出      `huang wins.`

# 答案
```java
import java.util.HashSet;
import java.util.Scanner;
import java.util.Set;

public class Main {

    public static void main(String[] args) {
        Set<Character> huang_set = new HashSet<>();
        Set<Character> cao_set = new HashSet<>();
        Scanner scanner = new Scanner(System.in);
        char[] sequence = scanner.nextLine().toCharArray();
        scanner.close();
        for (int i = 0; i < sequence.length; i++) {
            if((i&1)==0){
                huang_set.add(sequence[i]);
                if(win(huang_set)){
                    System.out.println("huang wins.");
                    return;
                }
            }else {
                cao_set.add(sequence[i]);
                if(win(cao_set)){
                    System.out.println("cao wins.");
                    return;
                }
            }
        }
        System.out.println("drew.");
    }

    public static boolean win(Set<Character> set) {
        char[][] chs = {
                {'1', '2', '3'}, {'4', '5', '6'}, {'7', '8', '9'},//横排
                {'1', '4', '7'}, {'2', '5', '8'}, {'3', '6', '9'},//竖排
                {'1', '5', '9'}, {'3', '5', '7'}//斜排
        };
        for (char[] ch : chs) {
            if (set.contains(ch[0]) && set.contains(ch[1]) && set.contains(ch[2])) {
                return true;
            }
        }
        return false;
    }
}
```
