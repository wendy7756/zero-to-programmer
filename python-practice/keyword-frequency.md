# 使用Python快速统计关键词及其词频

> 发布时间：2019.01.31  
> 阅读量：18,896  
> 点赞：22  
> 收藏：107  
> 评论：5  

## 项目概述

本文介绍如何使用Python快速统计文本中的关键词及其出现频率。通过jieba分词和字典统计，可以快速分析文本内容，提取关键信息。

## 实现思路

整体思路分为以下几个步骤：

1. **文本分词**：通过jieba库分词获取所有词语列表
2. **频次统计**：计算列表里出现词语及其对应的频次，存储为字典
3. **噪声过滤**：删除字典中无关且频次高的词语的键值对
4. **结果排序**：对字典里的词语按照频次进行排序
5. **结果输出**：输出频次前五的词语及其频次

## 环境准备

### 安装jieba库

如果没有安装jieba库，需要使用cmd进入命令提示符窗口，通过以下命令进行安装：

```bash
pip install jieba
```

### 导入必要的库

```python
import jieba
import re
from collections import Counter
import matplotlib.pyplot as plt
from wordcloud import WordCloud
```

## 基础实现

### 1. 简单词频统计

```python
def simple_word_frequency(text):
    """
    简单的词频统计实现
    """
    # 使用jieba进行分词
    words = jieba.cut(text)
    
    # 转换为列表
    word_list = list(words)
    
    # 统计词频
    word_count = {}
    for word in word_list:
        if word in word_count:
            word_count[word] += 1
        else:
            word_count[word] = 1
    
    # 按频次排序
    sorted_words = sorted(word_count.items(), key=lambda x: x[1], reverse=True)
    
    # 输出前5个高频词
    print("词频统计结果（前5个）：")
    for i, (word, count) in enumerate(sorted_words[:5]):
        print(f"{i+1}. {word}: {count}")
    
    return word_count

# 测试示例
text = """
Python是一种跨平台的计算机程序设计语言。是一个高层次的结合了解释性、编译性、
互动性和面向对象的脚本语言。最初被设计用于编写自动化脚本，随着版本的不断更新
和语言新功能的添加，越来越多被用于独立的、大型项目的开发。
"""

result = simple_word_frequency(text)
```

## 完整源代码实现

```python
import jieba

def analyze_text_frequency(file_path=None, text=None):
    """
    分析文本词频的完整实现
    
    Args:
        file_path: 文本文件路径
        text: 直接输入的文本内容
    """
    
    # 1. 读取文本内容
    if file_path:
        with open(file_path, 'r', encoding='utf-8') as file:
            content = file.read()
    elif text:
        content = text
    else:
        print("请提供文件路径或文本内容")
        return
    
    print(f"原始文本长度: {len(content)} 字符")
    
    # 2. 使用jieba分词
    words = jieba.cut(content)
    word_list = list(words)
    print(f"分词后词语数量: {len(word_list)}")
    
    # 3. 统计词频
    word_count = {}
    for word in word_list:
        # 过滤单字符和空白字符
        if len(word.strip()) > 1:
            word_count[word] = word_count.get(word, 0) + 1
    
    print(f"有效词语种类: {len(word_count)}")
    
    # 4. 删除无关高频词（停用词）
    stop_words = {
        '的', '了', '在', '是', '我', '有', '和', '就', '不', '人', 
        '都', '一', '一个', '上', '也', '很', '到', '说', '要', '去', 
        '你', '会', '着', '没有', '看', '好', '自己', '这', '那', '它',
        '他', '她', '们', '与', '及', '或', '但', '然而', '因为', '所以'
    }
    
    # 删除停用词
    filtered_words = {word: count for word, count in word_count.items() 
                     if word not in stop_words}
    
    print(f"过滤停用词后词语种类: {len(filtered_words)}")
    
    # 5. 按频次排序
    sorted_words = sorted(filtered_words.items(), key=lambda x: x[1], reverse=True)
    
    # 6. 输出结果
    print("\n=== 词频统计结果（前10名）===")
    for i, (word, count) in enumerate(sorted_words[:10]):
        print(f"{i+1:2d}. {word:10s} : {count:3d} 次")
    
    return sorted_words

# 使用示例
if __name__ == "__main__":
    # 示例文本
    sample_text = """
    人工智能是研究、开发用于模拟、延伸和扩展人的智能的理论、方法、技术及应用系统的一门新的技术科学。
    人工智能是计算机科学的一个分支，它企图了解智能的实质，并生产出一种新的能以人类智能相似的方式做出反应的智能机器。
    该领域的研究包括机器人、语言识别、图像识别、自然语言处理和专家系统等。
    人工智能从诞生以来，理论和技术日益成熟，应用领域也不断扩大，可以设想，未来人工智能带来的科技产品，
    将会是人类智慧的"容器"。人工智能可以对人的意识、思维的信息过程的模拟。
    """
    
    result = analyze_text_frequency(text=sample_text)
```

