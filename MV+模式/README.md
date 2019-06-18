# MV*模式
分为三种:
+ MVC(模型-视图-控制器)
+ MVP(模型-视图-表示器)
+ MVVM(模型-视图-视图模型)

## MVC
一种架构设计模式，通过关注点分离鼓励改进应用程序组织。

强调将业务(Model)与用户界面(View)隔离,第三个组件(Controller)来管理逻辑和用户输入

目的：解耦应用程序的这三个部分，同时运行应用程序的其他接口实现Model复用。

### Model
代表特定于领域的数据，不了解用户界面(View与Controller)。当一个Model改变时，它会通知它的观察者

### View
描绘的是Model的当前状态。`Observer`模式让View知道Model什么时候更新或修改

### Presentation
由View关注，但不只是单个View和Controller,屏幕上显示的每个部分或者元素都需要View-Controller对

### Controller
在某个View-Controller对中的作用是处理用户交互,为View做决定