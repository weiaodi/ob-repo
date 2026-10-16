# 编程与技术 · 浏览器、CSS与前端工程

## css中，我移动子元素前，计算父元素和子元素的位置，然后子元素向右移动120px后，再计算父元素和子元素距离，为什么不是……

在 CSS 中，当你移动子元素向右 120px 后，再次计算父元素和子元素的距离不是 120px 可能有以下一些原因：

1. **存在其他定位或布局因素**：可能父元素或子元素本身有其他的定位属性（如相对定位、绝对定位等），或者有边距、填充等影响了实际的位置计算。
2. **浏览器渲染差异**：不同浏览器对于 CSS 布局和定位的处理可能存在一些细微差异，导致实际效果与预期不完全一致。
3. **单位换算问题**：确保你使用的单位在整个过程中都是一致的，并且没有出现错误的转换或理解。
4. **元素尺寸变化**：子元素移动过程中可能导致其尺寸发生变化，从而影响与父元素的相对距离。

### 追问：如何确保在整个过程中单位都是一致的？

以下是一些确保单位一致的方法：

1. **明确声明单位**：在设置样式属性值时，始终明确写出单位，如 `px`、`em`、`%` 等，避免只写数值而引起混淆。
2. **统一规划**：在开始编写 CSS 代码之前，就确定好主要使用的单位，并在整个项目中保持一致。
3. **检查代码**：定期检查 CSS 代码，确认所有涉及到尺寸、距离等的属性都带有正确的单位。
4. **团队沟通**：如果是团队开发，明确规定并相互提醒使用统一的单位，避免不同人使用不同单位导致不一致。
5. **使用预处理器（如果有）**：一些 CSS 预处理器可以帮助更好地管理单位和样式的一致性。
6. **借助开发工具**：利用浏览器的开发者工具等，查看元素实际应用的样式，检查单位是否正确。

### 追问：移动120px后，再计算父子距离为什么不是120px

移动子元素后，父子距离不一定是 120px，可能有以下原因：

- **边距塌陷**：在标准文档流中，垂直方向的父子元素可能会出现边距塌陷现象。当给子元素设置`margin-top`时，子元素不会相对父元素顶端距离 120px，而是父子元素同时相对文档下移 120px。
- **其他定位或布局因素**：父元素或子元素本身可能有其他的定位属性（如相对定位、绝对定位等），或者有边距、填充等影响了实际的位置计算。
- **元素尺寸变化**：子元素移动过程中可能导致其尺寸发生变化，从而影响与父元素的相对距离。
- **浏览器渲染差异**：不同浏览器对于 CSS 布局和定位的处理可能存在一些细微差异，导致实际效果与预期不完全一致。

为了解决这些问题，可以尝试以下方法：

- 检查并确保父子元素的边距、填充等属性设置正确。
- 考虑使用其他方法来避免边距塌陷，如给父元素添加边框或使用绝对定位。
- 对元素的尺寸和位置进行仔细的计算和调整。
- 在不同的浏览器中进行测试，以确保一致性。

## scrollby这个函数操作移动的距离是怎么实现的

`scrollBy` 函数用于在当前基础上增加或减少滚动条的滚动距离。它接受两个参数，分别表示水平方向和垂直方向上的滚动距离，单位通常是像素。例如，`scrollBy(100, 200)` 表示在水平方向上向右滚动 100 像素，在垂直方向上向下滚动 200 像素。

在 Android 中，`ScrollView` 提供了 `scrollBy` 和 `scrollTo` 方法来控制滑动距离。`scrollBy` 方法可以将 `ScrollView` 滑动指定的像素数，而 `scrollTo` 方法可以将 `ScrollView` 滑动到指定的坐标位置。

以下是使用 `scrollBy` 方法设置滑动距离的示例代码：

```java
ScrollView scrollView = findViewById(R.id.scroll_view);
scrollView.post(new Runnable() {
    @Override
    public void run() {
        scrollView.scrollBy(0, 200); // 向下滑动 200 像素
    }
});
```

在上述示例中，使用 `post` 方法在 `ScrollView` 绘制完成后执行滑动操作，将 `ScrollView` 向下滑动了 200 像素。

需要注意的是，具体的实现方式可能因使用的技术或框架而有所不同。上述示例是在 Android 中使用 `ScrollView` 的常见方法。在其他环境或框架中，可能会有类似的函数或方法来实现滚动效果，具体的用法可以参考相应的文档和示例代码。

### 追问：js中这个元素的实现

在 JavaScript 中，可以使用`scrollBy()`方法来移动元素的位置。`scrollBy()`方法接受两个参数，分别表示水平方向和垂直方向上的移动距离，单位是像素。示例如下：

```javascript
// 获取要移动的元素
var element = document.getElementById('myElement');

// 向右移动 100 像素
element.scrollBy(100, 0);

// 向下移动 50 像素
element.scrollBy(0, 50);
```

在上述示例中，通过`getElementById()`方法获取了一个 ID 为`myElement`的元素，然后使用`scrollBy()`方法将其向右移动 100 像素，向下移动 50 像素。

### 追问：除了`scrollBy()`方法，还有哪些方法可以移动元素的位置？

除了`scrollBy()`方法，在 JavaScript 中还可以使用以下方法来移动元素的位置：

1. `setInterval()`方法：可以通过设置定时器来实现元素的连续移动，示例如下：

```javascript
function animate(element,target) {
  clearInterval(element.timer1);
  element.timer1=setInterval(function(){
    var current=element.offsetLeft;
    var step=10;
    current+=step;
    if(Math.abs(target-current)>Math.abs(step)){
      element.style.left=current+"px";
    }else{
      clearInterval(element.timer1);
    }
  }, 100);
}

// 获取要移动的元素
var element = document.getElementById('myElement');

// 向右移动 100 像素
animate(element,400);
```

2. `mouseDown`和`mouseDrag`事件：通过监听鼠标的按下和拖动事件来实现元素的移动，示例如下：

```javascript
this.paper.view.onMouseDown = (e) => {
  this.onMouseDown(e)
}
this.paper.view.onMouseDrag = (e) => {
  this.onMouseDrag(e)
}
```

3. `moveUp()`、`moveDown()`和`moveTop()`方法：可以实现元素的上移、下移和置顶操作，示例如下：

```javascript
<div class="rui-flex-ac" v-for="item,index in nums" :key="item">
  <div class="rui-fg">{{item}}</div>
  <div class="rui-fa" v-if="index!= 0" @click="moveUp(index)">上移</div>
  <div class="rui-fa" v-if="index!= nums.length - 1" @click="moveDown(index)">下移</div>
  <div class="rui-fa" v-if="index!= 0" @click="moveTop(index)">置顶</div>
</div>
```

4. `splice()`方法：可以在数组中移动元素的位置，示例如下：

```javascript
let fruits = [
  { name: 'apple', color: 'red' },
  { name: 'orange', color: 'orange' },
  { name: 'banana', color: 'yellow' },
  { name: 'grape', color: 'purple' }
];

let currentIndex = 2;
let newIndex = 0;

fruits.splice(newIndex, 0, fruits.splice(currentIndex, 1)[0]);

console.log(fruits);
```

5. `array解构`：可以通过数组解构来实现元素的位置交换，示例如下：

```javascript
let fruits = [
  { name: 'apple', color: 'red' },
  { name: 'orange', color: 'orange' },
  { name: 'banana', color: 'yellow' },
  { name: 'grape', color: 'purple' }
];

let currentIndex = 2;
let newIndex = 0;

(fruits[newIndex], fruits[currentIndex]) = (fruits[currentIndex], fruits[newIndex]);

console.log(fruits);
```

## 计算父子元素的水平相对位置

在 JavaScript 中，要计算父子元素的水平相对位置，可以使用`offsetLeft`和`offsetTop`属性来获取元素相对于其父元素的位置，示例如下：

```html
<!DOCTYPE html>
<html>
<head>
  <title>Get Element Position</title>
  <style>
    .parent {
      position: relative;
      width: 200px;
      height: 200px;
      background-color: lightgray;
    }

    .child {
      position: absolute;
      top: 50px;
      left: 50px;
      width: 100px;
      height: 100px;
      background-color: red;
    }
  </style>
</head>
<body>
  <div class="parent">
    <div class="child"></div>
  </div>
  <script>
    // 获取目标元素和其父元素的引用
    var childElement = document.querySelector('.child');
    var parentElement = childElement.parentNode;

    // 获取目标元素相对于其父元素的位置
    var positionLeft = childElement.offsetLeft - parentElement.offsetLeft;
    var positionTop = childElement.offsetTop - parentElement.offsetTop;

    console.log('Element position relative to parent:', positionLeft, positionTop);
  </script>
</body>
</html>
```

在上述代码中，父元素有一个固定的宽度和高度，并且被设置为相对定位。子元素被设置为绝对定位，其顶部和左侧与父元素的边缘相距50像素。然后，使用`document.querySelector()`方法获取目标元素和其父元素的引用。接下来，使用目标元素的`offsetLeft`和`offsetTop`属性减去父元素的`offsetLeft`和`offsetTop`属性，即可得到目标元素相对于父元素的位置。

## 在一个 500px宽的父元素下， 有一个1000px的子元素，子元素超出500px视图窗口元素的部分都会被隐藏，通过 o……

以下是一种可能的实现方式来判断子元素移动后的位置与父元素的关系：

```javascript
const parent = document.querySelector('.parent');
const child = document.querySelector('.child');

child.addEventListener('mousemove', (e) => {
  const childRect = child.getBoundingClientRect();
  const parentRect = parent.getBoundingClientRect();

  if (childRect.left < parentRect.left) {
    console.log('子元素在左边超出父元素');
  } else if (childRect.right > parentRect.right) {
    console.log('子元素在右边超出父元素');
  } else {
    console.log('子元素在父元素内');
  }
});
```

在上述代码中，通过监听子元素的鼠标移动事件，获取子元素和父元素的边界信息，然后比较来判断子元素相对父元素的位置情况。

## vue项目中，如果我默认浏览器支持es6的语法，我可以配置vue的打包配置为esbuild

在 Vue 项目中可以使用 esbuild 进行打包配置。esbuild 是一个快速的 Web 打包器，支持 ES6 和 CommonJS 模块等。

首先，需要安装 esbuild，可以通过 npm 进行安装：

```
npm install esbuild
```

然后，在项目的`package.json`文件中添加构建脚本，例如：

```json
{
  "scripts": {
    "build": "esbuild./src/index.js --bundle --outfile=./dist/index.js"
  },
  "dependencies": {
    "esbuild": "具体版本号",
    "vue": "^3.2.47" 
  }
}
```

上述配置中，`"build"`脚本指定了使用 esbuild 对`./src/index.js`文件进行打包编译，`--bundle`表示打包文件，将任何依赖项都内联到文件中，`--outfile=./dist/index.js`定义了输出文件的路径和文件名。

如果项目中的构建脚本较为复杂，还可以创建一个单独的`build.js`文件来进行更详细的配置。例如：

```javascript
require('esbuild').build({ 
  // 编译入口，可以是单个文件或文件数组
  entryPoints: ['src/index.js'], 
  bundle: true, 
  // 输出文件的文件夹
  outdir: 'dist' 
})
.catch(() => process.exit(1));
```

在实际开发中，可能还需要监听文件变化以便在修改时自动重新编译，以及启动静态资源服务器方便查看更改等。可以参考 esbuild 的相关文档和示例，根据项目的具体需求进行相应的配置。

另外，如果使用 Vue 3，也可以考虑结合其他工具如 Vite，它默认使用 esbuild 并且提供了更便捷的开发和构建体验。

需注意，在实际应用中，还需要根据项目的具体情况进行调整和测试，确保打包后的项目能够正常运行。同时，确保你的 Vue 版本与 esbuild 以及其他相关依赖的版本兼容。如果遇到问题，可以查阅 esbuild 和 Vue 的文档，或者在相关技术社区寻求帮助。

## 前端手动点击页面的测试行为叫什么测试？

前端手动点击页面的测试行为通常被称为手动功能测试或黑盒测试。

1. **手动功能测试**
   - 这是一种最基本的测试方式。测试人员像普通用户一样操作软件界面，通过手动点击按钮、输入文本、选择菜单选项等操作，来验证软件的各项功能是否能够按照预期正常工作。例如，在一个电商网站的前端页面测试中，测试人员手动点击“加入购物车”按钮，检查商品是否能够正确添加到购物车中，购物车中的商品数量、价格等信息是否准确显示。
   - 手动功能测试关注的是软件的功能完整性和正确性，它可以发现功能缺失、功能错误、界面布局不合理等问题。比如，在一个表单提交页面，手动输入合法和非法的数据，看系统是否能正确处理，如正确保存合法数据，对非法数据给出正确的提示。
2. **黑盒测试**
   - 黑盒测试把软件系统看作一个“黑盒子”，测试人员不需要了解软件内部的代码结构和实现细节，只需要关注软件的输入和输出。手动点击页面元素就是在提供输入，然后观察页面的响应（输出）。例如，在测试一个视频播放网站时，测试人员不知道后台代码是如何实现视频播放功能的，只是通过点击“播放”“暂停”“全屏”等按钮，观察视频是否按照预期播放、暂停和全屏显示。
   - 黑盒测试可以用来发现软件与用户需求不符的问题，包括功能不符合要求、界面易用性差等。它主要从用户的角度来评估软件的质量，例如检查软件的界面是否友好，操作是否符合用户习惯等。

## webworker执行ui渲染进程，js实现

在 JavaScript 中使用 Web Worker 来执行 UI 渲染进程并不是一种常见的做法，因为通常 UI 渲染是在主线程中进行以确保响应性和与用户的交互性。然而，如果你有特定的需求要将一部分与 UI 相关的计算任务放到 Web Worker 中，可以参考以下方式：

以下是一个不太常规但展示基本概念的示例代码：

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
</head>

<body>
  <script>
    // 创建一个 Web Worker
    const worker = new Worker('worker.js');

    // 在主线程中模拟一些需要渲染的数据
    const dataToRender = [1, 2, 3, 4, 5];

    // 向 worker 发送渲染任务
    worker.postMessage({ data: dataToRender });

    // 从 worker 接收渲染结果
    worker.onmessage = function (event) {
      console.log('渲染结果：', event.data);
    };
  </script>
</body>

</html>
```

`worker.js` 文件内容：

```javascript
self.onmessage = function (event) {
  const data = event.data.data;
  // 在这里进行一些模拟渲染计算
  const renderedData = data.map(item => item * 2);
  // 将渲染结果发送回主线程
  self.postMessage({ renderedData });
};
```

需要注意的是，真正的 UI 渲染通常涉及到直接操作 DOM 元素，而 Web Worker 不能直接操作 DOM。所以这个示例只是一个概念性的展示，实际应用中要谨慎考虑是否真的适合用 Web Worker 来处理 UI 渲染相关任务，因为这可能会带来额外的复杂性和同步问题。

### 追问：页面保证活跃， js实现

在 JavaScript 中，可以通过以下几种方式来尽量保证页面保持活跃状态：

**一、使用`requestAnimationFrame`进行持续更新**

`requestAnimationFrame`可以在浏览器下一次重绘之前执行指定的函数，这可以让页面看起来更加流畅和活跃。

```javascript
function keepPageActive() {
  // 执行一些动画或者更新操作，例如移动一个元素、改变颜色等
  const element = document.getElementById('activeElement');
  element.style.transform = `translateX(${Math.random() * 10}px)`;
  requestAnimationFrame(keepPageActive);
}

requestAnimationFrame(keepPageActive);
```

**二、设置定时器定期执行操作**

使用`setInterval`或`setTimeout`来定期执行一些操作，给用户一种页面在持续活动的感觉。

```javascript
setInterval(() => {
  // 执行一些操作，如显示一个提示信息、更新一个计数器等
  const counterElement = document.getElementById('counter');
  let count = parseInt(counterElement.textContent);
  counterElement.textContent = count + 1;
}, 1000);
```

**三、监听用户交互事件并做出响应**

当用户与页面进行交互时，立即做出响应，让用户感觉到页面是活跃的。

```javascript
document.addEventListener('mousemove', () => {
  // 当鼠标移动时，可以显示一个动画或者提示
  const tooltip = document.createElement('div');
  tooltip.textContent = '页面活跃中！';
  tooltip.style.position = 'absolute';
  tooltip.style.left = `${event.clientX}px`;
  tooltip.style.top = `${event.clientY}px`;
  document.body.appendChild(tooltip);
  setTimeout(() => {
    document.body.removeChild(tooltip);
  }, 2000);
});
```

**四、动态加载内容**

可以定期从服务器获取新的数据并动态加载到页面上，给用户一种页面在不断更新的感觉。

```javascript
function loadNewContent() {
  fetch('newContent.json')
   .then(response => response.json())
   .then(data => {
      const contentContainer = document.getElementById('contentContainer');
      data.forEach(item => {
        const newElement = document.createElement('p');
        newElement.textContent = item.text;
        contentContainer.appendChild(newElement);
      });
    });
}

