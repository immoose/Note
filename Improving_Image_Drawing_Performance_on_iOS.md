#Improving Image Drawing Performance on iOS
> 原文链接:http://developer.apple.com/library/ios/#qa/qa1708/_index.html

+ 避免频繁地使用 `-drawrect`

+ 如果遇到了image drawing的问题，很有可能使在 `-drawRect:` 中使用了 `CGContextDrawImage` 或 `UIImage/-drawInRect:`

+ `UIView`被视为一个单独的元素，当请求了一个`redraw`，无论是局部的`-setNeedsDisplayInRect:`还是`-setNeedsDisplay:`，整个`UIView`会被刷新

+ 每个`UIView`背后有一个`CALayer`，只要`CALayer`保存在view的层级结构中，作为layer content的image就会保存在内存里。
所以，大多数的操作，例如移动、旋转、缩放，并不需要redraw。  
所以，**最好的animating image的方法是为image建立view或者layer，并使用`CoreAnimation`做动画**

> 例子：需要画一个按z轴旋转的动画  
> 一种方法是，使用`CGContextRotateCTM`作旋转变化，并且每一帧在`UIView`的`-drawRect:`中重画`CGContextDrawImage`。这种方法并不是很效率。  
> 一种更有效的方法是设置`CALayer`的`contents`，使用layer的`transform`属性去绘制动画。  
> 具体细节，查阅[Core Animation Programming Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html)
```objective-c
imageView.image = image;
- (void)rotateView:(UIView *)view toAngle:(float)angle 
{    
     [UIView beginAnimations:nil context:nil];
     [view setTransform:CGAffineTransformMakeRotation(angle)];
     [UIView commitAnimations]; 
}
```

+ 如果需要对一个image的不同小块进行操作，必须在不同的独立layer中操作。

+ 有一些情况是无法使用`CALayer`和`Core Animation`的，但要记住使用`CGContextDrawImage`或`UIImage/-drawInRect:`会花大量的时间用于解压缩和重采样过程，因为这个image是不会cache到layer tree中。

+ 使image的大小和display的大小尺寸相同可以提升性能。

+ 能够通过把image drawing into a bitmap context的方法cache这个image。
```objective-c
- (UIImage*)resizeImage:(UIImage*)image toWidth:(NSInteger)width height:(NSInteger)height
{
	 // Create a graphics context with the target size
     // On iOS 4 and later, use UIGraphicsBeginImageContextWithOptions to take the scale into consideration
     // On iOS prior to 4, fall back to use UIGraphicsBeginImageContext
     CGSize size = CGSizeMake(width, height);
     if (NULL != UIGraphicsBeginImageContextWithOptions)
         UIGraphicsBeginImageContextWithOptions(size, NO, 0);
     else
         UIGraphicsBeginImageContext(size);

     CGContextRef context = UIGraphicsGetCurrentContext();

     // Flip the context because UIKit coordinate system is upside down to Quartz coordinate system
     CGContextTranslateCTM(context, 0.0, height);
     CGContextScaleCTM(context, 1.0, -1.0);

     // Draw the original image to the context
     CGContextSetBlendMode(context, kCGBlendModeCopy);
     CGContextDrawImage(context, CGRectMake(0.0, 0.0, width, height), image.CGImage);

     // Retrieve the UIImage from the current context
     UIImage *imageOut = UIGraphicsGetImageFromCurrentImageContext();

     UIGraphicsEndImageContext();

     return imageOut;
}
```