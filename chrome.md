# Browser (Chrome)

浏览器（Browser）是一种用于访问互联网上的信息的软件应用程序。它是计算机用户用来查看网页、浏览网站、下载文件等的工具。浏览器允许你输入网址，然后通过互联网访问并显示与该网址相关的内容。

以下是浏览器的一些主要功能和组成部分：

- `用户界面：` 用户界面包括地址栏、导航按钮（前进、后退）、书签栏等，以提供用户与浏览器进行交互的方式。
- `渲染引擎：` 渲染引擎是浏览器的核心组件，负责解析网页的 HTML 和 CSS，然后将其呈现为用户可以理解的图形界面。常见的渲染引擎有WebKit（用于 Chrome 和 Safari）、Gecko（用于 Firefox）等。
- `JavaScript 解释器：` 浏览器能够执行网页上的 JavaScript 代码，使得网页具有更丰富的交互性。JavaScript 解释器负责解释和执行这些脚本。
- `浏览器引擎：` 浏览器引擎是渲染引擎和 JavaScript 解释器的总称，它们共同工作以加载和显示网页。
- `网络组件：` 浏览器使用网络组件来发起和处理网络请求。这包括从服务器请求网页、图像、样式表等资源，并将它们显示在用户的屏幕上。
- `插件/扩展：` 浏览器允许用户通过插件或扩展来增强其功能，如广告拦截器、密码管理器、开发者工具等。
- `缓存：` 浏览器会将之前访问过的页面和资源缓存起来，以便在用户再次访问时加快加载速度。
- `隐私和安全设置：` 浏览器提供了一些设置，如隐私模式、弹出窗口阻止、安全证书检查等，以帮助用户更安全地浏览互联网。

>Google Chrome 的多进程架构中包含多个类型的进程，每个进程都有不同的作用，这有助于提高浏览器的性能、稳定性和安全性。

1. Browser 进程（浏览器进程）:

## 事件循环基础知识(Event Loop Fundamentals)

