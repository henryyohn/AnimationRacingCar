# AnimationRacingCar
赛车动画

![效果如下图](https://github.com/ete8652/AnimationRacingCar/blob/master/eitn3-tjw3p.gif?raw=true)


朋友拜托我写一个赛车动画，总结了一下需求。
[博客地址](https://blog.csdn.net/fenghuangjc/article/details/81260111)

### 需求：

1.赛车有加速动画，轮子滚动，有空气、火焰效果
2.能实时根据赛车位置，展示赛车排行。
3.背景图一直循环滚动

### 实现思路：

加速动画：原来我想用gif图替代，但是发现对CPU消耗太大，只加了两个轮子的gif图，模拟器CPU就很高，所以我使用了定时器，切换图片的方法，iPhone6上CPU使用率降到了15%。
```
//切换图片(定时器调用)
-(void)GIFAnimation{
if (_isSpeedUP) {
//加速状态,切换火焰和空气
self.airImageV.hidden = NO;
self.fireImageV.hidden = NO;
self.airImageV.image = self.arrayAir[_animationPicIndex];
self.fireImageV.image = self.arrayFire[_animationPicIndex];
}else{
//减速状态，隐藏火焰和空气
self.airImageV.hidden = YES;
self.fireImageV.hidden = YES;
}
self.carImageV.image = self.arrayCar[_animationPicIndex];
_animationPicIndex++;
if (_animationPicIndex == self.arrayCar.count) _animationPicIndex = 0;
if (self.carXBlock) {
self.carXBlock(self.layer.presentationLayer.frame.origin.x, self.index);
}
}
```

背景图循环滚动：滚动到指定位置后，再切换到初始位置。

```
-(void)startGame{
//地图移动
//背景移动
__weak typeof(self) weakSelf = self;
[UIView animateWithDuration:10 animations:^{
weakSelf.headBackImageV.frame = CGRectMake(weakSelf.headBackImageV.frame.size.width - weakSelf.frame.size.width, weakSelf.headBackImageV.frame.origin.y, weakSelf.headBackImageV.frame.size.width, weakSelf.headBackImageV.frame.size.height);
weakSelf.roadImageV.frame = CGRectMake(weakSelf.roadImageV.frame.size.width - weakSelf.frame.size.width, weakSelf.roadImageV.frame.origin.y, weakSelf.roadImageV.frame.size.width, weakSelf.roadImageV.frame.size.height);
weakSelf.finishersImageV.frame = CGRectMake(300, weakSelf.finishersImageV.frame.origin.y, weakSelf.finishersImageV.frame.size.width, weakSelf.finishersImageV.frame.size.height);

} completion:^(BOOL finished) {

}];
//隐藏起跑线
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(3 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
self.finishersImageV.hidden = YES;
});
}

-(void)movieImage{
CGFloat movieX = 5;
if (self.roadImageV.frame.origin.x > -5 || self.headBackImageV.frame.origin.x > -22) {
//一圈后背景切换
self.roadImageV.frame = _roadRect;
self.headBackImageV.frame = _headBackRect;
self.finishersImageV.hidden = YES;
}
if (!_roadRect.origin.x) {
//记录背景图起始点
_roadRect = self.roadImageV.frame;
_headBackRect = self.headBackImageV.frame;

//动画开始
[self startAnimationNew:YES];
}

//改变背景图位置
self.headBackImageV.frame = CGRectMake(self.headBackImageV.frame.origin.x + movieX , self.headBackImageV.frame.origin.y, self.headBackImageV.frame.size.width, self.headBackImageV.frame.size.height);
self.roadImageV.frame = CGRectMake(self.roadImageV.frame.origin.x + movieX , self.roadImageV.frame.origin.y, self.roadImageV.frame.size.width, self.roadImageV.frame.size.height);
}
```


