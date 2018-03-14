# 1. 浏览器的主要功能

浏览器的主要功能是通过向服务器请求并在浏览器窗口上显示您所选择的Web资源。资源格式通常是HTML，但也包括PDF，图像等等。资源的位置由用户使用URI（统一资源标识符）指定。HTML和CSS规范中指定了浏览器解释和显示HTML文件的方式。

浏览器的用户界面有很多共同之处。常见的用户界面元素包括：

- 用于插入URI的地址栏
- 后退和前进按钮
- 书签选项
- 用于刷新和停止加载当前文档的刷新和停止按钮
- 主页按钮，让你到你的主页

# 2. 浏览器的基本结构

浏览器的主要组件是：

1. **用户界面** - 包括地址栏，后退/前进按钮，书签菜单等。浏览器的每个部分都显示除了主窗口，您可以看到请求的页面。
2. **浏览器引擎** - 查询和操作渲染引擎的界面。
3. **渲染引擎** - 负责显示请求的内容。例如，如果请求的内容是HTML，则它负责解析HTML和CSS，并在屏幕上显示解析的内容。
4. **网络** - 用于网络呼叫，如HTTP请求。它具有与平台无关的接口，并在每个平台的底层实现。
5. **UI后端** - 用于绘制组合框和窗口等基本小部件。它公开了一个不是平台特定的通用接口。它下面使用操作系统用户界面方法。
6. **JavaScript解释器**。用于解析和执行JavaScript代码。
7. **数据存储**。这是一个持久层。浏览器需要保存硬盘上的各种数据，例如cookie。HTML5定义了“web数据库”，它是浏览器中的完整（尽管是轻量级）数据库。

![浏览器足见](./images/layers.png)

> Chrome浏览器拥有多个渲染引擎实例 - 每个标签一个。每个选项卡是一个单独的过程。

# 3. 渲染引擎

​	渲染，即在浏览器屏幕上显示所请求的内容。默认情况下，渲染引擎可以显示HTML和XML文档和图像。除此之外，它可以通过插件（浏览器扩展）显示其他类型，例如，使用PDF查看器插件显示PDF。	

> Firefox使用Gecko--一种“自制”的Mozilla渲染引擎。Safari和Chrome都使用Webkit。其中，Webkit是一个开源渲染引擎，作为Linux平台的引擎启动，并由Apple修改以支持Mac和Windows。

​	渲染引擎一般从网络层以8K块的形式开始获取所请求文档的内容，然后开始解析HTML文档。它首先解析HTML文档中的标签来建立DOM树，同时解析外部CSS文件和样式元素中的样式数据。其次根据样式信息和HTML中的可视指令来创建渲染树，进行渲染布局。此时，每个节点拥有了确切的坐标。最后，遍历渲染树，将每个节点使用UI后端图层进行绘制。流程图见下文。

> - 渲染树包含具有可视属性（如颜色和尺寸）的矩形。这些矩形按照正确的顺序显示在屏幕上。
> - 为了更好的用户体验，渲染引擎会尝试尽快在屏幕上显示内容。在开始构建和布置渲染树之前，它不会等到所有的HTML被解析。部分内容将被解析并显示，而同时解析来自网络的其余内容。

![渲染引擎基本流程](./images/flow.png)

## 3.1 两种常见的渲染引擎流程

- **webkit主流程图：**

![webkit主流程](./images/webkitflow.png)

- **Mozilla的Gecko渲染引擎主流程：**

![Gecko渲染引擎主流程](./images/image008.jpg)

​	从以上两张流程中可以看出，虽然Webkit和Gecko使用的术语略有不同，但流程基本相同。唯一的区别是Gecko在HTML和DOM树之间有一个额外的层——它被称为“content sink”，是制作DOM元素的工厂。

## 3.2 解析基本概念

​	**解析文档**意味着将其翻译成某种合理的结构 - 代码可以理解和使用的结构。**解析的结果**通常是代表文档结构的**节点树**。它被称为**分析树或语法树**。举个例子，学过数据结构中分析公式时的后缀（前缀、中缀）公式的读者都知道，一个数学计算式"2 + 3 - 1"可以被表达称为如下的树：

![数学表达树](./images/image009.png)

​	也就是说，遵循一定的语法，便可以将HTML文档解析构建成一种分析树。而这种语法一般为由特定的词汇规则和语法规则组成的确定性语法，即与实际显示的内容无关。在浏览器中一般通过**解析器**进行HTML解析。

### 3.2.1 解析器

​	解析器就和语法规则一样，由两部分组成——词法分析和语法分析。词法分析，就是将文档内容根据特定的词汇（或者标记）分解为有效标记的过程；除此之外，它可以自动去除不相关的字符，如空格和换行符。语法分析，则是根据文档语言规则进行解析的过程。所以解析就是在词法分析之后，根据语法规则来分析文档并建立解析树的过程。如下图所示。

![从源文件到解析树](./images/image011.png)

但是，值得注意的是，机械过程是迭代的。