***翻译自[原文地址](https://webperf.tips/tip/event-loop/) 感谢Joe Liccini!***
>浏览器事件循环可能是需要掌握的最重要的前端性能概念。它的行为决定了 Web 应用程序在运行时的行为方式。
主线程(The Main Thread)
>Web 应用程序需要在此单个主线程上执行某些关键操作。导航到 Web 应用程序时，浏览器将创建并向应用程序授予此线程，以便代码在其上执行。
>Web 应用程序的大多数运行时活动都发生在此单个线程上，线程上的每个活动都在 Task 中调用。
>
![Main Thread](https://webperf.tips/static/7946ebc12011f5f7a8a384a0dcbceec9/73a2e/EventLoop01.png)
>[!NOTE]
>*这并不意味着浏览器（即 Chrome）是单线程的。现代浏览器既是多进程的(multi-process)，也是多线程的(multi-threaded)。*

任务(Tasks)
>任务是在主线程上执行的运行时执行单元。
>
>任务的一些常见示例包括：
>
> - Parsing of HTML or CSS *HTML或CSS的解析*
> - User Input Handling, such as clicking or typing *用户输入处理，例如单击或键入*
> - Compiling and Executing JavaScript *编译和执行 JavaScript*
> - Receiving Network Data *接收网络数据*
> - The Render Steps *渲染步骤*
>
>![Tasks](https://webperf.tips/static/4098365b1ef46f93865174961e3353c6/302a4/EventLoop02.png)
>
>某些任务必须在主线程上运行。例如，任何直接需要访问 DOM(Document Object Model即文档)的东西都必须在主线程上运行因为,DOM 不是线程安全(thread-safe)的。这将包括大多数与 UI 相关的代码。
>
>一次只能在主线程上运行一个任务。
>
>此外，一个任务必须在主线程上运行到完成，然后才能运行另一个任务。浏览器没有"部分"执行任务的机制——每个任务都完整地运行直至完成。
>
>![ each Task is run in its entirety to completion.](https://webperf.tips/static/d84c2ed1d3cded9e393f126d6ff4281f/077b7/EventLoop03.png)
任务队列(Task Queue)
>不可能在主线程上同时运行多个任务，因此任何未主动运行的任务都驻留在称为任务队列的数据结构中。
>
>当你的 Web 应用程序在主线程上执行时，浏览器会将任务排入此数据结构中。
>
>当主线程准备好执行下一个任务时，它还会从此队列中读取数据。

![Task Queue](https://webperf.tips/static/706d223d0690a60ffd4eb1c83e23bd74/0f529/EventLoop04.png)
>[!NOTE]
> *任务队列不是FIFO队列。大多数浏览器都实现了基于启发式(heuristic-based)的调度逻辑，将某些任务排在其他任务之上。*
>
渲染步骤任务和帧(The Render Steps Task and Frames)
>渲染步骤任务负责将活动的 DOM 和 CSS 样式转换为用户屏幕上的可视像素。
>
>渲染步骤任务完成后生成的内容称为帧(Frame:)。
>
>![Render Steps Task](https://webperf.tips/static/530ba3d3b6e650871e904bb88e852562/0b79a/EventLoop06.png)
>
>对网页的 DOM 或 CSS 样式进行编辑将通知浏览器它需要生成一帧。当浏览器检测到它需要生成帧时，它将运行渲染步骤任务，而不是从任务队列中运行任务
>
>![example](https://webperf.tips/static/ec934bf694f3625117a1a64ab7ec77e9/7af19/EventLoop07.png)
>渲染步骤通常有 3 个关键步骤要完成：
>
> 1. Style - Assign CSS colors, fonts, and other styling properties to visual elements *样式 - 将 CSS 颜色、字体和其他样式属性分配给视觉元素*
> 2. Layout - Position visual elements with precise coordinates *布局 - 使用精确坐标定位视觉元素*
> 3. Paint - Instruct graphics libraries on how to draw positioned, styled visual elements *绘制 - 指导图形库如何绘制定位、样式化的视觉元素*

事件循环(The Event Loop)
>事件循环是这些基础部分之间的编排器(orchestrator)。
>
>它本质上是一个无限循环，负责：
>
> - 从任务队列中选择一个可用的任务，并将其放在主线程上以运行完成
> - 偶尔选择"渲染步骤"任务，而不是从"任务队列"中读取以生成帧
>
>在事件循环的每一回合中，它决定是选择任务还是将帧渲染到屏幕上。
>
>需要理解的关键是，浏览器可以运行任务或生成帧，但它不能同时执行这两项操作。
>无论哪个最终运行，它都会一直运行到完成。
>
>通常，任务持续时间应该很短，以便浏览器可以更快地调用渲染步骤来为用户生成帧。

非阻塞(Non-Blocking)
>根据设计，事件循环永远不会暂停或阻塞主线程;它将不断循环和运行任务并生成帧。
>
>但是，当我们需要执行异步任务(asynchronous task)时会发生什么？例如，如果我们的 Web 应用程序请求网络数据或使用计时器(timer)，这如何在事件循环中表现出来？
>
>浏览器提供的每个异步原语(asynchronous primitive)都表示为一个与事件循环执行模型兼容的 Task。
>
>浏览器 异步API 包括：
>
> - Loading network data via `XMLHttpRequest` or `fetch`
> - Responding to events via `addEventListener(...)`
> - Timers like `setTimeout` or `setInterval`
>
> 例如，使用 setTimeout(callback，500) 不会阻塞主线程，而是告诉浏览器将 Task 排序，以便在 500 毫秒后对 Task 队列运行callback。
>
示例
>
> ``` javascript
>let count = 0;
>// Queue a Task on the Task Queue every 500ms
>setInterval(() => {
>    // This code runs in a Task
>    count++;
>    const node = document.getElementById('output');
>    node.innerText = `${count} Timers Fired!`
>}, 500);
>```
>
>由于这会编辑 Task 中的 DOM，因此浏览器需要生成一个 帧 来更改表示为像素。
>
>最初,主线程将是空的，任务队列将是空的.浏览器被指示在 500 毫秒后将任务排序
>
>尽管线程在这 500 毫秒的等待时间内没有发生任何活动，但它是空闲的，并且能够响应单击、键入或其他任务(如果有任何已经排序过的)
>
>500 毫秒后，浏览器将顺序运行我们的回调以编辑 DOM：
>
>事件循环检测到排队等待运行的任务，并将其从队列移动到要运行的主线程(因为主线程不忙于执行任何其他操作)。
>
>运行此任务并编辑 DOM，该 DOM 通知事件循环运行渲染步骤：
>
>![渲染步骤：](https://webperf.tips/static/669c30c4b17f602791c42cdeb12b0e60/bf8c1/EventLoop11.png)
>一旦帧产生后，我们更新的文本就会出现在屏幕上！
>
>每隔 500 毫秒，这些确切的阶段将持续发生，计时器任务将排队。事件循环将从队列中删除每个线程，并将其放在主线程上以运行。这将导致事件循环运行渲染步骤任务并生成帧。

结论(Conclusion)
>Web 应用程序执行的几乎所有操作都可以归结为排队到任务队列中的任务，然后通过事件循环在主线程上运行。
>
>需要视觉更新的任务依靠事件循环来交错渲染步骤任务，以便在其他任务之间生成帧。
>
>通过优化任务的执行方式和时间来改善用户体验。
>
> - [Browser Process Model](#web-上的多进程浏览器进程模型-multi-process-on-the-web-the-browser-process-model)
> - Browser Scheduling Logic
> - Browser Rendering Pipeline
> - Long Tasks and Performance
>

## Web 上的多进程：浏览器进程模型 (Multi-process on the Web: The Browser Process Model)

>虽然 Web 应用程序被授予单个线程（主线程）来运行其大部分应用程序和 UI 逻辑，但 Web 浏览器绝对不是单线程（或单进程）环境
>
Web 应用程序(Web Application)
>Web 应用程序是用 HTML、JavaScript 和 CSS 编写的。它们通过网络从远程服务器传送给用户。
>
>加载并显示此远程代码后，用户可以在浏览器选项卡或窗口中与应用程序进行交互。
>
>![web Application](https://webperf.tips/static/6c4ce40848f49fd1f629e91a6bb39f43/fd8a5/ProcessModel01.png)
>
浏览器(Browsers)
>Web 浏览器是安装在用户设备上并从用户设备上启动的编译的 C++ 二进制文件(即 chrome.exe)
>
>它负责与各种来源进行交互。例如:
>
> - The device's filesystem, for loading user preferences, history, favorites, etc.*设备的文件系统，用于加载用户首选项、历史记录、收藏夹等*
> - The network, to load and deliver remote data *网络，用于加载和传送远程数据*
> - The OS's input events, such as clicking and typing *操作系统的输入事件，例如单击和键入*
> - The GPU and display, to present pixels on-screen
> - Remote code loaded for a web application, such as HTML, JavaScript, and CSS files defined by a web application *为 Web 应用程序加载的远程代码，例如 Web 应用程序定义的 HTML、JavaScript 和 CSS 文件*
>
>Web 浏览器还必须支持跨浏览器选项卡或窗口同时执行多个不同 Web 应用程序的实例。
>
>![Browser](https://webperf.tips/static/0085f9f18930b6e5b069b04bfbf33c07/5eb90/ProcessModel02.png)
>
进程和线程(Processes vs. Threads)
>![Process and Threads](https://webperf.tips/static/bf0943e54db8fa6133d54407fe4f90af/6bfbb/ProcessModel03.png)
>进程是软件在操作系统上执行的地方。它们具有对内存块(block of memory)的专用访问权限，以便在执行存储运行时状态时使用。一个进程无法访问操作系统上另一个正在执行的进程使用的内存。
>
>一个进程可能有一个或多个线程供程序执行指令。线程共享对专用于其包含进程的内存块的访问。
>
>一个进程可能有一个或多个线程供程序执行指令。线程共享访问归属进程的内存块。
>
进程通信(Process Communication)
>进程之间可以通过进程间通信(Inter-Process Communication缩写为IPC)进行通信。线程也可以在其归属进程中相互通信，但不能直接与其他进程中的线程通信。
>
>通常，进程之间的通信比进程内的跨线程通信要慢。
>
>![Process communication and Thread communication](https://webperf.tips/static/7723aa75674a226c29b498dd5b0a7a6c/d7050/ProcessModel04.png)
>
进程安全(Process Security)
>可以向进程授予对系统资源的各种级别的访问权限。例如，进程可以在"低"权限级别下运行，将其排除在对文件系统或注册表的访问之外。
>
>同样，进程可以在高权限级别运行，并且能够更新主机系统的敏感部分。如果被利用或破坏，这些进程是强大而危险的。
>
>![Process security](https://webperf.tips/static/c88bf56d6d4fefec3053a3258fab4b1e/9c2cf/ProcessModel05.png)
>
进程和浏览器(Processes and the Browser)
>虽然每个浏览器(即 WebKit、Chromium、Firefox)在进程模型的确切实现上会有所不同，但它们遵循相似的模式。
>
>一般来说，浏览器会：
>
> - Allocate low-privilege, sandboxed **WebContent Processes** for running Web Applications*为运行 Web 应用程序分配低权限的沙盒 WebContent 进程*
> - Have a single **Browser Process** for high-privilege actions. Examples include:
>   - Accepting User Input *接受用户输入*
>   - Accessing Saved Passwords *访问保存的密码*
>   - Updating Bookmarks and Favorites *更新书签和收藏夹*
> - Have a single **Network Process** for interfacing with the Network and Browser Cache *具有用于与网络和浏览器缓存交互的单个网络进程*
> - Other dedicated processes, for interfacing with Audio, Graphics / GPU, etc. *其他专用进程，用于与音频、图形/GPU 等连接*
>
> 专用进程(dedicated processes)的确切数量及其职责/权限将因浏览器实现而异。

![Processes and the Browser](https://webperf.tips/static/0befa2efd11185d686171f626c94d198/a3bed/ProcessModel06.png)
>[!NOTE]
>WebContent Process 的名称因浏览器而异。
>
>例如，在 Chromium 中，它被称为渲染器进程。在 WebKit 中，它被称为 WebProcess。
>
Multi-Process Model Motivation
>这个过程模型是有意为之的;它的设计有助于提供可靠、高性能和安全的 Web 浏览器体验。
>
安全优势(Security Benefits)
>从安全角度来看，限制对高特权操作的访问可减少恶意行为者可利用的外围应用。
>
>在WebContent进程中，在此过程中执行的代码是未知的，是从任意第三方获取的远程代码。在用户设备上安全执行此代码的唯一方法是使用尽可能低的权限。
>
>![Security Boundary](https://webperf.tips/static/26fe624f0dc8902751a77785a7e7c834/a4f81/ProcessModel07.png)
>
可靠性优势(Reliability Benefits)
>从可靠性的角度来看，通过在浏览器选项卡中大致*为每个 Web 应用程序分配单独的进程，Web 应用程序不会相互崩溃
>
>例如，如果有两个选项卡对不同的 Web 应用程序打开，则一个进程中的崩溃/段错误不会影响另一个选项卡或浏览器进程
>
>此外，如果浏览器内部进程(如网络进程*Network process*)崩溃，浏览器可以在用户甚至没有注意到崩溃的情况下重新启动它！
>
![different tabs](https://webperf.tips/static/37fea0e0afab8cee556f7a870a4a647b/d1882/ProcessModel08.png)
>[!NOTE]
>因为不同的浏览器有不同的算法来确定何时创建进程。行为往往会在`<iframe>`元素和打开指向新选项卡的链接等方案之间有所不同。
>
性能优势(Performance Benefits)
>与可靠性类似，从性能角度来看，将 Web 应用程序和浏览器相关工作转移到自己的专用进程中有助于减轻线程时间的竞争。
>
>例如，如果所有进程共享相同的主 UI 线程，则一个 Web 应用程序中的任何繁重工作都可能降低整个浏览器的速度和响应能力！
>
>![example](https://webperf.tips/static/e7f6b05628fd4ce19ca5424201f05509/7be33/ProcessModel09.png)
>
浏览器进程通信(Browser Process Communication)
>虽然 Web 应用程序和核心浏览器内部工作(如网络Networking)跨进程边界封装，具有适当范围的权限，但它们经常通过 IPC 相互通信
>
>IPC 通道允许进程公开功能以供其他进程使用。这保持了前面描述的多进程的好处，同时将核心功能公开给系统中的其他进程。
>
>![Browser process communication](https://webperf.tips/static/8a8a823be34c9473837c8f3252888048/966c1/ProcessModel10.png)
>
>例如，WebContent Process 中的 Web 应用程序可以通过使用 IPC 通道来请求网络数据，以通知网络进程所需的 URL 和方法。网络进程可以使用 IPC 通道在数据返回时通知 WebContent 进程。
>
>再举一个例子，浏览器进程可以通知 WebContent 进程用户从其键盘键入了一个字符。WebContent 进程可以处理此输入，并将按键事件调度到其 JavaScript 事件侦听器。
>

性能权衡(Performance Trade-offs)
>虽然我们已经讨论了浏览器多进程模型的性能优势，但也存在相关的成本和权衡。
>
内存开销(Memory Overhead)
>虽然进程提供了强大的安全边界，但启动新进程需要额外的内存分配。
>
>这意味着用户打开的选项卡越多，必须创建的进程就越多，浏览器必须消耗的内存就越多。
>
>虽然浏览器已经采取了缓解措施(mitigation)来帮助缓解内存受限环境中的这种成本，但内存消耗增加的总体趋势仍然存在。
>
>浏览器为帮助提高内存利用率而采用的缓解措施示例包括:
>
> - Draining stale tabs' memory onto disk*将过时选项卡的内存耗尽到磁盘上*
> - Combining processes in highly memory constrained environments (like low-end Android devices)*在高度内存受限的环境(如低端 Android 设备)中合并进程*
>
进程创建开销(Process Creation Overhead)
>创建新进程不一定很昂贵，但对于负载密集的低端设备，创建新进程的成本会更高，而不仅仅是现有进程中的新线程。
>
>浏览器也有相应的缓解措施来帮助解决这个问题，包括：
>
> - Keeping a spare WebContent Process available for a New Tab or Window to use*保留备用的 WebContent 进程以供新选项卡或窗口使用*
> - Parallelizing navigation requests with process creation to parallelize network and CPU activity when creating a new Tab or Window*通过进程创建并行化导航请求，以便在创建新选项卡或窗口时并行化网络和 CPU 活动*
>
IPC 开销(IPC Overhead)
>跨进程通信比在完全本地化中保持通信的单个进程要慢。
>
>此外，如果通信渠道堵塞，IPC 消息可能会延迟并降低用户体验。
>
>为了缓解这种情况，浏览器中的每个进程通常都有一个专门用于 IPC 消息传递的专用线程，该线程被设计为尽可能轻量级和响应式。消息格式也经过优化，可最大限度地减少进程之间的传输时间。
>
>