## 高级功能实现

### 1. 支持文件读取的版本

```python
def analyze_file_frequency(file_path, encoding='utf-8'):
    """
    从文件读取内容并分析词频
    """
    try:
        with open(file_path, 'r', encoding=encoding) as file:
            content = file.read()
        
        print(f"成功读取文件: {file_path}")
        return analyze_text_frequency(text=content)
        
    except FileNotFoundError:
        print(f"文件未找到: {file_path}")
        return None
    except UnicodeDecodeError:
        print(f"文件编码错误，尝试使用gbk编码")
        return analyze_file_frequency(file_path, encoding='gbk')

# 使用示例
# result = analyze_file_frequency('article.txt')
```

### 2. 自定义停用词

```python
def load_stopwords(stopwords_file=None):
    """
    加载停用词列表
    """
    # 默认停用词
    default_stopwords = {
        '的', '了', '在', '是', '我', '有', '和', '就', '不', '人', 
        '都', '一', '一个', '上', '也', '很', '到', '说', '要', '去', 
        '你', '会', '着', '没有', '看', '好', '自己', '这', '那', '它',
        '他', '她', '们', '与', '及', '或', '但', '然而', '因为', '所以',
        '可以', '这个', '那个', '这些', '那些', '什么', '怎么', '为什么',
        '虽然', '如果', '但是', '而且', '或者', '以及', '以上', '以下'
    }
    
    if stopwords_file:
        try:
            with open(stopwords_file, 'r', encoding='utf-8') as f:
                file_stopwords = set(line.strip() for line in f)
            return default_stopwords.union(file_stopwords)
        except FileNotFoundError:
            print(f"停用词文件未找到: {stopwords_file}，使用默认停用词")
    
    return default_stopwords

def advanced_word_frequency(text, stopwords_file=None, min_length=2, top_n=10):
    """
    高级词频分析功能
    """
    # 加载停用词
    stop_words = load_stopwords(stopwords_file)
    
    # 分词
    words = jieba.cut(text)
    
    # 过滤和统计
    word_count = {}
    for word in words:
        word = word.strip()
        # 过滤条件：长度、停用词、数字
        if (len(word) >= min_length and 
            word not in stop_words and 
            not word.isdigit()):
            word_count[word] = word_count.get(word, 0) + 1
    
    # 排序
    sorted_words = sorted(word_count.items(), key=lambda x: x[1], reverse=True)
    
    # 输出结果
    print(f"\n=== 高级词频分析结果（前{top_n}名）===")
    for i, (word, count) in enumerate(sorted_words[:top_n]):
        print(f"{i+1:2d}. {word:15s} : {count:3d} 次")
    
    return sorted_words[:top_n]
```

### 3. 词频可视化

