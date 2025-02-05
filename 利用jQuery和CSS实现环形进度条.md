PK
    ���H�{���1  �1  * 3 利用jQuery和CSS实现环形进度条.mdup/ 4�>�利用jQuery和CSS实现环形进度条.md## 利用jQuery和CSS实现环形进度条
最近项目里遇到一个有意思的效果，那就是圆形进度条，类似于这样的：
![enter image description here](http://cdn.w3cplus.com/cdn/farfuture/F-bBw6E74hIicbua5lCYr_GeKfy5VK1FsOwPEFeWzdw/mtime:1421035299/sites/default/files/styles/print_image/public/blogs/2014/1404/jquer-css3-progressbar-1.jpg)
#### 方法一：jQuery + CSS3
##### 实现原理

原理非常的简单，在这个方案中，最主要使用了CSS3的transform中的rotate和CSS3的clip两个属性。用他们来实现半圆和旋转效果。

##### 半环的实现

先来看其结构。

HTML

```
<div class="pie_right">
    <div class="right"></div>
    <div class="mask"><span>0</span>%</div>
</div>

```
结构非常简单。这样的结构，大家一看就清楚。
###CSS

```
.pie_right {
    width:200px; 
    height:200px;
    position: absolute;
    top: 0;
    left: 0;
    background:#0cc;
}
.right {
    width:200px; 
    height:200px;
    background:#00aacc;
    border-radius: 50%;
    position: absolute;  
    top: 0;
    left: 0;
}
.pie_right, .right {
    clip:rect(0,auto,auto,100px);
}
.mask {
    width: 150px;
    height: 150px;
    border-radius: 50%;
    left: 25px;
    top: 25px;      
    background: #FFF;
    position: absolute;
    text-align: center;
    line-height: 150px;
    font-size: 20px;
    background: #0cc;
    /* mask 是不需要剪切的，此处只是为了让大家看到效果*/
    clip:rect(0,auto,auto,75px); }
```
实现半圆还是挺简单的，利用border-radius做出圆角，然后利用clip做出剪切效果，（clip使用方法，详见站内介绍），mask的部分是为了遮挡外面的容器，致使在视觉上产生圆环的效果：
![enter image description here](http://cdn.w3cplus.com/cdn/farfuture/urgN87OJVXlldaQq-in_qYK0uKBHVpoZG-k7p_xv63w/mtime:1421035299/sites/default/files/styles/print_image/public/blogs/2014/1404/jquer-css3-progressbar-2.jpg)
旋转的话，那更容易实现了，就是在CSS的right中加入（我没做浏览器兼容代码，请大家自行加入）：

```
.right {
    transform: rotate(30deg);
}
```
![enter image description here](http://cdn.w3cplus.com/cdn/farfuture/q0jr3LAeCEkccIPnwzoWhf-TFdBpfXq1rPXCxUr0HWc/mtime:1421035299/sites/default/files/styles/print_image/public/blogs/2014/1404/jquer-css3-progressbar-3.jpg)

这样就可以看到一个半弧旋转的效果了。

##### 整环的实现

同样道理，拼接左半边圆环，为了让我们html结构更易懂以后写js更简便，我对结构做了一下改造，并作了效果优化：

##### HTML

```
<div class="circle">
    <div class="pie_left"><div class="left"></div></div>
    <div class="pie_right"><div class="right"></div></div>
    <div class="mask"><span>0</span>%</div>
</div>
```
##### css

```
.circle {
    width: 200px;
    height: 200px;  
    position: absolute;
    border-radius: 50%;
    background: #0cc;
}
.pie_left, .pie_right {
    width: 200px; 
    height: 200px;
    position: absolute;
    top: 0;left: 0;
}
.left, .right {
    display: block;
    width:200px; 
    height:200px;
    background:#00aacc;
    border-radius: 50%;
    position: absolute;
    top: 0;
    left: 0;
    transform: rotate(30deg);
}
.pie_right, .right {
    clip:rect(0,auto,auto,100px);
}
.pie_left, .left {
    clip:rect(0,100px,auto,0);
}
.mask {
    width: 150px;
    height: 150px;
    border-radius: 50%;
    left: 25px;
    top: 25px;
    background: #FFF;
    position: absolute;
    text-align: center;
    line-height: 150px;
    font-size: 16px;
}
```
![enter image description here](http://cdn1.w3cplus.com/cdn/farfuture/uiSR1DGohX2a09mjiS2J2bpsBa91tG9YgA9gPFWwE7U/mtime:1421035299/sites/default/files/styles/print_image/public/blogs/2014/1404/jquer-css3-progressbar-4.jpg)
圆环最终效果

Ok了，基本上我们的圆环已经实现完成了，下面是加入JS效果。

首先，我们需要考虑的是，圆环的转动幅度大小，是由圆环里面数字的百分比决定的，从0%-100%，那么圆弧被分成了每份3.6°；而在180°，也就是50%进度之前，左侧的半弧是不动的，当超过50%，左边的半弧才会有转动显示。

那么，原理明确之后，其jQuery代码如下（删除CSS中的旋转效果，在JS里重写）：

```
$(function() {
    $('.circle').each(function(index, el) {
        var num = $(this).find('span').text() * 3.6;
        if (num<=180) {
            $(this).find('.right').css('transform', "rotate(" + num + "deg)");
        } else {
            $(this).find('.right').css('transform', "rotate(180deg)");
            $(this).find('.left').css('transform', "rotate(" + (num - 180) + "deg)");
        };
    });

});
```
则，改变mask里面的span 的数值，我们就会看到最终效果。
![enter image description here](http://cdn2.w3cplus.com/cdn/farfuture/svVMXQuti7VQ0PxRsYYqhHIhFNphKFP5GFrqlU7mJ2c/mtime:1421035299/sites/default/files/styles/print_image/public/blogs/2014/1404/jquer-css3-progressbar-5.jpg)
这样我们只要从后台获取当前流程的进度值，传给span，那么我们页面上就能看到这样圆环的进度效果啦。

## jQuery + 图片

实现原理

这种方法相对来说就比较简单了，但是也是挺麻烦的一种。

首先，我们需要一个非常冗长的一个图片……图片的内容，就是每1°旋转角度，就是一张图片…100张…

例如：
![enter image description here](http://cdn2.w3cplus.com/cdn/farfuture/Zq_8SebgSSSjKJUjC4u2VFNkMvXvGpGopiN_kzUMymo/mtime:1421035299/sites/default/files/styles/print_image/public/blogs/2014/1404/jquer-css3-progressbar-6.jpg)
然后让容器为这个图片为背景，理由用background-position写100个类样式做背景偏移…很累啊….进度每改变一下，就引用相对应的类。

HTML

我们类似于有这样的一个结构，当然这里面有很多个这样的结构：

```
<div class="progressbar">
    <span>0</span>%     
</div>
```
比如说我们的案例的中结构：

```
<div class="progressbar">
    <span>10</span>%        
</div>
<div class="progressbar">
    <span>20</span>%        
</div>
<div class="progressbar">
    <span>30</span>%        
</div>
<div class="progressbar">
    <span>50</span>%        
</div>
<div class="progressbar">
    <span>70</span>%        
</div>
<div class="progressbar">
    <span>90</span>%        
</div>
<div class="progressbar">
    <span>100</span>%       
</div>
```
CSS

样式是比较苦逼的事情，我们需要在每个位置修改他的background-position。也就是从0%~100%，将会有100个：

```
.progressbar {
    text-align: center;
    line-height: 44px; 
    width: 44px; 
    display: block; 
    background: url(progressbar.png); 
    height: 44px; 
    font-size: 14px; 
    margin-left:60px;
}
.progressbar-1 {background-position: 0px 0px;}
.progressbar-2 {background-position: -54px 0px;}
.progressbar-3 {background-position: -108px 0px;}
.progressbar-4 {background-position: -162px 0px;}
.progressbar-5 {background-position: -216px 0px;}
.progressbar-6 {background-position: -270px 0px;}
.progressbar-7 {background-position: -324px 0px;}
.progressbar-8 {background-position: -378px 0px;}
.progressbar-9 {background-position: -432px 0px;}
.progressbar-10 {background-position: -486px 0px;}
.progressbar-11 {background-position: -540px 0px;}
.progressbar-12 {background-position: -594px 0px;}
.progressbar-13 {background-position: -648px 0px;}
.progressbar-14 {background-position: -702px 0px;}
.progressbar-15 {background-position: -756px 0px;}
.progressbar-16 {background-position: -810px 0px;}
.progressbar-17 {background-position: -864px 0px;}
.progressbar-18 {background-position: -918px 0px;}
.progressbar-19 {background-position: -972px 0px;}
.progressbar-20 {background-position: -1026px 0px;}
.progressbar-21 {background-position: -1080px 0px;}
.progressbar-22 {background-position: -1134px 0px;}
.progressbar-23 {background-position: -1188px 0px;}
.progressbar-24 {background-position: -1242px 0px;}
.progressbar-25 {background-position: -1296px 0px;}
.progressbar-26 {background-position: -1350px 0px;}
.progressbar-27 {background-position: -1404px 0px;}
.progressbar-28 {background-position: -1458px 0px;}
.progressbar-29 {background-position: -1512px 0px;}
.progressbar-30 {background-position: -1566px 0px;}
.progressbar-31 {background-position: -1620px 0px;}
.progressbar-32 {background-position: -1674px 0px;}
.progressbar-33 {background-position: -1728px 0px;}
.progressbar-34 {background-position: -1782px 0px;}
.progressbar-35 {background-position: -1836px 0px;}
.progressbar-36 {background-position: -1890px 0px;}
.progressbar-37 {background-position: -1944px 0px;}
.progressbar-38 {background-position: -1998px 0px;}
.progressbar-39 {background-position: -2052px 0px;}
.progressbar-40 {background-position: -2106px 0px;}
.progressbar-41 {background-position: -2160px 0px;}
.progressbar-42 {background-position: -2214px 0px;}
.progressbar-43 {background-position: -2268px 0px;}
.progressbar-44 {background-position: -2322px 0px;}
.progressbar-45 {background-position: -2376px 0px;}
.progressbar-46 {background-position: -2430px 0px;}
.progressbar-47 {background-position: -2484px 0px;}
.progressbar-48 {background-position: -2538px 0px;}
.progressbar-49 {background-position: -2592px 0px;}
.progressbar-50 {background-position: -2700px 0px;}
.progressbar-51 {background-position: -2754px 0px;}
.progressbar-52 {background-position: -2808px 0px;}
.progressbar-53 {background-position: -2862px 0px;}
.progressbar-54 {background-position: -2916px 0px;}
.progressbar-55 {background-position: -2970px 0px;}
.progressbar-56 {background-position: -3024px 0px;}
.progressbar-57 {background-position: -3078px 0px;}
.progressbar-58 {background-position: -3132px 0px;}
.progressbar-59 {background-position: -3186px 0px;}
.progressbar-60 {background-position: -3240px 0px;}
.progressbar-61 {background-position: -3294px 0px;}
.progressbar-62 {background-position: -3348px 0px;}
.progressbar-63 {background-position: -3402px 0px;}
.progressbar-64 {background-position: -3456px 0px;}
.progressbar-65 {background-position: -3510px 0px;}
.progressbar-66 {background-position: -3564px 0px;}
.progressbar-67 {background-position: -3618px 0px;}
.progressbar-68 {background-position: -3672px 0px;}
.progressbar-69 {background-position: -3726px 0px;}
.progressbar-70 {background-position: -3780px 0px;}
.progressbar-71 {background-position: -3834px 0px;}
.progressbar-72 {background-position: -3888px 0px;}
.progressbar-73 {background-position: -3942px 0px;}
.progressbar-74 {background-position: -3996px 0px;}
.progressbar-75 {background-position: -4050px 0px;}
.progressbar-76 {background-position: -4104px 0px;}
.progressbar-77 {background-position: -4158px 0px;}
.progressbar-78 {background-position: -4212px 0px;}
.progressbar-79 {background-position: -4266px 0px;}
.progressbar-80 {background-position: -4320px 0px;}
.progressbar-81 {background-position: -4376px 0px;}
.progressbar-82 {background-position: -4428px 0px;}
.progressbar-83 {background-position: -4482px 0px;}
.progressbar-84 {background-position: -4536px 0px;}
.progressbar-85 {background-position: -4590px 0px;}
.progressbar-86 {background-position: -4644px 0px;}
.progressbar-87 {background-position: -4698px 0px;}
.progressbar-88 {background-position: -4752px 0px;}
.progressbar-89 {background-position: -4806px 0px;}
.progressbar-90 {background-position: -4860px 0px;}
.progressbar-91 {background-position: -4914px 0px;}
.progressbar-92 {background-position: -4968px 0px;}
.progressbar-93 {background-position: -5022px 0px;}
.progressbar-94 {background-position: -5076px 0px;}
.progressbar-95 {background-position: -5130px 0px;}
.progressbar-96 {background-position: -5184px 0px;}
.progressbar-97 {background-position: -5238px 0px;}
.progressbar-98 {background-position: -5292px 0px;}
.progressbar-99 {background-position: -5346px 0px;}
.progressbar-100 {background-position: -5400px 0px;}
```
JavaScript

当然，在这个效果中，我们也离不开JavaScript:

```
$(function() {
    $('.progressbar').each(function(index, el) {
        var num = $(this).find('span').text();
        $(this).addClass('progressbar-' + num);
    });
});
```
![enter image description here](http://cdn1.w3cplus.com/cdn/farfuture/JY3IHZfzRGJO4iVLZiYIM1TXY3Wm9l-Ebgcipz7UfuY/mtime:1421035300/sites/default/files/styles/print_image/public/blogs/2014/1404/jquer-css3-progressbar-7.jpg)
虽然这种方法虽然比较繁琐比较麻烦，但是实现出来的效果，也较纯css3的丰富，兼容性也更好。

到这里，这个案例算是完成，看大家倾向于那种效果吧，个人感觉各有利弊各有春秋。如果有更好的方法和思路，欢迎大家一起讨论。


PK 
    ���H�{���1  �1  * 3               利用jQuery和CSS实现环形进度条.mdup/ 4�>�利用jQuery和CSS实现环形进度条.mdPK      �   g2    