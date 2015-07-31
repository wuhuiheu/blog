title: listview等item显示不同的view
date: 2015-07-31 17:07:45
tags: [Android, ListView, BaseAdapter]
---

有时会有这样的需求，listview根据不同postion显示不同的视图，例如微信的聊天界面，自己的聊天信息在左边，对方在右边。对于这个问题，我们可以重写BaseAdapter中的`int getItemViewType(int position)`以及`int getViewTypeCount()`的方法。前一个方法返回将要被创造的view的类型，后一个返回view的类型数。然后在`getView`的时候，调用`getItemViewType`，根据不同类型，初始不同的视图。

