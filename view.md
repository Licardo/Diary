# view相关心得

最近看了view相关的知识点，总觉得深度不够，还有一些问题也没有解答，故记录一下

##### 问题

* 如果在RecyclerView中有耗时的操作，在缓存item的时候是否会造成缓存失效？
* View动画是怎么做到不改变View实际位置，只操作View影像？
* Activity在创建之初，一边执行onCreate方法，一边绘制View(scheduleTraversals方法)，造成的结果是在onCreate、onStart、onResume无法获取View的宽高，如果都是在主线程运行，是如何做到的，如果View绘制是在子线程中执行，是在什么时机切回主线程的？

##### 知识点

* View的padding属性需要自己处理，margin属性是父布局ViewGroup处理，在自定义View时会用到
* View的宽高不只是由自己决定的，需要自己的LayoutParam和父布局的MeasureSpecMode共同决定
* 自定义View需要自己手动支持padding属性，在onDraw方法中实现；还要支持wrap_content属性，该属性在View源码中的定义是无论宽高设置了wrap_content，都会显示父布局的宽高。处理方式可以关注TextView。
* View事件的分发是非常精妙的设计，需要仔细体会。``` public void dispatchTouchEvent() { if(onInterceptTouchEvent() { super.onTouchEvent() } else { child.onTouchEvent() }) }```
* 如果ViewGroup拦截了ACTION_DOWN事件，那么ACTION_MOVE和ACTION_UP不会执行onterceptTouchEvent()方法，变量mFirstTouchTarget(好像是)起了关键作用
* 如果View拦截了ACTION_DOWN事件，那么剩余事件也不会执行onterceptTouchEvent()方法，有一个关键字影响父布局ViewGroup的判断
* mFirstTouchTarget其实是个链表，当ViewGroup消费的事件，则mFirstTouchTarget=null，如果View消费了事件mFirstTouchTarget=该View。
* View的滑动冲突是老生常谈的问题，需要有清晰的思路，同时注意上面几条知识点，可能需要重写onInterceptTouchEvent()，细节需要看任玉刚的书
* View的几种滑动方式，scrollTo/scrollBy:只能滑动View内容，不能滑动View位置，类似ScrollView；动画，属性动画和View动画；改变LayoutParam的值。
* 弹性滑动的几张方式：Scroller和postDelay、sleep来实现，思路就是把滑动分成许多很小的段，每段设置滑动时间然后延迟再滑动另一段。