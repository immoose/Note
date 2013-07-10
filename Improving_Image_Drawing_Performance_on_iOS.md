#Improving Image Drawing Performance on iOS
> ԭ������:http://developer.apple.com/library/ios/#qa/qa1708/_index.html

+ ����Ƶ����ʹ�� `-drawrect`

+ ���������image drawing�����⣬���п���ʹ�� `-drawRect:` ��ʹ���� `CGContextDrawImage` �� `UIImage/-drawInRect:`

+ `UIView`����Ϊһ��������Ԫ�أ���������һ��`redraw`�������Ǿֲ���`-setNeedsDisplayInRect:`����`-setNeedsDisplay:`������`UIView`�ᱻˢ��

+ ÿ��`UIView`������һ��`CALayer`��ֻҪ`CALayer`������view�Ĳ㼶�ṹ�У���Ϊlayer content��image�ͻᱣ�����ڴ��
���ԣ�������Ĳ����������ƶ�����ת�����ţ�������Ҫredraw��  
���ԣ�**��õ�animating image�ķ�����Ϊimage����view����layer����ʹ��`CoreAnimation`������**

> ���ӣ���Ҫ��һ����z����ת�Ķ���  
> һ�ַ����ǣ�ʹ��`CGContextRotateCTM`����ת�仯������ÿһ֡��`UIView`��`-drawRect:`���ػ�`CGContextDrawImage`�����ַ��������Ǻ�Ч�ʡ�  
> һ�ָ���Ч�ķ���������`CALayer`��`contents`��ʹ��layer��`transform`����ȥ���ƶ�����  
> ����ϸ�ڣ�����[Core Animation Programming Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html)
```objective-c
imageView.image = image;
- (void)rotateView:(UIView *)view toAngle:(float)angle 
{    
     [UIView beginAnimations:nil context:nil];
     [view setTransform:CGAffineTransformMakeRotation(angle)];
     [UIView commitAnimations]; 
}
```

+ �����Ҫ��һ��image�Ĳ�ͬС����в����������ڲ�ͬ�Ķ���layer�в�����

+ ��һЩ������޷�ʹ��`CALayer`��`Core Animation`�ģ���Ҫ��סʹ��`CGContextDrawImage`��`UIImage/-drawInRect:`�Ứ������ʱ�����ڽ�ѹ�����ز������̣���Ϊ���image�ǲ���cache��layer tree�С�

+ ʹimage�Ĵ�С��display�Ĵ�С�ߴ���ͬ�����������ܡ�

+ �ܹ�ͨ����image drawing into a bitmap context�ķ���cache���image��
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