```python
import matplotlib.pyplot as plt
import numpy as np

# 设置中文字体
plt.rcParams['font.sans-serif'] = ['SimHei']  # 用来正常显示中文标签
plt.rcParams['axes.unicode_minus'] = False    # 用来正常显示负号

def visualize_word_frequency(word_freq_list, top_n=10, chart_type='bar'):
    """
    词频可视化
    
    Args:
        word_freq_list: 词频列表 [(word, count), ...]
        top_n: 显示前n个词
        chart_type: 图表类型 'bar' 或 'pie'
    """
    if not word_freq_list:
        print("词频列表为空")
        return
    
    # 取前n个词
    top_words = word_freq_list[:top_n]
    words = [item[0] for item in top_words]
    counts = [item[1] for item in top_words]
    
    plt.figure(figsize=(12, 6))
    
    if chart_type == 'bar':
        # 柱状图
        bars = plt.bar(range(len(words)), counts, color='skyblue', alpha=0.8)
        plt.xlabel('词语')
        plt.ylabel('频次')
        plt.title(f'词频统计柱状图（前{top_n}名）')
        plt.xticks(range(len(words)), words, rotation=45)
        
        # 在柱子上显示数值
        for bar, count in zip(bars, counts):
            plt.text(bar.get_x() + bar.get_width()/2, bar.get_height() + 0.1,
                    str(count), ha='center', va='bottom')
    
    elif chart_type == 'pie':
        # 饼图
        plt.pie(counts, labels=words, autopct='%1.1f%%', startangle=90)
        plt.title(f'词频统计饼图（前{top_n}名）')
        plt.axis('equal')
    
    plt.tight_layout()
    plt.show()

def create_wordcloud_from_freq(word_freq_list, output_file=None):
    """
    根据词频创建词云
    """
    try:
        from wordcloud import WordCloud
        
        # 转换为词频字典
        freq_dict = dict(word_freq_list)
        
        # 创建词云
        wordcloud = WordCloud(
            font_path='C:/Windows/Fonts/simhei.ttf',  # 中文字体
            width=800,
            height=400,
            background_color='white',
            max_words=100,
            relative_scaling=0.5,
            colormap='viridis'
        ).generate_from_frequencies(freq_dict)
        
        # 显示词云
        plt.figure(figsize=(10, 5))
        plt.imshow(wordcloud, interpolation='bilinear')
        plt.axis('off')
        plt.title('词频词云图')
        
        if output_file:
            plt.savefig(output_file, bbox_inches='tight', dpi=300)
        
        plt.show()
        
    except ImportError:
        print("需要安装wordcloud库: pip install wordcloud")
```

## 批量文件处理

### 1. 批量分析多个文件

```python
import os
from pathlib import Path

def batch_analyze_files(directory_path, file_extension='.txt'):
    """
    批量分析目录下的文本文件
    """
    directory = Path(directory_path)
    
    if not directory.exists():
        print(f"目录不存在: {directory_path}")
        return
    
    # 查找所有文本文件
    text_files = list(directory.glob(f'*{file_extension}'))
    
    if not text_files:
        print(f"在 {directory_path} 中未找到 {file_extension} 文件")
        return
    
    print(f"找到 {len(text_files)} 个文件")
    
    all_results = {}
    
    for file_path in text_files:
        print(f"\n正在分析: {file_path.name}")
        
        try:
            with open(file_path, 'r', encoding='utf-8') as f:
                content = f.read()
            
            # 分析词频
            result = analyze_text_frequency(text=content)
            all_results[file_path.name] = result[:10]  # 保存前10个
            
        except Exception as e:
            print(f"分析文件 {file_path.name} 时出错: {e}")
    
    return all_results

def compare_file_keywords(results_dict):
    """
    比较多个文件的关键词
    """
    print("\n=== 文件关键词对比 ===")
    
    for filename, keywords in results_dict.items():
        print(f"\n{filename} 的前5个关键词：")
        for i, (word, count) in enumerate(keywords[:5]):
            print(f"  {i+1}. {word}: {count}")
```

### 2. 导出结果