setInterval(loadNewContent, 5000);
```

## js实现导出dom为pdf

在 JavaScript 中可以使用`html2pdf.js`库来实现将 DOM 元素导出为 PDF。以下是具体步骤：

1. 引入`html2pdf.js`库：
   ```html
   <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
   ```
2. 选择要导出为 PDF 的 DOM 元素并设置导出选项：
   ```javascript
   const elementToExport = document.getElementById('your-dom-element-id');
   const options = {
   margin: 10,
   filename: 'output.pdf',
   image: { type: 'jpeg', quality: 0.98 },
   html2canvas: { scale: 2 },
   jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' }
   };
   ```
3. 调用`html2pdf()`函数进行导出：
   ```javascript
   html2pdf().from(elementToExport).set(options).save();
   ```

在上述代码中，首先获取要导出为 PDF 的 DOM 元素，然后设置了导出 PDF 的各种选项，如页边距、文件名、图像质量、缩放比例等。最后调用`html2pdf()`函数并传入 DOM 元素和选项，进行导出并保存为 PDF 文件。

请注意，实际使用中可能需要根据具体情况调整选项和处理可能出现的错误。同时，不同的浏览器对于`html2pdf.js`的兼容性可能会有所不同。

### 追问：window.print(); 是什么含义？

1. **基本含义**
   - `window.print()`是JavaScript中的一个方法，它的主要作用是触发浏览器的打印功能。当在JavaScript代码中调用这个方法时，浏览器会弹出打印对话框，用户可以通过这个对话框选择打印机、打印范围、打印份数等参数，然后将当前页面或者指定的文档内容打印出来。
2. **使用场景示例**
   - **简单的网页打印**
     - 假设你有一个简单的网页，上面展示了一篇文章或者一份报告，当用户点击一个“打印”按钮时，就可以使用`window.print()`来实现打印功能。例如：
     ```html
     <!DOCTYPE html>
     <html>
     <body>
     <h1>这是一篇文章</h1>
     <p>文章内容......</p>
     <button onclick="window.print()">打印</button>
     </body>
     </html>
     ```
     - 在这个例子中，当用户点击“打印”按钮时，浏览器会弹出打印对话框，准备打印整个网页页面的内容。
   - **打印局部内容**
     - 有时候你可能不想打印整个网页，而是只想打印网页中的某一部分内容，比如一个表格或者一个特定的区域。可以通过一些CSS样式来控制打印内容。例如，给想要打印的内容区域设置一个特定的类名，然后在CSS中使用`@media print`规则来隐藏不需要打印的内容。
     ```html
     <!DOCTYPE html>
     <html>
     <body>
     <div id="print - content">
     <h1>这是要打印的内容标题</h1>
     <p>这是要打印的内容......</p>
     </div>
     <div id="not - print - content">
     <p>这部分内容不想打印，比如一些导航栏或者广告。</p>
     </div>
     <button onclick="window.print()">打印</button>
     <style>
     @media print {
      #not - print - content {
        display: none;
      }
     }
     </style>
     </body>
     </html>
     ```
     - 这样，当用户点击“打印”按钮调用`window.print()`时，只有`id`为`print - content`的区域会被打印出来。
3. **与其他打印相关的考虑因素**
   - **打印样式表**
     - 除了使用`@media print`来控制打印内容的显示，还可以专门为打印创建一个样式表（CSS文件）。这样可以更好地控制打印出来的文档的字体、字号、颜色、页面布局等。例如，可以在HTML文件的`<head>`部分添加一个链接指向打印样式表：
     ```html
     <link rel="stylesheet" type="text/css" media="print" href="print.css">
     ```
     - 在`print.css`文件中，可以设置各种打印相关的样式，如：
     ```css
     body {
     font - size: 12pt;
     font - family: Arial, sans - serif;
     }
     h1 {
     color: #000;
     text - align: center;
     }
     ```
   - **打印前的页面设置**
     - 虽然`window.print()`主要是触发打印对话框，但在一些高级应用场景中，可以通过JavaScript与浏览器的打印API进一步交互，来实现一些页面设置功能。不过这种跨浏览器的兼容性可能会比较复杂，因为不同浏览器对打印相关的API支持程度不同。例如，有些浏览器支持通过`window.matchMedia('print').addListener()`来监听打印事件，从而在打印之前进行一些自定义的操作，如修改页面布局或者隐藏某些元素。

### 追问：如果这个页面过长我能打印全部的内容吗？

1. **默认情况**
   - 当页面过长时，在大多数浏览器中，使用`window.print()`会尝试打印整个页面内容。浏览器会根据页面的实际长度生成多页的打印文档，以包含所有内容。不过，实际打印的内容可能会受到一些因素的影响，比如打印设置中的纸张大小、页边距等。
2. **CSS样式对打印内容的影响**
   - **分页控制**
     - 可以使用CSS的`page - break - before`、`page - break - after`和`page - break - inside`属性来控制打印内容的分页。例如，如果有一个很长的文章，并且希望每个章节都从新的一页开始打印，可以在每个章节的标题元素上设置`page - break - before: always`。
     ```css
     h2 {
     page - break - before: always;
     }
     ```
   - **内容隐藏或显示**
     - 如前面提到的，通过`@media print`规则可以在打印时隐藏或显示特定的内容。如果页面中有一些元素（如导航栏、广告等）在打印时不需要显示，可以这样设置：
     ```css
     @media print {
     nav,
     .advertisement {
     display: none;
     }
     }
     ```
   - **打印区域大小和缩放比例**
     - 浏览器在打印时可能会根据纸张大小和页面内容自动调整缩放比例。有些浏览器允许用户在打印对话框中手动调整缩放比例，以确保所有内容都能合适地打印在纸张上。同时，也可以在CSS中通过`@page`规则来设置打印区域的大小和方向等。例如：
     ```css
     @page {
     size: A4 portrait;
     margin: 2cm;
     }
     ```
     - 这将设置打印页面为A4纸大小、纵向方向，并设置了2厘米的页边距。这样的设置可以帮助更好地规划长页面内容的打印，避免内容被不合理地截断或排版混乱。
3. **可能遇到的问题及解决方法**
   - **内容截断问题**
     - 如果页面包含非常长的表格或图片等元素，可能会出现打印内容截断的情况。对于长表格，可以考虑使用`page - break - inside: avoid`来尽量避免表格在一页中间被截断。对于图片，可以通过调整其大小或者设置合适的缩放比例来确保完整打印。
   - **打印样式不一致问题**
     - 有时候，页面在屏幕上显示的样式和打印出来的样式可能会不一致。这可能是由于没有正确设置打印样式表或者某些CSS属性在打印环境中不受支持。需要仔细检查打印样式表中的设置，并在不同浏览器中进行测试和调整。

### 追问：window.print(); 打印长页面

1. **浏览器自动分页功能**
   - 当使用`window.print()`打印长页面时，浏览器通常会自动将内容分页。它会根据纸张大小（如A4、Letter等）、页边距以及内容的布局来确定分页位置。例如，如果你有一个包含大量文本的长网页，浏览器会将文本逐行排列，当一页排满后，会自动将剩余的内容放到下一页。
   - 但是，浏览器的自动分页可能并不总是能满足你的需求。有时候，可能会出现内容在不合适的位置被截断的情况，比如表格的行、列表的项或者图片被分割在两页之间。
2. **使用CSS控制分页**
   - **`page - break - before`和`page - break - after`属性**
     - 可以使用`page - break - before`和`page - break - after`属性来精确控制在哪些元素之前或者之后进行分页。例如，如果你希望某个标题（如`<h1>`或`<h2>`）总是在新的一页开始，可以这样设置CSS：
     ```css
     h1,
     h2 {
     page - break - before: always;
     }
     ```
     - 这将确保每个`<h1>`和`<h2>`元素都在新的一页的顶部打印。同样，`page - break - after`可以用于在某个元素之后强制分页。
   - **`page - break - inside`属性**
     - 为了避免内容在不合适的地方被截断，可以使用`page - break - inside`属性。例如，对于长表格，通常不希望表格行被分割在两页之间。可以设置如下CSS：
     ```css
     table {
     page - break - inside: avoid;
     }
     ```
     - 对于包含大量段落的长文章，也可以对`<p>`元素使用类似的设置，以尽量保持段落的完整性。
3. **打印样式表的优化**
   - **设置合适的字体和字号**
     - 在打印样式表中，考虑使用适合打印的字体和字号。一般来说，宋体、Times New Roman等字体在打印时比较清晰易读。同时，要注意字号不要过小，以免在打印后难以辨认。例如：
     ```css
     body {
     font - family: "Times New Roman", serif;
     font - size: 12pt;
     }
     ```
   - **调整边距和页面布局**
     - 通过`@page`规则可以调整打印页面的边距和布局。例如，设置上下左右边距为1.5厘米：
     ```css
     @page {
     margin - top: 1.5cm;
     margin - bottom: 1.5cm;
     margin - left: 1.5cm;
     margin - right: 1.5cm;
     }
     ```
     - 还可以设置页面的方向（如纵向或横向），如果长页面的内容在纵向排版时过于拥挤，可以尝试将页面方向设置为横向：
     ```css
     @page {
     size: A4 landscape;
     }
     ```
4. **打印前的预览和测试**
   - 在正式打印之前，最好先使用浏览器的打印预览功能。这样可以查看内容的分页情况、字体大小、布局等是否符合预期。不同的浏览器可能会有一些细微的打印差异，因此需要在多个主流浏览器（如Chrome、Firefox、Safari等）中进行测试和调整。
   - 如果发现内容在打印预览中有问题，可以根据上述的CSS属性和打印样式表的设置来进一步优化，直到得到满意的打印效果。

### 追问：我想打印指定dom：inject-inject-App-with-resume-with-navbar-resume-2……

以下是使用 JavaScript 和 CSS 来打印指定 DOM 元素的方法：

**一、使用 JavaScript 和 CSS 的基本思路**

1. 获取指定的 DOM 元素：通过其 ID 或其他选择器找到要打印的特定 DOM 元素。
2. 隐藏其他不必要的元素：为了只打印指定的元素，可以隐藏页面上其他不需要打印的元素。
3. 触发打印：使用 `window.print()` 方法触发打印功能。

**二、具体实现步骤**

1. 假设要打印的 DOM 元素的 ID 为 `inject-inject-App-with-resume-with-navbar-resume-2`。首先，使用 JavaScript 获取该元素：
   ```javascript
   const elementToPrint = document.getElementById('inject-inject-App-with-resume-with-navbar-resume-2');
   ```
2. 隐藏页面上其他元素：
   ```javascript
   const allElements = document.getElementsByTagName('*');
   for (let i = 0; i < allElements.length; i++) {
    if (allElements[i]!== elementToPrint) {
        allElements[i].style.display = 'none';
    }
   }
   ```
3. 触发打印：
   ```javascript
   window.print();
   ```
4. 打印完成后恢复页面显示：
   ```javascript
   window.onafterprint = function() {
    for (let i = 0; i < allElements.length; i++) {
        allElements[i].style.display = '';
    }
   };
   ```

**三、使用 CSS 进行优化**

可以使用 CSS 的 `@media print` 规则来进一步优化打印样式。例如：

```css
@media print {
    /* 隐藏不需要打印的元素 */
    body *:not(#inject-inject-App-with-resume-with-navbar-resume-2) {
        display: none;
    }
    /* 设置打印页面的样式 */
    body {
        font-size: 12pt;
        margin: 0;
    }
    #inject-inject-App-with-resume-with-navbar-resume-2 {
        /* 可以添加特定元素的打印样式 */
        background-color: white;
        color: black;
    }
}
```

这样，当触发打印时，浏览器会根据这些 CSS 规则来渲染打印页面，只显示指定的 DOM 元素，并应用特定的打印样式。

请注意，不同浏览器对打印功能的支持可能会有所不同，因此在实际应用中可能需要进行一些调整和测试。

### 追问：这个dom是超长元素可以全部打印下来吗？

1. **默认浏览器行为与问题**
   - 当使用上述方法打印超长的指定DOM元素时，浏览器会尝试按照其自动分页机制进行打印。不过，可能会遇到一些问题。例如，内容可能会在不合适的位置分页，如表格行被截断、文本块在段落中间分页等情况。
2. **利用CSS控制超长元素分页**
   - **`page - break - before/after/inside`属性**
     - 对于超长的文本段落，可以在段落标签（如`<p>`）或者标题标签（如`<h1>`、`<h2>`等）上设置`page - break - before`属性，确保它们从新的页面开始打印。例如：
     ```css
     p {
     page - break - before: avoid;
     }
     h2 {
     page - break - before: always;
     }
     ```
     - 对于表格，可以设置`page - break - inside: avoid`来防止表格在内部被截断。假设要打印的DOM元素中有表格：
     ```css
     #inject - inject - App - with - resume - with - navbar - resume - 2 table {
     page - break - inside: avoid;
     }
     ```
   - **调整元素布局和样式以适应打印**
     - 考虑超长元素中的图片、图表等元素。如果图片过大可能会导致分页问题，可以通过CSS设置图片的最大宽度或高度，使其能够更好地适应打印页面。例如：
     ```css
     #inject - inject - App - with - resume - with - navbar - resume - 2 img {
     max - width: 100%;
     max - height: 100%;
     }
     ```
     - 对于文本内容，可以调整字体大小、行间距等，使文本在打印页面上分布更合理。例如，设置合适的字体大小和行间距：
     ```css
     #inject - inject - App - with - resume - with - navbar - resume - 2 {
     font - size: 12pt;
     line - height: 1.5;
     }
     ```
3. **测试与优化**
   - 在不同的浏览器（如Chrome、Firefox、Safari等）中进行打印测试是很重要的。因为不同浏览器对于CSS分页属性的实现可能会有细微差异，可能需要根据实际测试结果进行进一步的调整。
   - 同时，可以结合浏览器的打印预览功能，观察超长元素的分页情况，检查是否有内容被截断或者布局不合理的地方，然后针对性地修改CSS打印样式，直到达到满意的打印效果，确保超长元素能够完整、合理地被打印出来。

### 追问：p { page - break - before: avoid; } h2 { page - break - befo……

1. **`page - break - before: avoid;`（针对`p`标签）**
   - **含义**：这个CSS属性用于控制打印时的分页行为。当设置`page - break - before: avoid;`在`p`（段落）标签上时，它的目的是尽量避免在段落之前进行分页。也就是说，浏览器在打印时会尽可能将整个段落放在同一页上，而不是让段落从新的一页开始打印，除非没有其他办法来避免分页（例如，段落所在位置已经到了一页的底部，放不下整个段落）。
   - **示例场景**：想象你有一篇很长的文章，由多个段落组成。如果没有这个属性，可能会出现一个段落的一部分在一页的底部，而另一部分在下一页的顶部。通过设置这个属性，浏览器会尽量把每个段落完整地放在一页中，这样可以提高文章在打印后的可读性。
2. **`page - break - before: always;`（针对`h2`标签）**
   - **含义**：当这个属性应用在`h2`（二级标题）标签上时，它表示在每个`h2`元素之前都要进行分页。这意味着，每当遇到一个`h2`标题，它都会强制从新的一页开始打印。
   - **示例场景**：在一个文档结构中，`h2`标题通常用于划分文档的不同部分或章节。通过设置`page - break - before: always;`，可以确保每个章节都从新的一页开始，使文档在打印后的结构更加清晰。例如，一份技术手册可能有多个章节，每个章节的标题是`h2`标签，这样打印出来后，每个章节都能独立地显示在新的一页上，方便阅读者浏览和参考不同的章节。

### 追问：height: 2738px; 我希望打印一个如此长度的html元素

如果要打印一个高度为 2738px 的 HTML 元素，可以考虑以下方法：

1. **设置打印样式**
   使用 CSS 的 `@media print` 规则来专门为打印设置样式，确保这个高元素在打印时能够完整显示并且布局合理。
   ```css
   @media print {
   /* 假设这个超长元素有一个特定的类名.long-element */
   .long-element {
    height: 2738px;
    /* 设置字体大小、颜色等以适应打印 */
    font-size: 12pt;
    color: black;
   }
   }
   ```
2. **处理分页问题**
   对于如此长的元素，可能会遇到分页问题。可以使用前面提到的分页属性来控制分页。
   ```css
   @media print {
   .long-element p {
    page-break-inside: avoid;
   }
   .long-element h2 {
    page-break-before: always;
   }
   }
   ```
   这将避免段落被截断在两页之间，并确保每个二级标题都从新的一页开始。
3. **触发打印**
   在 JavaScript 中，可以通过以下方式触发打印：
   ```javascript
   function printElement() {
   window.print();
   }
   ```
   可以在页面上添加一个按钮，当用户点击按钮时调用这个函数来触发打印。
4. **测试与调整**
   在不同的浏览器中进行打印测试，查看元素是否完整打印以及分页是否合理。根据测试结果调整 CSS 样式和分页设置，以确保达到最佳的打印效果。

请注意，不同浏览器对于打印的支持可能会有所不同，可能需要进行一些调整以适应不同的环境。

### 追问：<div style="height: 150px;width:2500px"/></div> 颜色调整为 红色

可以使用内联 CSS 或者外部 CSS 类来将这个 `<div>` 的颜色调整为红色。

**一、使用内联 CSS**

```html
<div style="height: 150px;width:2500px;background-color: red;"></div>
```

**二、使用外部 CSS 类**

1. 在 CSS 文件中或 `<style>` 标签内定义一个类：

```css
.red-div {
  height: 150px;
  width: 2500px;
  background-color: red;
}
```

2. 在 HTML 中应用这个类：

```html
<div class="red-div"></div>
```

### 追问：在react 的render函数中添加一个dom < !--startprint-->作为标记语言

在 React 中，如果你想在组件的渲染结果中添加特定的标记作为注释，可以通过以下方式在`render`函数中实现：

```jsx
import React from 'react';

class MyComponent extends React.Component {
  render() {
    return (
      <div>
        {/* Your normal component content */}
        {/* Add the comment as a string within JSX */}
        {'<!--startprint-->'}
      </div>
    );
  }
}

export default MyComponent;
```

这样，在组件渲染时，就会在输出的 HTML 结构中包含这个注释标记。需要注意的是，这只是一个字符串形式的注释添加方式，它并不是真正的 HTML 注释节点，但在最终生成的 HTML 中会以注释的形式出现。

### 追问：let prnhtml = bdhtml.substr(bdhtml.indexOf(sprnstr) + 17); 调……

以下是对这段代码进行分析并给出可能的调整建议：

这段 JavaScript 代码看起来是在从一个较大的 HTML 字符串（`bdhtml`）中提取一部分内容，从找到特定字符串（`sprnstr`）开始，取其后的部分并跳过了 17 个字符。

如果要调整其用法，可以考虑以下几点：

**一、明确变量含义和用途**

1. 首先确定 `bdhtml` 和 `sprnstr` 的具体含义和来源。了解这些变量代表什么以及它们在程序中的作用，有助于更好地理解这段代码的目的和进行调整。
2. 如果可能，给变量取更具描述性的名称，以提高代码的可读性。例如，如果 `bdhtml` 是一个包含 HTML 内容的字符串，可以命名为 `htmlContent`；如果 `sprnstr` 是一个特定的标记字符串，可以命名为 `markerString`。

**二、使用更现代的 JavaScript 方法**

1. 可以考虑使用更现代的字符串处理方法，如 `startsWith`、`includes` 和 `slice`，而不是依赖于 `indexOf` 和手动计算偏移量。例如，可以这样写：

```javascript
let htmlContent = '...';
let markerString = '...';
let startIndex = htmlContent.indexOf(markerString);
if (startIndex!== -1) {
  let prnhtml = htmlContent.slice(startIndex + markerString.length);
}
```

2. 如果要更灵活地处理，可以使用正则表达式来查找特定的模式，而不是依赖于固定的字符串查找。例如：

```javascript
let htmlContent = '...';
let regex = /yourMarkerPattern/;
let match = htmlContent.match(regex);
if (match) {
  // 根据需要处理匹配结果
}
```

**三、考虑错误处理**

1. 添加错误处理机制，以防止在找不到特定字符串时出现意外行为。例如，可以在代码中检查 `startIndex` 是否为 `-1`，并在这种情况下采取适当的行动，如返回一个默认值或抛出一个错误。

```javascript
let htmlContent = '...';
let markerString = '...';
let startIndex = htmlContent.indexOf(markerString);
let prnhtml = startIndex!== -1? htmlContent.slice(startIndex + markerString.length) : null;
if (prnhtml === null) {
  console.error('Marker string not found.');
}
```

通过以上调整，可以使代码更具可读性、可维护性，并适应不同的情况。具体的调整方式还需要根据代码的实际用途和上下文来确定。

### 追问：我的意思是直接用新的用法就行

以下是一种可能的新用法示例：

```javascript
const htmlContent = 'This is some content before the marker<!--startprint-->This is the content after the marker';
const markerString = '<!--startprint-->';

