# 跨领域句子级别中文省略消解评测（MCER2023）

* 主办单位：北京语言大学-信息科学学院-语言文化计算研究室
* 组织者：邵艳秋，李炜
* 联系人：祁佳璐
* 联系方式：blcu_lcclab@163.com

[评测报名链接](https://www.wjx.cn/vm/mWoc0nZ.aspx#)

## 最新通知

### 盲测集发布

盲测集将在6月4日以邮件的形式发送至各位团队负责人的邮箱，请注意查收。另外，请各位团队负责人添加微信：18347339956，备注：姓名+隶属单位，方便主办方在评测过程中及时与各团队取得联系。

### 结果提交

请在6月7日23：59前，将盲测结果与对应的代码实现打包发送至本次评测的官方邮箱 blcu_lcclab@163.com ，压缩包中应包含代码对应的readme文件，并在readme文件中写清每一个文件的作用/含义、模型的调用方式及对应的环境要求。

盲测结果格式请参考本次评测给出的训练集的数据格式，如得到这一格式的结果需对模型输出结果进行后处理，请将后处理部分代码一并打包提交。

## 1 任务简介

### 任务背景

省略是一种十分常见的语言现象，在中文领域尤为普遍。对于人类而言，自然语言中存在的省略现象并不影响语义理解；但是，对于机器而言，自然语言中存在的省略现象却是语义理解类自然语言处理任务面临的挑战之一，省略现象的存在一定程度上影响着机器阅读理解、机器翻译等下游任务的准确性。然而，中文自然语言处理领域关于省略的研究十分稀少，且语言学领域并没有被广泛认可的关于“省略”的定义。因此，我们推出针对中文自然语言处理的省略定义[1]，并依托CCL2023推出句子级别中文省略消解评测任务，希望能够引发更多对于“省略”这一语言现象的关注。

### 省略介绍

通俗地讲，省略即指省略文本中一些可以通过上下文理解的成分。比如，“他穿上外套，走出了房间”这个句子中，“走出了房间”前面缺少主语，但人类在语义理解这一环节，很容易通过上下文，明确走出房间的人是“他”，因此，出于“省力原则”，在语言表达的过程中则会对语义上重复的部分进行省略。

在语言学界，省略通常被划分为语法省略、语义省略和语用省略三大类[2, 3]。

* 语法省略：被省略的成分在句子中承担句法角色[4]，如上文提到的例子，逗号后面的短句中被省略的成分“他”在句子中充当主语。
* 语义省略：被省略的成分在句子中承担语义角色[4]，如“宝马的发动机比大众的好”，“大众的”与“好”之间省略了“发动机”，但句子的句法结构是完整的，只是对个别语义成分进行了省略，这样的省略即为语义省略。
* 语用省略：通常指由当前的语境导致的省略[2]（这里的语境可以是当前句子、对话、段落或整篇文章），例：

  ```
  - 你吃饭了吗？
  - 没有。
  ```

本次评测只研究单个句子中的语法和语义省略，且为保证补全的客观性，我们只对可以在原句中找到对应成分的句子进行补全。

### 任务内容

本次评测分为如下2个子任务：

* 子任务1，省略位置探测：探测省略句中省略现象出现的位置
* 子任务2，省略内容补全：**从当前句子中**找到可以补全当前省略的内容

## 2 评测数据

本次评测的数据集基于我们在NLPCC2022发布的数据集 MCER: A Multi-domain Dataset for Sentence-level Chinese Ellipsis Resolution，为该数据集的2.0版本，在原有数据的基础上，本次我们又针对微博语料进行了标注，当前数据集领域分布情况如下：


| 微博 | 新闻 | 小说 | 剧本 | 教材 | 产品评论 | Total |
| ------ | ------ | :----- | ------ | ------ | :--------: | :-----: |
| 2462 | 1815 | 521  | 282  | 579  |   434   | 6093 |

我们将数据集切分为训练集、验证集、测试集和盲测集4个部分，分布情况如下：


| 数据集（Data Set） | 句子数 |
| :-------------------: | :------: |
| 训练集（Train Set） |  3606  |
|  验证集（Dev Set）  |  602  |
|  测试集（Test A）  |  883  |
|  盲测集（Test B）  |  1002  |

数据样例：

```apache
{"uuid": "news_2007", "text": "中间不是没有抗拒过，我离开超视的一个原因，就是超视一位来自香港的总经理举香港电视台的《城市追击》节目为例，报导一位香港人在大陆「包二奶」，结果太太带着儿子跳楼，创下高收视率。", "targets": [{"index": "0", "content": "我"}, {"index": "53", "content": "《城市追击》"}]}
```

uuid为当前数据在数据集中的id，text为原句内容，targets中，index为省略现象出现的位置，content为这一位置应该补全的内容。（盲测集中只包含uuid和text两部分内容）

## 3 评价标准

### 子任务1：省略位置探测

1. 子任务1内容为省略位置探测，参赛者提交的结果应为整个句子中出现的所有省略现象的offset数组（对应训练数据中的index）。
2. 针对子任务1，我们主要关注Precision（P）、Recall（R）和F1值（F1）三个指标。其中 P = 正确预测出的省略位置个数/所有预测为省略的位置个数，R = 正确预测的省略位置个数/所有预测为省略位置的位置个数，F1 = 2 * P * R / （P + R）。
3. 我们将针对以上效果指标对所有题目计算平均值，作为参赛者在子任务1上的最终指标结果。
4. Baseline：针对子任务1，我们微调RoBERTa进行序列标注，并给出如下baseline：


   | Precision | Recall | F1-score |
   | :---------: | :------: | :--------: |
   |  0.8156  | 0.8553 |  0.8350  |

### 子任务2：省略内容补全

1. 子任务2内容为省略内容补全，参赛者提交的结果应为子任务1中预测出的省略位置和其对应的补全内容所组成的二元组。
2. 针对子任务2，我们主要关注以下几个指标：ROUGE-1、ROUGE-2、ROUGE-L[6]、Exact-match-score以及F1-score。其中ROUGE-1 = 预测结果与正确答案重合的1-gram个数/正确答案的1-gram总数，ROUGE-2 = 预测结果与正确答案重合的2-gram个数/正确答案的2-gram总数，ROUGE-L = 最长公共子序列长度/正确答案的总长度，Exact-match-score = 与正确答案完全一致的预测结果个数/预测结果总数。
3. 我们将针对以上效果指标对所有题目计算平均值，作为参赛者在子任务2上的最终指标结果。
   <img width="500" alt="image" src="https://user-images.githubusercontent.com/52641403/230570054-fd6e4b46-8d58-4287-a545-b44ca6259039.png">
4. Baseline：针对子任务2，我们微调BERT，采用问答的方法（将数据转化为SQUAD[5]格式，如上图，从而将省略补全任务转化为问答的形式），给出如下baseline：


   | ROUGE-1 | ROUGE-2 | ROUGE-L | Exact Match Score |
   | :-------: | :-------: | :-------: | :-----------------: |
   | 0.8052 | 0.5751 | 0.8049 |      0.6817      |

### 子任务一体化

我们将子任务1和子任务2中提到的baseline方法以pipeline的形式进行拼接，并给出如下baseline（在这种情况下，我们只关注子任务2中提到的ROUGE-1、ROUGE-2、ROUGE-L以及Exact-match-score几个指标）：


| ROUGE-1 | ROUGE-2 | ROUGE-L | Exact Match Score |
| :-------: | :-------: | :-------: | :-----------------: |
| 0.6955 | 0.4921 | 0.6955 |      0.6076      |

参赛者亦可以考虑使用联合学习，或对两个子任务联合建模等方法，对两个子任务进行一体化。

### 综合评分标准

1. 我们将取F1值作为子任务1的最终得分
2. 子任务2的最终得分：score2 = 0.4 * Exact-match-score + 0.3 * ROUGE-L + 0.2 * ROUGE-2 + 0.1 * ROUGE-1
3. 子任务一体化最终得分计算方法同子任务2
4. 以上三个部分的平均分将作为整个任务的综合得分

### 结果提交

参赛队伍提交的结果，可以参考训练集的数据格式。

## 4 评测赛程


|            时间            |                    事项                    |
| :--------------------------: | :------------------------------------------: |
| 2023年4月1日-2023年5月20日 |                  开放报名                  |
|       2023年4月11日       |             训练集、验证集发布             |
|       2023年5月11日       |                 测试集发布                 |
|        2023年6月4日        |                 盲测集发布                 |
|        2023年6月7日        | 参赛队提交盲测集省略现象自动探测及补全结果 |
|       2023年6月12日       |           公布参赛队伍成绩和排名           |
|       2023年6月25日       |        参赛队提交中文或英文技术报告        |
|       2023年6月28日       |           中文或英文技术报告反馈           |
|        2023年7月3日        |           正式提交中英文评测论文           |
|        2023年7月7日        |                公布获奖名单                |
|       2023年7月10日       |              评测论文录用通知              |
|       2023年7月15日       |            论文Camera Ready提交            |
|       2023年8月3-5日       |   CCL2023评测研讨会：获奖队伍做技术报告   |

## 5 报名方式

点击[评测报名链接](https://www.wjx.cn/vm/mWoc0nZ.aspx#)进行报名

## 6 奖项设置

云孚科技（北京）有限公司为本次获奖队伍提供总价值10000元的奖金：

一等奖：0-2名，奖金合计5000元

二等奖：0-2名，奖金合计3000元

三等奖：0-2名，奖金合计2000元

中文信息学会将会为本次评测获奖队伍提供荣誉证书

## 7 参考文献

[1] Qi, Jialu, et al. "MCER: A Multi-domain Dataset for Sentence-Level Chinese Ellipsis Resolution." Natural Language Processing and Chinese Computing: 11th CCF International Conference, NLPCC 2022, Guilin, China, September 24–25 (2022)

[2] Wang, S.: Study of ellipsis. studies of the Chinese Language (6), 409–415 (1985)

[3] Merchant J.: Three types of ellipsis. Context-Depend. Perspect. Relat. **6** , 141–192 (2010)

[4] McShane, M.J.: A Theory of Ellipsis. Oxford University Press on Demand (2005)

[5] Rajpurkar, P., Zhang, J., Lopyrev, K., Liang, P.: Squad: 100,000+ questions for machine comprehension of text. arXiv preprint arXiv:1606.05250 (2016)

[6] Lin, C.-Y.: Rouge: A package for automatic evaluation of summaries. Text summarization branches out. In: Text summarization branches out, pp. 74–81 (2004)

[7] Aralikatte, R., Lamm, M., Hardt, D., et al.: Ellipsis resolution as question answering: An evaluation. arXiv preprint arXiv:1908.11141 (2019)