```python
import csv
import json

def export_results(word_freq_list, output_format='csv', filename='word_frequency'):
    """
    导出词频分析结果
    
    Args:
        word_freq_list: 词频列表
        output_format: 输出格式 'csv', 'json', 'txt'
        filename: 输出文件名（不含扩展名）
    """
    
    if output_format == 'csv':
        with open(f'{filename}.csv', 'w', newline='', encoding='utf-8') as f:
            writer = csv.writer(f)
            writer.writerow(['词语', '频次'])
            writer.writerows(word_freq_list)
        print(f"结果已保存到 {filename}.csv")
    
    elif output_format == 'json':
        data = [{'word': word, 'count': count} for word, count in word_freq_list]
        with open(f'{filename}.json', 'w', encoding='utf-8') as f:
            json.dump(data, f, ensure_ascii=False, indent=2)
        print(f"结果已保存到 {filename}.json")
    
    elif output_format == 'txt':
        with open(f'{filename}.txt', 'w', encoding='utf-8') as f:
            f.write("词频统计结果\n")
            f.write("=" * 30 + "\n")
            for i, (word, count) in enumerate(word_freq_list):
                f.write(f"{i+1:2d}. {word:15s} : {count:3d} 次\n")
        print(f"结果已保存到 {filename}.txt")
```

## 实际应用案例

### 1. 分析小说文本

```python
def analyze_novel(file_path):
    """
    分析小说文本的词频特征
    """
    print("=== 小说文本分析 ===")
    
    # 读取小说文本
    with open(file_path, 'r', encoding='utf-8') as f:
        content = f.read()
    
    print(f"小说总字数: {len(content)}")
    
    # 添加小说专用停用词
    novel_stopwords = {
        '道', '说道', '笑道', '想道', '心中', '心里', '一声', '一下',
        '起来', '过来', '回来', '出来', '进来', '下去', '上去', '过去',
        '什么', '怎么', '这样', '那样', '如此', '一般', '一番', '一阵'
    }
    
    # 分析人物名词频（假设人名是高频词）
    result = advanced_word_frequency(content, top_n=20)
    
    # 可视化
    visualize_word_frequency(result, top_n=15)
    
    return result

# 使用示例
# novel_analysis = analyze_novel('novel.txt')
```

### 2. 分析新闻文本

```python
def analyze_news(text):
    """
    分析新闻文本的关键词
    """
    print("=== 新闻文本分析 ===")
    
    # 新闻专用停用词
    news_stopwords = {
        '记者', '报道', '消息', '据悉', '了解', '表示', '认为',
        '指出', '强调', '提到', '介绍', '透露', '宣布', '发布'
    }
    
    # 分析
    result = advanced_word_frequency(text, top_n=15)
    
    # 创建词云
    create_wordcloud_from_freq(result[:50])
    
    return result
```

## 总结

本文实现的词频统计工具具有以下特点：

### 主要功能
1. **基础词频统计**：使用jieba分词和字典计数
2. **停用词过滤**：去除无意义的高频词
3. **结果排序**：按频次降序排列
4. **可视化展示**：柱状图、饼图、词云图
5. **批量处理**：支持多文件分析
6. **结果导出**：多种格式输出

### 应用场景
- **文本分析**：了解文档核心内容
- **关键词提取**：SEO和内容优化
- **情感分析**：结合情感词典分析
- **舆情监控**：分析热点话题
- **学术研究**：文献关键词统计

### 扩展建议
1. **添加词性过滤**：只统计名词、动词等
2. **支持英文分析**：添加英文停用词和处理
3. **TF-IDF权重**：使用更高级的权重算法
4. **词语相似度**：聚类相似词语
5. **时间序列分析**：分析词频变化趋势

通过这个工具，可以快速了解任何文本的核心内容和关键信息，为进一步的文本分析打下基础。

---

**实用价值**：这个词频统计工具为文本分析提供了完整的解决方案，从基础统计到高级可视化一应俱全。