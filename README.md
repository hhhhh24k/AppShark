## AppShark——涉诈APP智能识别分析系统

### 一、项目背景
随着数字化转型的加速发展，犯罪形态发生了显著变化，在诸多诈骗类型中，又以电信网络诈骗犯罪最为普遍。这类犯罪从传统的电话、短信诈骗，逐步转向利用APP等网络工具进行，成为当前发展增速最快、涉及范围最广的刑事犯罪类型，各种诈骗APP层出不穷，严重威胁公民安全。
本项目旨在开发一个“涉诈APP智能识别分析系统”，利用人工智能技术，提高对电信网络诈骗软件的研判能力。系统将识别和分析潜在的涉诈APP，助力营造APP绿色生态。
### 二、功能简介
#### 1. apk采集
我们采用爬虫程序，根据输入或识别二维码得到的url，递归爬取其子页面下的所有apk链接。我们采用chrome浏览器内核驱动，模拟用户真实的浏览行为，获取动态渲染后的网页。同时，通过添加随机的点击和滚动操作，确保能够获取到隐藏的下载链接。程序将提取二维码内包含的下载链接，自动获取相应的apk。此外，用户也可直接提供apk文件，系统将统一管理用户上传和互联网采集到的apk文件。
#### 2. apk解析
此功能分为静态解析和动态解析两部分。
对于静态解析，系统将先解包apk，通过特征提取与黑白名单对比，再基于序列预测分类模型（BiLSTM）进行源代码分析，基于多模态图像分类模型（CLIP-CN）进行涉诈图像识别，基于卷积神经网络模型（URLNet）进行恶意网址检测。最后，系统将根据上述分析结果与信息为用户生成分析报告，供用户查看。
对于动态解析，我们使用docker打包了Android sdk ，appium ，frida ，adb等安卓动态调试分析工具，为每位用户提供安卓虚拟环境，保证用户隐私安全的同时允许用户运行可能涉诈的APP。在APP运行过程中我们将实时捕捉应用产生的网络流量，抓取应用界面截图，劫持应用运行时函数调用，综合分析APP。
我们基于训练过的大语言模型（qwen2 1.8B）提供报告生成服务，根据APP解析后的各种信息，可以生成专业精准的分析报告。
#### 3. 黑白名单管理
黑名单管理用于识别和管理已确认存在安全风险或涉诈行为的APP。该模块支持全用户社区共享，支持 及时更新和扩展黑名单库，防止这些有害应用继续传播。 
白名单管理用于管理和维护已确认安全的APP列表。该模块同样支持全用户社区共享和及时更新扩展，确保这些应用程序不会被误判为潜在威胁。
#### 4. AI Chat
本系统全程支持AI专业问答，助力用户对APP解析信息进行更专业、更深入的了解。我们采用微调过的qwen2 7B大模型，使用大量安全领域的论文对模型进行微调。同时涉及一些指令上的微调，以输出更好的措辞。根据用户的输入，模型将根据对当前APP解析信息的理解作出解答。
### 三、亮点创新
#### 1. 高效涉诈代码检测模型
系统使用当前恶意代码分类领域的SOTA模型——BiLSTM，进行代码检测。这一模型不仅深度挖掘了代码中的潜在欺诈模式，还通过其卓越的序列处理能力，对代码的逻辑流与结构进行了细致入微的分析。
除题目特供的APK数据集外，我们另外收集并标注了2000+个apk文件进行扩充，对BiLSTM模型进行了预训练，使其能够精准地适应并识别本题特定场景下的欺诈行为，从而实现高准确度检测。
#### 2. 多模态图像分析模型
系统引入了CLIP-CN多模态模型，对APK文件中的图像资源进行深入分析。通过提取图像的纹理、形状、颜色等特征，输入CLIP-CN模型中计算标签相似度，结合文本描述和源码分析，使得系统能够更准确地识别涉诈类型。
#### 3. CNN恶意网址检测模型
系统采用URLNet，利用深度学习技术从URL中自动学习嵌入特征，实现捕捉语义信息、通联地址，摆脱了手工特征工程依赖。通过卷积神经网络处理字符和单词，系统实现了对新生成恶意URL的高效检测，并在大规模数据集上验证了其显著的性能提升。
#### 4. 浏览行为仿真爬虫
系统采用BFS算法，从给定的初始URL开始，逐层爬取网页及其子页面，寻找下载链接。这种方法确保了爬虫能够高效地发现所有潜在的下载链接。且考虑到许多下载链接是通过JavaScript动态生成的，系统采用Selenium等工具模拟真实用户的浏览行为，获取动态渲染后的网页内容。这种方法提高了下载链接的发现率。
#### 5. 大语言模型
系统基于解析APP得到的应用基本信息、涉诈图片、恶意网址等数据训练大语言模型qwen2 1.8B，可以提供AI报告生成服务。根据APP解析后的各种信息，系统可以生成专业全面的分析报告。
系统还采用大量安全领域的论文对qwen2 7B大模型进行了微调，可以提供AI专业问答服务。同时涉及一些指令上的微调，以输出更好的措辞。
#### 6. 高并发
在单个APK的分析过程中，我们进行了流程优化，实现了高并发处理，使得特征分析、源码分析、图像分析、URL分析四个模块可以并发执行。通过降低分析步骤间的耦合度，我们确保了各个模块之间的独立性与灵活性，保证互不干扰地处理不同任务。对于多个APK文件的分析，系统同样支持并发处理。这种设计不仅提升了处理速度，还确保了在高负载环境下的稳定性能，保证了系统的吞吐量。