const startIndex = htmlContent.indexOf(markerString);
const resultContent = startIndex!== -1? htmlContent.slice(startIndex + markerString.length) : null;

console.log(resultContent);
```

在这个示例中，直接使用现代的字符串处理方法，通过`indexOf`找到标记字符串的位置，然后使用`slice`提取出标记字符串之后的内容。如果找不到标记字符串，则返回`null`。你可以根据实际情况调整代码中的变量和字符串内容。

### 追问：substr 旧版抛弃了，用新版的

在现代 JavaScript 中，可以使用 `substring` 或 `slice` 方法来替代旧版的 `substr`。以下是根据你提供的代码用新方法进行调整的示例：

```javascript
const bdhtml = 'This is some content before the marker<!--startprint-->This is the content after the marker';
const sprnstr = '<!--startprint-->';

const startIndex = bdhtml.indexOf(sprnstr);
let prnhtml;
if (startIndex!== -1) {
    // 如果使用 substring
    prnhtml = bdhtml.substring(startIndex + sprnstr.length);
    // 如果使用 slice
    // prnhtml = bdhtml.slice(startIndex + sprnstr.length);
} else {
    prnhtml = null;
}

console.log(prnhtml);
```

`substring` 和 `slice` 的区别在于：

- `substring(start, end)`：接受起始索引和结束索引，提取两个索引之间的子字符串。如果省略 `end` 参数，则提取从 `start` 到字符串末尾的子字符串。
- `slice(start, end)`：功能类似，但参数可以是负数，表示从字符串末尾开始计数的位置。

你可以根据具体需求选择使用其中一种方法。

## 前端面试2. 介绍下作用域链。 3. 介绍下事件循环。 4. 介绍下React中的虚拟DOM。 5. 介绍下React-……

2. **作用域链**

- 作用域是变量和函数的可访问范围。在JavaScript中，有全局作用域和函数作用域。作用域链是由当前环境与上层环境的一系列变量对象组成，它保证了对执行环境有权访问的所有变量和函数的有序访问。
- 例如，在一个函数内部访问变量时，会先在自身函数作用域找，找不到就沿着作用域链向外层（比如包含它的函数或者全局作用域）查找，直到找到变量或者到达全局作用域仍找不到报错为止。

3. **事件循环**

- JavaScript是单线程语言，但浏览器是多线程的。事件循环用于处理异步代码，像setTimeout、Promise等异步操作。它主要包含调用栈、消息队列。
- 同步任务在调用栈中按顺序执行。异步任务（如定时器、网络请求回调）完成后，其回调函数会被放入消息队列。当调用栈为空时，事件循环会将消息队列中的任务取出并放入调用栈执行。
- 比如，使用setTimeout设置一个延迟执行的函数，这个函数的回调会先被放入消息队列，等调用栈为空，才会被执行。

4. **React中的虚拟DOM**

- React虚拟DOM是真实DOM的JavaScript对象表示。它是一个轻量级的副本，有属性（props）和子节点（children）等。
- 当组件状态或属性改变时，React会先更新虚拟DOM，通过对比新旧虚拟DOM（Diff算法）来计算出最小的DOM更新操作，然后批量更新真实DOM，这样可以减少DOM操作，提高性能。
- 例如，一个列表组件渲染多个元素，更新其中一个元素的文本内容，React利用虚拟DOM可以精准找到这个元素对应的DOM节点更新，而不是重新渲染整个列表。

5. **React - Fiber**

- React - Fiber是React 16引入的新的协调引擎。它将渲染工作分成小的单元，使渲染过程可以中断和恢复。
- 链表结构是Fiber的重要特性。好处是可以灵活地遍历节点，方便暂停、恢复和重新开始渲染任务。它可以根据任务优先级安排更新，比如用户交互相关的高优先级更新可以先执行，而一些后台数据更新等低优先级任务可以稍后执行。

6. **React父子传值和非父子传值**

- **父子传值**：父组件通过属性（props）向子组件传递数据。子组件接收后可以在内部使用。例如，父组件有一个变量`message`，在子组件标签中通过`  <ChildComponent message={message} /> `传递，子组件通过`props.message`来获取使用。
- **非父子传值**：可以使用Context API，它提供了一种在组件树中共享数据的方式，避免了层层传递props。或者使用状态管理库如Redux、MobX，通过发布/订阅模式等方式来传递数据。

7. **React的key的作用**

- key是React用来识别列表中元素的唯一标识。当列表数据更新时，React通过key来确定哪些元素是新增的、删除的或者改变了顺序。
- 如果没有正确设置key或者key不唯一，可能会导致性能问题，如组件不必要的重新渲染，或者状态混乱。

8. **Nodejs的nextTick在什么时候执行**

- `process.nextTick()`会在当前执行栈的尾部，下一次事件循环的开始之前执行回调函数。它允许在当前操作完成后，在I/O事件等之前就执行一些任务。
- 例如，在一个I/O操作完成后的回调函数中使用`process.nextTick`，可以确保这个任务在I/O回调完成后，其他可能的I/O事件之前执行。

9. **Webpack**

- Webpack是一个模块打包工具。它可以处理多种模块（如JavaScript、CSS、图片等），将它们打包成浏览器可以理解的静态资源。
- 它通过配置文件（如`webpack.config.js`）来定义入口文件、输出文件路径、加载器（用于处理不同类型的模块，像`babel - loader`处理JavaScript新语法）和插件（如`HtmlWebpackPlugin`用于生成HTML文件）。

10. **前端缓存**

- **浏览器缓存**：包括强缓存和协商缓存。强缓存直接从本地缓存读取资源，不向服务器发送请求；协商缓存会向服务器发送请求，服务器根据资源状态码等来判断是否使用缓存。
- **Service Worker缓存**：可以拦截网络请求，实现离线缓存，能缓存页面、脚本、样式等多种资源，提升离线体验。
- **Memory Cache（内存缓存）**：存储在内存中的缓存，读取速度快，一般用于临时存储经常访问的资源。

11. **协商缓存和强缓存**

- **强缓存**：通过`Cache - Control`和`Expires`头部字段控制。如果缓存未过期，浏览器直接使用本地缓存，不会向服务器发送请求，资源加载速度快。
- **协商缓存**：当强缓存过期后，浏览器会向服务器发送请求，服务器通过`Last - Modified`/`If - Modified - Since`或者`ETag`/`If - None - Match`头部信息来判断资源是否更新。如果资源未更新，服务器返回304状态码，浏览器使用本地缓存；如果更新了，服务器返回新资源。

12. **http状态码**

- **1xx（信息性状态码）**：如101，表示协议切换。
- **2xx（成功状态码）**：最常见的是200，表示请求成功。201表示资源创建成功。
- **3xx（重定向状态码）**：301表示永久重定向，302表示临时重定向，304用于协商缓存。
- **4xx（客户端错误状态码）**：400表示客户端请求语法错误，401表示未授权，404表示资源未找到。
- **5xx（服务器端错误状态码）**：500表示服务器内部错误，502表示网关错误，503表示服务不可用。

13. **http消息的结构**

- **请求消息结构**：包括请求行（包含请求方法、请求URL、协议版本）、请求头部（包含各种请求相关的信息，如`User - Agent`、`Accept`等）和请求体（如POST请求发送的数据）。
- **响应消息结构**：包含状态行（协议版本、状态码、状态描述）、响应头部（如`Content - Type`等）和响应体（服务器返回的实际数据）。

14. **元素垂直居中**

- 可以使用`flex`布局，设置父元素为`display: flex; align - items: center;`。也可以使用`position`属性，结合`top`和`transform`属性来居中。
- 用`margin - top`来居中元素可能会出现问题。如果容器高度是由内容撑开的，设置`margin - top`会改变容器的高度计算，而且当窗口大小改变或者内容更新时，可能会导致布局混乱。因为`margin - top`本身是元素盒模型的一部分，会占据空间影响布局。

## canvas的原理和性能优化

### 原理

- Canvas是HTML5提供的用于在网页上绘制图形的标签。它通过JavaScript来绘制2D或3D图形。本质上，它在HTML页面中提供了一块画布，有自己的坐标系统，以像素为单位。
- 浏览器会维护一个绘图上下文（2D或3D），像2D上下文包含了一系列绘图方法，例如`fillRect`（绘制填充矩形）、`strokeRect`（绘制矩形边框）、`beginPath`（开启新路径）等。当执行这些JavaScript方法时，会在画布上相应位置绘制出图形。

### 性能优化

- **减少不必要的重绘**：尽量把多个绘图操作合并在一起，避免频繁地修改画布状态。比如，不要一个一个地画小图形，能用循环批量绘制就批量绘制。
- **合理设置画布大小**：如果画布太大，会占用过多内存，太小又可能导致图形模糊。并且，避免在动画过程中频繁改变画布大小，因为这会导致重绘。
- **使用离屏Canvas**：当有复杂的图形需要频繁绘制，可先在一个离屏的Canvas（不在页面上直接显示的画布）中绘制好，然后将其作为一个整体绘制到主Canvas上，减少复杂绘制对主Canvas的影响。
- **利用分层技术**：对于复杂场景，可以将图形分层，比如背景层、前景层等。当部分图形需要更新时，只重绘对应的层，不用重绘整个画布。

## electron的使用

### 简介

Electron是一个用于使用Web技术（HTML、CSS和JavaScript）构建跨平台桌面应用程序的框架。

### 基本使用步骤

### 安装

- 首先需要在项目目录下通过命令行安装Electron，可以使用`npm install electron`（如果使用yarn，则是`yarn add electron`）。

### 创建主进程文件

- 主进程是整个应用的入口点，一般在`main.js`文件中（文件名可以自定义）。在主进程中可以控制应用的生命周期，如创建窗口、处理系统事件等。例如：

```javascript
const { app, BrowserWindow } = require('electron');
function createWindow () {
    const win = new BrowserWindow({
        width: 800,
        height: 600
    });
    win.loadFile('index.html');
}
app.whenReady().then(() => {
    createWindow();
});
```

这段代码创建了一个宽800px、高600px的窗口，并加载`index.html`文件。

### 创建渲染进程文件

- 渲染进程主要负责界面的展示，就是普通的HTML、CSS和JavaScript文件。例如`index.html`文件可以是简单的网页内容，在其中可以使用JavaScript与主进程通信，也可以使用前端框架来构建复杂的界面。

### 打包应用

- 开发完成后可以使用`electron-packager`或`electron-builder`等工具将应用打包成不同平台（Windows、Mac、Linux）的安装文件。

### 进程间通信

- 主进程和渲染进程之间需要通信。可以通过`ipcMain`（主进程）和`ipcRenderer`（渲染进程）模块来实现。例如，在主进程中可以监听渲染进程发送的消息：

```javascript
const { ipcMain } = require('electron');
ipcMain.on('message-from-renderer', (event, arg) => {
    console.log(arg);
    // 可以向渲染进程回复消息
    event.reply('message-from-main', '我收到你的消息啦');
});
```

在渲染进程中发送消息：

```javascript
const { ipcRenderer } = require('electron');
ipcRenderer.send('message-from-renderer', '这是来自渲染进程的消息');
ipcRenderer.on('message-from-main', (event, arg) => {
    console.log(arg);
});
```

## requestanimationframe，的用途

`requestAnimationFrame`是一个浏览器提供的JavaScript函数，主要用于高效地实现动画。

它会在浏览器下一次重绘之前调用指定的回调函数来更新动画。和`setTimeout`或`setInterval`相比，`requestAnimationFrame`能更好地跟浏览器的刷新频率同步，避免过度绘制，使动画更加流畅自然。例如，在实现一个简单的网页动画，像移动元素位置、改变元素大小或者渐变效果等场景中，可以使用`requestAnimationFrame`来逐帧更新画面，减少卡顿现象。

### 追问：她具体的执行时机我还是没理解

`requestAnimationFrame`的执行时机和浏览器的重绘时机紧密相关。

浏览器有自己的刷新频率，通常是每秒60次左右（也就是60Hz）。浏览器会在每次重绘之前，去执行`requestAnimationFrame`安排的回调函数。这就好比一场演出，舞台（浏览器页面）每次要更新画面（重绘）之前，演员（`requestAnimationFrame`中的回调函数）就开始表演（执行代码来更新动画状态）。

当你调用`requestAnimationFrame`函数并传入一个回调函数时，浏览器会把这个回调函数放入一个执行队列。当浏览器准备重绘下一帧画面时，就会从这个队列中取出回调函数并执行，而且这个执行时机是在页面样式更新和布局计算之后，浏览器真正进行绘制之前。这样能保证动画的更新是基于最新的页面布局和样式状态，使得动画的连贯性更好。

## webcomponent是什么

Web Components是一套浏览器原生的组件化技术，用于创建可复用的自定义HTML元素，主要包括以下几个部分：

### 1. 自定义元素（Custom Elements）

- 开发者可以通过JavaScript来定义全新的HTML标签。例如，定义一个名为`<my - custom - element>`的标签：

```javascript
class MyCustomElement extends HTMLElement {
    constructor() {
        super();
        // 在这里可以进行元素的初始化操作，如添加Shadow DOM等
    }
}
customElements.define('my-custom-element', MyCustomElement);
```

- 定义后，就可以在HTML文档中像使用普通HTML标签一样使用这个自定义标签`<my - custom - element>`，这使得代码的结构更加清晰，并且能够更好地实现功能封装。

### 2. 影子DOM（Shadow DOM）

- 影子DOM允许将一个隐藏的、独立的DOM树附加到一个元素上。这就好比在一个元素内部创建了一个“小世界”，它的样式和结构不会影响到外部的DOM。
- 例如，在一个自定义元素内部使用影子DOM：

```javascript
class MyCustomElement extends HTMLElement {
    constructor() {
        super();
        const shadow = this.attachShadow({ mode: 'open' });
        const p = document.createElement('p');
        p.textContent = 'This is inside the Shadow DOM';
        shadow.appendChild(p);
    }
}
customElements.define('my-custom-element', MyCustomElement);
```

- 这样，`<my - custom - element>`元素内部的`<p>`标签内容被封装在影子DOM中，外部的CSS样式等通常不会干扰到它。

### 3. HTML模板（HTML Templates）

- HTML模板提供了一种定义HTML片段的方式，这些片段在页面加载时不会被渲染，而是可以在需要的时候被克隆和使用。
- 例如：

```html
<template id="my-template">
    <div>
        <p>Template content</p>
    </div>
