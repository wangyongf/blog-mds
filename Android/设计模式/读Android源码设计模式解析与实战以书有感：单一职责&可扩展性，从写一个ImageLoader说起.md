# 读 Android 源码设计模式解析与实战以书有感：单一职责&可扩展性，从写一个 ImageLoader 说起

看完这本书前几节之后发现这种写作方式很好玩儿，想仿照这种写作风格写点东西，同时加入一些自己的思考，不喜欢迎各种喷，不服你来建个 issue 打我呀

## 需求很简单，打造一个图片加载器 ImageLoader

话说老码刚刚毕业到公司的时候还是小码，那时候小码还是个少不更事的小司机，啥都不懂，真滴是青葱岁月呀，咳咳，好像跑题了，赶紧拽回来......那会儿不是刚进公司嘛，小码刚开始熟悉项目，也没啥事儿，花老大就给小码出了一个小作业（对了，忘了说了，小码他老大姓花，没错儿，花花公子的 🌺），难度也不大，就是写一个图片加载器 ImageLoader，因为这玩意儿也是项目中必备的基础组件之一，写好了也能放到项目中直接用起来。

小码在接到了这个任务之后，心中一阵狂喜，老子终于有活儿干了，可憋死我了。小码对花老大表示，坚决完成任务，绝对不辜负党和组织的期望！话毕，小码就撸起袖子开工啦。

不就是一个小小的图片加载器嘛，老子三下五除二就能写一个出来，一定让花老大大惊失色，小码心里暗喜。小码从中午吃完饭接到任务开始一直写到下午三点，在经历了一遍又一遍的修改润色之后，觉得这个 ImageLoader 已经差不多可以见人了，准备和花老大一起 Review 代码了。如下是小码写的代码：

```java
/**
 * 一个简单的图片加载器
 *
 * @author scottwang1996@qq.com
 * @since 2018/4/16.
 */
public class ImageLoader {

    //图片缓存
    LruCache<String, Bitmap> imageCache;
    ExecutorService executorService = Executors.newFixedThreadPool(Runtime.getRuntime().availableProcessors());

    public ImageLoader() {
        initImageCache();
    }

    private void initImageCache() {
        //计算最大可用内存
        int maxMemory = (int) (Runtime.getRuntime().maxMemory() / 1024);
        //使用最大可用内存的1/4作为图片缓存的最大大小
        int maxCacheSize = maxMemory / 8;
        imageCache = new LruCache<String, Bitmap>(maxCacheSize) {
            @Override
            protected int sizeOf(String key, Bitmap value) {
                return value.getAllocationByteCount() / 1024;
            }
        };
    }

    public void displayImage(final String url, final ImageView imageView) {
        //标记一下imageview正在加载的图片的url
        imageView.setTag(url);
        executorService.submit(new Runnable() {
            @Override
            public void run() {
                if (imageView.getTag() == null || !imageView.getTag().equals(url)) {
                    return;
                }
                Bitmap cacheBitmap = imageCache.get(url);
                Bitmap bitmap = cacheBitmap == null ? downloadImage(url) : cacheBitmap;
                if (imageView.getTag() != null && imageView.getTag().equals(url)) {
                    imageView.setImageBitmap(bitmap);
                }
                imageCache.put(url, bitmap);
            }
        });
    }

    public Bitmap downloadImage(String imageUrl) {
        Bitmap bitmap = null;
        try {
            URL url = new URL(imageUrl);
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            bitmap = BitmapFactory.decodeStream(conn.getInputStream());
            conn.disconnect();
        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return bitmap;
    }
}
```

## Code Review 第一弹

花老大和小码开始一起 Review 了。花老大过了一下小码的代码，然后对着小码笑了笑。小码见状，还以为花老大要称赞自己，心中暗暗自喜起来。

然鹅，花老大说的是，小码啊，你这个 ImageLoader 写得大有问题哦，耦合也太严重了，咱就先不说你这 ImageLoader 功能太简单的问题了。你现在写的 ImageLoader 有图片加载和图片缓存的功能，单看你这所有功能都放在一个类就可以看出来，这样写的可扩展性和灵活性太差啦。你想想看，随着我需求的越来越多，你如何来保证你每次的改动都最小化？如何保证为了实现各种需求时的开发效率？这些都是需要在设计之初考虑的，如果设计有问题，那么可能改着改着代码就变成一坨浆糊了，维护到后期甚至需要推翻重造！太可怕了，所以在写代码之前，得先想清楚，先设计好。

花老大又补充道，说了这么多，你可能听得没什么感觉，甚至会觉得迷迷糊糊的，其实归根到底就想说一句话：你把 ImageLoader 的功能想清楚咯，然后把各个功能模块拆分出来，至少先实现单一职责吧。

小码听完花老板的一顿教诲，瞬间感觉自己菜极了，不过花老板说的确实很有道理，一套一套的，虽然不太听得懂，但至少最后那个`单一职责`算是明白了，不就是得把`图片加载`、`图片下载`、`图片缓存`这三块儿给分开么，知道啦啦啦啦啦啦。然后，小码就和花老大说了下自己重构的思路，花老大表示了赞同，之后小码就又回到了自己的工位开始重构代码了。

## 重构第一波：单一职责

小码是个聪明人，在花老大的点拨之下迅速开窍。回到工位之后，小码打开百度搜索（没错儿，小码用的还是百度搜索），搜索了一些关于`单一职责`的资料，这些资料大体讲的就是每个功能模块的功能都应该是单一的，尽量减少耦合，这样也方便以后的修改和扩展。

查完资料之后，小码对自己的图片加载器 ImageLoader 大致规划了一下，得到了如下这张结构图：

![ImageLoader结构图](http://on-img.com/chart_image/5ad61d01e4b0b74a6de0b3fa.png)

大致梳理了一下思路之后，小码开始了他的第一波重构之旅（然鹅，他并不知道等着他的是多少波重构，23333）。

......

时间过得很快，几个小时过去了，晚上八点左右，小码重构完了（画外音：没错，小码公司九点下班）。于是乎，他又屁颠屁颠儿地跑到师傅那里求喷求指教了。下面是他重构后的代码，遵照单一职责原则：

```java

```

## Code Review 第二弹

## 后记

这篇文章是在看了 Android 源码设计模式解析与实战的第一章第一节的一个例子之后写的，毕竟做 Android 也快 1 年了，大概半年前摸过这本书，现在再看又有些不同的感受，觉得这个例子写的很好，于是便有了这篇文章。本文算是对该例子的一点补充和延伸，基于我个人的理解，因为觉得原例子写得很好，讲的是如何优化代码，实现单一职责，但是个人觉得意犹未尽，总觉得可以继续写点儿什么，于是就用[MWeb](http://zh.mweb.im/)记下了心中所想。如果文章有任何问题，请直接在仓库提 issue 吧，欢迎之至 😜😜😜

## 参考资料

* Android 源码设计模式解析与实战
