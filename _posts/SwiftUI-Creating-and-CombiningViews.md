layout: SwiftUI

title: SwiftUI-Creating and Combining Views

date: 2020-07-20 14:18:02

tags: SwiftUI

---



### 1. Create a New Project and Explore the Canvas

- 创建一个新的Xcode 项目 (**File > New > Project**.)
- Select the **Single View App**
- select **SwiftUI** for the user interface

<!-- more -->

Example Code

```Swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        Text("Hello, World!")
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```



> 默认情况下, `SwiftUI`视图文件声明了两个结构体 , 第一个结构体, 描述视图的内容和布局。第二个结构声明了该视图的预览。

### 2. 新版Xcode有了更加便捷了预览



<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ggx9m5faekj30p60e8751.jpg" alt="截屏2020-07-20 上午10.22.39" style="zoom:50%;" />

> 在Xcode的右侧, 新增了视图预览窗口, 点击Resume可以进行静态预览
>
> 显示/关闭预览 **Editor > Editor and Canvas**

### 3. Customize the Text View

#### 3.1 以下是通过代码对Text进行简单的设置, 更加详细的使用请看Text 相关API

```Swift
struct ContentView: View {
    var body: some View {
        Text("Hello, summerxx")
            .foregroundColor(.blue)
            .font(Font.system(Font.TextStyle.largeTitle))
    }
}
```

#### 3.2 还可以点击command键 +然后点击要修改的Text/ command+点击文字



<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ggx9mt2ojqj30l00l813c.jpg" alt="截屏2020-07-20 上午10.46.47" style="zoom:50%;" /><img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ggx9n2kt9ij30h20r2tlr.jpg" alt="截屏2020-07-20 上午10.50.54" style="zoom:50%;" />

#### 3.3 Combine Views Using Stacks

```Swift
struct ContentView: View {
    var body: some View {
        VStack {
            Text("summerxx0")
                .font(.title)
                .foregroundColor(.blue)
                .font(Font.system(Font.TextStyle.largeTitle))
                .frame(width: 150, height: 150, alignment: Alignment.leading)
                
            HStack {
                Text("summerxx1")
                .font(.title)
                .foregroundColor(.blue)
                    .font(Font.system(Font.TextStyle.largeTitle))
                Text("summerxx2")
                .font(.title)
                .foregroundColor(.blue)
                    .font(Font.system(Font.TextStyle.largeTitle))
            }
        }
    }
}
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ggx9lt0ngqj30hu0kqh00.jpg" alt="截屏2020-07-20 上午11.11.36" style="zoom:50%;" />

上面是简单组合的演示, 同时 你可能需要多种不同的控件添加 - 可以通过Xcode右上角的加号进行, 控件的添加 (常用的有 scrollView, button, list, Form 等等) 



### 4. Create a Custom Image View

```Swift
Image("summerxx").frame(width: 400.0, height: 400.0).clipShape(Circle())
```

### 5. Use UIKit and SwiftUI Views Together

- 创建XTCustomMapView继承于 `UIViewRepresentable` 然后重写父类方法`makeUIView`,  `updateUIView`
- 显示 latitude: 34.011286, longitude: -116.166868 跨度两个经纬度的地理位置

```Swift
struct XTCustomMapView: UIViewRepresentable {
    func makeUIView(context: Context) -> MKMapView {
        MKMapView(frame: .zero)
    }
    
    func updateUIView(_ uiView: MKMapView, context: Context) {
        // 1. 经纬度
        let coordinate = CLLocationCoordinate2D(
            c)
        // 2. 跨度
        let span = MKCoordinateSpan(latitudeDelta: 2.0, longitudeDelta: 2.0)
        // 3. 地区
        let region = MKCoordinateRegion(center: coordinate, span: span)
        // 4. 设置
        uiView.setRegion(region, animated: true)
    }
}
```

### 6. Compose the Detail View

```Swift
struct ContentView: View {
    var body: some View {
        VStack {
            XTCustomMapView().edgesIgnoringSafeArea(.top)
            .frame(height: 300)
            
            Image("summerxx1").frame(width: 200.0, height: 200.0).clipShape(Circle())
            Text("夏天然后")
                .font(.title)
                .foregroundColor(.blue)
                .font(Font.system(Font.TextStyle.largeTitle))
                .frame(width: 150, height: 50, alignment: Alignment.center)
                
            HStack {
                Text("个人博客")
                .font(.title)
                .foregroundColor(.blue)
                    .font(Font.system(Font.TextStyle.largeTitle))
                Text("summerxx.com")
                .font(.title)
                .foregroundColor(.blue)
                    .font(Font.system(Font.TextStyle.largeTitle))
            }
            Button(action: /*@START_MENU_TOKEN@*/{}/*@END_MENU_TOKEN@*/) {
                Text("编辑资料")
            }.frame(width: 100, height: 40, alignment: Alignment.center)
                .font(Font.system(size: 18))
        }
    }
}
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ggxefvp3qfj30n01ds12g.jpg" alt="Simulator Screen Shot - iPhone 11 - 2020-07-20 at 14.00.44" style="zoom:50%;" />


总结: 以上就是包含了SwiftUI的一些简单语法, 以及UI控件的构建, 编译器的一些简单使用

**Demo地址** 将会持续更新SwiftUI相关代码
[https://github.com/summerxx27/SwiftUITutorials](https://github.com/summerxx27/SwiftUITutorials)