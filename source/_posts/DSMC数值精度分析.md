---
title: DSMC数值精度分析
date: 2020-03-22
tags: DSMC
---

DSMC和CFD一样是一种计算模拟方法，那么作为计算手段面临的质疑自然也无法避开，“你这结果算的对吗？”，“你这结果计算精度咋样？”，“你这算的快吗，效率如何？”。由于不是专门做计算的，只能凭自己的使用经验来回答这些问题。概括来说，“在DSMC适用的情况下，满足DSMC的一些计算要求后，可以很有效率地得到满足精度的可靠结果”。这里的适用情况当然指的是偏稀薄的条件，而计算要求就是后面要具体介绍的内容，而效率性问题算是计算技巧，没有这些技巧硬算也能算出来，无非就是计算核时长一点。<!--more-->

## 一、DSMC数值稳定性

由于DSMC是一种粒子方法，通过统计粒子的微观属性得到宏观流动信息，因此CFD中的数值稳定性问题，总是稳定的。

## 二、DSMC的数值精度

类似于CFD中的网格无关性验证，DSMC其实也需要这样的验证步骤。需要网格、时间步长、模拟分子数等参数取合适的值才能等到较为精确的结果，使得加密网格或减小时间步长不会再影响流场。这些量的选取前人有较为经验性的总结，不需要再对每一个算例进行重复操作，只需要在设置算例时给这些量选取合适的值，最终结果分析时，注意这些指标是否满足。

### 1. 网格尺寸

> 网格尺寸要小于当地分子平均自由程，$ \Delta x / \lambda = 0.3 \sim 1 $

DSMC中划分的网格是为了方便寻找合适的碰撞对，程序会在单个网格内对分子对进行遍历，判断是否会发生碰撞。因此，若网格选取过大，可能会出现相距很“远”的两个粒子之间发生了碰撞的情况，“远”到远超过当地的分子平均自由程，这显然是非物理的。所以，网格尺寸不能过大，另一方面，由于宏观流动是通过统计网格内粒子的微观属性得到的，流场物理量的梯度对网格尺寸也有限制，过大的网格无法体现出流场梯度。

网格过小的话，计算域内的网格数会大幅度增加，为了保证每个网格内的粒子数，总的模拟粒子数会大幅度增加，从而整体计算量呈量级增加。一般网格尺寸保持在当地分子平均自由程的0.3~1为宜。

### 2. 时间步长 $ \Delta t \lt \Delta x / \bar{c} $

> 时间步长要小于分子平均碰撞时间，单位时间步内粒子运动距离不超过一个网格。

对于网格尺寸的要求使得其与分子平均碰撞距离相近，若在DSMC进行分子运动运动那一步时，分子运动超过一个网格，就意味着分子在超过分子平均自由程的路径上没有发生碰撞，这不符合物理，因此需要时间步长小一些。单位时间步内分子运动距离不超过一个网格的要求比时间步长小于分子平均碰撞时间更严格，因此一般据此估计。

为了方便估计，分子最概然速率可以用当地声速来估计，用网格尺寸除以声速就得到了合适的时间步长，如果是非定常流动，流场密度变化快，时间步长可能需要相应调整来捕捉流动的非定常性。

### 3. 每个网格内的模拟粒子数 $ n = 10 \sim 20 $

> 每个网格内的粒子数要足够多

DSMC从分子的微观属性统计得到宏观流动信息，因此，每个网格内的分子数 $ n $ 不能太少，否则统计出来的结果涨落过大。虽然真实的物理世界中，分子的属性确实存在涨落，但由于每个网格内的分子数 $ n $ 过少引起的这种涨落是不真实的。增加 $ n $在统计后会平均这个不真实的涨落，但是不管如何增长，微观上本身具有的涨落都会一直存在。

前人指出，每个网格内分子数以 $ 10 \sim 20 $ 个为宜。过多会增加不必要的计算量。当然，这一指标不是在设计算例时直接调整的，而是需要调整每个模拟粒子所代表的真实粒子数fnum。可估算$ f_{num} = \frac{N_{p,real}} {Nx \cdot Ny \cdot 10} $，其中$ N_{p,real} $为流场初始时的总的真实粒子数。

## 三、算例设计时的参数选定

不难发现，上述三个要求，分别时为了避免在DSMC算法的“碰撞”、“运动”和“统计”三个主要过程中出现不真实/非物理的过程，从而降低模拟的计算精度。严格来说，采取更严格的计算要求，比如网格非常密，时间步长非常小，每个网格内的粒子数非常多，也能得到精确的结果，但是相比“达到要求”，“远超过要求”提升的精度十分有限，增加的计算量确实非常多。加之DSMC模拟本身就时常被计算量所困，因此，达到这些要求即可。

回到SPARTA设计算例的操作中，除去流动条件（速度，密度，温度，壁面条件等），计算上需要设置的是网格，fnum和时间步。首先需要确定的是网格尺寸（网格数量随之确定），根据流场初始的分子状态计算其分子平均自由程，网格尺寸与之相近。然后需要确定fnum，根据总的真实分子数和网格数可以估算得到。最后是选定时间步长，时间步长可以用网格尺寸和分子平均速率估计。

由于流场发展过程中，流场的密度会分布不均，某些地方会更稠密，有些地方会更稀疏，SPARTA提供了相应的函数来调整网格，可以根据判据1来调整，保持网格尺寸$ \Delta x $与当地分子平均自由程$ \lambda $一致，若$ \Delta x \gt \lambda $，则将该网格划分成更小的4个或9个网格，反之亦然；也可以根据判据3来调整，保持每个网格内的粒子数在$ 10 \sim 20 $，若网格内的粒子数超过了某一阈值，比如说50个，则将该网格拆分成4个小网格，反之亦然。这样就能在计算的过程中保证一个判据肯定是满足的，最终的计算结果是否精确，只需要观察另一个判据是否满足。

## 四、经验

1. 在处理计算量非常大的算例时，在设置算例时，可以放松这些要求，然后在计算的过程中对分子数进行加倍，加密网格，减小时间步，逐步满足要求，否则在流场发展的初期，计算得非常慢，然而流场建立的过程又不是真实需要的，可以粗略计算，在流场稳定后再逐步收紧各个判据，提高计算精度。DS2V程序中，默认采用了这种逻辑，而且大部分判据程序自动调整使其满足要求，只需要通过ratio的值（即判据1）就能判断计算精度如何。
