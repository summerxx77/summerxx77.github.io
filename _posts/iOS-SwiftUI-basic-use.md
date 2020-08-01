layout: SwiftUI

title: SwiftUI-Text,TextField,Image,Button,List,SrcollView使用

date: 2020-07-21 18:18:02

tags: SwiftUI

------

[TOC]

前言: 上一篇进行了SwiftUI的初步尝试, 以及对整体运行环境的探索, 介绍了一些基础的概念, 横向堆栈(HStack), 守竖直堆栈(VStack) 以及编辑器的快捷使用, 实时预览的初体验等等, 这篇简单写一下我们常用的一些SwiftUI控件, 代码Demo放在文末.



<!--more-->

### 1. Text 的简单使用

```swift
private let summerxx = "summerxx.com"

struct TextTest: View {
    var body: some View {
        VStack(spacing: 15) {
            Text("SwiftUI")
            Text("SwiftUI")
                .foregroundColor(.orange)
                .bold()
                .font(.system(.largeTitle))
                .fontWeight(.medium)
                .italic()
                .shadow(color: .black, radius: 1, x: 0, y: 2)
            
            Text(summerxx)
                .underline(true, color: Color.gray)
                .font(.system(size: 16, design: .serif)).onTapGesture {
                    print(summerxx)
            }
            
            HStack {
                Text("Text")
                Text("Text.bold").bold()
                Text("SecureField").foregroundColor(.orange)
            }
            
            Text("Views and controls are the visual building blocks of your app’s user interface." +
                " Use them to present your app’s content onscreen.")
                .lineLimit(nil)
        }
    }
}
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ggyq42c4ymj30n01dsac8.jpg" alt="Simulator Screen Shot - iPhone 11 - 2020-07-21 at 17.28.54" style="zoom: 33%;" />

### 2. TextField 的简单使用

```Swift
struct TextFieldTest: View {
    
    @State var username : String = ""
    @State var password : String = ""
    
    var body: some View {
        
        VStack(spacing: 15){
            
            Text("Your username is \(username)!")
            Text("Your password is \(password)!")
            
            HStack {
                Text("名字:")
                TextField("User Name", text: $username, onEditingChanged: { (value) in
                    
                    print("onEditingChanged:\(self.username)")
                    
                }) {
                    
                    print("onCommit:\(self.username)")
                    
                    }.textFieldStyle(RoundedBorderTextFieldStyle()).padding(10).padding(EdgeInsets(top: 0, leading: 20, bottom: 0, trailing: 20))
            }
            
            HStack {
                Text("密码:")
                TextField("Nick Name", text: $password, onEditingChanged: {
                    (value) in
                    print("onEditingChanged:\(self.password)")
                    
                }) {
                    print("onCommit:\(self.password)")
                }.textFieldStyle(RoundedBorderTextFieldStyle()).padding(10).padding(EdgeInsets(top: 0, leading: 20, bottom: 0, trailing: 20))
            }
        }
        .padding()
        .offset(y: -150)
        .navigationBarTitle(Text("TextField"))
    }
}

```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ggyq6etyeij30n01dsmyw.jpg" alt="Simulator Screen Shot - iPhone 11 - 2020-07-21 at 17.29.26" style="zoom:33%;" />

### 3. Image 的简单使用

```Swift
struct ImageTest: View {
    let range = 0..<5
    
    var body: some View {
        VStack {
            ForEach(range) { index in
                Image("summerxx1")
                    .resizable()
                    .frame(width: CGFloat(30 * index),
                           height: CGFloat(30 * index),
                           alignment: .center)
                    .padding(15)
                    .clipShape(Circle().inset(by: 15.0))
                    .onTapGesture {
                    print("Tap \(index)")
                }
            }
        }.navigationBarTitle(Text("Image"))
    }
}
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ggyq8oe6nwj30n01dstfd.jpg" alt="Simulator Screen Shot - iPhone 11 - 2020-07-21 at 17.29.31" style="zoom:33%;" />

