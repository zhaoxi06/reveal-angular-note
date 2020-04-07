1、一个完整的angular应用主要由六个重要部分构成，分别是：组件、模板、指令、服务、依赖注入、路由。
![核心模块关联图]('./img/core-module.jpg')

* 组成组件包含两个要素：模板和组件类。与用户直接交互的是模板视图，组件类用以维护组件的数据模型及功能逻辑。
* 路由的功能是控制组件的创建和销毁。
* 指令作用于模板，最重要的作用是增强模板特性，间接扩展了模板的语法。
* 服务是封装若干功能逻辑的单元。服务可以通过依赖注入机制引入到组件内部，作为组件功能的扩展。

2、组件数据流动
&emsp;组件之间层层嵌套，自上而下构成组件树，最外层的组件为根组件。树状结构的组件关系&意味着每个组件并不是孤立的存在，父子组件之间存在着双向的数据流动。
&emsp;模板和组件类之间的数据交互称为数据绑定，包含属性绑定和事件绑定。
&emsp;属性绑定和事件绑定既可用于父子组件的数据传递，也可用于组件数据和模板之间的数据传递。
![数据流动]('./img/data-flow.jpg')

3、模板
angular模板基于html，但angular模板不止于此，详细内容在模板章节介绍。

4、指令
* 指令与模板密切相关，指令可以改变DOM样式，或是改变布局。组件也是指令的一种，组件与指令的区别在于组件有单独的模板，即DOM元素。
* 指令作用在已有的DOM元素上，一般指令分为两种：结构指令和属性指令。
* 结构指令能够添加、删除或修改DOM，从而改变布局，如ngIf。
* 属性指令用来改变元素的外观或行为，如ngStyle。

5、服务
服务是封装单一功能的单元，类似于工具库，常被引用于组件内部，作为组件的功能扩展。

6、依赖注入
&emsp;依赖注入是一种帮助开发者管理模块依赖的设计模式。通过依赖注入机制，服务等模块可以被引入到任何一个组件（或模块，或其他服务）中，而开发者无需关注这些模块是如何被初始化。
&emsp;providers属性是依赖注入操作的关键，它会创建一个注入器对象，并新建该服务的实例存储到这个注入器里。
&emsp;服务的每一次注入（也就是providers声明），该服务都会被创建出新的实例。

7、路由
&emsp;angular作为一个单页应用，路由的作用是建立url路径和组件之间的对应关系，根据不同的url路径匹配出相应的组件并渲染。
&emsp;路由指令router-outlet起着类似于“插座”的作用，根据当前的url路径，匹配插入对应的组件节点，实现了主体内容（页面）的刷新。路由指令还支持多重嵌套，实现子路由功能。

8、应用模块
&emsp;将angular应用中的六个主要组成部分：组件、模板、指令、服务、路由、依赖注入组织起来，构成一个完整的功能单元甚至是完整应用。
&emsp;每个angular应用至少有一个模块，一般需要有一个模块作为应用的入口，这个入口模块称为根模块，通过引导运行根模块来启动angular应用。

angular已经封装了不少常用的特性模块，如：
* ApplicationModule：封装一些启动相关的工具；
* CommonModule：封装一些常用的内置指令和内置管道等；
* BrowserModule：封装在浏览器平台运行时的一些工具库，同时将CommonModule和ApplicationModule打包导出，所以通常在使用时引入BrowserModule就可以了；
* FormsModule和ReactiveFormsModule：封装表单相关的组件指令等；
* RouterModule：封装路由相关的组件指令等；
* HttpModule；封装网络请求相关的服务等。

&emsp;angular应用的启动过程：angular在运行前，都需要经过编译器对模块、组件等进行编译，编译完成后才开始启动应用并渲染界面。
&emsp;angular引导根模块启动，引导方式有两种：动态引导和静态引导。
&emsp;动态引导和静态引导的区别就在编译的时机不同，动态引导是将所有代码加载到浏览器后，在浏览器进行编译；而静态引导是将编译过程前置到开发时的工程打包阶段，加载到浏览器的将是编译后的代码。

动态引导示例代码：
```
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { RootModule } from './app/root.module';

platformBrowserDynamic().bootstrapModule(RootModule);
```
静态引导示例代码：
```
import { platformBrowser } from '@angular/platform-browser';
//AppModuleNgFactory是AppModule编译后生成的模块
import { AppModuleNgFactory } from './app.module.ngfactory';

platformBrowser().bootstrapModuleFactory(AppModuleNgFactory);
```
静态引导由于整个应用已经被预先编译，所以编译器并不会打包到项目代码，代码包体更小，加载更快，而且也省去看浏览器编译这个步骤，因此应用启动的速度也会更快。
动态引导开发流程简单明了，适合小型项目或者大型应用的开发阶段使用，而静态引导需要在开发阶段加入预编译流程，稍显复杂但性能提升明显，推荐使用。