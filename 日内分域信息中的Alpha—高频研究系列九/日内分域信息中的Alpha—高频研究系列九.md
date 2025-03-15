# 日内分域信息中的Alpha—高频研究系列九

原创 兴证金工团队 

[XYQuantResearch](javascript:void(0);)

 *2024年08月22日 08:00* *上海*



投资要点

- **2022年以来，兴证金工团队先后推出了阐述高频研究方法论的《高频漫谈》，以及八篇高频因子深度研究。**在首篇高频漫谈中，我们阐述了高频因子的构建逻辑、因子的回测方法以及高频风险的识别。在后续八篇高频因子研究报告中，我们构建了约45个因子，分类涵盖分布、时序和关联信息等。
- **本文中，我们将重点放在另类信息中的一种：日内分域信息。**对于日内交易而言，投资者通常会从交易所处的时间、价格和交易活跃度三个角度进行分析判断。**在本文中，我们将尝试从时间、股价和成交量维度出发，对日内信息进行主观分域，并以此衍生出显著性和股票间成对相关性多类特异性因子。**
- **算法层面，我们创新性地将日内量价分域和股票间量价成对相关性进行结合，并对显著性算法进行适应性改造。**具体来说，在分域维度，我们拆分出分域使用的数据以及域内特征数据，并采用不重叠方式进行分域。在股票间成对相关性中，我们基于分域，进行标准化处理以减弱日内数据导致的周期型和噪音问题。在显著性维度，我们将收益率替换为多维度数据进行因子刻画。
- **新因子在具备选股能力的同时兼具优秀的特异性，尤其是从成对相关性角度 构建的因子，其特异性更加出色。**以部分为例：自身显著性因子intraDS Rank IC均值大多接近0.04；算法更加精细的“同伴”显著性因子peerDS同样优秀。此外，全市场相似性和“同伴”显著性因子在算法层面与之前大多数高频因子存在差异，因子表现出极强的特异性。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9ibUOlKPdgN0kANOFsTgZ7MwGnTlqRRpAO31ArvuWzsyfhXLiaKV169icQ/640?wx_fmt=png&from=appmsg&wxfrom=13)

**风险提示：模型结果基于历史数据的测算，在市场环境转变时模型存在失效的 风险。**



**1、高频研究回顾与日内分域信息简析**



1.1

**高频研究回顾**



2022年以来，兴证金工团队先后推出了阐述高频研究方法论的《高频漫谈》，以及《收益率分布因子构建》、《收益率分布中的Alpha(2)》、《成交量分布中的Alpha》、《时序信息中的Alpha》和《关联信息中的Alpha》高频因子深度研究。在《高频漫谈》中，我们阐述了高频因子的构建逻辑、因子的回测方法以及高频风险的识别。该篇也是我们整个高频系列的基础篇和框架篇，后续所有的研究均建立在此基础上。据此我们介绍了四类高频指标的信息：分布信息、时序信息、关联信息与另类信息。在后续八篇高频因子研究报告中，我们构造了约45个高频量价因子，其中不乏多个思路新颖、具有较强特异性的分布和时序信息因子。**本文中，我们将聚焦于第四类信息，另类信息中的一种刻画方式—日内分域信息。**



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9uTXsiccH2mKbibGicOibC8lARt7XRJdrXBSKEz8iabX5fF0AFlTV6wZWNzw/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

1.2

**日内分域信息简析**



在前序文章中，我们将高频因子分为四大类信息。其中，另类信息指的是指标*g*依赖于不属于数据***data\***的其他信息，如研究人员事先规定的概念，尾盘、开盘、大单等等。如果参数是通过其他信息确定的，那么指标*g*含有另类信息。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9A48XkIwH0YBOwC3lmm1YmEYY0uuRQp1z6SbPiarkiazyBGLq5u9OiaNBw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

不难看出，另类信息本质是限定条件较少的一类因子刻画方式，研究者可以在额外信息中加入各类的限定方式以进行特征的刻画。对尾盘成交占比这类因子而言，**其核心逻辑在于通过观察市场交易者的共同特征，在某一特定交易环境中刻画特异信息，进而刻画选股因子。**因此，若能够从交易者的角度出发规定不同的交易环境，我们便能相对准确地刻画出个股在交易上的特异性。**对于日内交易信息而言，投资者通常会从当下交易所处的时间、价格和交易活跃度三个角度进行分析判断：**对于时间而言，有大量研究论证投资者倾向于在开盘、中盘和尾盘中交易，不同时段内的交易特征存在差异；对于价格而言，日内的不同价格区域中投资者的情绪存在差异，如在高价区域可能出现更多的“追涨”行为；对于交易活跃度而言，集中交易使得投资者有机会隐藏交易行为，或降低交易成本，尾盘的交易行为本身也是投资者集中交易的体现。**在本文中，我们将尝试从时间、股价和成交量维度出发，对日内信息进行主观分域，并以此刻画出多种多样的Alpha信息。**本文结构如下：

