---
title: "Angular学习日记"
date: 2017-04-11
categories: Angular
description: 基本完成了项目从AngularJs升级到Angular2。在升级过程中又一些体会，记录一下。
tags: 
- Angular
- JavaScript
---

## Angular学习笔记  
升级Angular已经有一段时间了，从最开始的各种不习惯，重新学习指令，了解新路由，到现在可以正常使用，总算积累了一些知识，现在做个简单的小结，以后学习过程中也能时常回顾。  
#### 组件  
自从组件这个概念随着react的大火被大家普遍接受以来，基本所有主流框架都采用了组件这个概念。而组件，正是Angular的核心理念。  
>Angular的核心概念是组件，模块化机制NgModule是为组件化服务的，实际上所有其它机制都是围绕组件化而来的。只有从组件化这个角度才能把握Angular的精神内核。  

组件通常都是由模版和业务逻辑组成，这里我们随便写一个简单的组件。  

```
// hello.component.ts 
import { Component } from '@angular/core';

@Component({              
    selector: 'hello',
    template: '<p> {{greeting}} </p>',
    styles: [`p { color: red;}`]
})
export class HelloComponent{
    private greeting: string;
    constructor(){
        this.greeting = 'Hello,world!';
    }
}

// 使用
<hello></hello>

// 渲染结果
<hello>
    <p> Hello,world! </p> 
</hello> 
```

定义类HelloComponent的时候，加上装饰器@Component（Typescript语法），标明类是组件类。selector属性说明了该组件对外的使用标记，template就是组件的模版，styles是组件的样式。而HelloComponent中定义的就是该组件的业务逻辑了。

如果模版内容太多，可以单独写在一个html文件中，用templateUrl属性引入；同理，样式文件用styleUrls引入。  

#### 模块  
与AngularJs相同，Angular很重要的概念之一仍然是模块。可以说，Angular整个框架就是由许许多多的模块组成。Angular自带了许多非常常用的模块以减轻开发者的开发难度。我们开发的一个完整单元也是一个模块。  
模块的组成由组件，服务，指令，管道等等组成。下面是一个简单的模块。

```
@NgModuel({
    declarations: [],   // 用到的组件，指令，管道
    providers: [],      // 依赖注入服务 
    imports: [],        // 导入需要的模块
    exports: [],        // 导出的模块，跨模块交流
    entryComponents: [] // 需提前编译好的模块
    bootstrap: []       // 设置根组件
    
})
export class AppModule { }
```
所有用到的组件，指令，管道，模块都需要事先在模块中声明好，才能在具体组件中使用。服务可以在模块，组件，指令中的providers声明，也可以直接在运行时提供。  

#### 指令
其实Angular的指令与AngularJs的指令差不多，下面就是一个简单的指令。

```
import { Directive,Input,ElementRef } from '@angular/core';

@Directive({
    selector: 'hello'
})
export class HelloDirective { 
    @Input() name: string;

    constructor(private el: ElementRef) {}

    public ngOnInit(): void {
        
        this.el.nativeElement.innerText = `hello ${this.name}!`;
    }
}

// 使用组件指令
<hello name="Liting"></hello>

// 渲染结果
<hello> hello, Liting! </hello>
```

我管这个叫功能指令（当然官方没有这个说法），与属性指令、结构指令（官方定义）区分开来。  
属性指令就是仅仅改变元素的样式或者行为。  

```
import { Directive, ElementRef, Renderer2 } from '@angular/core';  

@Directive({   
  selector: '[highLight]'  
})  

export class HighLightDirective {    
  constructor(private el: ElementRef, private renderer2: Renderer2) { }    
  
  ngAfterViewInit() {   
    this.renderer2.addClass(this.el.nativeElement, 'highlight');   
  }  
}

// 使用属性指令
<p highLight> 这一段会高亮显示 </p>
```

结构指令就是模板中提到的*ngIf，ngFor等指令，它会修改DOM结构。举个例子，重写ngIf：
```
import { Directive, Input, ViewContainerRef, TemplateRef } from '@angular/core';   

@Directive({   
  selector: '[myIf]'  
})  

export class MyIfDirective {    

  constructor(private templateRef: TemplateRef<any>,   
      private viewContainer: ViewContainerRef) { }   

  @Input() set appMyIf(condition: boolean) {   
    if (condition) {   
      this.viewContainer.createEmbeddedView(this.templateRef);   
    } else {   
      this.viewContainer.clear();   
    }   
  }  
}  

// 使用结构指令
<p *myIf="false"> 这一段不会显示 </p>
```


#### 服务  

可以简单地认为服务是一个功能模块，重要在于它是单例对象，并且可以注入到其他的地方使用。在Angular中定义一个服务很简单，主要在类之前加上@Injectable装饰器的功能。  

```
import { Injectable } from '@angular/core';  

@Injectable() 
export class Service {
    counter: number = 0;
    
    getData(){
        return this.counter++;
    }
}
```
然后在模块的providers中声明：  

```
import { Service } from './service';

@NgModule({
    imports: [
        ...
    ],
    declarations: [
        ...
    ],
    providers: [ Service ],  // 注入服务
    bootstrap: [...]
})
export class AppModule {
}
```
使用的时候需要在构造器中建立关联：

```
import { Component } from '@angular/core'; 
import { Service } from './service';
...

@Component({
    selector: 'my-app',
    templateUrl: './app.component.html',
    styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(public service: Service) {
        // this.service被成功注入
        // 相当于 this.service = new Service(); 
        // 然后可以调用服务
        this.service.getData();
    }
}
```
由于该服务是在模块中注入，所以该模块中的所有组件使用这个服务时，使用的都是同一个实例。  

除了在模块中声明，还可以在组件中声明。  

Angular还有个分层依赖注入的概念，也就是说，你可以为任一组件创建自己独立的服务。就像上面的例子，如果想要HomeComponent不和它的父组件同使用一个服务实例的话，只要在该组件中重新注入即可：  

```
...
@Component({
    selector: 'home',
    templateUrl: './home.component.html',
    styleUrls: ['./home.component.css'],
    providers: [ Service ],  // 重新注入服务
})
export class HomeComponent {
    ...
}

```

--
相关资料：  
[Angular官网](https://angular.io/)  
[AngularCli](https://cli.angular.io/)  
[大漠穷秋Angular指南](https://www.youtube.com/channel/UC6cY3GTGIk4-ahaIRj7Bk-Q)  
[Angular指令快速入门](https://segmentfault.com/a/1190000009674089#articleHeader14)
