# [Kalman and Bayesian Filters in Python](https://github.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python)

本项目是[Kalman and Bayesian Filters in Python](https://github.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python)的中文翻译，这本书以足够通俗易懂的方式来解释了卡尔曼滤波器等一众滤波器的原理，相比其他书中一下子列出一堆公式更容易理解一些，所以我打算尝试翻译这本书，也算是督促和巩固我的学习进度，如有翻译有误还请谅解，并直接修改提交，或者提issue，如果有能力还是建议直接阅读原文。

本书主要介绍了卡尔曼和贝叶斯滤波器的原理和用法。文中的所有代码都是用Python编写的，本书本身是使用Juptyer Notebook编写的，因此您可以在浏览器中运行和修改代码。 还有什么比这更好的学习方法吗！


**"Kalman and Bayesian Filters in Python" looks amazing! ... your book is just what I needed** -  and O'Reilly作者，Allen Downey教授.

**Thanks for all your work on publishing your introductory text on Kalman Filtering, as well as the Python Kalman Filtering libraries. We’ve been using it internally to teach some key state estimation concepts to folks and it’s been a huge help.** - Sam Rodkey, SpaceX

原文支持binder和Azure badge的线上阅读:


[![Binder](http://mybinder.org/badge.svg)](https://beta.mybinder.org/v2/gh/rlabbe/Kalman-and-Bayesian-Filters-in-Python/master)
<a href="https://notebooks.azure.com/import/gh/rlabbe/Kalman-and-Bayesian-Filters-in-Python"><img src="https://notebooks.azure.com/launch.png" /></a>



![alt tag](https://raw.githubusercontent.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python/master/animations/05_dog_track.gif)

什么是卡尔曼和贝叶斯滤波器？
-----

所有的传感器都会带来噪声。我们所在的世界充满了我们想要测量的数据和跟踪事件，但我们不能依靠传感器来为我们提供完美的信息。我车里的GPS可以报告高度。每当我通过某条道路上的同一个地方的时候，它报告的高度总会略有不同。如果我用厨房里的秤称同一个物体称重两次，每次称的重量也会不同。

在简单的情况下，有很简单的解决方案。如果我的秤给出的读数略微不同，我可以只取几个读数并取平均值。或者我可以换一个更准确的秤。但是当传感器的误差就是很大或者需要测量的环境使收集数据收集很困难时，我们该怎么办？比如，我们可能正试图追踪一个低空飞行器的运动。又比如我们想为无人机做一个自动驾驶仪，或者我们想用农用拖拉机播种，确保覆盖整个农田。我从事的是计算机视觉工作，我需要跟踪图像中的移动物体，但是计算机视觉算法会产生非常大的误差和不可靠的结果。

本书教你如何解决这些问题。我使用了许多不同的算法，但它们都是基于贝叶斯概率的。简单来说，贝叶斯概率根据过去的信息来确定未来哪些可能是真实的。

我来举个栗子，如果我此刻问你的车头朝哪个方向，你可能无法确定。你可以说出1到360度之间的数字，并且有1/360的机会是正确的。现在假设我告诉你，2秒前它的朝向为243∘∘。 并且在2秒内，我的车无法转得很远，那么你就可以做出更加准确的预测。你这就是在使用过去的信息来更准确地推断有关当前或未来的信息。

真实世界同样充满噪声。预测可以帮助您做出更好的估算，但同时也会受到噪声的影响。我可能只是为了一条狗而踩刹车，或是在坑洞里转弯。路上的强风和冰对我车的轨迹产生外部影响。在相关文献中，我们都把这些叫做噪声，尽管你可能不会这样认为它们不是。

贝叶斯概率有很多种，但你只需要了解主要的思想。知识是不确定的，我们需要根据正确的事实来不断改变我们的原有的认知。卡尔曼滤波器和贝叶斯滤波器将噪声、传感器有限的能力、和我们对含有噪声的系统所掌握的有限条件综合考虑在一起，产生对系统状态的最佳估计。我们的原则是永远不丢弃信息。

假设我们正在跟踪一个物体，并且传感器报告它突然改变方向。它是真的转了，还是因为噪音数据？这需要视情况而定。如果这是一架喷气式战斗机，那我们更倾向于相信它是在突然机动的结论。如果是沿直线轨道运动的货运列车，我们需要折中考虑。我们将根据传感器的准确度来进一步修正我们的认知。我们的认知取决于过去的经验以及我们对所跟踪的系统的了解以及传感器的特性。

卡尔曼滤波器是由Rudolf Emil Kálmán发明的，他通过数学上最优方法来解决这类问题。它第一次的应用是阿波罗登月，从那以后它被用于各种各样的领域。在飞机，潜艇和巡航导弹上都有卡尔曼滤波器的使用。华尔街使用它来跟踪市场。它还用于机器人，IoT（物联网）传感器和实验室仪器。化工厂使用它来控制和监测反应。它也用于医学成像技术，去除心脏信号中的噪声。凡是涉及传感器和/或时间序列数据，通常都会涉及卡尔曼滤波器或与卡尔曼滤波器密切相关的其他滤波器。

目的
-----

写作本书源于我希望能能对卡尔曼滤波做一个详细的介绍。我是一名软件工程师，在航空电子学领域从事了将近二十年的时间，所以我一直在用卡尔曼滤波器，但我自己从未实现过。当我开始用计算机视觉解决跟踪问题时，这种需求变得迫切。该领域有很多经典的教科书，如Grewal和Andrew写很棒的*卡尔曼过滤*。但是，如果你没有相关的背景知识，坐下来试图阅读这些书会让你感到十分的低落。通常，前几章将讲完需要好几年学习的本科基础数学知识，轻松地向您介绍微积分等内容，并在几个简短的段落中介绍完需要一整个学期的统计学。这些书是高年级本科课程很好的教材，也是相关研究人员和专业人士的宝贵的参考工具，但对于一般的读者而言，读起来确实很困难。引入的符号没有解释，不同的文献对同一个概念使用不同的术语和变量，而且书籍几乎没有例子或实际问题。我经常发现自己能够明白字面意思并理解所定义的数学公式，但却不知道他们到底描述的是现实世界中的什么现象。 “但是，这到底有什么*意义*？”我一度在思考。

然而，当我最终理解卡尔曼滤波器时，我意识到其实它的基本概念非常简单。简单的概率规则，我们基于常识来解释日常生活中事件的直觉，以及卡尔曼滤波器的核心概念都是可以理解的。卡尔曼滤波器以困难著称，但由于缺少了许多正式的术语，主题和数学的美对我来说变得清晰，我爱上了这个课题。

当我开始理解数学和理论后，出现了更多的困难。一本书或一篇论文的作者作了一些事实的陈述，并提出了一个图表作为证据。不幸的是，我不清楚为什么这句话是真的，也不清楚如何证明它。或者我想知道“如果r=0，这是真的吗？”，或者作者在提供了十分精炼的伪代码，以至于实现起来并不容易。有些书提供matlab代码，但我没有使用这种昂贵软件包的许可证。最后，许多书在每章的最后提供了许多有用的练习。如果你想自己练习实现卡尔曼滤波器，你需要理解，但是这些练习没有答案。如果你在学校里用这些本书，也许这没有影响，但这对独立阅读者来说实在太可怕了。我讨厌作者对我隐瞒信息，大概是为了避免学生在课堂上作弊。

在我看来，这些都不必要。当然，如果你在为飞机或导弹设计卡尔曼滤波器，你必须彻底掌握典型卡尔曼滤波器教科书中的所有数学理论。而我只想跟踪屏幕上的图像，或者为Arduino项目编写一些代码。我想知道书中的图像是如何制作的，并且尝试不同于作者选择的参数。我希望可以运行模拟。我想在信号中注入更多的噪声，看看滤波器是如何工作的。在日常编码中有成千上万的机会使用卡尔曼滤波器，然而那些最基础的理论还是让火箭科学家和学者来研究吧。

我写这本书就是为了满足所有这些需求。如果你为波音公司设计导航计算机或为雷神公司设计雷达，那这不是你想看的书。你需要去佐治亚理工大学、华盛顿大学或类似的地方攻读个高级的学位。这本书是为那些对这方面感兴趣、好奇和工作中需要过滤平滑数据的工程师准备的。

这本书是交互式的。虽然您可以将它作为静态内容在线阅读，但我极力建议您有计划的使用它。它是使用Jupyter Notebook 编写的，它将文本、数学公式、python代码和python的输出组合在一个地方。本书中的每一个图、每一段数据都由python生成的，这些代码您都可以修改。想将参数的值加倍吗？单击python单元，更改参数的值，然后单击“运行”。新的绘图或打印输出就会出现在书中。

这本书中有练习，但也有答案。我相信你。如果你只需要一个答案，你可以继续阅读答案。如果你想消化吸收这些知识，那么在阅读答案之前，请试着自己实现这些练习。

这本书需要计算统计，绘制与过滤器相关的图，以及我们所涉及的各种过滤器的库。这确实要提醒一下；大多数代码是以教学目的而编写的。我很少选择最有效的解决方案（这样常常会掩盖了代码的原本意图），而且在书的第一部分，我并不关心数值稳定性。理解这一点很重要——飞机上的卡尔曼滤波器是经过精心设计和实现，所以在数值上是稳定的；在许多情况下，原始的实现并不稳定。如果你是认真的学习卡尔曼过滤器，那么这本书将不会是最后一本你需要的书。我的目的是向你们介绍概念和数学公式，并让你们能够理解教科书所表达的程度。

最后，这本书是免费的。即使对我这样的硅谷工程师来说，购买学习卡尔曼滤波器所需的书籍的成本也有点令人望而却步；我无法相信，这些书籍能让那些在经济能力不高或经济困难的学生买到。我从诸如python之类的免费软件和AllenB.Downey[这里](http://www.grenteapress.com/)的免费书籍中获益匪浅。是时候报答了。因此，这本书是免费的，它托管在免费的服务器上，并且它只使用免费和开放的软件，如ipython和mathjax来创建这本书。

## Reading Online

The book is written as a collection of Jupyter Notebooks, an interactive, browser based system that allows you to combine text, Python, and math into your browser. There are multiple ways to read these online, listed below.

### binder

binder serves interactive notebooks online, so you can run the code and change the code within your browser without downloading the book or installing Jupyter.

[![Binder](http://mybinder.org/badge.svg)](https://beta.mybinder.org/v2/gh/rlabbe/Kalman-and-Bayesian-Filters-in-Python/master)


### nbviewer

The website http://nbviewer.org provides an Jupyter Notebook server that renders notebooks stored at github (or elsewhere). The rendering is done in real time when you load the book. You may use [*this nbviewer link*](http://nbviewer.ipython.org/github/rlabbe/Kalman-and-Bayesian-Filters-in-Python/blob/master/table_of_contents.ipynb) to access my book via nbviewer. If you read my book today, and then I make a change tomorrow, when you go back tomorrow you will see that change. Notebooks are rendered statically - you can read them, but not modify or run the code.

nbviewer seems to lag the checked in version by a few days, so you might not be reading the most recent content.


### GitHub

GitHub is able to render the notebooks directly. The quickest way to view a notebook is to just click on them above. However, it renders the math incorrectly, and I cannot recommend using it if you are doing more than just dipping into the book.


PDF Version
-----

A PDF version of the book is available [here](https://drive.google.com/open?id=0By_SW19c1BfhSVFzNHc0SjduNzg)


The PDF will usually lag behind what is in github as I don't update it for every minor check in.


## Downloading and Running the Book

However, this book is intended to be interactive and I recommend using it in that form. It's a little more effort to set up, but worth it. If you install IPython and some supporting libraries on your computer and then clone this book you will be able to run all of the code in the book yourself. You can perform experiments, see how filters react to different data, see how different filters react to the same data, and so on. I find this sort of immediate feedback both vital and invigorating. You do not have to wonder "what happens if". Try it and see!

The book and supporting software can be downloaded from GitHub by running this command on  the command line:

    git clone --depth=1 https://github.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python.git
    pip install filterpy

Instructions for installation of the IPython ecosystem can be found in the Installation appendix, found [here](http://nbviewer.ipython.org/github/rlabbe/Kalman-and-Bayesian-Filters-in-Python/blob/master/Appendix-A-Installation.ipynb).

Once the software is installed you can navigate to the installation directory and run Juptyer notebook with the command line instruction

    jupyter notebook

This will open a browser window showing the contents of the base directory. The book is organized into chapters. To read Chapter 2, click on the link for chapter 2. This will cause the browser to open that subdirectory. In each subdirectory there will be one or more IPython Notebooks (all notebooks have a .ipynb file extension). The chapter contents are in the notebook with the same name as the chapter name. There are sometimes supporting notebooks for doing things like generating animations that are displayed in the chapter. These are not intended to be read by the end user, but of course if you are curious as to how an animation is made go ahead and take a look.

This is admittedly a somewhat cumbersome interface to a book; I am following in the footsteps of several other projects that are somewhat repurposing Jupyter Notebook to generate entire books. I feel the slight annoyances have a huge payoff - instead of having to download a separate code base and run it in an IDE while you try to read a book, all of the code and text is in one place. If you want to alter the code, you may do so and immediately see the effects of your change. If you find a bug, you can make a fix, and push it back to my repository so that everyone in the world benefits. And, of course, you will never encounter a problem I face all the time with traditional books - the book and the code are out of sync with each other, and you are left scratching your head as to which source to trust.


Companion Software
-----

[![Latest Version](http://img.shields.io/pypi/v/filterpy.svg)](http://pypi.python.org/pypi/filterpy)

I wrote an open source Bayesian filtering Python library called **FilterPy**. I have made the project available on PyPi, the Python Package Index.  To install from PyPi, at the command line issue the command

    pip install filterpy

If you do not have pip, you may follow the instructions here: https://pip.pypa.io/en/latest/installing.html.

All of the filters used in this book as well as others not in this book are implemented in my Python library FilterPy, available [here](https://github.com/rlabbe/filterpy). You do not need to download or install this to read the book, but you will likely want to use this library to write your own filters. It includes Kalman filters, Fading Memory filters, H infinity filters, Extended and Unscented filters, least square filters, and many more.  It also includes helper routines that simplify the designing the matrices used by some of the filters, and other code such as Kalman based smoothers.


FilterPy is hosted github at (https://github.com/rlabbe/filterpy).  If you want the bleading edge release you will want to grab a copy from github, and follow your Python installation's instructions for adding it to the Python search path. This might expose you to some instability since you might not get a tested release, but as a benefit you will also get all of the test scripts used to test the library. You can examine these scripts to see many examples of writing and running filters while not in the Jupyter Notebook environment.

Alternative Way of Running the Book in Conda environment
----
If you have conda or miniconda installed, you can create environment by

    conda env update -f environment.yml

and use

    source activate kf_bf

and

    source deactivate kf_bf

to activate and deactivate the environment.


Issues or Questions
------

If you have comments, you can write an issue at GitHub so that everyone can read it along with my response. Please don't view it as a way to report bugs only. Alternatively I've created a gitter room for more informal discussion. [![Join the chat at https://gitter.im/rlabbe/Kalman-and-Bayesian-Filters-in-Python](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/rlabbe/Kalman-and-Bayesian-Filters-in-Python?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)


License
-----
<a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">Kalman and Bayesian Filters in Python</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="https://github.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python" property="cc:attributionName" rel="cc:attributionURL">Roger R. Labbe</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/4.0/">Creative Commons Attribution 4.0 International License</a>.

All software in this book, software that supports this book (such as in the the code directory) or used in the generation of the book (in the pdf directory) that is contained in this repository is licensed under the following MIT license:

The MIT License (MIT)

Copyright (c) 2015 Roger R. Labbe Jr

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.TION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

Contact
-----

rlabbejr at gmail.com
