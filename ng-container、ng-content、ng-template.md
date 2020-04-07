转载自：[ng-container、ng-content、ng-template](https://www.jianshu.com/p/0f5332f2bbf8)

## ng-container
ng-container可以直接包裹任何元素，包括文本，但本身不会生成元素标签，也不会影响页面样式和布局。包裹的内容，如不通过其他指令控制，会直接渲染到页面中。
我们知道ngFor和ngIf指令不能同时作用在一个元素上，此时，在不想增加额外的标签的情况下达到同样的效果，就只用ng-container。
```
<ul>
    <ng-container *ngFor="let list of lists; let index=index">
        <li *ngIf="index%2 === 0">{{list}}</li>
    </ng-container>
</ul>
```

## ng-content
ng-content是内容映射指令(也叫内容嵌入)，内容映射指的是在组件中嵌入模板代码，方便定制可复用的组件，很好地扩充组件的功能，方便代码的复用。可以简单地理解为，ng-content就是一个占位符，类似于路由出口router-outlet，之后会把相应的内容放到这个位置上来。
### select属性
ng-content提供select属性，用于选择投影的内容（组件或HTML标签），如果没有设置select属性，则所有的内容都投影过来。select属性选择器与css选择器作用是类似的，可以匹配标签选择器、类选择器、属性选择器。
注意，select的值不能设置为动态的。
```
<div>
    // 匹配第一个header标签
    <ng-content select="header"><ng-content>
    // 匹配class为class-select的元素
    <ng-content select=".class-select"></ng-content>
    // 匹配name属性为footer的元素
    <ng-content select="[name=footer]"></ng-content>
</div>
```
### ngProjectAs
用select匹配元素是作用在直接子节点上，非直接子节点则匹配不到，这是就可以用ngProjectAs。
```
// app-content-parent
<ng-content select="app-content-child"></ng-content>

//some other component
<app-content-parent>
    //<app-content-child>没有投影到ng-content
    <ng-container>
        <app-content-child>子组件</app-content-child>
    </ng-container>

    //<app-content-child>能投影到ng-content
    <ng-container ngProjectAs="app-content-child">
        <app-content-child>子组件</app-content-child>
    </ng-container>
</app-content-parent>
```
### @ContentChild和@ContentChildren
@ContentChild和@ContentChildren能获取ng-content里面包含的内容。

## ng-template
ng-template用于定义模板渲染HTML。定义的模板不会直接显示出来。我们可以通过以下几个方法把它显示出来。
* 借助结构指令来显示，如*ngIf
```
<div *ngIf="condition else elseTemplate">condition is true</div>
<ng-template #elseTemplate>
    <div>condition is false</div>
</ng-template>
```
* 通过TemplateRef、ViewContainerRef来显示。TemplateRef对应ng-template的引用，ViewContainerRef则是view容器的引用。
```
import {AfterViewInit, Component, TemplateRef, ViewChild, ViewContainerRef} from '@angular/core';

@Component({
    selector: 'app-template-section',
    template: `
        <ng-template #tpl>
            Hello, ng-template!
        </ng-template>
    `,
    styleUrls: ['./template-section.component.less']
})
export class TemplateSectionComponent implements AfterViewInit {
    @ViewChild('tpl')
    tplRef: TemplateRef<any>;

    constructor(private vcRef: ViewContainerRef) {
    }

    ngAfterViewInit() {
        // 这样tplRef对应的ng-template内容就显示出来了
        this.vcRef.createEmbeddedView(this.tplRef);
    }
}
```
* 通过NgTemplateOutlet指令来显示已有的ng-template对应的视图。NgTemplateOutlet指令基于已有的TemplateRef对象，插入对应的内嵌视图。同时我们还可以通过ngTemplateOutletContext属性来设置ng-template的上下文对象。绑定的上下文应该是一个对象，ng-template中通过let语法来声明绑定上下文对象属性名。
```
import {Component, Input, TemplateRef} from '@angular/core';

@Component({
    selector: 'app-template-input',
    template: `
        <!-- 没有传递参数的时候就使用defaultTemplate里面的布局 -->
        <ng-template #defaultTemplate>
            <div>咱们没有传递参数</div>
        </ng-template>
        <ng-container *ngTemplateOutlet="inputTemplate ? inputTemplate: defaultTemplate"></ng-container>

    `,
    styleUrls: ['./template-input.component.less']
})
export class TemplateInputComponent {

    /**
     * 模板作为参数
     */
    @Input()
    inputTemplate: TemplateRef<any>;


}


// 使用的时候的代码

<!-- 我们定义一个组件，把ng-template的内容作为参数传递进去 -->
<ng-template #paramTemplate>
    <div>我是参数</div>
</ng-template>
<app-template-input [inputTemplate]="paramTemplate"></app-template-input>
```