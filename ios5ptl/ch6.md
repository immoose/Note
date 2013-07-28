# Chapter6 Better Drawing

## drawing system (from top to bottom)
+ UIKit
+ Core Graphic (Quartz 2D)
+ Core Animation (2D & 3D)
+ Core Image (image filtering)
+ OpenGL ES

## UIKit and the View Drawing Cycle
+ hidden = YES  -> (run loop) set hidden flag & view marked as redraw

+ UIKit isn't thread safe, cannot modify view on a background thread
> _一些UIKit(例如CGLayer)的方法能够在后台线程中执行，但不能draw on main context_

+ 后台执行long-running operation

        Dispatch_queue_t bgQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
        dispatch_async(bgQueue, ^{
            [self somethingTimeConsuming];
            dispatch_async(dispatch_get_main_queue(), ^{
                [self.activity stopAnimation];
            });
        });

+ IOS在run loop中获取所有的drawing request，一次性地draw them

## View Drawing versus View Layout
+ UIView分离了layout("rearranging") of subviews和drawing("display")，这是因为layout往往比drawing更cheaper
> _UIView caches drawing onto GPU-optimized bitmaps_

+ `setNeedsDisplay`标记UIView为"dirty"，并且在下一个drawing cycle中re-draw
+ 当屏幕方向改变或者scroll的时候，UIView的subviews需要rearrange，UIKit会调用`setNeedsLayout`  
这会调用`layoutSubviews`。Override `layoutSubviews`会使rotation和scroll变得更流畅，可以只rearrange subviews而不draw them

## Custom View Drawing
+ UIView有三种方法绘制：subviews, layers, `drawRect:`
+ 2D drawing的分类(使用UIKit/Core Graphic需要注意顺序性)
  + Lines
  + Paths
  + Text
  + Images
  + Gradients

### Drawing With UIKit ###
+ `UIBezierPath`，UIKit依然缺乏对lines, gradients, shading, anti-aliasing等的支持
+ `UIRectFrame` / `UIRectFill`

        - (void)drawRect:(CGRect)rect {
          [[UIColor redColor] setFill];
          UIRectFill(CGRectMake(10, 10, 100, 100));
        }

+ `drawRect:`中提供的是特殊的view graphic context, 也有其他的context，例如PDF和bitmap context。他们使用了相同的drawing technology，但是view graphic context对draw on screen进行了优化。

### Paths ###


