---
layout:     post
title:      Xcode9中TableViewCell高度自适应
date:       2017-11-01 15:51:43
author:     liangtong
catalog: true
categories: iOS
tags: 问题记录

---



​        前几天升级了Xcode9后，在对一个项目进行调试时突然发现有个使用UITableView的自定义Cell无法展示了，然后控制台报了一大堆的约束警告。

```Objective-C
    "<NSLayoutConstraint:0x60c0000977f0 UIView:0x7fbb8c7841d0.bottom == UILabel:0x7fbb8c7870e0.bottom + 8>",
    "<NSLayoutConstraint:0x60c0000979d0 UIView:0x7fbb8c7841d0.top == UITableViewCellContentView:0x7fbb8c781b70.top>",
    "<NSLayoutConstraint:0x60c000097a70 UITableViewCellContentView:0x7fbb8c781b70.bottom == UIView:0x7fbb8c7841d0.bottom + 2>",
    "<NSLayoutConstraint:0x60c000098bf0 UITableViewCellContentView:0x7fbb8c781b70.height == 44>"
```

最初以为是有人动了代码，检查各种约束（比如log中有提到的UITableViewCellContentView高度44），发现UI并没有变更。
从log上看，觉得应该是tableview的高度设置有问题。于是查看对应 **Controller** 相关代码。

```Objective-C
    //注册自定义TableViewCell
    [self.tableView registerNib:[UINib nibWithNibName:@"GKProjectSearchHeaderFilterView" bundle:nil] forHeaderFooterViewReuseIdentifier:GKProjectSearchHeaderFilterViewIdentifier];
    self.tableView.separatorStyle = UITableViewCellSeparatorStyleNone;
    self.tableView.estimatedRowHeight = 60.0f;
```

### rowHeight

代码没有问题，无奈点开UITableView文件进行查看，发现还有个 **rowHeight** 属性，印象中之前如果使用 **estimatedRowHeight** 时不设置 **rowHeight** 也没有影响。考虑到log信息，决定添加上该属性设置

```Objective-C
    self.tableView.rowHeight = UITableViewAutomaticDimension;
```

界面竟然正常了！！！！

如果不设置 **rowHeight** 属性时，实现 **UITableViewDelegate** 协议的方法也可以，但是从性能方面考虑，不建议使用协议的方式。

```Objective-C
-(CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath{
    return UITableViewAutomaticDimension;
}
```


### 结论

应该是Xcode9在处理UITableView时，对属性 **rowHeight** 的支持出现了问题。以下摘自UIKit中源码。

```Objective-C
@property (nonatomic) CGFloat rowHeight;             // default is UITableViewAutomaticDimension
```

注释中提到该属性的默认值就是UITableViewAutomaticDimension。



