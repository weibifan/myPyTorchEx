weibifan，2022-10-24  
对于ML或DL来说，基本要素就是数据和模型  

## 数据集
数据集：是一个模糊的概念，通常指标注好的数据  
抽象出来就是多个对象，如果标注好了，说明有对象有标签。对象之间默认没有序关系。  
dataset = {(item1，label1), (item2, label2), ...} ，item一般由多个feature组成。  

- 根据任务的不同分为：文本数据，图像数据，语音数据等。Image Datasets, Text Datasets, and Audio Datasets  

- 根据训练模型的不同用途分为：train，test，valid等


## 模型
模型：也是一个模糊的概念，一般包括2部分，可运行的代码+这些代码的参数。  
训练：通过数据集，让模型的参数获得具体的值。

## PyTorch有专门的包来支持数据集和模型的操作
数据集的操作：加载，保存，访问（随机访问，迭代访问），可视化/预览，预处理（这个最复杂）。  
torch.utils.data.Dataset:表格式案例数据集，每行有标签。  
torch.utils.data.DataLoader:  表格数据的迭代器（实例的访问指针），minibatch迭代器。  
当在CPU上学习时，DataLoader的num_workers 只能设置为0，表示只用主进程访问数据。  

模型的操作：加载，保存，可视化，预训练等。


## PyTorch针对典型数据集和模型提供了3个工具包，torchvision, torchtext, torchaudio
这3个数据集和torch并不在一个空间内，需要单独下载。  
1）一些典型的数据集。比如FashionMNIST（图像分类），AG_NEWS（文本分类）等。  
2）一些典型的模型。比如VGG16  
3）包括这些数据集和模型的基本处理工具。  

torchvision包括：
1）数据集。
2）模型。①只有参数。②模型和参数。   扩展名都是pth
3）数据集和模型相关的工具。

TorchText：文本数据集及处理工具。https://github.com/pytorch/text
torchtext 基于TorchData  https://github.com/pytorch/data.

1）一个处理文本数据的工具包，将文本文件转换为Datasets对象。
功能包括：分词，将word变成数值，构建instance，将外存的文本数据加载到CPU的mem等
2）常见的文本数据集，及对应的对象。

### 文本处理的预训练模型：
Language modeling: WikiText2, WikiText103, PennTreebank, EnWik9
Machine translation: IWSLT2016, IWSLT2017, Multi30k
Sequence tagging (e.g. POS/NER): UDPOS, CoNLL2000Chunking
Question answering: SQuAD1, SQuAD2
Text classification: SST2, AG_NEWS, SogouNews, DBpedia, YelpReviewPolarity, YelpReviewFull, YahooAnswers, AmazonReviewPolarity, AmazonReviewFull, IMDB
Model pre-training: CC-100

### 视觉处理的预训练模型：

## 对于标注好的数据集的使用规范
  
第1步：从网络下载到本地。网络地址，本地缓存地址。  
网络地址：一个数据集可能出现在多个网络地址。  
①PyTorch数据集。  
②HuggingFace数据集  
③阿里云，百度Paddle等数据集，含有大量中文数据集。  
  
PyTorch数据集，本地默认位置的根是：C:\ Users\Wei/.cache\  

第2步：从本地加载到CPU的内存，转换为Tensor，加载到GPU，浏览数据的结构，补充标签信息。  
datasets.XXX()

文本数据：直接使用print函数  
图像数据：使用matplotlib的可视化函数。  

第3步：构建数据集的访问指针。分为随机访问，序列访问。  
由于深度学习模型默认是batch方式，造成需要的batch指针。一般不需要随机访问。

DataLoader()

第4步：对于深度学习，对数据集进行分区，还需要提供batch方式。  
分区方式1：train，test  
分区方式2：train，valid，test  

第5步：数据与模型的匹配，有时也称数据预处理。  
图像数据：需要将二维矩阵变成一维向量（flatten）。  
文本数据：需要分词，构建字典，变成索引，句子变成向量等。  

## 加载预训练模型的规范
对于训练好的模型： WebAPI方式，本地方式（离线方式）。下面介绍本地方式。   

第1步：从网络下载到本地。网络地址，本地缓存地址。  
网络地址：一个模型可能出现在多个网络地址。  
①PyTorch模型和参数。主要是经典论文的模型。  
比如VGG模型地址：https://download.pytorch.org/models/vgg16_bn-6c64b313.pth   
②HuggingFace模型和参数，HuggingFace Hub存放大量预训练好的模型。   
③Paddle模型和参数，含有大量中文数据集。   

PyTorch模型的本地缓存：C:/Users/Wei/.cache/torch/hub/checkpoints   
HuggingFace模型的本地缓存：   

第2步：第2步：模型和参数从本地加载到CPU的内存并转换为Tensor，浏览模型的结构及参数的值。    
需要 看说明是哪类模型：代码+参数，参数，代码   

第3步：准备模型的输入数据。

第4步：使用模型，得到输出，可视化输出。

第5步：保存模型。


## TorchData：PyTorch Dataset的升级版，
TorchData对标TensorFlow的tf.data，而functorch对标谷歌的JAX。
TorchData提供了一种新的数据构建方式：DataPipes，以替代PyTorch现有的torch.utils.data.Dataset，
这篇文章将简单介绍两种方式的区别以及新方式的主要用法。

Dataset需要实现 getitem() 和 len() 两个接口，这样就可以通过索引来获取样例：dataset[idx]。
Map-style datasets是我们最常用的dataset，还有另外一种dataset是iterable-style-datasets（IterableDataset），
它需要实现__iter__()接口，其实这两种数据类型都属于iterable，即可迭代对象，
可迭代对象可以通过调用iter()函数生成迭代器（iterator），也可以直接用for循环来进行遍历。
两种datasets适用场景有所区别：map-style dataset适合已知所有样本（比如上面的图像路径和标签），
并且可以全部加载到内存中，这样可以通过索引来随机读取；
但当随机读取成本较高或者不可能时，iterable-style dataset就比较适合了，比如实时产生的流式数据。
从本质上说，iterable-style dataset更通用，因为map-style dataset都可以转化为iterable-style dataset，
但是对于很多实际场景中，我们已知全部数据集，往往会采用map-style dataset，
它的一个好处是可以用torch.utils.data.Sampler来控制采样，
比如在分布式训练中用torch.utils.data.distributed.DistributedSampler来拆分数据，
而iterable-style dataset则不支持samper，此外在多进程读取时，iterable-style dataset也要单独处理防止数据重复加载，


TorchData引入DataPipes来替代现有的Dataset，它是一种可组合的数据加载和构建流程，
这和TensorFlow的tf.data非常相似，可以将DataPipes看成可组合的Dataset，
这使得很多操作比如读取和解析都模块化，从而增加代码复用性。

和原有的Dataset一样，DataPipes也有两种类型：IterDataPipes和MapDataPipes，
其中IterDataPipes对标IterableDataset，需要实现__iter__接口；
而MapDataPipes对标Dataset，需要实现__getitem__() 和 len() 两个接口。

TorchData库已经内置了很多的DataPipes，我们可以组合不同的IDataPipes来构建数据。