1. **基础型因子构建：**我们根据时序或股价的相对高低对日内数据进行分块，进而刻画出相对有效的基础型高频因子；
2.  **自身显著性因子构建：**我们基于分域思想，从股票自身日内的三种交易特征出发，基于显著性理论构建多种特异性因子；
3. **全市场与“同伴”显著性因子构建：**我们跳脱出仅基于个股日内数据刻画因子的框架，基于全市场日内量价数据刻画出股票间成对相关性，再以此构建出两类特异性因子。



**2、分域信息基础型因子**





在往期系列报告中，我们均会优先介绍基础型因子。这类因子通过相对简单的方法构建，以刻画本篇报告主题的基础型信息。我们的输入数据包括分钟级收盘价、最高价、最低价、成交量等。



2.1

**日内时序分域**



分时是最为简单的分域逻辑：即通过将日内数据进行较长间隔周期的切片，得到能够代表一段时间内股价走势的量价数据，进而根据特征构建因子。对于时序分域来说，最简单且最直观的特征便是开盘和尾盘的差异。我们尝试根据开盘和尾盘在量价信息上的差异，刻画基础型高频因子。因子算法展示如下。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9WSKFs2Fd4nkXBwjQibstkKaUBYemPR6iazVe2QQuxLmXApRxptNEnPLw/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

2.2

**因子表现**



我们首先测试上述3个因子的表现。从日度Rank IC测试结果上看，大多数分时段特征基础型因子IC均值在0.03左右，表现出较好的股价预测能力。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9AibRILgpgqQcuHf6EtK5Goh7ORBV6n0DctXluGWExvA2kpmDMItaUwA/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从日度组合测试结果上看，我们构建的分时段特征基础型因子的表现均相对优秀，大部分因子的多空夏普大于3。具体来看，从多空组合测试上看，lh_stdDiff因子的多空收益率在35%左右，空头效应十分显著，多头效果则相对一般。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9licMkvbBUSuw7MbaFibfX3cKKNicKGplltcJHhnSWg2kAqoWQrlf3OP9Q/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

2.3

**日内股价分域**



在前一小节中，我们根据时序信息对日内量价特征进行拆分，并通过差异刻画构建因子。在本节中，我们尝试按照股价进行分域，进而针对高价和低价区域的股价特征进行差异性和相似性刻画。然而，由于日内分钟数据的随机性较高，若想要通过高低价进行分域，我们尝试通过累加的方式减少估计的误差，并引入最高价和最低价作为输入数据。具体算法如下：

