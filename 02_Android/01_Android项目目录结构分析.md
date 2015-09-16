##Eclipse项目

项目结构图：<br/>
![](https://github.com/liguoying521/GithubOSCImages/raw/master/001_AndroidEclipseProject.png)

* src

	>放置项目源码。

* gen

	>程序自动生成的代码，不能手动修改，里面有两个文件：
	>
	>* BuildConfig.java : 调试（Debug）时使用，一般不管。
	>* R.java : 

* Android 4.1.2

	>Android 类库

* Android Private Libraries

	>该目录出现在ADT16以后的版本中，是ADT第三方库新的引用方式，当我们需要引用第三方库的时候，只需要将该库拷贝到libs文件夹中，ADT就会自动完成对该库的引用(如本例中android-support-v4.jar)

* assets

	>存放那些不需要被编译、压缩优化的资源，比如视频/音频等资源。

* libs

	>放置的是第三方jar包，但最新版本的SDK会将这些第三方包以Android Private Library的形式展现。

* res

	>资源文件的放置位置。<br/>
	>
	>res下面还有几个比较重要和常见的文件夹：

	>* `drawable-ldpi` / `drawable-mdpi` / `drawable-hdpi`：<br/>分别放置低、中、高等分辨率的图片，程序会自动根据运行设备的分辨率更换匹配大小的图片。
	>* `layout`：放置布局文件。
	>* `values`： 放置资源文件。
		* `array.xml` : 定义数组
		* `colors.xml` : <br/>
			定义color与颜色、drawable与颜色对应的字符串值(color string values)。<br/>使用Resource.getDrawable()和Resources.getColor()分别获得这些资源。
		* `dimens.xml` : <br/>
			定义尺寸值(dimension value)。<br/>使用Resources.getDimension()获得这些资源。
		* `strings.xml` : <br/>
			定义字符串(string)值。<br/>使用Resources.getString()或者Resources.getText()获取这些资源。getText()会保留在UI字符串上应用的丰富的文本样式。
		* `styles.xml` : 定义样式(style)对象。
	>* `raw` : 与 assets 目录相同，存放那些不需要被编译、压缩优化的资源。

	**assets 与 res/raw 的异同点：**

	>**相同点**：
	>
	>* 两者都用来存放那些不需要被编译、压缩优化的资源。

	>**不同点**：

	>* res/raw 里的资源文件会通过R.java来生成ID，而assets里的资源文件则不会。
	>* res/raw 下不可以有目录结构，而 assets 则可以有目录结构，也就是assets目录下可以再建立文件夹。
	>* 读取资源的方式不同：
		1. 读取 res/raw 下的文件资源，通过以下方式获取输入流来进行写操作:<br/>
			`super.getResources().openRawResource(id)`
		2. 读取assets下的文件资源，通过以下方式获取输入流来进行写操作:<br/>
			`AssetManager assetManager = super.getResources().getAssets();`<br/>
			`assetManager.open(fileName);`

	**关于 Android 中的资源访问**

	>在很多时候，我们需要访问android中的资源文件，这些资源文件主要分为两类，<br/>
	>
	>* 一种出于assets目录下，称为原生文件，这类文件在被打包成apk文件时是不会进行压缩的
	>* 另一类则是res下的文件，这类文件在打包成apk文件时，会进行小内存优化的哦。

	>两种不同类型的文件，对应着不同的访问模式。<br/>
	>
	>Android中有一个专门的类来处理应用对asset文件的访问，这个类就是AssetManager。其内有一个重载的open(...)方法可以根据用户提供的文件名，返回一个InputStream对象供用户使用。

	>我们在activity中可以通过如下方法访问res目录下的资源，InputStream inputStream = Resources.openRawResource(int id);

	**总结一下res目录的特点**

	>会在R.java文件下生成标记，这里的资源会在运行打包操作的时候判断哪些被使用到了，没有被使用到的文件资源是不会打包到安装包中的。 <br/>
	>在res文件夹下其实还可以定义一下目录： 
	>
	>* res/anim : 这里存放的是动画资源。 
	>* res/xml : 可以在Activity中使用getResource().getXML()读取这里的资源文件 
	>* res/raw : 该目录下的文件可以直接复制到设备上，编译软件时，这里的数据不需要编译，直接加入到程序安装包中，使用方法是getResource().OpenRawResources(ID),其中参数ID的形式是R.raw.XXX.

* manifest.xml

	>有关项目的全局配置