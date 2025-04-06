# 贝叶斯邮件分类系统

## 一、核心功能说明
本项目基于多项式朴素贝叶斯算法实现邮件分类，支持高频词特征与TF-IDF特征的灵活切换。系统包含以下核心模块：

### 1. 算法基础
#### 1.1 多项式朴素贝叶斯
**特征独立性假设**：
假设所有特征在给定类别下相互独立：
$P(x_1, x_2, \ldots, x_n | C)=\prod_{i = 1}^{n} P(x_i | C)$


**贝叶斯定理应用**：  
$P(C|X)=\frac{P(X|C)P(C)}{P(X)}\approx\frac{\prod_{i = 1}^{n}P(x_i|C)P(C)}{P(X)} $

#### 1.2 邮件分类实现
- **类别定义**：垃圾邮件（标签1）/普通邮件（标签0）
- **概率计算**：  
  
$P(C) = \frac{\text{类别C邮件数}}{\text{总邮件数}}, \quad P(x_i | C) = \frac{\text{词}i\text{在}C\text{中出现次数}+1}{\text{类别}C\text{总词数}+\text{词表大小}}$



## 二 数据处理流程
### 1. 分词处理
代码中使用 jieba.cut() 方法对邮件文本进行分词处理。具体实现是逐行读取邮件文件，对每行文本进行清洗（去除无效字符）后，调用 jieba.cut() 方法将文本切分成单个的词语。例如：
```python
with open(filename, 'r', encoding='utf - 8') as fr: for line in fr: line = line.strip() line = re.sub(r'[.【】0 - 9、——。，！~*]', '', line) line = cut(line)
```
### 2. 停用词过滤
停用词过滤是一个重要的预处理步骤。停用词是指在文本中频繁出现但对文本分类任务没有太大帮助的词语，如“的”“了”“是”等。可以通过定义一个停用词列表，在分词后过滤掉这些词语。示例代码如下：
```python
stopwords = set([line.strip() for line in open('stopwords.txt', 'r', encoding='utf - 8').readlines()]) words = [word for word in words if word not in stopwords]使用markdown实现
```
## 三 特征构建对比

| 特征类型      | 数学表达式               | 实现方式            | 特点         |
|-----------|---------------------|-----------------|------------|
| 高频词特征     |$f_{ij} = \text{词}i\text{在文档}j\text{中的出现次数}$       | CountVectorizer | 直接统计词频     |
| TF-IDF 特征 | $TF - IDF_{ij} = TF_{ij} \times IDF_{j}$ | TfidfVectorizer | 综合词频与逆文档频率 |


### 3.1 数学差异
#### 实现差异
#### 高频词特征
实现过程中，首先遍历所有邮件文件，将所有词语汇总到一个列表中，然后使用 collections.Counter() 统计每个词语的出现次数，选取出现次数最多的 
k 个词语作为特征词。对于每封邮件，统计这些特征词在该邮件中出现的次数，构建特征向量。示例代码如下：
```python
def get_top_words(top_num, filename_list):
all_words = [] for filename in filename_list: 
all_words.extend(get_words(filename)) freq = Counter(all_words) return [i[0] for i in freq.most_common(top_num)]

top_words = get_top_words(100) vector = [] for words in all_words:
word_map = list(map(lambda word:
words.count(word), top_words)) vector.append(word_map)
```
#### TF-IDF特征
实现过程中，首先遍历所有邮件文件，将所有词语汇总到一个列表中，然后使用 collections.Counter() 统计每个词语的出现次数，选取出现次数最多的 
k 个词语作为特征词。对于每封邮件，统计这些特征词在该邮件中出现的次数，构建特征向量。示例代码如下：
```python
def get_top_words(top_num, filename_list):
all_words = [] for filename in filename_list:
all_words.extend(get_words(filename)) freq = Counter(all_words) 
return [i[0] for i in freq.most_common(top_num)]

top_words = get_top_words(100) vector = [] for words in all_words:
word_map = list(map(lambda word:
words.count(word), top_words)) vector.append(word_map)
```
## 高频词/TF - IDF 两种特征模式的切换方法
代码中通过 extract_features 函数的 feature_method 参数来实现两种特征模式的切换。具体如下：
```python
def extract_features(filename_list, feature_method='top_words', top_num=100):
```
### 高频词特征
<img src="https://github.com/huangchenglin11/dev_skills_images/blob/master/images/gaopingci%202025-03-29%20222934.png">

### TF-IDF特征
<img src=https://github.com/huangchenglin11/dev_skills_images/blob/master/images/TF_IDF%202025-03-29%20223008.png>