- 解析器通常会向词法分析器请求一个新的标记，并尝试将该标记与其中一个语法规则进行匹配。
- 如果规则匹配，则将与该令牌相对应的节点添加到解析树中，解析器将请求另一个令牌。 
- 如果没有规则匹配，解析器将在内部存储该令牌，并继续询问令牌，
  - 直到找到与所有内部存储的令牌匹配的规则。
  - 如果没有规则被发现，则解析器将引发异常。这意味着文档无效并包含语法错误。

## 3.3 HTML解析器

### 3.3.1 HTML语法定义

### 3.3.2 不是上下文无关的语法

### 3.3.3 HTML DTD

### 3.3.4 DOM

### 3.3.5 解析算法

### 3.3.6 标记算法

### 3.3.7 构建树算法

### 3.3.8 解析完成时的动作

### 3.3.9 浏览器容错

## 3.4 CSS解析器

### 3.4.1 Webkit CSS解析器

## 3.5 解析scripts

## 3.6 scripts和css处理顺序

### 3.6.1 脚本

### 3.6.2 推测解析

### 3.6.3 CSS

# 4. 渲染树

## 4.1 DOM树与渲染树的关系

## 4.2 渲染树的建立流程

## 4.3 样式计算

### 4.3.1 共享样式数据

### 4.3.2 Firefox规则树

#### 4.3.2.1 结构划分

#### 4.3.2.2 采用规则树进行样式数据计算

### 4.3.3 操纵简单匹配的规则

### 4.3.4 以正确的级联顺序引用规则

#### 4.3.4.1 样式表级联顺序

#### 4.3.4.2 特殊点

#### 4.3.4.3 规则优先级

## 4.4 循序渐进的计算过程

# 5. 布局

## 5.1 脏位系统

## 5.2 全局布局和增量布局

## 5.3 异步布局和同步布局

## 5.4 布局优化

## 5.5 布局处理过程

## 5.6 宽度计算

## 5.7 断行处理

# 6. 绘图

## 6.1 全局绘图和增量绘图

## 6.2 绘图顺序

## 6.3 Firefox显示列表

## 6.4 Webkit矩形的存储

# 7. 动态变化

# 8. 渲染引擎的线程

## 8.1 事件循环

# 9. CSS2

## 9.1 canvas

## 9.2 CSS盒子模型

## 9.3 CSS定位方案

## 9.4 CSS盒子类型

## 9.5 CSS定位

## 9.6 CSS分层表示

# 10. 文档参考资源

1. 浏览器架构
   1. Grosskurth，Alan。Web浏览器的参考体系结构 http://grosskurth.ca/papers/browser-refarch.pdf
2. 解析
   1. Aho，Sethi，Ullman，Compilers：Principles，Techniques，and Tools（aka the Dragon book），Addison-Wesley，1986
   2. 瑞克Jelliffe。大胆和美丽：HTML 5的两个新草案 http://broadcast.oreilly.com/2009/05/the-bold-and-the-beautiful-two.html
3. 火狐
   1. L. David Baron，更快的HTML和CSS：Web开发人员的布局引擎内部 http://dbaron.org/talks/2008-11-12-faster-html-and-css/slide-6.xhtml
   2. L. David Baron，更快的HTML和CSS：Web开发人员的布局引擎内部（Google tech talk video）http://www.youtube.com/watch?v=a2_6bGNZ7bA
   3. L. David Baron，Mozilla的布局引擎 http://www.mozilla.org/newlayout/doc/layout-2006-07-12/slide-6.xhtml
   4. L. David Baron，Mozilla风格系统文档。http://www.mozilla.org/newlayout/doc/style-system.html
   5. Chris Waterson，关于HTML Reflow的注释。http://www.mozilla.org/newlayout/doc/reflow.html
   6. 克里斯沃特森，壁虎概述。http://www.mozilla.org/newlayout/doc/gecko-overview.htm
   7. Alexander Larsson，HTML HTTP请求的生命。https://developer.mozilla.org/en/The_life_of_an_HTML_HTTP_request
4. WebKit
   1. David Hyatt，实施CSS（第1部分）http://weblogs.mozillazine.org/hyatt/archives/cat_safari.html
   2. David Hyatt，WebCore概述 http://weblogs.mozillazine.org/hyatt/WebCore/chapter2.html
   3. David Hyatt，WebCore渲染 http://webkit.org/blog/114/
   4. David Hyatt，FOUC问题 http://webkit.org/blog/66/the-fouc-problem/
5. W3C规范
   1. HTML 4.01规范 http://www.w3.org/TR/html4/
   2. HTML5规范 http://dev.w3.org/html5/spec/Overview.html
   3. 层叠样式表2级修订1（CSS 2.1）规范http://www.w3.org/TR/CSS2/
6. 浏览器构建指令
   1. Firefox浏览器  https://developer.mozilla.org/en/Build_Documentation
   2. Webkit的 http://webkit.org/building/build.html