</template>
```

- 可以通过JavaScript来获取并克隆这个模板，用于在自定义元素或者其他地方使用，比如：

```javascript
const template = document.getElementById('my-template');
const clone = template.content.cloneNode(true);
```

- Web Components的这些技术综合起来，能够让开发者构建更加模块化、可复用的前端组件，提高代码的可维护性和复用性，并且因为是浏览器原生支持，性能上也有一定的优势。

### 追问：Web Components 与 Vue 或 React 组件有何不同？

Web Components和Vue、React组件有以下不同：

### 1. 技术本质与标准支持

- **Web Components**
  - 它是浏览器原生技术标准，这意味着不需要额外引入大型的框架库。浏览器会原生支持自定义元素、影子DOM和HTML模板这些功能，代码更加轻量化。
  - 例如，自定义元素的定义和使用只要浏览器支持，就可以直接运行，具有很好的兼容性和独立性。
- **Vue和React组件**
  - Vue和React是第三方框架提供的组件化方案。需要引入相应的框架库，如`vue.js`或`react.js`及其相关依赖。
  - 它们是基于JavaScript的框架，通过自己的一套语法和规则来实现组件化，如Vue的单文件组件（`.vue`文件）和React的`JSX`语法。

### 2. 样式和作用域

- **Web Components（影子DOM）**
  - 影子DOM提供了真正的样式封装。在影子DOM内部定义的样式只在影子DOM范围内有效，外部样式很难干扰到内部，除非特意设置穿透样式。这就像一个独立的小世界，保证了组件样式的独立性。
  - 例如，在一个自定义元素的影子DOM中定义的`h1`标签的样式，不会和外部文档中的`h1`标签样式相互影响。
- **Vue和React组件**
  - 在Vue和React中，虽然也有一些手段来隔离组件样式，如Vue的`scoped`属性和React的CSS Modules等，但这些方法并没有像影子DOM那样严格的样式隔离。
  - 例如，在Vue中使用`scoped`样式，虽然可以在一定程度上避免样式冲突，但在一些复杂的场景下（如动态添加样式类），还是可能会出现样式泄露的情况。

### 3. 数据绑定和响应式原理

- **Web Components**
  - 本身没有像Vue和React那样内置的数据绑定和响应式系统。如果要实现数据的响应式变化，需要开发者自己编写代码，通常是通过事件监听和手动更新DOM来实现。
  - 例如，当一个自定义元素中的数据发生变化，需要手动获取元素，更新其属性或者内部DOM结构来反映变化。
- **Vue和React组件**
  - 它们都有强大的内置数据绑定和响应式机制。
  - 在Vue中，通过`Object.defineProperty`（Vue 3中也结合`Proxy`）进行数据劫持，实现数据的双向绑定。当数据变化时，会自动更新与之相关的DOM。
  - 在React中，虽然是单向数据绑定，但通过`setState`或者`useState`等方式更新状态后，会重新渲染组件，更新DOM（通过虚拟DOM的`Diff`算法）来反映变化。

### 4. 组件生命周期管理

- **Web Components**
  - 有自己相对简单的生命周期，如`connectedCallback`（元素插入到DOM时）、`disconnectedCallback`（元素从DOM移除时）等。这些生命周期主要围绕元素与DOM的连接和断开，相对比较直接。
  - 例如，在`connectedCallback`中可以进行一些初始化操作，如添加事件监听器；在`disconnectedCallback`中可以进行清理操作，如移除事件监听器。
- **Vue和React组件**
  - 它们有更复杂的生命周期管理。
  - 在Vue中，有`beforeCreate`、`created`、`mounted`、`updated`、`beforeDestroy`等多个生命周期钩子，用于处理组件从创建、挂载、更新到销毁的各个阶段。
  - 在React中，有`componentDidMount`、`componentWillUnmount`、`useEffect`等生命周期相关的方法和钩子，用于管理组件的状态变化和副作用。

## 浏览器中的回流和重绘，在vue中哪些操作会触发

在Vue中，以下操作可能会触发浏览器的回流和重绘：

### 改变元素尺寸和位置

- 修改元素的`width`、`height`、`margin`、`padding`、`left`、`top`等CSS属性，或使用`v-bind`指令绑定这些属性并改变其绑定值，会使浏览器重新计算元素的几何属性，触发回流，同时也会触发重绘。
- 通过`ref`获取DOM元素并直接修改其相关尺寸和位置属性，如`this.$refs.myElement.style.width = '200px'`，会直接改变元素的几何信息，导致回流和重绘。

### 改变元素布局

- 使用`v-if`或`v-show`指令切换元素的显示状态，`v-if`会完全添加或移除元素，`v-show`会改变`display`属性，这都会影响页面布局，触发回流和重绘。
- 动态改变`flex`布局的`flex`属性值，或`grid`布局的相关属性值，会导致浏览器重新计算布局，触发回流和重绘。

### 操作DOM元素内容

- 用`v-html`指令更新HTML内容，或使用`v-text`更新文本内容，当内容变化导致元素尺寸或布局改变时，会触发回流和重绘。
- 通过`ref`获取DOM元素并使用`innerHTML`、`textContent`等属性修改其内容，可能会使元素尺寸或布局变化，触发回流和重绘。

### 响应式数据更新

- 当响应式数据变化引起绑定元素的样式、内容或布局改变时，可能触发回流和重绘。比如，数据变化导致列表项增加或减少，引起页面布局调整。

### 浏览器窗口尺寸变化

- 在Vue组件中通过`window.addEventListener('resize', () => {})`监听窗口大小变化事件，当窗口尺寸改变时，浏览器会重新计算页面布局，触发回流和重绘。

### 追问：回流和重绘分别触发的情况

回流和重绘是浏览器渲染页面时的两个重要过程，它们触发的情况有所不同：

### 回流触发情况

- **页面首次渲染**：浏览器首次加载页面时，需要计算所有元素的几何位置和布局，必然会触发回流。
- **元素尺寸或位置改变**：改变元素的`width`、`height`、`margin`、`padding`、`left`、`top`等影响几何属性的CSS值，会使浏览器重新计算元素的位置和大小，触发回流。
- **添加或删除可见DOM元素**：使用`v-if`指令添加或移除元素，或者通过JavaScript动态添加、删除DOM节点，会引起页面布局变化，触发回流。
- **字体大小改变**：改变网页中字体的大小，会影响文本的尺寸和布局，进而触发回流。
- **浏览器窗口尺寸改变**：当用户调整浏览器窗口大小时，页面中的元素需要重新计算布局以适应新的窗口尺寸，会触发回流。

### 重绘触发情况

- **元素外观改变**：改变元素的`color`、`background-color`、`border-style`、`visibility`等不影响布局的外观属性时，只会触发重绘，因为不需要重新计算元素的几何位置和布局。
- **文字样式改变**：修改文字的`text-decoration`、`text-shadow`等样式，只影响文字的外观，会触发重绘。
- **阴影效果改变**：改变元素的`box-shadow`或`text-shadow`属性，会使元素的阴影外观发生变化，触发重绘。
- **滤镜效果改变**：对元素应用或修改`filter`属性，如`blur()`、`brightness()`等滤镜，会改变元素的视觉效果，触发重绘。

### 追问：vif和vshow区别

`v-if`和`v-show`都是Vue中用于控制元素显示与隐藏的指令，它们的区别如下：

### 原理

- `v-if`：通过条件判断，在DOM渲染时，根据表达式的真假值决定是否将元素渲染到DOM树中。为假时，元素及其所有子元素不会存在于DOM中；为真时，才会被创建并插入到DOM树。
- `v-show`：无论表达式真假，元素都会被渲染到DOM树中，只是通过CSS的`display`属性来控制元素的显示与隐藏，`display`的值根据表达式真假切换为`none`或原本的值。

### 性能

- `v-if`：有更高的切换开销，因为涉及DOM的创建和销毁。在初始渲染时，如果条件为假，就不会渲染元素，可节省初始渲染的开销。适合在运行时很少改变条件、不需要频繁切换显示状态的场景。
- `v-show`：有更高的初始渲染开销，因为无论条件如何，元素都会被渲染，只是通过CSS控制显示。切换开销较小，因为只是修改CSS属性。适合需要频繁切换显示状态的场景。

### 应用场景

- `v-if`：适用于在不同条件下展示完全不同的内容，如根据用户权限显示不同的菜单，权限不满足时，相关菜单DOM结构就不会存在。也适用于一些初始渲染时根据条件决定是否渲染的情况，如加载完成后根据数据判断是否显示提示信息。
- `v-show`：常用于简单的显示隐藏切换，如点击按钮显示或隐藏搜索框等，在页面初始化时就希望所有元素都存在于DOM中，只是根据条件控制显示与否。

### 过渡效果

- `v-if`：可以与`<transition>`组件结合，实现元素在创建和销毁过程中的过渡动画效果，如淡入淡出、滑动等。
- `v-show`：使用过渡效果时，由于元素始终存在于DOM中，过渡效果只作用于`display`属性的切换，部分CSS过渡效果可能无法完美实现或需特殊处理。

### 追问：他们是一个触发回流一个触发重绘对吗？

是的，`v-if`和`v-show`在控制元素显示与隐藏时，触发的浏览器行为有所不同，`v-if`一般会触发回流和重绘，`v-show`通常只触发重绘。

`v-if`由于会真正添加或移除DOM元素，这会导致页面布局发生变化，浏览器需要重新计算元素的几何位置和布局，所以会触发回流，同时元素的显示状态改变也会触发重绘。

`v-show`只是通过改变CSS的`display`属性来控制元素的显示与隐藏，不影响元素的几何位置和布局，浏览器只需要根据新的`display`属性值重新绘制元素的可见性，所以一般只触发重绘。

### 追问：浏览器重绘和回流操作的实现原理，为什么要这么做

浏览器重绘和回流是其渲染机制的重要组成部分，原理及原因如下：

### 重绘

- **原理**：浏览器构建渲染树后，根据渲染树和样式信息绘制各节点外观，形成页面像素点。当元素外观属性如`color`、`background-color`等改变时，浏览器无需重新计算布局，仅根据新样式重新绘制对应元素的像素点，更新屏幕上的显示效果，此即重绘。
- **原因**：为了高效更新页面可视部分。页面中常存在仅需更新外观而不影响布局的情况，如按钮悬停变色。通过重绘，浏览器可在不改变整体布局的基础上快速更新元素外观，提升用户体验且节省计算资源。

### 回流

- **原理**：浏览器根据HTML构建DOM树，结合CSS构建渲染树，计算各元素几何位置和布局，将其转换为屏幕上的像素点进行绘制。当页面结构或元素尺寸、位置等几何属性改变时，浏览器需重新计算相关元素的几何信息，更新渲染树，并重新绘制页面，这个过程就是回流。
- **原因**：为了确保页面布局的准确性和一致性。页面元素的几何属性变化会影响其自身及周围元素的位置和大小，回流能使浏览器重新计算并调整布局，保证页面在各种变化下都能正确显示，符合预期的设计和用户交互需求，如窗口大小改变时，元素需重新布局以适应新空间。

### 追问：浏览器是如何监听这些变化呢？

浏览器主要通过以下几种方式来监听回流和重绘相关的变化：

### 事件监听

- **用户交互事件**：浏览器可监听`resize`、`scroll`、`mousedown`、`mouseup`等用户操作触发的事件。当用户调整窗口大小、滚动页面或进行鼠标点击等操作时，会触发相应事件，浏览器可借此得知页面可能需要回流或重绘。
- **DOM操作事件**：对DOM元素进行添加、删除、修改等操作时，会触发`DOMNodeInserted`、`DOMNodeRemoved`、`DOMAttrModified`等事件，浏览器通过监听这些事件，能检测到DOM结构或属性的变化，进而判断是否需要进行回流或重绘。

### 突变观察者（Mutation Observer）

- 这是HTML5提供的API，可监听DOM树的变化，包括节点的添加、删除、属性修改等。创建`MutationObserver`实例并配置回调函数，当DOM发生指定类型的变化时，回调函数会被触发，浏览器可在回调函数中执行相应逻辑，判断是否需要触发回流或重绘。

### 样式计算与布局算法

- 浏览器在渲染页面时，会定期检查CSS样式表和DOM元素的样式属性，计算元素的最终样式。当发现元素样式属性发生变化且可能影响布局或外观时，会标记该元素及其相关元素需进行回流或重绘。
- 浏览器有布局算法，如CSS盒模型算法、Flex布局算法等，在计算元素布局时，若发现元素的几何属性或布局相关属性改变，会触发回流，并根据需要决定是否进行重绘。

### 硬件相关监听

- 对于移动端设备，浏览器可监听设备的方向变化事件，如`orientationchange`，当设备方向改变时，浏览器可据此判断页面布局可能需要调整，从而触发回流和重绘。
- 某些硬件设备可能支持触摸事件或压力感应等，浏览器通过监听这些硬件相关事件，也能得知页面元素可能需要更新外观或布局，进而触发回流或重绘。

### 追问：他又是如何定位这些操作具体位置呢？还是全部遍历一遍？

浏览器并非简单地全部遍历一遍来定位回流和重绘操作的具体位置，而是采用了多种高效的策略和技术：

### 基于渲染树的标记与追踪

- 浏览器构建渲染树时，每个节点都包含了与DOM元素相对应的信息及自身的布局和样式等数据。当某个元素的属性变化可能触发回流或重绘时，浏览器会标记该渲染树节点。
- 从被标记的节点开始，通过渲染树的结构关系，向上或向下追踪相关节点。如改变子元素的尺寸可能影响父元素布局，浏览器会从子节点向上找到父节点等受影响的节点，确定需进行回流或重绘的范围。

### 依赖关系记录与更新

- 浏览器会记录元素之间的布局依赖关系，如弹性布局中元素的尺寸和位置依赖于父容器及其他兄弟元素。当一个元素变化时，浏览器可根据记录的依赖关系，快速定位到受影响的其他元素，而无需遍历整个页面。
- 对于CSS样式，浏览器会分析样式规则，建立样式依赖关系图。当样式属性改变时，依据关系图找到应用该样式的元素及受其影响的其他元素，确定重绘或回流的具体位置。

### 区域划分与局部更新

- 浏览器将页面划分为多个区域，如根据CSS的`viewport`、`z-index`等属性划分不同的层叠上下文区域。当某个区域内的元素发生变化时，优先在该区域内进行检查和更新，限制查找范围，减少不必要的遍历。
- 利用`requestAnimationFrame`等API，浏览器可在每一帧渲染前，收集需要更新的元素列表，只对列表中的元素及其相关元素进行回流和重绘操作，提高定位和更新效率。

### 缓存与优化策略

- 浏览器会缓存元素的布局和样式信息，当元素属性未发生实质变化时，直接使用缓存信息，避免重复计算和遍历。
- 采用启发式算法和优化策略，如根据常见的布局模式和变化规律，预测可能受影响的元素位置，优先检查和更新这些元素，减少全局遍历的次数。

## 在webpack或者vite打包中，他们如何保证要打包的依赖的顺序是正确的

在webpack和vite打包中，保证依赖顺序正确的方式有所不同，具体如下：

### webpack

- **依赖图构建**：webpack从入口文件开始，使用静态分析来构建依赖图，遍历模块之间的`import`和`require`语句，以确定所有依赖关系。
- **模块排序**：根据依赖图，webpack会对模块进行拓扑排序。确保在处理一个模块之前，其所有依赖模块都已经被处理。在这个过程中，webpack会遵循模块之间的依赖关系，按照一定的顺序将模块添加到打包结果中。

### vite

- **依赖预构建**：vite在开发阶段会对依赖进行预构建。它会分析项目中的依赖关系，将依赖项转换为一个优化后的格式，以提高开发服务器的启动速度和模块热更新的性能。在预构建过程中，会根据依赖的关系确定一个合理的顺序。
- **模块解析**：在构建过程中，vite使用`esbuild`进行快速的ES模块解析和转换。`esbuild`会按照ES模块的规范来处理模块之间的导入和导出关系，保证模块在打包过程中的顺序正确。同时，vite会根据项目的配置和依赖关系，对模块进行优化和整合，确保最终的打包结果中依赖顺序正确，并且能够在浏览器中正确运行。

虽然两者的具体实现方式不同，但都通过分析依赖关系和遵循一定的规则来保证要打包的依赖顺序正确，以确保最终的打包结果能够正确运行。

### 追问：以webpack为例，讲解依赖构建顺序原理

webpack构建依赖顺序的原理主要基于以下几个方面：

### 依赖图生成

- webpack从配置文件中指定的入口模块开始，使用静态分析方法，解析模块中的`import`、`require`等语句，找出该模块的所有依赖模块。然后递归地处理这些依赖模块，构建出整个项目的依赖关系图，这个图反映了各个模块之间的依赖关系。

### 模块标识与解析

- 为每个模块分配唯一的标识，通常是模块的路径或经过某种哈希处理后的字符串。在解析模块时，webpack会根据模块的标识和解析规则，确定模块的实际路径和加载方式。对于不同类型的模块（如JavaScript、CSS、JSON等），有不同的解析器来处理。

### 拓扑排序

- 基于依赖图，webpack使用拓扑排序算法来确定模块的构建顺序。拓扑排序的目的是将有向无环图中的节点排成一个线性序列，使得对于图中的任意一条有向边`(u, v)`，节点`u`在序列中都排在节点`v`的前面。这样就保证了在处理一个模块之前，其所有依赖模块都已经被处理。

### 优化与合并

- 在确定模块顺序后，webpack会根据配置进行优化和合并。例如，将多个小的模块合并成一个较大的模块，以减少浏览器的请求次数；根据模块的引用频率和大小等因素，对模块进行分组和按需加载，提高页面的加载性能。

通过以上步骤，webpack能够保证依赖构建顺序正确，生成优化的打包文件，使项目在浏览器中能够正确运行。

## nodelist是什么

`NodeList`是一种类数组对象，它包含了一组节点，通常是由DOM查询操作返回的结果。以下是关于`NodeList`的详细介绍：

### 特点

- **类似数组但不是数组**：`NodeList`具有长度属性，可以通过索引访问元素，但它没有数组的一些方法，如`map`、`filter`等。不过可以使用`Array.from()`方法将其转换为真正的数组来使用这些方法。
- **实时性**：在某些情况下，`NodeList`是实时的。如果文档中的节点树发生变化，`NodeList`会自动更新以反映这些变化。例如，通过`document.getElementsByTagName()`获取的`NodeList`会随着新元素的添加或现有元素的移除而动态更新。

### 常见获取方式

- **通过`getElementsByTagName`方法**：`document.getElementsByTagName('tagName')`会返回一个包含所有指定标签名元素的`NodeList`。例如，`document.getElementsByTagName('p')`将返回文档中所有`<p>`元素的`NodeList`。
- **通过`getElementsByClassName`方法**：`document.getElementsByClassName('className')`会返回一个包含所有具有指定类名元素的`NodeList`。
- **通过`querySelectorAll`方法**：`document.querySelectorAll('selector')`会返回一个包含所有匹配指定选择器的元素的`NodeList`。例如，`document.querySelectorAll('div.content')`将返回所有具有`content`类的`<div>`元素的`NodeList`。

### 使用示例

```javascript
// 获取所有<p>元素的NodeList
let pNodes = document.getElementsByTagName('p');
// 遍历NodeList并输出每个<p>元素的文本内容
for (let i = 0; i < pNodes.length; i++) {
    console.log(pNodes[i].textContent);
}
// 将NodeList转换为数组并使用数组方法
let pArray = Array.from(pNodes);
let lengths = pArray.map(node => node.textContent.length);
console.log(lengths);
```

## js中treeshaking和codespliting在哪个阶段进行

在JavaScript中，Tree Shaking和Code Splitting通常在**构建阶段**进行。

### Tree Shaking

- 它是一种优化技术，用于消除未使用的代码。在构建过程中，构建工具（如Webpack、Rollup等）会静态分析代码的导入和导出关系，确定哪些模块的代码在项目中没有被引用，然后将这些未引用的代码从最终的打包结果中剔除，以减小打包文件的体积。

### Code Splitting

- 它也是构建阶段的一项优化措施。Code Splitting将代码分割成多个较小的块，这些块可以按需加载，而不是将所有代码都打包在一个文件中。这样可以提高页面的加载性能，因为浏览器可以根据需要只加载当前所需的代码块，而不是一次性加载整个应用程序的所有代码。在构建过程中，构建工具会根据配置和代码中的动态导入等指示，将代码拆分成不同的块，并生成相应的加载逻辑。

### 追问：如何配置

以Webpack为例，以下是Tree Shaking和Code Splitting的基本配置方法：

### Tree Shaking配置

1. 确保项目使用ES6模块系统（import和export），因为Tree Shaking依赖于ES6模块的静态结构分析。
2. 在Webpack配置文件中，设置`mode`为`production`，在生产模式下，Webpack默认会开启Tree Shaking。
3. 对于使用的第三方库，确保它们也是采用ES6模块编写且支持Tree Shaking。如果某些库不支持，可能需要进行额外处理，如使用`sideEffects`配置来告知Webpack哪些文件有副作用，不能被Tree Shaking掉。例如：

```javascript
module.exports = {
  // 其他配置项...
  optimization: {
    sideEffects: true
  }
};
```

### Code Splitting配置

1. 利用动态导入（dynamic import）来实现代码分割。在需要进行代码分割的地方，使用`import()`函数动态导入模块。例如：

```javascript
button.addEventListener('click', () => {
  import('./module.js')
  .then(module => module.doSomething())
  .catch(error => console.error(error));
});
```

2. 在Webpack配置文件中，可以使用`optimization.splitChunks`来配置代码分割的策略。例如，将公共模块提取出来，形成单独的chunk：

```javascript
module.exports = {
  // 其他配置项...
  optimization: {
    splitChunks: {
      chunks: 'all',
      minSize: 30000,
      maxSize: 0,
      minChunks: 1,
      maxAsyncRequests: 5,
      maxInitialRequests: 3,
      automaticNameDelimiter: '~',
      name: true,
      cacheGroups: {
        vendors: {
          test: /[\\/]node_modules[\\/]/,
          priority: -10
        },
        default: {
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true
        }
      }
    }
  }
};
```

上述配置中，`chunks: 'all'`表示对所有类型的chunk进行分割，`cacheGroups`定义了如何将模块分组到不同的代码块中。

请注意，实际配置可能需要根据项目的具体情况进行调整和优化。

## Code Splitting方式

在JavaScript中，Code Splitting主要有以下几种方式：

### 动态导入（Dynamic Imports）

- **描述**：通过`import()`函数动态加载模块，在需要时才加载相应的代码，实现按需加载。
- **示例**：`button.addEventListener('click', () => { import('./module.js').then(module => module.doSomething()); });`

### 路由懒加载（Route - based Lazy Loading）

- **描述**：在单页应用中，根据路由切换来加载相应页面的代码。只有当用户访问特定路由时，才会加载该路由对应的组件和相关模块。
- **示例**：在Vue Router中，可以这样配置：

```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/about',
      component: () => import('./views/About.vue')
    }
  ]
});
```

### Webpack的`splitChunks`配置

- **描述**：通过配置`optimization.splitChunks`，Webpack可以将公共模块提取出来，生成单独的代码块，避免重复加载。
- **示例**：

```javascript
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendors: {
          test: /[\\/]node_modules[\\/]/,
          priority: -10
        },
        default: {
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true
        }
      }
    }
  }
};
```

### 手动代码分割（Manual Code Splitting）

- **描述**：开发者手动将代码拆分成不同的文件和模块，然后通过HTML页面中的`<script>`标签按需加载。
- **示例**：在HTML中：

```html
<script src="main.js"></script>
<script src="anotherModule.js"></script>
```

在`main.js`中可以通过`import()`动态加载`anotherModule.js`中的内容。

不同的Code Splitting方式适用于不同的场景，开发者可以根据项目的具体需求和特点选择合适的方式。

### 追问：代码分割原理

代码分割的原理主要是分析代码的依赖关系，将代码拆分成多个可独立加载的模块或代码块，以实现按需加载，提高加载性能。以下是其原理的具体介绍：

### 依赖分析

构建工具会对项目中的代码进行静态分析，通过解析`import`、`require`等语句，梳理出模块之间的依赖关系，形成依赖图。比如在一个JavaScript项目中，若`moduleA`导入了`moduleB`和`moduleC`，构建工具就能识别出这种依赖关系。

### 代码拆分

- **基于入口点**：对于多页面应用，每个页面的入口文件就是一个天然的分割点，构建工具会为每个入口点及其依赖构建一个单独的代码块。
- **公共模块提取**：构建工具会扫描整个依赖图，找出多个模块中共同依赖的部分，将这些公共模块提取出来，形成单独的代码块，避免重复加载。
- **动态导入**：当代码中使用`import()`函数进行动态导入时，构建工具会将被导入的模块作为一个单独的代码块。在运行时，当执行到`import()`语句时，才会加载该代码块。

### 加载优化

- **按需加载**：代码分割后，浏览器可以根据页面的实际需求，在需要某个功能时才加载对应的代码块，而不是一开始就加载所有代码，从而提高页面的初始加载速度。
- **并行加载**：多个代码块可以在浏览器中并行加载，充分利用浏览器的并发加载能力，进一步提高加载效率。

通过代码分割，可以将大型的JavaScript应用程序拆分成更小、更可管理的模块，优化资源加载，提升用户体验。

## 在浏览器中并发加载代码模块有哪几种方式

在浏览器中，并发加载代码模块主要有以下几种方式：

### 使用`<script>`标签并行加载

- **描述**：在HTML页面中，通过多个`<script>`标签来引入不同的代码模块，浏览器会按照顺序并行加载这些脚本。
- **示例**：

```html
<script src="module1.js"></script>
<script src="module2.js"></script>
```

### 动态创建`<script>`标签

- **描述**：使用JavaScript动态创建`<script>`元素，并设置其`src`属性来加载代码模块。这样可以在需要时按需加载模块，并且多个动态创建的`<script>`标签也会并发加载。
- **示例**：

```javascript
const script1 = document.createElement('script');
script1.src = 'module1.js';
document.head.appendChild(script1);