### 4. 如何给Image赋值网络图片

```swift
struct WebImageTest: View {
    
    @State private var uiImage: UIImage? = nil
    // 站位图是夏目图片, 下载后变成一张表情包?!
    let placeholderImage = UIImage(named: "summerxx1")!
    
    var body: some View {
        Image(uiImage: self.uiImage ?? placeholderImage)
            .resizable()
            .onAppear(perform: downloadWebImage)
            .frame(width: 80,
                   height: 80,
                   alignment: .center)
            .onTapGesture {
                print("Tap ")
        }.navigationBarTitle(Text("WebImage"))
    }
    
    func downloadWebImage() {
        
        guard let url = URL(string: "https://tva1.sinaimg.cn/large/007S8ZIlly1ggyn061g4jj305e05gwfo.jpg") else {
            print("Invalid URL.")
            return
        }
        URLSession.shared.dataTask(with: url) { (data, response, error) in
            if let data = data, let image = UIImage(data: data) {
                self.uiImage = image
            }else {
                print("error: \(String(describing: error))")
            }
        }.resume()
    }
}
```

### 5. Button的简单使用

```swift
struct ButtonTest: View {
    var body: some View {
        VStack(spacing: 0) {
            
            Button(action: {
                print("点了我1")
            }) {
                Text("I'm a Button1").bold()
                    .font(.system(size: 40,design: .rounded))
                    .shadow(radius: 1)
            }
            
            Button(action: {
                print("点了我2")
            }) {
                Text("I'm a Button2").bold()
                    .font(.system(size: 40,design: .rounded))
                    .shadow(radius: 1)
            }
            
            Button(action: {
                print("点了我3")
            }) {
                // 包裹一层 VStack，变为上下
                VStack {
                    Text("I'm a Button3")
                    Image("summerxx1").resizable().renderingMode(.original)
                }
            }
                // 字体颜色, 和默认图片渲染色
                .foregroundColor(Color.blue)
                .padding()
                .background(Color.orange)
                .cornerRadius(20)
                .shadow(radius: 10)
                .frame(width: 160, height: 160)
            
        }.navigationBarTitle(Text("Button"))
    }
}
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ggyqck6xo8j30n01dsmze.jpg" alt="Simulator Screen Shot - iPhone 11 - 2020-07-21 at 17.29.46" style="zoom:33%;" />

### 6. List的简单使用

```swift
struct ListTest: View {
    // 更多可以看 : https://www.jianshu.com/p/867e4a9a13fc
    var body: some View {
        List(0..<15) { item in
            Text("Hello World !").frame(width: UIScreen.main.bounds.size.width, height: 60, alignment: Alignment.center)
        }.navigationBarTitle(Text("List"), displayMode: .large)
    }
}
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ggyqe836h7j30n01dswg3.jpg" alt="Simulator Screen Shot - iPhone 11 - 2020-07-21 at 17.29.54" style="zoom:33%;" />

### 7. ScrollView的示例代码

```Swift
struct ScrollViewTest: View {
    var body: some View {
        ScrollView {
            Text("SwiftUI-ScrollView").padding(20)
            Divider()
            Image("icon")
                .resizable()
                .frame(width: 300, height: 300, alignment: .center)
            Divider()
            Text("Views and controls are the visual building blocks of your app’s user interface.")
        }
        .border(Color.gray, width: 1)
        .cornerRadius(10)
        .padding(10)
        .navigationBarTitle(Text("ScrollView演示"))
    }
}
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ggyqfxluqfj30n01ds0u9.jpg" alt="Simulator Screen Shot - iPhone 11 - 2020-07-21 at 17.30.01" style="zoom: 33%;" />

**Demo地址** 将会持续更新SwiftUI相关代码
[https://github.com/summerxx27/SwiftUITutorials](https://github.com/summerxx27/SwiftUITutorials)