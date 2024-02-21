---
title:  Kubernetes的派对规划指南：如何让你的Pod找到心仪的Node节点
date:   2024-02-21 09:39:00 +0800
categories: [运维]
tags: [Kubernetes]
---

Kubernetes（我们亲切地叫它k8s，因为它是那种喜欢用缩写的酷孩子）是一个编排大师，它决定哪些Pod应该去参加哪个Node节点的派对。但是，不是所有的Pod都是社交蝴蝶，有些可能只愿意和特定Node节点玩耍。那么，k8s如何做这个派对策划人呢？以下是几种让Pod们“挑剔”的方法：

### 1.节点选择器（NodeSelector）：
想象一下，Node节点是各种口味的冰激凌车，而Pod是带着地图的小朋友，地图上画着他们想吃的冰激凌口味。nodeSelector就像是那个地图，告诉Pod：“嘿，你得去那个有‘disktype=ssd’口味的冰激凌车那里！”
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: my-image
  nodeSelector:
    disktype: ssd
```

### 2.节点亲和性（Node Affinity）：
这可比节点选择器复杂多了，它就像是Pod的高级味蕾。节点亲和性不仅说“我只要吃草莓味”，它还会说“我绝对不吃香草味”。如果你告诉它去找‘disktype=ssd’的节点，它就会像小狗寻找埋藏的骨头一样，直到找到为止。
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: my-image
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
```

### 3.节点反亲和性（Node Anti-Affinity）：
这是Pod的“反骨”期。如果Node节点带着‘disktype=hdd’这样的标签，Pod会像个叛逆少年一样说：“我才不要和你玩！”然后它会跑去寻找其他没有这种标签的Node节点。
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: my-image
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: NotIn
            values:
            - hdd
```

### 4.污点（Taints）和容忍度（Tolerations）：
污点就像是Node节点上的“禁止入内”标志，只有真正胆大的Pod才会无视它。容忍度则是Pod的小本本，上面写着“我不怕你的标志，我有特殊通行证！”所以，即使Node节点挂出了“NoSchedule”的大牌子，有容忍度的Pod还是可以大摇大摆地走进去。
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: my-image
  tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
```

总之，k8s就像一个有着各种各样派对邀请函的聚会策划人。不管你是挑食的美食家，还是有特殊饮食要求的健身达人，k8s总有办法让你找到合适的派对——或者说，Node节点。