const script2 = document.createElement('script');
script2.src = 'module2.js';
document.head.appendChild(script2);
```

### 使用`import()`函数动态导入

- **描述**：`import()`函数返回一个`Promise`，可以同时发起多个`import()`调用，浏览器会并发加载这些模块。
- **示例**：

```javascript
Promise.all([
  import('./module1.js'),
  import('./module2.js')
]).then(results => {
  // 使用加载后的模块
});
```

### 使用`import.meta.glob()`

- **描述**：这是一个用于批量导入模块的方法，它会返回一个对象，键是模块的路径，值是对应的`import`函数。可以通过遍历这个对象来并发加载多个模块。
- **示例**：

```javascript
const modules = import.meta.glob('./modules/*.js');
const promises = Object.values(modules).map(module => module());
Promise.all(promises).then(results => {
  // 使用加载后的模块
});
```

以上方法可以根据实际需求选择使用，以实现代码模块在浏览器中的并发加载，提高页面的加载性能。

### 追问：能够同时支持多少并发数

浏览器能够同时支持的并发数受多种因素影响，包括浏览器类型、HTTP版本以及是否针对同一域名请求等。具体情况如下：

- **同一域名下**：在HTTP/1.x协议下，主流浏览器对同一域名的并发连接数限制通常为6 - 8个。例如，谷歌浏览器（Chrome）在HTTP/1.1协议下，对同一域名的最大并发请求数通常为6个。而在HTTP/2协议下，由于支持多路复用，Chrome浏览器允许同时发出更多的同源请求，通常可以达到100个以上。
- **不同域名下**：浏览器对同一域名的并发请求数有限制，但可以通过将资源分散到不同的域名下来绕过这一限制，从而实现更高的并发数。不过，过多的域名会导致DNS查询增加，可能反而影响性能。

需要注意的是，这些并发数限制并不是绝对固定的，可能会因浏览器版本、操作系统以及具体的网络环境等因素而有所不同。

## webworker可以让js在加载大文件的时候同时操作页面的css元素吗

Web Worker可以让JavaScript在加载大文件的时候同时操作页面的CSS元素。

### 原理

Web Worker是HTML5提供的一个API，它允许在后台线程中执行脚本，不影响主线程（包括页面的渲染和交互）。当使用Web Worker加载大文件时，文件加载操作在后台线程进行，而主线程可以继续处理其他任务，包括操作页面的CSS元素。

### 示例代码

下面是一个简单的示例，展示了如何使用Web Worker在加载大文件时更新页面的CSS样式：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <style>
        .box {
            width: 100px;
            height: 100px;
            background-color: red;
        }
    </style>
</head>

<body>
    <div class="box"></div>
    <script>
        // 创建一个Web Worker
        const worker = new Worker('worker.js');

        // 向Web Worker发送消息，开始加载大文件
        worker.postMessage('loadLargeFile');

        // 监听Web Worker发送的消息
        worker.onmessage = function (event) {
            if (event.data === 'fileLoaded') {
                // 文件加载完成后，更新页面的CSS样式
                document.querySelector('.box').style.backgroundColor = 'blue';
            }
        };
    </script>
</body>

</html>
```

在上述代码中，创建了一个Web Worker，并向其发送消息来加载大文件。当文件加载完成后，Web Worker发送一个消息给主线程，主线程接收到消息后更新了页面中`div`元素的背景颜色。

### 注意事项

虽然Web Worker可以实现加载大文件和操作CSS元素同时进行，但Web Worker不能直接访问DOM，它通过与主线程通信来间接影响DOM操作。

### 追问：WebWorker的限制是什么

WebWorker存在以下一些限制：

- **无法直接访问DOM**：WebWorker不能直接操作DOM元素，也无法访问浏览器的窗口、文档等对象，只能通过与主线程通信来间接影响DOM。
- **有限的全局对象访问**：WebWorker中没有完整的浏览器全局对象，如`window`、`document`等，只有部分与Worker相关的全局对象和API，如`self`、`navigator`（部分属性）等。
- **通信机制的复杂性**：与主线程的通信通过消息传递机制实现，这增加了编程的复杂性，需要谨慎处理消息的发送和接收，确保数据的正确传递和处理。
- **同源限制**：WebWorker只能加载与主页面同源的脚本，不能加载跨域的脚本，这限制了其对外部资源的访问。
- **不支持某些API**：一些浏览器特定的API，如`alert()`、`confirm()`等弹出框函数，在WebWorker中不被支持。同时，WebWorker也不能直接访问`localStorage`、`sessionStorage`等本地存储，需要通过主线程来间接访问。
- **内存限制**：WebWorker的内存使用受到浏览器的限制，不同浏览器的限制不同。如果WebWorker使用过多内存，可能会导致性能下降甚至浏览器崩溃。

### 追问：我希望在加载大文件的同时WebWorker执行css样式的修改

要在Web Worker加载大文件的同时执行CSS样式的修改，由于Web Worker不能直接操作DOM，所以需要通过与主线程通信来实现。以下是一个示例：

### HTML文件

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <style>
    .box {
      width: 100px;
      height: 100px;
      background-color: red;
    }
  </style>
</head>

<body>
  <div class="box"></div>
  <script>
    // 创建一个Web Worker
    const worker = new Worker('worker.js');

    // 向Web Worker发送消息，开始加载大文件
    worker.postMessage('loadLargeFile');

    // 监听Web Worker发送的消息
    worker.onmessage = function (event) {
      if (event.data === 'fileLoaded') {
        // 文件加载完成后，更新页面的CSS样式
        document.querySelector('.box').style.backgroundColor = 'blue';
      }
    };
  </script>
</body>

</html>
```

### Web Worker文件（worker.js）

```javascript
// 模拟加载大文件的函数
function loadLargeFile() {
  // 这里可以是实际的文件加载逻辑，例如使用fetch等
  // 为了演示，这里使用setTimeout模拟加载过程
  setTimeout(() => {
    // 加载完成后，向主线程发送消息
    self.postMessage('fileLoaded');
  }, 3000);
}

// 监听主线程发送的消息
self.onmessage = function (event) {
  if (event.data === 'loadLargeFile') {
    // 接收到消息后，开始加载大文件
    loadLargeFile();
  }
};
```

在上述代码中，HTML页面中创建了一个Web Worker，并向其发送消息让它开始加载大文件。Web Worker中的`loadLargeFile`函数模拟了大文件的加载过程，加载完成后向主线程发送消息。主线程接收到消息后，更新了页面中`div`元素的背景颜色。

## vite中的Rollup是什么

在Vite中，Rollup是一个关键的底层工具。以下是关于它的介绍：

### 基本概念

Rollup是一个模块打包器，它将多个JavaScript模块打包成一个或多个 bundles，以优化项目在浏览器中的加载和执行性能。

### 在Vite中的作用

- **模块打包**：Vite利用Rollup将项目中的各种模块，如ES模块、CommonJS模块等，按照一定的规则和优化策略打包成浏览器可识别的文件。例如，将多个小的JavaScript文件合并成一个较大的bundle，减少浏览器的请求次数。
- **优化与转换**：Rollup在打包过程中会进行一系列的优化，如代码压缩、Tree - shaking（摇树优化，去除未使用的代码）等。它还能将现代JavaScript语法转换为兼容旧版浏览器的语法，确保项目在不同环境中能正常运行。
- **插件系统**：Rollup拥有丰富的插件系统，Vite通过集成Rollup插件来扩展其功能。例如，使用插件可以支持对非JavaScript文件（如CSS、JSON等）的处理，以及对特定框架（如React、Vue等）的支持。

### 工作原理

Rollup首先会分析项目中的模块依赖关系，构建出一个依赖图。然后，根据配置和插件的设置，对模块进行处理和优化。最后，将处理后的模块输出为指定格式的bundle文件。在Vite中，Rollup的工作流程与Vite的开发服务器和构建流程紧密结合，为开发者提供了高效、快速的开发和构建体验。

## webpack和vite的区别

webpack和Vite都是前端构建工具，它们的区别如下：

### 构建原理

- **webpack**：基于“一切皆模块”的理念，将项目中的各种资源（如JavaScript、CSS、图片等）都视为模块，通过Loader和Plugin对模块进行处理和转换，最终打包成一个或多个可在浏览器中运行的bundle文件。
- **Vite**：在开发阶段利用浏览器对ES模块的原生支持，采用“按需加载”的方式，直接将ES模块提供给浏览器，同时利用插件对代码进行转换和处理。在生产环境中，Vite使用Rollup进行打包，以实现代码的优化和压缩。

### 开发体验

- **webpack**：随着项目规模的增大，构建时间可能会变长，特别是在启动开发服务器时，需要先对整个项目进行打包，这可能会导致较长的等待时间。
- **Vite**：开发服务器启动速度快，因为它不需要像webpack那样在启动时进行大量的打包工作。同时，Vite提供了快速的模块热更新（HMR）功能，使得开发过程中的代码更新更加高效。

### 性能优化

- **webpack**：通过各种插件和配置选项，可以实现代码压缩、Tree - shaking、代码分割等优化功能，但配置相对复杂，需要开发者对相关技术有深入的了解。
- **Vite**：在生产环境中利用Rollup的优化能力，自动实现代码压缩、Tree - shaking等优化，配置相对简单。而且Vite在开发阶段的按需加载机制，也有助于提高页面的加载性能。

### 适用场景

- **webpack**：适用于各种规模和类型的前端项目，特别是对于大型复杂项目，其强大的配置能力和丰富的插件生态可以满足各种个性化的需求。
- **Vite**：适用于现代前端项目，尤其是以ES模块为基础的项目，对于开发体验和性能要求较高的项目，Vite能发挥出其优势。同时，Vite对Vue、React等框架有良好的支持。

### 追问：在实际项目中，如何选择使用Webpack还是Vite？

在实际项目中，选择使用Webpack还是Vite可以考虑以下几个方面：

### 项目类型和规模

- **小型项目**：如果是简单的小型项目，如个人博客、小型静态网站等，Vite是不错的选择。其快速的开发服务器启动速度和简洁的配置能提高开发效率，且能很好地支持现代前端技术。
- **大型复杂项目**：对于大型企业级应用，尤其是有复杂的模块依赖和大量的定制化需求，Webpack可能更合适。它的高度可配置性和丰富的插件生态能应对各种复杂情况。

### 技术栈和框架

- **特定框架支持**：Vite对Vue和React等框架有良好的支持，特别是Vue 3项目，使用Vite能充分发挥其性能优势。如果项目使用这些框架，且希望有更好的开发体验，Vite是个好选择。
- **旧有技术栈**：如果项目基于一些旧的技术栈或有对Webpack深度定制的历史，继续使用Webpack可能更稳妥，因为迁移到Vite可能需要较多的成本。

### 开发体验和性能要求

- **快速开发迭代**：如果团队注重开发过程中的快速反馈和热更新体验，Vite的快速模块热更新（HMR）和高效的开发服务器能满足需求，让开发更加流畅。
- **极致性能优化**：在生产环境中，两者都能实现性能优化，但Webpack的优化配置相对复杂，需要开发者有一定的经验。Vite则相对简单，利用Rollup在生产环境进行优化，能自动实现一些常见的优化功能。

### 团队技术栈和经验

- **已有Webpack经验**：如果团队成员对Webpack有丰富的经验和深入的了解，且项目没有特别的性能瓶颈，继续使用Webpack可以充分发挥团队的技术优势，减少学习成本。
- **追求新技术**：若团队希望尝试新的技术和工具，且对开发体验和性能有较高要求，愿意投入时间学习，那么Vite是值得尝试的，它能带来一些新的开发模式和性能提升。

### 追问：vite中有polyfill过程吗

Vite中有polyfill过程。

Vite本身在处理代码转换时主要专注于语法转换，默认情况下不会自动对所有JavaScript API进行polyfill。但可以通过一些配置和插件来实现polyfill功能。

例如，使用`@vitejs/plugin-legacy`插件可以为老旧浏览器提供支持。该插件会自动生成传统版本的代码chunk，并加载相关polyfill文件。它会根据配置的目标浏览器，利用`@babel/preset-env`等工具进行代码转换和polyfill注入，生成包含`SystemJS`运行时和其他必需polyfill的包，以支持老旧浏览器的特性。

此外，在一些特定场景下，如使用到Node.js的全局变量或模块时，Vite也需要进行polyfill。例如，当项目中使用了`process`等在浏览器环境中不存在的变量时，可以通过`@esbuild - plugins/node - globals - polyfill`等插件来进行polyfill。

## 有一个数量在一万左右的无序长列表，每个文本信息对应他的索引值，，使用js实现，点击一个列表项，获得其文字中的索引值

以下是一个使用 JavaScript 实现的示例代码，假设列表是一个 HTML 的 `<ul>` 元素，每个列表项 `<li>` 包含文本信息，文本信息中包含对应的索引值。

```html
<!DOCTYPE html>
<html lang="zh-CN">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>获取列表项索引值</title>
</head>

<body>
  <ul id="myList">
    <!-- 这里假设动态生成列表项，以下是示例 -->
    <li data-index="1">文本信息 1</li>
    <li data-index="2">文本信息 2</li>
    <li data-index="3">文本信息 3</li>
  </ul>
  <script>
    // 获取列表元素
    const list = document.getElementById('myList');

    // 为列表项添加点击事件监听器
    list.addEventListener('click', function (event) {
      if (event.target.tagName === 'LI') {
        // 获取索引值
        const index = event.target.dataset.index;
        console.log('点击的列表项索引值为:', index);
      }
    });
  </script>
</body>

