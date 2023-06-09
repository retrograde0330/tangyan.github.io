# 最大似然估计

看完这个视频就懂了 下面整理一下 <https://www.bilibili.com/video/BV1Hb4y1m7rE>

## 小球抽取

> 假设有一个超级大的盒子里面装着超级多的球，球有两种颜色，黑色和白色。抽取五次后，得到的结果为3黑2白。估计盒中黑白球所占比例。

设黑球比例为 $\theta$ 则白球比例为 $1-\theta$

在任意抽取五次的情况下，得到3黑2白的概率为 $p = \theta^3 (1-\theta)^2$

因为实际抽取五次得到了3黑2白的结果，也即该事件发生，因此我们推测 $p$ 的值最大

$p$ 关于 $\theta$ 求导，令结果等于$0$
得到 $\hat{\theta} = \frac{3}{5}$

这些步骤中的核心是 让现实中实际发生的事件的概率最大 也就是让$p$取最大值
