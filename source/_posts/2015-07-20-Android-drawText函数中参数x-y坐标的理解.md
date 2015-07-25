title: 'Android drawText函数中参数x,y坐标的理解'
date: 2015-07-20 11:24:24
tags: [Android, Canvas, drawText]
---
&nbsp;&nbsp;&nbsp;&nbsp;大部分graphics APIs中绘制字体的x,y参数都是指定baseline的位置的。而有关字体的FontMetrics示意图如下所示：

![FontMetrics示意图](http://i.imgur.com/GYiCXNK.png)


&nbsp;&nbsp;&nbsp;&nbsp;有了上面的了解，我们在自己写TextView是，就能够明白为什么最后绘制字体是通过这条语句绘制出来的了：

	canvas.drawText(mText, getWidth() / 2 - mBound.width() / 2, getHeight() / 2 + mBound.height() / 2, mPaint);//mBound字的实际占用的区域