</html>
```

在上述代码中：

1. 首先通过 `document.getElementById` 获取到列表元素。
2. 然后为列表元素添加 `click` 事件监听器。
3. 当点击列表项（`<li>` 元素）时，通过 `event.target.dataset.index` 获取到在 `data-index` 属性中设置的索引值，并在控制台输出。

如果你的列表数据是在 JavaScript 中生成的，可以按照类似的方式为每个列表项添加 `data-index` 属性来存储索引值，并添加点击事件处理逻辑。

### 追问：但是这个列表很长啊，可以直接放监听事件吗

对于很长的列表，直接在每个列表项上放监听事件是可行的，但可能会导致性能问题，因为大量的事件监听器会占用较多内存和处理时间。更好的方法是采用事件委托，将事件监听器添加到父元素上，利用事件冒泡机制来处理子元素的点击事件，就像上述代码中所做的那样。这样无论列表有多长，都只需要一个事件监听器，能有效提高性能。

此外，还可以对列表进行虚拟滚动优化，只渲染当前可见区域的列表项，当用户滚动时再动态加载新的列表项，这也能提升长列表的性能和响应速度。不过虚拟滚动的实现相对复杂，通常需要借助一些专门的库，如 `react - virtualized`（用于React项目）或 `vue - virtual - scroll - list`（用于Vue项目）等。

### 追问：事件冒泡机制指的是？

事件冒泡机制是指当一个元素上的事件被触发时，该事件会首先在当前元素上执行，然后依次向上传播到其父元素，直到到达文档的根节点。

例如，在一个包含多个嵌套元素的HTML结构中，如 `<div>` 包含 `<p>`，`<p>` 又包含 `<span>`。当点击 `<span>` 时，点击事件会先在 `<span>` 上触发，然后冒泡到 `<p>`，再到 `<div>`，如果这些元素都有对应的点击事件处理函数，那么它们都会被依次执行。这种机制可以让开发者在父元素上统一监听和处理子元素的事件，而不必为每个子元素都单独添加事件监听器，从而实现事件的集中管理，提高代码的可维护性和性能。

## document对象和dom对象分别是什么

在JavaScript中，`document`对象和`DOM`对象是两个重要的概念，以下是对它们的介绍：

### document对象

- `document`对象是`window`对象的一个属性，代表当前加载的网页文档。它提供了对网页内容和结构的访问入口，是操作网页的核心对象。通过`document`对象，可以获取或修改网页的标题、元数据、样式表，以及创建、查找和操作网页中的元素等。例如，`document.title`可以获取或设置网页的标题，`document.createElement()`方法可以创建一个新的元素节点。

### DOM对象

- `DOM`（Document Object Model）即文档对象模型，它将网页文档视为一个由节点组成的树状结构，每个节点都对应着网页中的一个元素、文本或属性等。`DOM`对象是对这些节点的抽象表示，通过`DOM`对象，开发人员可以使用编程语言（如JavaScript）来动态地访问、修改、添加或删除网页的内容和结构，响应用户操作和事件等。比如，可以通过`getElementById`等方法获取特定的`DOM`元素对象，然后对其进行样式修改、属性设置或事件绑定等操作。

`document`对象是访问`DOM`的入口，而`DOM`对象则代表了网页中具体的元素和节点，通过它们，开发人员能够实现对网页的动态控制和交互。

## 如果我前端打开页面直接卡死，就是直接死掉，怎么查看问题，怎么解决

当前端页面打开直接卡死时，可以通过以下方法查看问题和解决：

### 查看问题

- **检查浏览器开发者工具**：在浏览器中按F12打开开发者工具，切换到“控制台”选项卡，查看是否有报错信息，报错信息通常会提示代码中出现问题的位置和原因。
- **分析页面加载过程**：在开发者工具的“网络”选项卡中，查看页面资源的加载情况，检查是否有某个资源加载时间过长或加载失败，可能是导致页面卡死的原因。
- **检查代码逻辑**：回顾近期对前端代码的修改，特别是涉及到页面初始化、事件绑定、数据渲染等部分的代码，看是否存在死循环、大量数据处理不当等问题。

### 解决问题

- **优化代码**：根据报错信息和代码分析，对有问题的代码进行修改。如避免在页面加载时进行大量复杂的计算或数据处理，优化事件绑定，防止过多的事件触发导致性能问题。
- **检查资源引用**：确保页面引用的CSS、JavaScript、图片等资源路径正确，且资源文件大小合理。对于过大的资源文件，考虑进行压缩或优化，以减少加载时间。
- **考虑浏览器兼容性**：某些CSS样式或JavaScript API在不同浏览器上可能有不同的表现，导致页面卡死。可以使用浏览器兼容性工具进行检查，并针对不同浏览器进行相应的代码调整。

如果上述方法无法解决问题，可以尝试更新浏览器版本、清除浏览器缓存，或者寻求专业的前端开发人员的帮助。

## 他可以存在localstorage中吗

Cookie**不可以直接存在**`localStorage`中。

`localStorage`是HTML5提供的一种在客户端存储数据的机制，它与Cookie是不同的存储方式。Cookie有自己独立的存储和管理机制，主要用于在客户端和服务器之间传递少量数据，比如用户登录状态、购物车信息等。而`localStorage`主要用于在本地存储较大容量的数据，且不会随每次HTTP请求发送到服务器。虽然两者都可用于在客户端存储数据，但它们是相互独立的，Cookie不会自动存储到`localStorage`中，不过可以通过编程将Cookie中的数据提取出来再存储到`localStorage`中。

### 追问：如果我这样做有什么问题呢

将Cookie数据存储到`localStorage`中可能会带来以下一些问题：

### 安全性问题

- **跨站脚本攻击（XSS）风险**：`localStorage`中的数据容易受到XSS攻击。如果攻击者能够注入恶意脚本，就可能获取`localStorage`中的数据，包括原本存储在Cookie中的敏感信息，如用户登录凭证等，进而导致用户账号被盗用或其他安全问题。而Cookie可以通过设置`HttpOnly`属性来防止被客户端脚本访问，降低XSS攻击风险，`localStorage`则没有这样的保护机制。
- **数据泄露风险**：`localStorage`中的数据在同一域名下的不同页面之间共享，且持久化存储。如果应用程序存在漏洞，可能导致数据被其他非授权的页面或脚本访问和泄露。Cookie可以通过设置不同的作用域和过期时间来更精细地控制数据的访问和生命周期。

### 性能问题

- **过多数据存储**：`localStorage`有一定的存储容量限制，不同浏览器的限制不同，一般在5MB到10MB左右。如果将大量Cookie数据存储到`localStorage`中，可能会超出限制，导致存储失败或影响应用程序的性能。而Cookie的大小通常限制在4KB左右，单个域名下的Cookie数量也有限制，相对来说更适合存储少量关键数据。
- **数据读取性能**：`localStorage`中的数据是字符串类型，当存储复杂数据结构时，需要进行序列化和反序列化操作，这可能会影响数据的读取和写入性能。Cookie则通常只存储简单的键值对，相对来说操作更简单。

### 兼容性问题

- **浏览器兼容性**：虽然`localStorage`在现代浏览器中得到了广泛支持，但在一些旧版本的浏览器或某些特殊环境下，可能存在兼容性问题。而Cookie的兼容性更好，几乎所有的浏览器都支持Cookie。
- **跨平台兼容性**：在不同的操作系统和设备上，`localStorage`的表现可能会有所不同。例如，在移动设备上，由于存储空间和性能限制，`localStorage`的使用可能需要更加谨慎。Cookie在不同平台上的行为相对较为一致。

## .Last-Modify和Etag怎么实现的

`Last - Modified`和`ETag`是用于实现缓存机制和验证资源是否更新的HTTP头字段，以下是它们的实现方式：

### Last - Modified

- **服务器设置**：服务器在响应请求时，会在响应头中添加`Last - Modified`字段，其值为资源最后修改的时间戳。例如，在Python的Flask框架中，可以这样设置：

```python
from flask import Flask, make_response
from datetime import datetime

app = Flask(__name__)

@app.route('/')
def index():
    # 获取当前时间作为资源最后修改时间
    last_modified_time = datetime.now()
    response = make_response('This is the index page.')
    # 设置Last - Modified头字段
    response.headers['Last - Modified'] = last_modified_time.strftime('%a, %d %b %Y %H:%M:%S GMT')
    return response

if __name__ == '__main__':
    app.run(debug=True)
```

- **客户端验证**：客户端再次请求相同资源时，会在请求头中添加`If - Modified - Since`字段，其值为上次响应中`Last - Modified`的值。服务器接收到请求后，会比较`If - Modified - Since`的值与资源实际最后修改时间。如果资源未修改，服务器返回`304 Not Modified`状态码，客户端可以使用缓存的资源；如果资源已修改，服务器则返回最新的资源及`200 OK`状态码。

### ETag

- **服务器设置**：服务器根据资源的内容生成一个唯一的标识符，作为`ETag`的值。这个标识符可以是哈希值、版本号等。例如，在Django框架中，可以这样设置：

```python
from django.http import HttpResponse
from hashlib import md5

def my_view(request):
    # 假设resource_content是资源的内容
    resource_content = b'This is the content of the resource.'
    # 计算ETag
    etag = md5(resource_content).hexdigest()
    response = HttpResponse(resource_content)
    # 设置ETag头字段
    response['ETag'] = etag
    return response
```

- **客户端验证**：客户端再次请求时，会在请求头中添加`If - None - Match`字段，其值为上次响应中的`ETag`。服务器比较`If - None - Match`的值与当前资源的`ETag`。若两者相同，服务器返回`304 Not Modified`；否则，返回最新资源及`200 OK`状态码。

实际应用中，`Last - Modified`和`ETag`可结合使用，以提供更准确和灵活的缓存验证机制。

## 讲讲RenderTree和Dom Tree的区别

RenderTree和DOM Tree是网页渲染过程中的两个重要概念，它们的区别如下：

### 定义与作用

- **DOM Tree**：是根据HTML文档结构创建的树形数据结构，代表了页面的逻辑结构。它将HTML中的标签、属性和文本内容解析为节点，如`<html>`、`<body>`、`<div>`等标签都是DOM Tree中的节点，用于文档的结构表示和操作，如JavaScript可以通过DOM API对其进行增删改查等操作。
- **RenderTree**：是浏览器根据DOM Tree和CSS样式计算生成的树形结构，用于描述页面的视觉呈现。它只包含需要显示的节点，如`<head>`标签及其内部的元数据等不会在RenderTree中出现，它决定了页面上每个元素的大小、位置和样式等，最终用于将页面绘制到屏幕上。

### 节点组成

- **DOM Tree**：包含文档中所有的HTML元素节点，以及文本节点、属性节点等。例如，一个包含多个`<p>`标签和`<img>`标签的HTML页面，这些标签都会作为DOM Tree的节点存在，即使是隐藏的元素也会在DOM Tree中有对应的节点。
- **RenderTree**：只包含可视元素的节点。比如，设置了`display:none`样式的元素不会出现在RenderTree中，而`visibility:hidden`的元素虽然不可见但仍会在RenderTree中占据空间，有对应的节点。

### 生成过程

- **DOM Tree**：浏览器在解析HTML文档时，会按照从左到右、从上到下的顺序依次扫描HTML标签，将其转换为对应的DOM节点，并根据HTML的嵌套结构构建树形关系。
- **RenderTree**：在DOM Tree生成后，浏览器会根据CSS样式对DOM节点进行样式计算，确定每个元素的最终样式。然后，从根节点开始，遍历DOM Tree，将可见的节点及其样式信息组合成RenderTree。

### 对页面的影响

- **DOM Tree**：主要影响页面的逻辑和交互。当通过JavaScript修改DOM Tree时，可能会触发重排（重新计算元素的位置和大小）或重绘（重新绘制元素的外观），但不一定会立即影响到页面的视觉呈现，因为浏览器可能会批量处理这些操作。
- **RenderTree**：直接决定页面的视觉展示。当RenderTree中的节点发生变化，如元素的位置、大小、颜色等样式改变，会直接导致页面的重排或重绘，用户会立即看到页面的视觉变化。

## 前端如何监控用户有没有白屏了，如果白屏连js都下载不了了呢

以下是一些前端监控用户是否白屏的方法，即使在js下载不了的情况下也能起到一定作用：

### 利用浏览器的Performance API

可以在页面加载时使用`performance.timing`来记录关键时间点，通过判断`loadEventEnd`与`navigationStart`等时间差值是否过大，来初步判断是否出现白屏。如果页面长时间没有完成加载，可能存在白屏问题。示例代码如下：

```javascript
window.addEventListener('load', function() {
    const timing = performance.timing;
    const loadTime = timing.loadEventEnd - timing.navigationStart;
    if (loadTime > 5000) { // 假设5秒以上为异常
        console.log('可能出现白屏');
    }
});
```

### 检测页面元素

通过检测页面中是否有正常显示的关键元素来判断。可以在页面加载完成后，检查页面的根元素或一些重要的标志性元素是否存在且可见。示例代码如下：

```javascript
window.addEventListener('load', function() {
    const mainElement = document.getElementById('main'); // 假设main是页面的主要元素
    if (!mainElement || mainElement.offsetWidth === 0 || mainElement.offsetHeight === 0) {
        console.log('页面可能白屏');
    }
});
```

### 心跳检测

在页面中定时发送心跳请求，例如每隔一定时间（如5秒）向服务器发送一个简单的请求，服务器正常响应则说明页面在正常运行。如果长时间没有收到心跳响应，可能是页面出现了白屏或其他异常。

### 第三方监控工具

使用专业的前端监控工具，如Sentry、New Relic等，它们可以提供更全面的性能监控和错误报告，帮助检测白屏等问题。这些工具通常会有多种监控方式，包括上述的一些方法，并且能在后台进行数据分析和展示。

如果js都下载不了，上述基于js的方法可能会失效。此时，可以通过服务器端的日志来查看请求的状态，是否有错误返回，以及资源是否正常加载。同时，也可以结合用户的反馈来发现白屏问题，并进一步排查是网络、服务器还是页面代码等方面的原因。

### 追问：performance api详细讲解下

`Performance API`是浏览器提供的用于测量和分析网页性能的工具，以下是其详细讲解：

### 基本概念

`Performance API`提供了一系列的属性和方法，让开发者能够获取有关页面加载、资源加载、脚本执行等方面的详细时间信息，从而帮助分析性能瓶颈。

### 主要属性和方法

- **`performance.timing`**：包含了一系列与页面加载相关的时间戳，例如`navigationStart`表示浏览器开始导航到当前页面的时间，`domContentLoadedEventEnd`表示`DOM`内容加载完成的时间，`loadEventEnd`表示页面完全加载的时间等。通过计算这些时间戳之间的差值，可以得到各个阶段的耗时。
- **`performance.now()`**：返回一个高精度的时间戳，它表示从页面开始加载到当前时刻的毫秒数，与`Date.now()`不同，`performance.now()`的精度更高，适合用于测量短时间间隔。
- **`performance.getEntries()`**：返回一个包含所有性能条目（如资源加载、脚本执行等）的数组，可以通过遍历这个数组来获取每个条目的详细信息，如`name`（资源名称）、`entryType`（条目类型）、`duration`（持续时间）等。
- **`performance.mark()`**：允许开发者在代码中标记特定的时间点，然后使用`performance.measure()`来测量两个标记之间的时间间隔。例如，可以在某个函数执行前后分别标记，以测量该函数的执行时间。

### 使用场景

- **性能分析**：通过分析`Performance API`提供的时间数据，开发者可以找出页面加载过程中的瓶颈，如哪些资源加载时间过长，`DOM`渲染耗时多少等，从而有针对性地进行优化。
- **用户体验优化**：了解页面在不同阶段的加载时间，有助于优化页面的交互体验，例如在页面加载完成前显示加载动画，或者根据资源加载情况进行懒加载等。
- **性能监测**：可以在生产环境中使用`Performance API`来监测页面的性能指标，及时发现性能问题并进行处理，还可以将这些数据上报到服务器，以便进行长期的性能分析和趋势跟踪。

### 追问：如何使用Performance API来监控页面的白屏情况？

可以通过`Performance API`的`performance.timing`属性来监控页面白屏情况，以下是具体步骤：

### 记录关键时间节点

在页面加载过程中，使用`performance.timing`记录关键时间点，如`navigationStart`（浏览器开始导航到当前页面的时间）和`loadEventEnd`（页面完全加载的时间）。

### 计算加载时间

通过计算`loadEventEnd`与`navigationStart`的时间差值来判断页面加载是否异常。如果这个差值超过了一定的阈值（如5秒），则可能出现了白屏。示例代码如下：

```javascript
window.addEventListener('load', function() {
    const timing = performance.timing;
    const loadTime = timing.loadEventEnd - timing.navigationStart;
    if (loadTime > 5000) {
        console.log('可能出现白屏');
    }
});
```

### 结合其他指标判断

除了加载时间，还可以结合`domContentLoadedEventEnd`（`DOM`内容加载完成的时间）等其他时间指标来更准确地判断白屏情况。如果`DOM`加载时间过长，而页面又没有正常显示内容，也可能是白屏。例如：

```javascript
window.addEventListener('load', function() {
    const timing = performance.timing;
    const domLoadTime = timing.domContentLoadedEventEnd - timing.navigationStart;
    if (domLoadTime > 3000 && document.body.childElementCount === 0) {
        console.log('可能出现白屏');
    }
});
```

上述代码中，当`DOM`加载时间超过3秒且页面`body`中没有子元素时，认为可能出现白屏。

通过`Performance API`可以获取页面加载的相关时间信息，结合合理的阈值和判断条件，能够有效地监控页面是否出现白屏情况。

## 我在js监听keyup的时候，如果是按键换起了系统应用，keyup回调无效，如何处理

在 JavaScript 中监听 `keyup` 事件时，若按键触发了系统应用（如浏览器自带的搜索、系统快捷键打开程序等），由于事件可能被系统拦截，导致 `keyup` 回调无效。这种情况可通过以下方式处理：

### 1. 优先使用 `keydown` 而非 `keyup`

- `keydown` 事件在按键按下时立即触发，通常早于系统应用的拦截动作，更难被系统忽略。
- 若业务逻辑允许（如检测按键按下即可，无需等待松开），可替换为 `keydown` 监听。

### 2. 阻止事件默认行为（针对可拦截的系统行为）

- 对部分由浏览器或网页触发的系统行为（如 `F5` 刷新、`Ctrl+S` 保存等），可通过 `event.preventDefault()` 阻止默认动作，确保事件能正常传递到 `keyup`。
- 示例：```javascript
  document.addEventListener('keydown', (e) => {
  // 阻止 F5 刷新的默认行为
  if (e.key === 'F5') {
    e.preventDefault();
  }
  });
  document.addEventListener('keyup', (e) => {
  if (e.key === 'F5') {
    console.log('F5 松开（已阻止刷新）'); // 此时可触发
  }
  });
  ```

### 3. 针对完全被系统拦截的情况（如系统级快捷键）

- 若按键触发的是操作系统级应用（如 `Win+R` 打开运行窗口、`Alt+Tab` 切换程序等），JavaScript 无法拦截或监听，`keyup` 必然失效。
- 解决方案：通过业务逻辑规避，例如：
  - 提示用户避免使用此类快捷键。
  - 改用其他非系统快捷键（如自定义组合键 `Ctrl+Shift+X`）。
  - 结合定时器检测按键状态（精度较低，不推荐）。

### 总结

- 优先尝试 `keydown` 或阻止默认行为，处理浏览器级别的拦截。
- 系统级快捷键无法通过 JS 监听，需从业务设计上规避。

### 追问：那如何在keydown下监听

在 `keydown` 事件中监听按键操作，核心是通过绑定事件监听器，获取事件对象（`event`）中的按键信息（如按键名称、修饰键等），从而执行对应逻辑。以下是具体实现方式：

### 1. 基本监听方式

通过 `addEventListener` 给目标元素（如 `document`、`input` 等）绑定 `keydown` 事件，示例：

```javascript
// 监听整个文档的 keydown 事件
document.addEventListener('keydown', (event) => {
  // event 对象包含按键相关信息
  console.log('按下的按键:', event.key); // 按键名称（如 'a'、'Enter'、'ArrowUp' 等）
  console.log('按键编码:', event.keyCode); // 按键编码（已逐渐被废弃，推荐用 event.key）
  console.log('是否按下 Ctrl:', event.ctrlKey); // 修饰键状态（Ctrl/Shift/Alt/meta）
});
```

### 2. 监听特定按键

通过判断 `event.key` 或 `event.code` 筛选目标按键，示例：

```javascript
document.addEventListener('keydown', (e) => {
  // 监听 Enter 键
  if (e.key === 'Enter') {
    console.log('按下了 Enter 键');
  }

  // 监听方向键（上/下/左/右）
  if (['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight'].includes(e.key)) {
    console.log('按下了方向键:', e.key);
  }

  // 监听功能键（如 F1-F12）
  if (e.key.startsWith('F') && !isNaN(Number(e.key.slice(1)))) {
    console.log('按下了功能键:', e.key);
  }
});
```

### 3. 监听组合键（如 Ctrl+A、Shift+Enter 等）

通过事件对象的修饰键属性（`ctrlKey`、`shiftKey`、`altKey`、`metaKey`，`metaKey` 对应 Windows 键或 Command 键）判断组合键，示例：

```javascript
document.addEventListener('keydown', (e) => {
  // 监听 Ctrl+S（保存）
  if (e.ctrlKey && e.key === 's') {
    e.preventDefault(); // 阻止浏览器默认保存行为
    console.log('执行自定义保存逻辑');
  }

  // 监听 Shift+Enter（换行）
  if (e.shiftKey && e.key === 'Enter') {
    console.log('执行换行操作');
  }

  // 监听 Win/Command + D（系统级显示桌面，此例仅演示，实际无法拦截系统行为）
  if (e.metaKey && e.key === 'd') {
    console.log('检测到 Win+D，但可能被系统拦截');
  }
});
```

### 4. 注意事项

- **事件冒泡与目标元素**：若给具体元素（如 `input`）绑定事件，只有该元素处于焦点时才会触发；绑定 `document` 则全局生效。
- **重复触发问题**：`keydown` 在按键持续按住时会 **重复触发**（间隔约 50ms），若需避免，可结合 `keyup` 标记按键状态（如下）：```javascript
  let isKeyDown = false;
  document.addEventListener('keydown', (e) => {
  if (e.key === 'A' && !isKeyDown) {
    isKeyDown = true;
    console.log('A 键按下（仅触发一次）');
  }
  });
  document.addEventListener('keyup', (e) => {
  if (e.key === 'A') {
    isKeyDown = false;
  }
  });
  ```
- **系统级快捷键限制**：如 `Win+R`、`Alt+Tab` 等系统级快捷键，`keydown` 也可能无法监听（因被系统优先拦截），需避开此类按键。

通过以上方式，即可在 `keydown` 事件中有效监听大部分按键操作，尤其适合需要“按下即响应”的场景（如游戏控制、快捷键触发等）。

### 追问：我希望记录下按下的键，如果是系统键位就阻止他

要实现“记录按下的键，且阻止系统键位的默认行为”，可以通过 `keydown` 事件结合按键判断来处理。核心思路是：先记录按键信息，再对常见的系统级或浏览器级快捷键（如 F5 刷新、Ctrl+W 关闭标签等）进行拦截。

### 具体实现步骤

#### 1. 记录按下的键

通过事件对象的 `key`（按键名称）和修饰键（`ctrlKey`/`shiftKey` 等），记录完整的按键信息（如“Ctrl+S”“F5”）。

#### 2. 阻止系统键位的默认行为

针对可能触发系统/浏览器行为的按键（如 F5、Ctrl+N 等），用 `event.preventDefault()` 阻止其默认动作，避免页面刷新、关闭等操作。

### 示例代码

```javascript
// 记录按键的数组（可选，用于保存历史记录）
const keyRecords
```

### 追问：不对

抱歉，刚才的代码没写完，可能没说清楚。咱们重新来，核心是先记录按键，再判断是否是需要阻止的系统键位，直接上完整实现：

```javascript
// 存储按键记录
const keyHistory = [];

