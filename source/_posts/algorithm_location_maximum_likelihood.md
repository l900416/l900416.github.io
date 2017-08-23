---
layout:     post
title:      基于二维空间的极大似然估计算法
date:       2017-08-23 16:41:27
author:     liangtong
catalog: true
categories: Algorithm
tags: 位置

---

  基于距离的极大似然估计法利用多个已知节点的位置及节点与目标点位置的距离来计算出目标点的位置。

#### 极大似然估算法   

极大似然估算法定位原理：假设定位区域目标点，n个已知位置和距离目标点距离的节点，可得到目标点与n个节点之间的距离方程。
设节点的坐标为(x<sub>1</sub>,y<sub>1</sub>) , (x<sub>2</sub>,y<sub>2</sub>) , ... , (x<sub>n</sub>,y<sub>n</sub>)，目标点坐标 (x,y)，各节点到目标点的距离d<sub>1</sub> , d<sub>2</sub>,..., d<sub>n</sub>，则可的方程组 ( x<sub>i</sub> - x )²  + (y<sub>i</sub> -y)² = d<sub>i</sub>² , 其中I∈[1,n]。
对方程组进行求解，可得x，y坐标

#### 代码实现

用于运算的节点简单二维空间节点对象，主要包含坐标及距离，用于极大似然算法估算。

```Objective-C
/**
  * 节点对象，包含坐标点和距离属性
 **/
@interface LikelihoodNode : NSObject
@property (nonatomic , assign) CGPoint point;
@property (nonatomic , assign) float distance
-(id)initWithX:(float)x Y:(CGFloat)y distance:(float)distance;
@end

@implementation LikelihoodNode
-(id)initWithX:(float)x Y:(CGFloat)y distance:(float)distance{
    self = [super init];
    if (self != nil) {
        self.point = CGPointMake(x, y);
        self.distance = distance;
    }
    return self;
}
@end

```

极大似然算法，对已知节点进行计算，得到目标节点

```Objective-C

/**
  * 极大似然估算，输入多个点数组，计算出目标点坐标
 **/
@interface MaximumLikelihood : NSObject
+ (CGPoint) maximumLikelihoodPointFromArray:(NSArray*)array;
@end

@implementation MaximumLikelihood

+ (CGPoint) maximumLikelihoodPointFromArray:(NSArray*)array{
    CGPoint retPoint = CGPointZero;
    if (array.count > 2) {//至少3个点来计算极大似然数
        long double segmaDeletaX = 0.0;
        long double segmaDeletaY = 0.0;
        long double segmaDeletaX_Y = 0.0;
        
        LikelihoodNode* lastNode = [array lastObject];
        
        /**
         * segma deleta x 2
         * segma deleta y 2
         * segma deleta x * delta y
         **/
        for (int i = 0; i < array.count - 1; ++i) {
            LikelihoodNode* node = [array objectAtIndex:i];
            long double deltaX = [MaximumLikelihood deltaXiWithNode:node and:lastNode];
            long double deltaY = [MaximumLikelihood deltaYiWithNode:node and:lastNode];
            segmaDeletaX += pow(deltaX,2);
            segmaDeletaY += pow(deltaY,2);
            segmaDeletaX_Y += deltaX * deltaY;
        }
        
        long double preNumber = 0.0;
        long double temp = segmaDeletaX * segmaDeletaY - pow(segmaDeletaX_Y, 2);
        
        if (temp != 0) {
            preNumber = (2 / temp) / 4;
        }
        
        long double maximumLikehoodX = 0.0;
        long double maximumLikehoodY = 0.0;
        for (int i = 0; i < array.count - 1; ++i) {
            LikelihoodNode* node = [array objectAtIndex:i];
            long double deltaX = [MaximumLikelihood deltaXiWithNode:node and:lastNode];
            long double deltaY = [MaximumLikelihood deltaYiWithNode:node and:lastNode];
            long double fDi = [MaximumLikelihood getFr_xFrom:node and:lastNode];
            maximumLikehoodX += (segmaDeletaY * deltaX - segmaDeletaX_Y * deltaY) * fDi;
            maximumLikehoodY += (segmaDeletaX * deltaY - segmaDeletaX_Y * deltaX) * fDi;
        }
        
        retPoint.x = maximumLikehoodX * preNumber;
        retPoint.y = maximumLikehoodY * preNumber;
    }
    
    return retPoint;
}

//deltaX
+(long double)deltaXiWithNode:(LikelihoodNode*)node and:(LikelihoodNode*)lastNode{
    long double ans = 0.0;
    ans = node.point.x - lastNode.point.x;
    return ans;
}

//deltaY
+(long double)deltaYiWithNode:(LikelihoodNode*)node and:(LikelihoodNode*)lastNode{
    long double ans = 0.0;
    ans = node.point.y - lastNode.point.y;
    return ans;
}

//f(di)
+(float)getFr_xFrom:(LikelihoodNode*)node and:(LikelihoodNode*)lastNode{
    double ans = 0.0;
    ans = (node.point.x * node.point.x) - (lastNode.point.x * lastNode.point.x) + (node.point.y * node.point.y) - (lastNode.point.y * lastNode.point.y) - (node.distance * node.distance) + (lastNode.distance * lastNode.distance);
    
    return ans;
}

```

考虑到实际情况下，节点与目标点会随距离的增大导致误差变大，故可以通过节点吸附对目标点坐标进行校正。
在参与节点与目标点满足情况的前提下，校正步骤如下：
 + 判断目标点距离最近节点nNode的距离distance
 + 若disatance小于吸附临界值(比如：1m)
   + 根据像素与实际地理米转换关系计算出极大似然估算法节点mNode与nNode的距离distancePointToNearest
   + 若distancePointToNearest不大于distance，则不需要进行校正
   + 否则计算出mNode与nNode位置之间连线与distnace处交点的坐标，即为校正后的坐标
 + 否则不进行吸附校正

```Objective-C

/**
 * 校正、吸附算法。
 * 假设参与计算的节点按照距目标点距离递增的顺序排列
 * 此处仅在计算结果与最近节点距离小于1米的情况下进行校正。
 **/
+ (CGPoint) maximumLikelihoodPointFromArray:(NSArray*)array mToPxScale:(CGFloat)mToPxScale{
    CGPoint retPoint = [MaximumLikelihood maximumLikelihoodPointFromArray:array];
    //根据像素与米的转换关系，判断若最近的小于1米，且计算坐标在最近区域外时，取交点坐标为最终结果坐标
    if (array.count > 2) {
        LikelihoodNode* nearest = [array firstObject];
        if (nearest.distance <= 1.0) {
            CGFloat xDist = (retPoint.x - nearest.point.x) * mToPxScale;
            CGFloat yDist = (retPoint.y - nearest.point.y) * mToPxScale;
            CGFloat distancePointToNearest = sqrt((xDist * xDist) + (yDist * yDist));
            if (distancePointToNearest > nearest.distance) {
                CGFloat λ = nearest.distance / (distancePointToNearest - nearest.distance);
                CGFloat x =  (nearest.point.x + λ * retPoint.x) / (1 + λ);
                CGFloat y =  (nearest.point.y + λ * retPoint.y) / (1 + λ);
                retPoint = CGPointMake(x, y);
            }
        }
    }
    return retPoint;
}
```



#### 参考文档

https://wenku.baidu.com/view/1db9c08a71fe910ef12df8df.html
https://wenku.baidu.com/view/3dba1baef524ccbff12184af.html?re=view
http://www.doc88.com/p-6037723035623.html