1. **日内高价/低价时序特征刻画：**我们使用分钟最高价和最低价序列，首先分别计算日内序列的排序百分位，再进一步计算序列过去15分钟均值，作为短时间内股价的高位特征和低位特征。具体计算公式为：

   ![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9gZ0ZIBicbo0ZvCz5aWO77BxOibQHuJCqBkgPGKFKBfBcFicMNPdVjd1iaA/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

   其中， *high*和*low*代表个股日内分钟最高价和最低价，*pct rank*为排序百分位。

2. **日内高价/低价时点确定：**我们选择高位和低位特征最强的时间点作为日内该股的高价或低价时点，即对于高价时点而言，我们选择排序最高的时点、对于低价时点我们选择排序最小的时点；

   

   ![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9p2cKBpaiaicCW2M5icnBlx738xuaJsVDE8l8ku35so2jKibUZydxR27o0g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

3. **日****内高价/低价时点高频指标刻画：**确定好高价时点和低价时点后，我们计算高价时点和低价时点的出现时间点、过去15分钟内分钟已实现波动率和15分钟内成交量占比之和，并进一步计算高点对应特征减低点对应特征，构建三种差异因子。

   

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9t7rLPxibib64lwHW1zwvZLCVUscaGibx6BwD03zqQUTFk8bGPUHF5UZ1Q/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

2.4

**因子表现**



我们首先测试上述3个因子的表现。从日度Rank IC测试结果上看，部分高低价区域特征基础型因子IC均值在0.03左右，表现出较好的股价预测能力。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9wIaw0ia7hgiaBNqdDUEJUcmopLP2T5iagwCr8pk2Nz4W9zrcGib3DNDCMQ/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从日度组合测试结果上看，我们构建的日内股价分域特征基础型因子的表现相对优秀，部分因子的多空夏普大于3。具体来看，从多空组合测试上看，diff_vol因子的多空收益率在23%左右，多头年化收益率约为13%，多空夏普比率约为4。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9S6ENMiceNYn2PiaJhTwAMLgzXd4gWHdoLYQgL2X3kOHYVGVvicBdlxtKg/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**3、****分域信息特异性因子：日内显著性**





在前文中，我们从时序和价格两个维度出发，刻画出了股票在分域中的数据差异，进而构造出基础型因子。在本章中，我们首先通过分域后的信息，从自身角度出发，基于显著性理论构建多种特异性因子。



3.1

**投资者有限注意与显著理论**





在传统金融市场理论中，市场参与者通常被假设为完全理性的个体，他们能够有效地获取、分析市场信息，并据此做出最优的投资决策。然而，现实世界中的金融市场往往表现出非理性行为，这促使行为金融理论应运而生。行为金融学从投资者的实际心理和决策过程出发，对现代主流金融理论提出挑战，并为解释市场异常现象提供了新的视角。**行为金融理论的一个重要分支是投资者有限注意理论。**该理论最早由Kahneman（1973）提出，他指出个体的认知资源是有限的，需要在不同的信息源之间进行分配。当个体将注意力集中在某一特定信息上时，对其他信息的注意力就会相应减少。这种现象被称为有限注意力现象。

在金融市场中，由于投资者的注意力是有限的，他们往往会选择性地关注那些显著或易于理解的信息，而忽视其他可能同样重要的信息。这种选择性关注可能导致投资者对市场信息的不完全处理，从而影响其投资决策的质量和效率。**在非有效市场中，基本面和量价类信息均为投资者关注的信息。投资者的有限注意属性将导致股价走势对投资行为形成反馈机制。**当股票出现相对明显的优秀走势、交易过度活跃或波动率极低时，有限注意的投资者将过度关注该股，进而又吸引了其他投资者的注意。由此，学术界引出了显著理论，该理论尝试解决由于注意力差异产生的股票未来收益差异问题。研究者们发现，低显著性公司确实存在短期动量，而高显著性公司则存在强烈的短期反转。对于单只股票而言，股票自身在日内的走势也存在着显著性特征：**即在日内某一种交易环境下出现过有别于自身正常状态的显著走势特征的股票关注度将提升。在本小节中，我们首先以股票日内自身的交易属性作为出发点，刻画股票日内某一交易环境下是否出现过相对显著的走势，并进一步构建选股因子。**



3.2

**基于日内交易环境差异的分域算法介绍**





具体来说，日内显著性刻画分两个部分：**首先根据时间、价格、成交量等特征对日内量价信息进行分域，进而确定不同交易环境的范围（域），再根据每个域内的特征刻画显著性。**其中，在分域维度，我们尝试针对时间、价格和成交量作为输入进行分域，并在之后的计算中尝试不同维度的数据进行域内特征刻画。

**首先是相对复杂价格和成交量的分域方法介绍。**由于个股日内的分钟级别数据存在较大的随机性，因此我们采用切片方式来提高分域的鲁棒性。我们分域的核心处理方式为：**采用不重叠方法分域，再构建域内特征数据。**具体来说，我们首先按照不重叠原则，将需要被分域的时间序列数据进行特征序列刻画。进一步，我们对特征序列进行等频分域。最后，我们计算每个分域内的量价特征。

**其次是时间分域。**时间分域则相对简单，即采用半小时作为间隔进行分域，并进一步刻画不同时间域内的量价特征，如半小时波动率特征。其大致公式如下：



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUfOyu1o91WxdrWb6ChqwiaKbnYVLQCszrZRLcwhjtkT4fQk5uWsdFK68icdp6mjMtEsL2DU0mfVAGCA/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

综上，在上述算法中，我们首先需要先验地确认需要被分域的特征，分别是时间、价格和成交量。进一步，对于每种被确定的被分域特征，我们引入剩余数据刻画最终的域内特征。我们在本章中尝试的构建方式如下：



![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9SibfBMtO7dQmL1r7Ks9uzaswVABomp13WDCe5RGptrwVLT2KKbdTVug/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

3.3

**分域下交易特征显著性算法与因子构建**





在得到分域和域内特征之后，我们进一步刻画日内股价的显著性。显著性算法来自于Mathijs Cosemans和Rik Frehen于2021发布的期刊。在该文章中，作者提出了显著性算法的要求，即其函数需要满足三个条件：**保序性，敏感性递减和对称性。**保序性意味着随着特征偏离总体程度上升，函数数值应该变大；敏感性递减意味着随着总体特征水平统一上升，显著性应当下降；对称性指的是显著性只取决于特征的大小，并不取决于特征的符号。在该文章以及后续研究中，学者们大多以收益率作为输入数据。在本节中，我们将跳脱出原始使用方式，**使用域内特征对单只股票进行日内显著性判断。**具体来说，我们基于每种分域方式（时间、价格、成交量），首先得到每种方式下的域内特征***qcutCHR***，在进一步引入显著性算法，针对每种域内特征（如波动率、成交量占比等）计算显著性，最终得到因子值。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUfOyu1o91WxdrWb6ChqwiaKbOnhxPx6HZWhImbFHxbJouKtwrz6K99QBB60gEvpzUGpy0eiczzlgwVg/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

基于前一小节的分域和显著性算法，我们可以批量性地构造日内显著性因子：对于三种日内分域方式，我们各有两种域内特征，共计六种因子计算方式。对于每个显著性因子，我们均预期因子为升序排序，即显著性程度越大，预期收益越低。此外，我们均在时序上取20日均值作为最终测试因子。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf91mJtAj7rVCs3DstlGkOTuREVb53A3Pf6blxibA8Bok5oeeGciara5byg/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

3.4

**自身显著性因子表现**



从日度IC测试结果上看，自身显著性因子表现较好，多数因子Rank IC均值大于0.04，ICIR大于0.5，表现出较好的股价预测能力。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9sECrj7fMgO5huIiaeKmiaaW9M3DkSWbnU60G0DUL6MkRr5VxP1Qmm0tw/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从日度组合测试结果上看，自身显著性因子的表现十分优秀，绝大多数因子多空夏普均较高，且无明显回撤，多头收益率较高。其中，以价格和成交量分域得到的因子表现更优，时序分域中收益率显著性因子表现相对一般。具体来看，从多空组合测试上看，intraDSVol_byP的多空收益率在33%左右，多头收益率约为15%，夏普比率在6左右。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9fUhYibIwiaM2jTdmMg85vlV9gt9ELwXVu4vE397oBAbjX2kXaOkWmOyA/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我们选择intraDSVol_byP展示Rank IC和多空净值时序图。从Rank IC时序特征、多空净值曲线以及分位数组合测试结果上看，intraDSVol_byP因子的多空净值长期呈现上升趋势，且长期和2024年以来均无明显回撤，表现十分稳定。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9MjfTkKvzhU6jwDqYWZGqIUfWpXuKgXxibMmsT3A0ksfg0ib899177ZyQ/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

3.5

**自身显著性反转因子构造**





从上一章节中可以看出，直接基于显著性构建的因子获得了不错的选股能力。在本节中，我们尝试将日内动量反转特征加入显著性因子刻画中。具体来说，对于一只存在日内显著走势的股票来说，其本身的显著性可以作为预期收益的判断方式，但显著性本身不带方向性。在本节中，我们假设当股票日内出现显著的交易特征，且股价走势相对较好时，有限注意的投资者将过度关注该股，同时吸引了更多投资者的注意，推高股价进而导致股价短期内存在被高估的可能性。**因此，日内显著本质是针对日内动量反转特征进行精细刻画，强调存在显著差异特征情况下个股反转属性。**

因此，我们在前一章节的基础之上，将当期显著性指标乘上对应区间的收益率。对于时间分域而言，该收益率即为显著性最强的时序区间收益率。对于股价和成交量而言，该收益率即为显著性最强的域内收益率均值。与前文类似，我们也共计构建出6个因子。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9v8ibInYyPUQWFxnzRBiasicLLV0uib8BErfxuPxa2JRIOtiaSkfGOd59qYg/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

3.6

**自身显著性反转因子表现**





从日度IC测试结果上看，自身显著性反转因子表现相对较好，但并未比之前的原始显著性因子表现更好。部分因子Rank IC均值为0.03，ICIR大于0.5，表现出较好的股价预测能力。以成交量进行分域刻画的反转因子表现更为出色。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf94SvmtAWn8H4tvg32dibF2F3QrY94dV226xDRM6DDAib1Sfg02aG81Icg/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从日度组合测试结果上看，自身显著性反转因子的表现十分优秀，绝大多数因子多空夏普均较高，且无明显回撤，多头收益率较高。其中，以成交量分域得到的因子表现更优。具体来看，从多空组合测试上看，intraDS2Rtn_byV的多空收益率在33%左右，多头收益率约为12%，夏普比率在5左右。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9A7xKWpB0lSZicfvG7HT6bsdngOQicyVZxRicv3jwArvw5oYLCyd4Uo6JQ/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我们选择intraDS2Rtn_byV展示Rank IC和多空净值时序图。从Rank IC时序特征、多空净值曲线以及分位数组合测试结果上看，intraDS2Rtn_byV因子的多空净值长期呈现上升趋势，且长期以来无明显回撤，表现十分稳定。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf92jzVzZkibgfHRNNLlg9WibYIcjgjLenD1iaNPicdibIFDVxAiawC1byb15Qw/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

3.7

**因子特异性分析**



由于自身显著性和自身显著性反转因子的构造方式类似，我们首先测算上述因子间的相关性，并展示如下。首先聚焦于自身显著性和自身显著性反转因子两类因子之间的相关性（对应右上和左下角），**我们发现两者间绝大多数的因子相关性较低，均值接近0.34，这表明两者之间获取收益的能力存在差异。**进一步，我们聚焦于自身显著性和显著性反转因子自身（对应左上角和右下角），从测试结果上看，除了成交量分域的两个因子之外，时序和股价分域得到的两个因子组内相关性较低，可见该算法下，**因子特异性或主要来源于域内特征的刻画：域内特征为波动率、收益率或成交量相关的因子相关性较高。**



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf96VWCavfWkic1d7EHvsZmntVia86tDFyITaDssKrUH1lCL8iaDciaSyTHkQ/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

除此之外，我们进一步探究新高频因子和传统低频量价因子的特异性。具体来说，我们选择常见的动量反转、波动率、换手率等低频量价因子，与自身显著性因子进行截面相关性检验分析。我们的测试周期为周频（日频效果与此类似，便不在此赘述）。我们以箱形图的形式展示因子低频量价因子的相关性。与从结果上看，**自身显著性和显著性反转因子与传统低频量价因子保持着较低的相关性，显著性反转因子的特异性更强。**整体统计而言，上述因子截面相关因子均值约为0.14，最高相关性接近0.5，同样维持了较高的特异性。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9Pv2qK7Tsqa9GiaNtaJKZPiaOgZBQVOl05W0pOmicF932nz3RZVcic04dHg/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

结合相关性和因子表现，我们选择表现相对优秀的因子和底层现有高频因子进行正交化处理分析。具体来说，我们在自身显著性中选择intraDSVol_byT和intraDSVol_byP；在自身显著性反转中选择intraDS2Rtn_byV，共计三个因子。三个因子均和底层所有高频因子中相关最高的因子进行正交化处理，**处理后因子与底层所有因子时序相关性均值约为0.35，保持较高的特异性。**

从日度IC测试结果上看，三个因子表现相对较好，部分因子Rank IC均值维持或接近0.03，ICIR基本稳定在0.5左右，表现出较好的股价预测能力。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9dx0kkUX5lLU5dUCfMyTFibfSYhianibqzwhXBeia3EnSjf4FS5EpKUNmtg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从日度组合测试结果上看，自身显著性因子正交化后的表现仍然相对优秀，多空夏普比率均较高，且无明显回撤，多头收益率较高。经过正交化之后，其表现并未出现明显下降。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9lRqeSz54QadNTL78xvEqpgxxfd1KsFvibElxmL5poSYSYARecH9wiaog/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf90LP09IMibvkg0QpOq5rlAf9Nj5ElBpEibibFDxlYaTP17OdNt0wBtenpw/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**4、分域信息特异性因子：股票间显著性**





在上一章节中，我们首先构建了基于日内量价信息的分域方式，并进一步引入显著性算法，基于股票自身的走势特征计算不同维度的显著性。在本节中，我们尝试跳脱出基于个股自身刻画因子的框架，基于全市场数据刻画出股票间的成对相关性和显著性。



4.1

**股价变化模式与股票间相似度**





金融市场中，投资者对于有着一致认知的股票，可能存在着相似的交易行为，并最终导致股价出现相似的走势特征。因此，无论是日内还是日间，学术界和投资者们均尝试通过股价或交易特征相似程度来挖掘个股之前的相似性，常见如DTW算法等。在得到个股之间的成对相似性后，研究者们就此展开了相似动量、配对交易、热点追踪等相关研究。**兴证金工团队就曾基于专利、产业链、分析师共同覆盖等维度构建选股因子或相关策略开发，并已经取得一定的成果。**

然而，不同于日间，日内的量价则较少用于相似性的刻画，其主要原因在于日内切片颗粒度导致的噪音问题以及日内交易的周期性问题。日内的量价特征本身存在着更低的信噪比，其对于相似算法的要求将提升，而现如今大多数算法难以直接引用在日内数据之上。因此，大多数研究者们转而针对部分时间段或部分典型特征（如大单等）进行相似刻画，但这也损失了部分信息。此外，日内数据本身存在着一定的周期性，如开盘和收盘阶段的交易集中、或者大量套利投资者在极端价格趋于交易导致趋同性等。因此，日内交易的周期性会导致股票在相似价格位置或相似时序位置存在一定的共性运动，进而导致以日内股价信息作为输入数据来计算相似性时显得较为困难。**综上，在基于日内数据进行相似性刻画时，我们通过分域和分域标准化的方式，尝试减弱日内数据导致的周期型和噪音问题，并最终构建因子。**



4.2

**日内量价关联性刻画与股票成对相关性**





正如前文所述，若想要基于日内交易特征进行相似度刻画时，我们难以绕开周期性问题以及日内交易的噪音问题。因此，我们在处理过程中尝试将分域想法引入相似的刻画方式中，并进一步将各个域内的特征进行标准化处理，并最终刻画出股票间的量价关联性。具体计算方式如下：

1. **引入分域数据：**在前一章节中，我们已经通过时间、价格和成交量数据对股票的日内数据进行分域。具体来说，对于时间数据而言，我们以半小时作为间隔将日内数据分为8块，每块内引入收益率和成交量数据、在价格和成交量维度，我们通过相对复杂的方式将日内数据分为5块，每块内同样引入不同维度的数据。因此，对于当日而言，我们分域最终获得的数据维度均为矩阵化数据 ***qcutCHRALL***，记为分域特征矩阵：

   ![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9rzmz11OfU11GhLMUdRKna3zsVBDvqz7tUZ7tvXwHkxaJt1JnXTDptw/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

   其中，*qcutCHRij* 代表单只股票对应分域特征数据在第 *j* 个域内的量价特征。对于时间维度的分域而言*J = 8*，对于成交量和价格维度的分域而言 *J = 5*。此外，每个维度下单个交易日全市场共包含两个分域特征矩阵，对应引入的两种数据。

2. **分域标准化：**对于单个分域维度而言，我们基于每个域特征数据进行标准化处理，以此消除日内数据的周期性问题。具体来说，我们针对分域特征矩阵按列进行标准差处理，得到 ，其中每个项的计算公式为：

   

   ![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9CfPHzYZBfHQxdGVl97k7jAicetyc0pHl5vUa4WlVxvaJTrhmrlguMFw/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

   其中, qcutCHRSTDij 代表单只股票对应标准化分域特征数据在第 *j* 个域内的量价特征。与之前一样，每个维度下单个交易日全市场共包含两个分域特征矩阵，对应引入的两种数据。

3. **股票间相似度刻画：**得到标准化分域特征矩阵后，我们以日度为周期回望过去一段时间，并在每个域内通过计算股票间相似度。具体来说，以回望周期为10为例，任意两只股票在每个域上均有长度为10的特征向量，我们以此计算皮尔逊相关系数得到相似度数值，并最终遍历域后取均值得到相似度均值：

   

   ![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9JDU3MMAL0nHAovwR2QpqyibkdiaWEGaTRUOK8IVwqhAeial5HKYjVzOLQ/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

   其中, ***qcutCHRSTDaj*** 分别代表两家公司，代表过去一段时间内 *a* 公司在域 *j* 上的特征数据，*corrCHRa,b* 则代表两家公司在某一特征数据上的相似度。最终，由于每个维度下有两种数据，我们最终同样取均值得到最终的股票间相似度矩阵***corrCHR***。

   总结而言，在基于日内量价数据的相似刻画中，我们以分域作为核心思想，通过分域、域内标准化和域内相似度刻画的方式，尝试减弱日内数据信噪比低以及交易周期性问题。**最终，我们基于上述算法，结合之前的三类分域数据总共可以得到以下几种相似度矩阵，数据结构均为每个交易日一个成对相似度矩阵。**

   

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9PZc4ZpkxGibeBQOv0vdfZ725P4meoNysGDMMyV4icrvQ47vTyZheCNWQ/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

4.3

**股票间相似度基础型因子刻画与因子表现**





在得到个股之间的相似度矩阵之后，我们首先对矩阵进行基础型刻画，其本质是对每个交易日的成对相似度矩阵进行压缩。具体来说，我们通过刻画相似度矩阵的均值和标准差进行简单的压缩，以刻画个股和市场上所以股票的相似度情况，若均值越高或者标准差越低，说明该股与市场整体的趋同性更强，反之可能走势相对独立。因此，从逻辑上看，均值应当为正向因子，标准差可能为反向因子。我们的最终因子值均经过过去20日均值处理。在数据维度，我们沿用之前的三种分域数据以及各两种分域特征数据，并均测试均值和标准差的方法。具体的因子定义如下，共计测试12个因子。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9GREevW3DL3Y3UJPibicdJO6I8kQ0kJrrbmOlqiaXZHfXRCINWfznKSialQ/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我们首先展示上述所有因子的日度Rank IC测试。从结果上看，相似度基础型因子表现相对优秀，Rank IC均值大多在0.03左右。其中，以分域数据来说，基于成交量和股价分域得到的因子表现整体相对更好；以特征数据而言，收益率相关因子表现更加出色。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9pI3Wa6VYOKV5kVXia2oe70VnzqAZU5v55uVP7XDkdTeNyrNplyG5bqw/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9FZ64WrKVHulDEtvYb7mOGjYKIghQUzXbaE2uXC1VeQWc0Zic0PVpE8Q/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我们进一步统计相似度基础型因子的特异性。具体来说，我们统计了这些因子和底层所有高频因子的时序相关性，并以箱形图的形式展示如下。从测试结果上看，剔除部分表现相对较差的因子，绝大多数因子保持了较高的特异性，**其相关系数均值约为0.3，最高值也接近0.6。即使不进行正交化处理，同伴显著性因子也具备极强的特异性。**



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9icjDQDic2L2wCHFjLCXU34hMSBMnGFZVgj5PSYvRQelExiaLgYxcPic0CQ/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

4.4

**显著性进阶—同伴间显著性因子**





在上述章节中，我们基于日内分域思想，刻画出了个股日内走势的成对相似度。**在本节中，我们尝试将显著性理论与相似性结合，刻画个股在“同伴”间的股票显著性。**

在早期的显著性理论中，学者们将显著差异与股价走势结合，通过对回望周期内的股价走势进行加权处理以得到股票被高估的相对幅度，进而刻画选股因子。在实践中发现，这类算法刻画出来的特征在A股市场中与传统的动量反转因子相关性较高。**往后，学者们开始从公司相对市场的显著性刻画转而研究公司相对其“同伴”公司之间的显著性，以提高特征的特异性。**无论是基本面还是量价维度，投资者对于股票的认知均会重点参考其对应的“同伴”公司。Yili Chen等人于2024年探讨了在“同伴”公司对比中由显著性引起的注意力扭曲。他们研究发现：如果一只股票的近期表现相对于一组有代表性的“同伴”公司是显著突出的，那么投资者就会倾向于对价格变化或这些价格变化的相关新闻做出过度反应，这或许是由于注意力被扭曲了。具体而言，当股票与“同伴”公司的股价走势或基本面信息出现差异时，将自下而上地刺激注意力，较高水平（较低水平）的显著性会吸引（分散）注意力，从而导致股价异动。

**在该思路中，如何定义“同伴”公司则变得至关重要。**在基本面维度，研究者们使用行业、分析师覆盖、专利等形式刻画相似公司。在量价和交易维度，我们则可以沿用上一章节提及的分域相似矩阵。日内数据带有更细致的交易信息，能够提供有别于传统数据的相似刻画方式。因此，我们沿用之前的算法，以个股之间的成对相关性作为输入，引入“同伴”公司概念和显著性算法，刻画“同伴”显著性因子。最终，我们计算得到三种因子，分别代表时间、价格和成交量分域下的同伴显著性因子。显著性越高的股票预期收益相对偏低。除此之外，我们进一步取过去20日均值作为最终的因子值。



4.5

**同伴显著性因子表现**



从日度IC测试结果上看，同伴显著性因子表现相对较好，Rank IC均值均接近0.04，ICIR大于0.5，表现出较好的股价预测能力。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9qHuqILmveBnsKhuSGACCicO10OLzqvevMsq4OvqyxQq6oPdVyUobKSg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从日度组合测试结果上看，同伴显著性因子的表现十分优秀，绝大多数因子多空夏普均较高，且无明显回撤，多头收益率较高。其中，以时序和成交量分域得到的因子表现更优。具体来看，从多空组合测试上看，peerDS_byT的多空收益率在34%左右，多头收益率约为15%，夏普比率在4.5左右。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9wtZNrC4C9aE8zib63I1CQYZxrZqXJugA7ZUK9757v5IdbiaFF2xLdYkg/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我们选择peerDS_byT展示Rank IC和多空净值时序图。从Rank IC时序特征、多空净值曲线以及分位数组合测试结果上看，peerDS_byT因子的多空净值长期呈现上升趋势，且长期以来无明显回撤，表现十分稳定。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf95rxicyp7WogLfziaXNfrzaibeibRK0Fcw2XSiajo2uhgto8bgKwTrGSy3fw/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

4.6

**同伴显著性特异性分析**





我们进一步统计同伴显著性因子的特异性。具体来说，我们统计了这三个因子和底层所有高频因子的时序相关性，并以箱形图的形式展示如下。从测试结果上看，三个因子均保持了较高的特异性，**其相关系数均值约为0.35。即使不进行正交化处理，同伴显著性因子也具备极强的特异性**。进一步，我们将三个因子和底层所有高频因子中相关最高的因子进行正交化处理，处理后因子与底层所有因子时序相关性均值约为0.1左右，**保持极强的特异性**。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9Vmt3B8C4ZJiaCJ3j0xwF98Qp2L2PdVdNbIV8QPRibMOc7xnr21bpONSw/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

除此之外，我们进一步测试因子与传统低频量价因子的相关性。与前文类似，我们测试“同伴”显著性因子与动量反转、波动率、换手率等因子之间的截面相关性，并同样以箱形图的形式展示如下。**从结果上看，三个“同伴”显著性因子与传统低频量价因子保持着较低的相关性，其截面相关因子均值约为0.17，最高相关性接近0.35，**同样维持了较高的特异性。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9WR8wIOB2HgXwOetF6etBQdCqAEs5jCvoibLJJNNcdZBPDXtDIVW0ibHg/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我们接下来测试经过正交化后的因子表现。从日度IC测试结果上看，三个正交化后的因子表现相对较好，部分因子Rank IC均值为0.03，ICIR基本稳定在0.5左右，表现出较好的股价预测能力。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9Ql6Y5Suib3ZrRiaUEZzokEvXpSJ7fCDShtSviaf6ibQe0xhicV8KDuElf0g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**5、总结**





在本文中，我们将尝试从时间、股价和成交量维度出发，对日内信息进行主观分域，并以此刻画出多种多样的Alpha信息。对于基础型因子，我们根据时序或股价的相对高低对日内数据进行分块，进而刻画出相对有效的基础型高频因子。对于自身显著性因子而言，我们基于分域思想，从股票自身日内的三种交易特征出发，基于显著性理论构建多种特异性因子。对于全市场与“同伴”显著性因子而言，我们跳脱出仅基于个股日内数据刻画因子的框架，基于全市场日内量价数据刻画出股票间成对相关性，再以此构建出两类特异性因子。在后续的报告中，我们将继续深耕该方向，将更优秀的研究分享给投资者们。





**附录**





- **回测设定说明**



在本文中如无额外说明，我们因子的回测规则设定如下：

1. 因子回测区间：2014年12月31日—2024年6月30日；
2. 回测规则：剔除当期不在市、涨跌停以及特殊处理的股票；
3. 交易规则：对于T日收盘构建的因子值，我们首先与T+1日判断当日股票的交易情况，剔除当日不可交易和涨跌停的股票后于T+1日收盘买入，并于T+2日收盘卖出；
4. 回测结果说明：本文中，回测表格中提及的年化波动率、夏普比率、最大回撤、胜率均为因子回测时的多空净值对应统计量，多头换手率为多头组的单边换手率，且我们这里的多空是两分组并按照因子值进行加权（参见《高频漫谈》）。



- **低频量价因子表**



在本文部分段落中，我们将高频因子与低频量价因子进行相关性检验。本节中我们展示用于检验的低频量价因子定义表，引入的低频量价包括动量反转、波动率、换手率等。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/317OgPOEicUcra3hEC6tlVVxtfI4LEzf9IL3xkuzSPl4vVQW0QH1iaa2dadibnCNzgiaDtc0IKT7bWIDNGeInibPxeA/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**参考文献**





[1]  Bordalo, P., Gennaioli, N. and Shleifer, A. (2022) Salience, Annual Review of Economics.

[2]  Chen, Y., Wang, H., & Yu, J. (2023). Salience and short-term momentum and reversals. SSRN Electronic Journal. 

[3]  Kahneman, D. (1973). Attention and Effort. Englewood Cliffs, NJ: Prentice-Hall.

[4]  Tilak, G., Széll, T., Chicheportiche, R., & Chakraborti, A. (2013). Study of statistical correlations in Intraday and daily financial return time series. Econophysics of Systemic Risk and Network Dynamics, 77-104.





**风险提示：模型结果基于历史数据的测算，在市场环境转变时模型存在失效的风险。**





注：文中报告节选自兴业证券经济与金融研究院已公开发布研究报告，具体报告内容及相关风险提示等详见完整版报告。

证券研究报告：《高频研究系列九—日内分域信息中的Alpha》

对外发布时间：2024年7月31日

报告发布机构：兴业证券股份有限公司（已获中国证监会许可的证券投资咨询业务资格）

\--------------------------------------

分析师：

郑兆磊

SAC执业证书编号：S0190520080006

E-mail: zhengzhaolei@xyzq.com.cn



\--------------------------------------



更多量化最新资讯和研究成果，欢迎关注我们的微信公众平台（微信号：XYQuantResearch）！