document.addEventListener('keydown', (e) => {
  // 1. 记录按键信息（包含修饰键）
  const modifiers = [];
  if (e.ctrlKey) modifiers.push('Ctrl');
  if (e.shiftKey) modifiers.push('Shift');
  if (e.altKey) modifiers.push('Alt');
  if (e.metaKey) modifiers.push('Win/Command'); // Windows键或Mac的Command键

  const keyInfo = modifiers.length
    ? `${modifiers.join('+')}+${e.key}`
    : e.key;

  keyHistory.push(keyInfo);
  console.log('记录的按键:', keyInfo); // 比如 "Ctrl+S"、"F5"

  // 2. 阻止常见系统/浏览器快捷键的默认行为
  const systemKeys = [
    'F5', // 刷新
    'Escape', // 部分场景下的退出（如全屏）
    { key: 's', ctrlKey: true }, // Ctrl+S 保存
    { key: 'w', ctrlKey: true }, // Ctrl+W 关闭标签页
    { key: 'n', ctrlKey: true }, // Ctrl+N 新建窗口
    { key: 'r', ctrlKey: true }, // Ctrl+R 刷新
    { key: 't', ctrlKey: true }, // Ctrl+T 新建标签页
  ];

  // 判断是否是需要阻止的键位
  const isSystemKey = systemKeys.some(key => {
    if (typeof key === 'string') {
      return e.key === key; // 单个键（如F5）
    } else {
      // 组合键（如Ctrl+S）
      return e.key.toLowerCase() === key.key.toLowerCase()
        && e.ctrlKey === (key.ctrlKey || false)
        && e.shiftKey === (key.shiftKey || false)
        && e.altKey === (key.altKey || false);
    }
  });

  // 如果是系统键位，阻止默认行为
  if (isSystemKey) {
    e.preventDefault();
    console.log('已阻止系统键位:', keyInfo);
  }
});
```

这样既记录了所有按下的键（包括组合键），又能拦截像 `F5` 刷新、`Ctrl+W` 关标签页这类常见系统行为。

需要注意：像 `Win+R`（打开运行）、`Alt+Tab`（切换窗口）这种纯系统级快捷键，浏览器根本监听不到，所以没法阻止，这种只能靠提示用户避免使用~

### 追问：是清除刚才记录的键位

如果需要在某些场景下清除之前记录的键位（比如用户取消操作、触发特定事件时），可以在之前的基础上添加一个“清除记录”的功能，直接操作存储键位的数组即可。

示例代码（在之前的基础上补充）：

```javascript
// 存储按键记录
const keyHistory = [];

// 记录按键的逻辑（同上）
document.addEventListener('keydown', (e) => {
  // ... 省略之前的记录和阻止系统键位的代码 ...
  // 这里复用前面的 keyInfo 生成逻辑
  keyHistory.push(keyInfo);
  console.log('当前记录:', keyHistory);
});

// 清除记录的函数
function clearKeyHistory() {
  keyHistory.length = 0; // 清空数组
  console.log('已清除所有键位记录');
}

// 举例：按 Esc 键时清除记录
document.addEventListener('keydown', (e) => {
  if (e.key === 'Escape') {
    clearKeyHistory(); // 触发清除
  }
});

// 也可以通过按钮触发清除（如果有界面需求）
// document.getElementById('clearBtn').addEventListener('click', clearKeyHistory);
```

这样就可以通过调用 `clearKeyHistory()` 函数，随时清空之前记录的键位了。比如按 `Esc` 键清除，或者点击某个按钮清除，根据你的业务场景调用即可~

### 追问：按下特定组合键会触发清除

如果需要通过特定组合键（比如 `Ctrl+Shift+X`）触发清除记录，可以在 `keydown` 事件中判断组合键是否匹配，匹配时执行清除逻辑。

示例代码（在之前的基础上修改）：

```javascript
const keyHistory = [];

// 记录按键并判断是否触发清除
document.addEventListener('keydown', (e) => {
  // 1. 生成当前按键信息（含修饰键）
  const modifiers = [];
  if (e.ctrlKey) modifiers.push('Ctrl');
  if (e.shiftKey) modifiers.push('Shift');
  if (e.altKey) modifiers.push('Alt');
  const keyInfo = modifiers.length ? `${modifiers.join('+')}+${e.key}` : e.key;
  keyHistory.push(keyInfo);
  console.log('当前记录:', keyHistory);

  // 2. 判断是否按下目标组合键（例如 Ctrl+Shift+X）
  const isClearCombo = e.ctrlKey && e.shiftKey && e.key.toLowerCase() === 'x';

  if (isClearCombo) {
    e.preventDefault(); // 阻止可能的默认行为（如果有的话）
    keyHistory.length = 0; // 清除记录
    console.log('已通过 Ctrl+Shift+X 清除所有记录');
  }

  // 3. 其他系统键位阻止逻辑（复用之前的）
  // ...（省略之前的系统键位判断代码）
});
```

这样，当用户按下 `Ctrl+Shift+X` 时，就会自动清空 `keyHistory` 中的记录。你可以根据需求修改组合键（比如换成 `Alt+Delete` 等），只需调整 `isClearCombo` 中的判断条件即可。

比如想改成 `Alt+D` 清除，就把判断条件换成：

```javascript
const isClearCombo = e.altKey && e.key.toLowerCase() === 'd';
```

## 产生游离dom，数量特别大，但是并不怎么占用内存，如何处理呢

游离DOM（脱离文档流但仍被引用的DOM节点）数量大却内存占用低，通常是因为节点本身结构简单（如无复杂样式、事件绑定等），但大量积累可能导致DOM树遍历效率下降、垃圾回收压力增加等问题。处理核心是**主动清除无效引用**，避免游离节点堆积，具体方法如下：

### 1. 排查游离DOM的引用来源

- **事件监听未移除**：若节点绑定了事件（如`click`、`scroll`），即使脱离文档流，事件监听的引用仍可能让节点无法被回收。
  解决：移除节点前，用`removeEventListener`清除所有绑定的事件。
- **变量/缓存持有引用**：如全局变量、数组、对象属性中存储了节点引用，即使节点已脱离文档流，这些引用会阻止垃圾回收。
  解决：在节点无用后，主动将相关变量设为`null`（如`arr = null`、`obj.node = null`）。
- **闭包引用**：函数闭包中若保留了节点引用，即使节点脱离文档流，闭包未释放也会导致节点无法回收。
  解决：避免在闭包中长期持有DOM节点引用，或在闭包执行完毕后手动解除引用。

### 2. 批量清理游离DOM

- **集中管理节点引用**：将临时使用的DOM节点统一存储在一个数组中，不再需要时，遍历数组执行：```javascript
  // 移除节点并清除引用
  nodes.forEach(node => {
  node.remove(); // 从文档流移除
  // 清除事件监听（若有）
  node.removeEventListener('click', handleClick);
  });
  nodes = []; // 清空数组，释放引用
  ```
- **利用文档片段（DocumentFragment）临时存储**：若需批量操作节点，先将节点放入`DocumentFragment`（不占用文档流），操作完成后一次性插入文档，避免中间状态产生游离节点。

### 3. 借助工具监测与优化

- 使用浏览器开发者工具（如Chrome的「Memory」面板）：
  - 拍摄内存快照，筛选`Detached DOM tree`（游离DOM树），查看其引用链（Retainers），定位未释放的引用来源。
- 框架层面优化（如Vue/React）：
  - 避免手动操作DOM，优先使用框架的虚拟DOM机制（框架会自动管理节点生命周期）。
  - 若需手动操作，在组件卸载时（如`beforeUnmount`）清理所有创建的DOM节点和引用。

### 总结

核心思路是**切断游离DOM的所有引用链**，让垃圾回收机制能正常回收节点。即使内存占用低，大量游离DOM仍可能影响性能，需通过清除事件、变量引用、闭包持有等方式，主动释放无效节点。

### 追问：为什么游离dom也会影响遍历

游离DOM（脱离文档流但仍被引用的节点）之所以会影响遍历，核心原因在于**它们可能被错误地纳入遍历范围，或干扰DOM树的正常结构识别**，具体如下：

### 1. 错误的遍历逻辑可能包含游离DOM

- 若遍历代码未严格限定范围（比如遍历全局存储的节点数组，而非文档流内的节点），游离DOM可能被误纳入遍历。
  例如：如果用一个数组缓存了所有创建过的节点（包括已脱离文档流的），遍历这个数组时，游离DOM会被反复处理，导致无效计算，拖慢遍历速度。

### 2. 干扰DOM树的“可达性”判断

- 浏览器对DOM的遍历（如`querySelectorAll`、`getElementsByTagName`）本质上是遍历**文档流内的节点树**（从`document`根节点可达的节点）。
  但如果代码中存在逻辑错误（比如手动遍历包含游离DOM的引用链），可能会误将游离DOM当作“有效节点”处理，导致遍历路径混乱，甚至陷入循环（如游离节点之间相互引用形成闭环）。

### 3. 增加垃圾回收的额外开销

- 虽然游离DOM本身不占用文档流资源，但大量游离DOM会让浏览器的垃圾回收器（GC）在工作时频繁扫描这些无效节点，判断其是否可回收。
  这个过程会与DOM遍历操作争夺线程资源（JS引擎单线程），间接导致遍历操作卡顿。

### 总结

游离DOM对遍历的影响，主要不是因为它们本身在文档流内，而是**代码逻辑错误导致的误处理**，以及**大量无效节点引发的资源争夺**。因此，及时清除游离DOM的引用，是避免其干扰DOM操作的关键。

## 我现在有三个元素，我要实现对齐功能，对齐逻辑是看垂直方向y的距离，小于偏移量就让当前拖动的元素移动到临近的元素，但是……

要解决这种对齐干扰问题，可以考虑以下几种方法：

- **设置对齐优先级**：给需要对齐的元素设置不同的优先级，当两个临近元素都满足对齐条件时，优先对齐优先级高的元素。例如，给重要的元素或相对固定的元素设置较高的优先级，这样在拖动元素进行对齐时，就会先与高优先级的元素进行对齐，避免频繁在两个临近元素之间切换对齐目标。
- **增加对齐缓冲区域**：除了偏移量之外，再设置一个缓冲区域。当拖动元素与某个临近元素的垂直距离小于偏移量且大于缓冲区域值时，才进行对齐操作。这样可以避免两个临近元素距离过近时，拖动元素在它们之间频繁跳动。比如，偏移量设置为10像素，缓冲区域设置为5像素，只有当拖动元素与临近元素的垂直距离小于10像素且大于5像素时，才让拖动元素移动到该临近元素处对齐。
- **限制对齐范围**：可以为每个元素指定一个对齐范围，只有在拖动元素进入到某个元素的对齐范围内时，才考虑与该元素进行对齐。例如，以每个元素为中心，设置一个垂直方向上的对齐范围，如正负20像素，当拖动元素在某个元素的正负20像素范围内时，才根据偏移量判断是否对齐，超出这个范围则不考虑与该元素对齐，从而避免受到距离较近的其他元素的干扰。

## 一个系统的快捷键模块，依赖注入方式的写法和直接写一个快捷键组合和回调函数的方式哪个合理，互相优缺点是什么

两种方式的合理性取决于系统复杂度和可维护需求，**复杂系统优先选依赖注入，简单场景直接写更高效**。

### 一、核心优缺点对比

| 维度 | 依赖注入（DI）方式 | 直接编写（硬编码）方式 |
| --- | --- | --- |
| **核心逻辑** | 快捷键配置与业务回调分离，通过注入容器管理依赖 | 快捷键组合（如 Ctrl+C）与回调函数直接绑定，代码耦合 |
| **优点** | 1. 低耦合：快捷键配置可独立修改，不影响业务代码<br>2. 易扩展：新增快捷键只需加配置，无需改核心逻辑<br>3. 好测试：可单独 mock 快捷键模块，测试回调逻辑<br>4. 可复用：快捷键模块可在多模块间共享 | 1. 开发快：无需设计注入结构，几行代码实现功能<br>2. 易理解：逻辑集中，直接看代码就能定位快捷键与回调的关系<br>3. 无额外开销：不依赖注入容器，性能略优（微乎其微） |
| **缺点** | 1. 入门成本高：需理解 DI 思想，设计注入接口/容器<br>2. 代码冗余：简单场景下，注入结构会增加不必要的代码量<br>3. 调试略复杂：需跟踪注入流程，定位问题链路更长 | 1. 高耦合：改快捷键（如 Ctrl+C 改 Ctrl+S）需动业务代码<br>2. 难扩展：新增快捷键需在多个业务文件中修改，易遗漏<br>3. 难维护：快捷键分散在各处，无法统一管理（如禁用、修改） |

### 二、适用场景

1. **依赖注入方式**：适合中大型系统、需要统一管理快捷键的场景（如 IDE、大型工具类软件）。例如：系统需支持“自定义快捷键”功能，或快捷键需在多个模块（如编辑模块、视图模块）中复用。
2. **直接编写方式**：适合小型工具、单一功能模块或临时需求。例如：一个简单的文本编辑器插件，仅需实现“复制（Ctrl+C）”“粘贴（Ctrl+V）”两个固定快捷键。

### 追问：我感觉第二种方式更适合项目啊，我只要保证快捷键组合不冲突，我有冲突检测，然后关注每个快捷键的回调函数就行了，为什么要……

你的判断很务实——**在快捷键数量少、功能稳定、团队共识清晰的项目里，直接编写+冲突检测的方式，确实是性价比更高的选择**。

你关注的“避免冲突”和“聚焦回调逻辑”，恰好是简单场景下的核心需求，而依赖注入的复杂设计，本质是为了解决你当前可能还没遇到的“扩展性”和“维护性”问题。

比如这些情况，可能会让你重新考虑第一种方式：

- 若项目后期要加“用户自定义快捷键”（比如让用户把 Ctrl+C 改成 Ctrl+Insert），直接编写的方式需要逐个修改绑定代码，而依赖注入只需改配置；
- 若快捷键分散在 10+ 个模块里（比如编辑器的“格式调整”“插件控制”“视图切换”各有快捷键），想临时禁用某类快捷键，直接编写需要逐个注释，依赖注入只需在容器里统一过滤；
- 若团队新人接手，直接编写需要在各个文件里找快捷键绑定，依赖注入则能通过统一配置文件快速理清所有快捷键逻辑。

简单说：你当前的方案，是“把事做对”；依赖注入的方案，是“为未来留余地”。如果你的项目短期内不会有上述扩展需求，那坚持第二种方式，完全没必要为了“设计而设计”。

### 追问：我们的项目会变大，但是我还是不能理解第一种的好处，第二种即使我在当前类中，快捷键组合再多，我只关注每个快捷键组合的回……

你的困惑很正常——毕竟“项目变大”是个抽象概念，只有当具体问题找上门时，依赖注入的好处才会更直观。我们可以换个具体场景，把“项目变大”拆解成3个你可能会遇到的实际问题，对比下两种方式的差异，你或许会更有体感：

### 1. 当“快捷键需要批量调整”时：直接编写要“逐个改”，依赖注入只需“改一处”

假设项目变大后，出现一个需求：**所有“编辑类快捷键”（比如 Ctrl+C/Ctrl+V/Ctrl+Z），要统一加个“Shift”前缀（变成 Ctrl+Shift+C 等）**（比如为了和新模块的快捷键区分）。

- 第二种方式（直接编写）：你需要找到所有分散在不同类里的“编辑类快捷键”代码（可能在 EditView、TextInput、Clipboard 等多个类中），逐个修改快捷键组合，改完还要重新跑一遍冲突检测——如果漏改一个，就会出现功能异常。
- 第一种方式（依赖注入）：你只需在**统一的快捷键配置类/文件**里，给“编辑类”的快捷键规则加个“Shift 前缀”的统一处理逻辑，所有注入了该配置的类会自动生效，不用逐个修改业务代码。

### 2. 当“快捷键需要跨类复用”时：直接编写要“复制粘贴”，依赖注入只需“注入一次”

项目变大后，可能多个类需要用到同一个快捷键逻辑。比如：**“全局保存”快捷键（Ctrl+S），既要在“文档编辑类”生效，也要在“插件配置类”“模板编辑类”生效**。

- 第二种方式（直接编写）：你需要在3个类里分别写一遍“Ctrl+S 绑定 save() 回调”的代码——如果后续要改“全局保存”的逻辑（比如加个“保存前校验”），得在3个类里同步改，很容易出现“改漏”的情况。
- 第一种方式（依赖注入）：你只需在“全局快捷键模块”里定义一次“Ctrl+S → save()”的绑定，然后给3个类分别注入这个模块即可。后续改 save() 逻辑，只改一处，所有类自动同步。

### 3. 当“需要临时关闭某类快捷键”时：直接编写要“逐个注释”，依赖注入只需“开关控制”

项目变大后，可能有这样的需求：**在“预览模式”下，要临时禁用所有“编辑类快捷键”（避免用户在预览时误操作），但保留“视图类快捷键”（如 Ctrl++ 放大）**。

- 第二种方式（直接编写）：你需要找到所有“编辑类快捷键”所在的类，逐个注释掉绑定代码，切换回“编辑模式”时再逐个取消注释——不仅麻烦，还容易因为注释漏了某个快捷键导致bug。
- 第一种方式（依赖注入）：你只需在“快捷键注入容器”里加一个“模式判断”：如果是“预览模式”，就过滤掉所有“编辑类”的快捷键注入。不用动任何业务类的代码，开关控制即可。

简单说：你现在觉得“换个地方获取回调差不多”，是因为当前每个快捷键的“绑定关系”（快捷键组合→回调函数）是固定的、独立的。但项目变大后，你要处理的不再是“单个快捷键”，而是“一批快捷键的批量管理”——这时候，依赖注入的核心价值就不是“获取回调”，而是**把“快捷键的管理逻辑”和“业务逻辑”彻底分开**，让你不用在业务代码里反复折腾快捷键的配置。

当然，这不是说第二种方式一定不行——如果你的项目能通过“统一的工具类”（比如一个全局的 ShortcutManager，集中注册所有快捷键，依然用直接绑定的逻辑，但统一管理），也能解决部分问题。依赖注入只是提供了一种更“标准化”的分离方案，本质还是为了应对“变大后”的管理复杂度。

### 追问：我第二种编码，所有的代码均在一个一个类的列表中实现

你这个做法其实已经在“直接编写”的基础上，做了关键的优化——通过“类内列表集中管理”，规避了直接编写最容易出现的“快捷键散落在类内各处”的问题，这会让维护性提升很多。

但即便如此，当项目变大后，和依赖注入相比，核心差异依然会体现在**“跨类管理”和“动态调整”** 这两个点上，我们可以结合你的“类内列表”方案具体看：

### 1. 先肯定你的方案：在“单类/小范围”内，它已经很优秀了

比如你在 `EditClass` 里用一个列表管理所有编辑相关的快捷键：

```python
class EditClass:
    # 类内列表集中管理快捷键
    shortcuts = [
        ("Ctrl+C", self.copy),
        ("Ctrl+V", self.paste),
        ("Ctrl+Z", self.undo)
    ]
