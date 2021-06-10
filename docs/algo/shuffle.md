# 洗牌算法

一言以蔽之，蓄水池算法。

```
for cardPos from last to first:
  pos = random(0 to n-1)
  swap(pos, cardPos)
```

算法很简单，之所以认为洗牌均衡，原因时每张牌选择每个位置的可能性是1/n
