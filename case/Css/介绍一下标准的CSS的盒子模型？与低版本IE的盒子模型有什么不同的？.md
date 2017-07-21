# 盒子模型
在网页中，一个元素占有空间的大小由几个部分构成，其中包括元素的内容（content），元素的内边距（padding），元素的边框（border），元素的外边距（margin）四个部分。这四个部分占有的空间中，有的部分可以显示相应的内容，而有的部分只用来分隔相邻的区域或区域。4个部分一起构成了css中元素的盒模型。  （IE和其他浏览器中的盒子有兼容性）

## 标准W3C盒子模型和IE盒子模型CSS布局经典盒子模型(转)

盒子模型是css中一个重要的概念，理解了盒子模型才能更好的排版。其实盒子模型有两种，分别是 ie 盒子模型和标准 w3c 盒子模型。他们对盒子模型的解释各不相同，先来看看我们熟知的标准盒子模型：

![](http://images.cnblogs.com/cnblogs_com/cchyao/%E6%A0%87%E5%87%86W3C%E7%9B%92%E5%AD%90%E6%A8%A1%E5%9E%8B%E5%92%8CIE%E7%9B%92%E5%AD%90%E6%A8%A1%E5%9E%8BCSS%E5%B8%83%E5%B1%80%E7%BB%8F%E5%85%B8%E7%9B%92%E5%AD%90%E6%A8%A1%E5%9E%8B/1.JPG)

　从上图可以看到标准 w3c 盒子模型的范围包括 margin、border、padding、content，并且 content 部分不包含其他部分。

## IE盒子模型

ie 盒子模型的 content 部分包含了 border 和 pading。

![](http://images.cnblogs.com/cnblogs_com/cchyao/%E6%A0%87%E5%87%86W3C%E7%9B%92%E5%AD%90%E6%A8%A1%E5%9E%8B%E5%92%8CIE%E7%9B%92%E5%AD%90%E6%A8%A1%E5%9E%8BCSS%E5%B8%83%E5%B1%80%E7%BB%8F%E5%85%B8%E7%9B%92%E5%AD%90%E6%A8%A1%E5%9E%8B/2.JPG)

 　例：一个盒子的 margin 为 20px，border 为 1px，padding 为 10px，content 的宽为 200px、高为 50px，假如用标准 w3c 盒子模型解释，那么这个盒子需要占据的位置为：宽 20*2+1*2+10*2+200=262px、高 20*2+1*2*10*2+50=112px，盒子的实际大小为：宽 1*2+10*2+200=222px、高 1*2+10*2+50=72px；假如用ie 盒子模型，那么这个盒子需要占据的位置为：宽 20*2+200=240px、高 20*2+50=70px，盒子的实际大小为：宽 200px、高 50px。

　那应该选择哪中盒子模型呢？当然是“标准 w3c 盒子模型”了。怎么样才算是选择了“标准 w3c 盒子模型”呢？很简单，就是在网页的顶部加上 doctype 声明。假如不加 doctype 声明，那么各个浏览器会根据自己的行为去理解网页，即 ie 浏览器会采用 ie 盒子模型去解释你的盒子，而 ff 会采用标准 w3c 盒子模型解释你的盒子，所以网页在不同的浏览器中就显示的不一样了。反之，假如加上了 doctype 声明，那么所有浏览器都会采用标准 w3c 盒子模型去解释你的盒子，网页就能在各个浏览器中显示一致了。

解决IE8及更早版本不兼容问题可以在HTML页面声明 <!DOCTYPE html>即可。

	 <style>
        .outdiv{
            position:relative;
            left:40px;
            top:40px;
            width:200px;
            height:200px;
            padding:10px;
            background:red;

        }
        .innerdiv{
            border:3px solid blue;
            padding:4px;
            width:100%;
            height:100%;
            background:green;
            -webkit-box-sizing: border-box;
            -moz-box-sizing: border-box;
            box-sizing: border-box;
        }
    </style>


	<div class="outdiv">
    	<div class="innerdiv"></div>
	</div>