## Maven生命周期

一个完整的项目构建过程通常包括清理、编译、测试、打包、集成测试、验证、部署等步骤，Maven从中抽取了一套完善的、易扩展的生命周期。Maven的生命周期是抽象的，其中的具体任务都交由插件来完成。Maven为大多数构建任务编写并绑定了默认的插件，如针对编译的插件：maven-compiler-plugin。用户也可自行配置或编写插件。

Maven定义了三套生命周期：clean、default、site，每个生命周期都包含了一些阶段（phase）。三套生命周期相互独立，但各个生命周期中的phase却是有顺序的，且后面的phase依赖于前面的phase。执行某个phase时，其前面的phase会依顺序执行，但不会触发另外两套生命周期中的任何phase。

1. **clean生命周期**

	* pre-clean ：执行清理前的工作；
	* clean ：清理上一次构建生成的所有文件；
	* post-clean ：执行清理后的工作；

* **default生命周期**

	default生命周期是最核心的，它包含了构建项目时真正需要执行的所有步骤。

    * validate
    * initialize
    * generate-sources
    * process-sources
    * generate-resources
    * process-resources ：复制和处理资源文件到target目录，准备打包；
    * compile ：编译项目的源代码；
    * process-classes
    * generate-test-sources
    * process-test-sources
    * generate-test-resources
    * process-test-resources
    * test-compile ：编译测试源代码；
    * process-test-classes
    * test ：运行测试代码；
    * prepare-package
    * package ：打包成jar或者war或者其他格式的分发包；
    * pre-integration-test
    * integration-test
    * post-integration-test
    * verify
    * install ：将打好的包安装到本地仓库，供其他项目使用；
    * deploy ：将打好的包安装到远程仓库，供其他项目使用；

* **site生命周期**

    * pre-site
    * site ：生成项目的站点文档；
    * post-site
    * site-deploy ：发布生成的站点文档
