# StartUp

Aplication中初始化：三方库多了凌乱，影响开启速度。

介绍：可以用于加速App启动速度的一个库。可以将所有用于初始化的ContentProvider合并成一个，从而使App的启动速度变得更快。

使用ContextProvider原理：避免显示地调用初始化接口，而是可以自动调用初始化接口，这种办法就是借助ContentProvider。

原因ContentProvider onCreate方法执行在 Application onCreate之前。进程开启时自动调用。


使用ContextProvider弊端：一个空的ContentProvider大约会占用2ms的耗时，随着ContentProvider的增加，耗时也会跟着一起增加。


生命周期顺序：

Application#attachBaseContext->ContentProvider#onCreate->Application#onCreate



依赖库：

```groovy
// jetPack Component
implementation "androidx.startup:startup-runtime:1.0.0" // startup
```

[参考](https://blog.csdn.net/guolin_blog/article/details/108026357)