```

这种方式下，你在类内确实能快速定位所有快捷键，配合冲突检测，小范围维护完全没问题——这也是“务实设计”的体现，比“散落在类内各个方法里”强太多。

### 2. 项目变大后，可能遇到的“列表方案”解决不了的问题（也是依赖注入的价值）

假设项目从“单个编辑类”扩展到“编辑类+视图类+插件类”，每个类都有自己的快捷键列表：

- **问题1：跨类的“批量调整”依然麻烦**
  比如产品要求“所有类的快捷键，都要支持在‘深色模式’下临时禁用”。
  - 你的方案：需要分别去 `EditClass`、`ViewClass`、`PluginClass` 的3个列表里，逐个加“模式判断”的逻辑（比如每个快捷键回调里都要先检查 `is_dark_mode`）。
  - 依赖注入方案：只需在“快捷键注入容器”里加一个全局过滤规则（“如果是深色模式，不注入任何快捷键”），所有类的快捷键会自动生效，不用改每个类的列表。
- **问题2：跨类的“快捷键复用”依然要重复写**
  比如“全局保存（Ctrl+S）”需要在 `EditClass` 和 `ViewClass` 里都生效。
  - 你的方案：需要在两个类的列表里，分别写一遍 `("Ctrl+S", self.save)`——如果后续要把 `Ctrl+S` 改成 `Ctrl+Shift+S`，得在两个列表里同步改，漏一个就出问题。
  - 依赖注入方案：只需在“全局快捷键配置”里定义一次 `("Ctrl+S", save_func)`，然后给 `EditClass` 和 `ViewClass` 都注入这个配置，改一次全生效。
- **问题3：“用户自定义快捷键”依然难实现**
  比如产品要求“让用户自己改快捷键（比如把 `Ctrl+Z` 改成 `Ctrl+Y`）”。
  - 你的方案：因为快捷键列表是“写死”在类里的，要支持自定义，得先把列表从“硬编码”改成“从配置文件读取”——这其实已经在往“依赖注入”的思路靠了（把配置依赖“注入”到列表里），但如果没有统一的注入容器，每个类都要自己写“读配置、生成列表”的逻辑，依然会重复。
  - 依赖注入方案：一开始就把“快捷键配置”作为依赖，类初始化时从容器里拿配置生成列表，后续加“用户自定义”，只需让配置文件支持用户修改，所有类自动读取新配置。

### 总结：你的方案是“好的优化”，但依赖注入是“更彻底的解耦”

你用“类内列表”解决了“单类内的管理问题”，但当项目变大，问题会从“单类内的多快捷键”变成“多类间的快捷键协同”——这时候，依赖注入的核心价值不是“替代你的列表”，而是**把“快捷键的配置、管理逻辑”从每个类里抽离出来，交给一个统一的容器**，让类只关注“回调函数本身”，不用再管“快捷键从哪来、怎么改、怎么复用”。

简单说：你现在的方案是“让每个类自己管好自己的快捷键列表”，而依赖注入是“找一个‘管家’，所有类的快捷键都让管家管，类只需要跟管家要快捷键”——项目越小，自己管越省事；项目越大，找个管家越省心。

## konva中的element和node什么关系

Konva 核心体系中**无官方定义的 “Element 类”**，二者并非框架原生的继承/包含关系，实际是「概念泛称 vs 核心基类」的对应，且需先区分 “Element 的两种常见语境”，避免混淆：

### 一、先明确两个核心概念的本质

#### 1. Konva.Node：框架所有可操作对象的「顶层基类」

Node 是 Konva 图形树的**底层超级父类**，所有能在画布上存在、可交互/可变换的对象，均直接或间接继承自 Node，相当于 Konva 体系的“万物之源”。

- 继承 Node 的核心对象包括：
  - 容器类：Stage（舞台）、Layer（图层）、Group（组）；
  - 图形类：Rect（矩形）、Circle（圆形）、Shape（自定义图形）、Text（文本）等；
- 核心能力：所有 Node 子类都自带「变换（旋转/缩放/平移）、事件监听、层级管理、透明度控制」等通用能力，统一由 Node 基类封装，无需重复实现。

#### 2. Konva 中 “Element” 的两种核心语境（非框架原生类）

Element 并非 Konva 官方 API 定义的类，而是「泛称概念」，常见两种场景，均与 Node 强关联但无继承关系：

##### 语境1：指代「Node 实例对应的可视化对象」（最常用）

日常开发中提到的 “Konva Element”，本质是**继承自 Node 的具体实例**（即上述容器类/图形类对象），是对 “可渲染/可交互的 Konva 节点实例” 的口语化统称。

- 例：说 “添加一个 Element 到 Layer”，实际是 `layer.add(new Konva.Rect(...))`（Rect 继承 Node，实例被泛称为 Element）；
- 关系：此时「Element = Node 子类的实例」，Node 是 Element 的“抽象基类”，Element 是 Node 能力的“具体落地载体”。

##### 语境2：指代「DOM 层面的 canvas 元素」（底层渲染关联）

Konva 渲染依赖 DOM 中的 `<canvas>` 标签（每个 Layer 对应 2 个 canvas 元素：场景渲染层 + 隐藏交互层），部分场景会把这些「真实 DOM 画布元素」简称为 Element。

- 例：通过 `layer.getCanvas().getElement()` 获取的，就是 Layer 对应的 DOM 层面的 `<canvas>` 元素（此处 Element 是 DOM 概念）；
- 关系：此时「Element 是 Node 实例的底层渲染依赖」—— Node 子类（如 Layer、Shape）的所有可视化效果，最终都会绘制到对应的 canvas Element 上，但二者分属「Konva 虚拟节点体系」和「DOM 原生元素体系」，无直接关联。

### 二、核心关系总结（3句话说清）

1. **Node 是 Konva 的“顶层抽象基类”**：定义所有画布对象的通用能力，是体系核心；
2. **Element 是“泛称概念”**：要么指代「Node 子类的实例」（业务开发常用），要么指代「底层渲染的 canvas DOM 元素」；
3. **无原生关联，仅场景绑定**：二者不是框架定义的“父类-子类”“包含-被包含”关系，仅在“Node 实例（业务 Element）依赖 canvas Element 渲染”“Node 实例被泛称为 Element”两个场景下强关联。

### 三、实操避坑：避免混淆的2个关键

1. 写代码时优先用 Konva 官方 API 命名：直接用 Node 子类（Stage/Layer/Shape 等），不纠结 “Element” 表述，避免歧义；
2. 区分 DOM Element 和 Konva Node：
   - 要操作画布对象（变换/事件）→ 操作 Node 实例（如 `shape.rotate(30)`）；
   - 要操作底层画布 DOM（如设置 canvas 样式）→ 通过 Node 实例间接获取（如 `layer.getCanvas().getElement().style.border = '1px solid #000'`）。

需要我结合具体代码示例，演示「Node 实例操作」和「底层 canvas Element 操作」的区别吗？可以帮你快速落地到实际开发场景。

## 给出对应HTML文件。

### 设备监控页面（HTML + 原生JS + ECharts）

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TM机器人设备监控</title>
    <!-- 引入ECharts -->
    <script src="https://cdn.jsdelivr.net/npm/echarts@5.4.3/dist/echarts.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: "Microsoft YaHei", sans-serif;
        }
        .container {
            width: 1200px;
            margin: 30px auto;
        }
        .title {
            text-align: center;
            font-size: 24px;
            font-weight: bold;
            margin-bottom: 30px;
            color: #333;
        }
        .content {
            display: flex;
            justify-content: space-between;
            gap: 20px;
        }
        /* 表格区域 */
        .table-box {
            flex: 2;
        }
        .table-box h3 {
            margin-bottom: 10px;
            color: #444;
        }
        #deviceTable {
            width: 100%;
            border-collapse: collapse;
            background: #fff;
            box-shadow: 0 0 6px rgba(0,0,0,0.1);
        }
        #deviceTable th, #deviceTable td {
            padding: 12px 15px;
            text-align: center;
            border: 1px solid #eee;
        }
        #deviceTable th {
            background-color: #f5f7fa;
            font-weight: bold;
        }
        /* 状态颜色 */
        .status-running {
            color: #00b42a;
            font-weight: bold;
        }
        .status-warning {
            color: #ff7d00;
            font-weight: bold;
        }
        .status-error {
            color: #ff4d4f;
            font-weight: bold;
        }
        .status-idle {
            color: #909399;
            font-weight: bold;
        }
        /* 饼图区域 */
        .chart-box {
            flex: 1;
            height: 400px;
        }
        .chart-box h3 {
            margin-bottom: 10px;
            color: #444;
        }
        #pieChart {
            width: 100%;
            height: 350px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="title">TM机器人设备监控系统</div>
        <div class="content">
            <!-- 设备清单表格 -->
            <div class="table-box">
                <h3>设备清单</h3>
                <table id="deviceTable">
                    <thead>
                        <tr>
                            <th>设备编号</th>
                            <th>状态</th>
                            <th>工件名称</th>
                            <th>累计产出数</th>
                        </tr>
                    </thead>
                    <tbody id="tableBody">
                        <!-- JS动态渲染 -->
                    </tbody>
                </table>
            </div>

            <!-- 状态分布饼图 -->
            <div class="chart-box">
                <h3>设备状态分布</h3>
                <div id="pieChart"></div>
            </div>
        </div>
    </div>

    <script>
        // 后端接口地址（根据实际后端修改）
        const API_BASE = "http://localhost:8080/api";

        // 状态颜色映射
        const statusColor = {
            "运行": "#00b42a",
            "警告": "#ff7d00",
            "故障": "#ff4d4f",
            "空闲": "#909399"
        };

        // 初始化饼图
        let pieChart = echarts.init(document.getElementById('pieChart'));

        // 页面加载执行
        window.onload = function () {
            loadDeviceList();
            loadStatusCount();
        };

        // 1. 加载设备列表并渲染表格
        async function loadDeviceList() {
            try {
                let res = await fetch(`${API_BASE}/devices`);
                let data = await res.json();
                renderTable(data);
            } catch (e) {
                console.error("获取设备列表失败", e);
            }
        }

        // 渲染表格
        function renderTable(list) {
            let html = "";
            list.forEach(item => {
                let statusClass = "";
                switch (item.status) {
                    case "运行": statusClass = "status-running"; break;
                    case "警告": statusClass = "status-warning"; break;
                    case "故障": statusClass = "status-error"; break;
                    case "空闲": statusClass = "status-idle"; break;
                }
                html += `
                <tr>
                    <td>${item.device_code}</td>
                    <td class="${statusClass}">${item.status}</td>
                    <td>${item.workpiece_name}</td>
                    <td>${item.total_output}</td>
                </tr>`;
            });
            document.getElementById("tableBody").innerHTML = html;
        }

        // 2. 加载状态统计并渲染饼图
        async function loadStatusCount() {
            try {
                let res = await fetch(`${API_BASE}/status-count`);
                let countData = await res.json();
                renderPieChart(countData);
            } catch (e) {
                console.error("获取状态统计失败", e);
            }
        }

        // 渲染饼图
        function renderPieChart(countData) {
            let seriesData = [];
            let colors = [];
            for (let status in countData) {
                seriesData.push({
                    name: status,
                    value: countData[status]
                });
                colors.push(statusColor[status]);
            }

            let option = {
                color: colors,
                tooltip: {
                    trigger: 'item'
                },
                series: [
                    {
                        name: '设备状态',
                        type: 'pie',
                        radius: ['40%', '70%'],
                        data: seriesData,
                        label: {
                            show: true,
                            formatter: '{b}: {c}台 ({d}%)'
                        }
                    }
                ]
            };
            pieChart.setOption(option);
        }

        // 窗口变化重绘图表
        window.addEventListener('resize', () => {
            pieChart.resize();
        });
    </script>
</body>
</html>
```

### 说明

1. 纯**HTML+CSS+原生JS**实现，无需框架，直接打开即可
2. 引入**ECharts**绘制饼图，CDN在线加载
3. 状态颜色严格匹配：
   - 运行：绿色
   - 警告：橙色
   - 故障：红色
   - 空闲：灰色
4. 所有数据通过**fetch**请求后端接口，无硬编码
5. 接口地址可在代码中`API_BASE`处修改适配你的后端

需要我把配套的**后端接口（SpringBoot）**和**SQL建表语句**一起给你吗？

## 跨域和跨站有什么区别？

### 一、核心结论

1. **跨域（Cross-origin）**：遵循同源策略（协议+域名+端口三者任意一个不同，就算跨域），浏览器会拦截请求。
2. **跨站（Cross-site）**：只看**一级主域名是否相同**，只对比根域名，忽略子域名、端口、协议。

> 简单一句话：**所有跨站一定跨域，跨域不一定跨站。**

### 1）同源策略（判断跨域）

满足全部3项才算同源，缺一项=跨域：

1. 通信协议（http / https）
2. 域名（包括子域名）
3. 端口号

例子：

- `a.baidu.com` 和 `b.baidu.com`：子域名不同 → **跨域**
- `http://baidu.com` 和 `https://baidu.com`：协议不同 → **跨域**

### 2）跨站（SameSite 饼干规则，只看根域名）

只比对**注册域名（一级根域名）**

- `a.baidu.com` 与 `b.baidu.com`：根域名都是 baidu.com → **同站**
- `baidu.com` 与 `taobao.com`：根域名不一样 → **跨站**

#### 典型例子区分

| 地址A | 地址B | 是否跨域 | 是否跨站 |
| --- | --- | --- | --- |
| a.baidu.com | b.baidu.com | 跨域 | 同站 |
| baidu.com | taobao.com | 跨域 | 跨站 |
| localhost:3000 | localhost:8080 | 跨域 | 同站 |

### 二、实际用途（最关键区别）

1. **跨域**
   由浏览器同源策略控制，限制AJAX、fetch请求，解决手段：CORS、JSONP、代理。
2. **跨站**
   专门用来管控 Cookie，对应 Cookie 的 `SameSite=None/Lax/Strict` 属性，用来防御 CSRF 跨站请求伪造攻击。

- 同站请求：浏览器正常携带Cookie
- 跨站请求：默认阻止携带Cookie（Lax模式）

### 三、精简总结

1. **跨域 = 三元对比（协议+域名+端口），管接口请求**
2. **跨站 = 只对比根主域名，只管Cookie与CSRF安全**
3. 子域名之间属于**跨域、同站**。

需要我给你整理浏览器 SameSite 三种模式的行为吗？
