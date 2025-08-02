# Python基于机器学习的文本情感分析详细步骤

> 发布时间：2020.04.11  
> 阅读量：36,684  
> 点赞：62  
> 收藏：511  
> 评论：14  

## 前言

最近在研究情感分析，感谢CSDN上很多博主的文章，让我受益匪浅。因此在跑出准确率高达88%的分类结果后，写下自己的总结回馈大家。

## 项目概述

本项目使用机器学习方法对文本进行情感分析，能够自动判断文本的情感倾向（正面、负面、中性）。通过系统的数据预处理、特征工程和模型训练，最终实现了88%的分类准确率。

## 一、文本数据预处理

情感分析主要涉及两个数据集：

### 数据集说明
1. **数据集A**：人工标注好的数据集，包含情感标签和文本
   - 用途：训练和测试模型
   - 格式：(文本, 标签)

2. **数据集B**：原始数据集，只包含文本
   - 用途：获得情感分析结果
   - 格式：纯文本

### 数据集选择
本文使用斯坦福大学的Sentiment140数据集作为训练数据。该数据集包含：
- 160万条推特数据
- 情感标签：0(负面)、4(正面)
- 数据质量高，标注准确

### 数据预处理步骤

#### 1. 数据加载和基础清洗

```python
import pandas as pd
import numpy as np
import re
from sklearn.model_selection import train_test_split

# 加载数据
def load_data(file_path):
    """
    加载数据集
    """
    columns = ['target', 'id', 'date', 'flag', 'user', 'text']
    data = pd.read_csv(file_path, encoding='latin-1', names=columns)
    return data

# 数据清洗
def clean_text(text):
    """
    清洗文本数据
    """
    # 转换为小写
    text = text.lower()
    
    # 移除URL
    text = re.sub(r'http\S+|www\S+|https\S+', '', text, flags=re.MULTILINE)
    
    # 移除用户@和标签#
    text = re.sub(r'\@\w+|\#\w+', '', text)
    
    # 移除标点符号和数字
    text = re.sub(r'[^a-zA-Z\s]', '', text)
    
    # 移除多余空格
    text = re.sub(r'\s+', ' ', text).strip()
    
    return text
```

#### 2. 文本标准化

```python
import nltk
from nltk.corpus import stopwords
from nltk.stem import PorterStemmer
from nltk.tokenize import word_tokenize

# 下载必要的NLTK数据
nltk.download('punkt')
nltk.download('stopwords')

def preprocess_text(text):
    """
    文本预处理：分词、去停用词、词干提取
    """
    # 分词
    tokens = word_tokenize(text)
    
    # 去停用词
    stop_words = set(stopwords.words('english'))
    tokens = [token for token in tokens if token not in stop_words and len(token) > 2]
    
    # 词干提取
    stemmer = PorterStemmer()
    tokens = [stemmer.stem(token) for token in tokens]
    
    return ' '.join(tokens)
```

## 二、特征工程

### 1. TF-IDF特征提取

```python
from sklearn.feature_extraction.text import TfidfVectorizer

def extract_tfidf_features(texts, max_features=10000):
    """
    提取TF-IDF特征
    """
    vectorizer = TfidfVectorizer(
        max_features=max_features,
        ngram_range=(1, 2),  # 使用1-gram和2-gram
        min_df=2,            # 过滤低频词
        max_df=0.95,         # 过滤高频词
        stop_words='english'
    )
    
    features = vectorizer.fit_transform(texts)
    return features, vectorizer
```

### 2. 词向量特征

```python
from gensim.models import Word2Vec
import numpy as np

def train_word2vec(texts, vector_size=100, window=5, min_count=2):
    """
    训练Word2Vec模型
    """
    # 分词
    sentences = [text.split() for text in texts]
    
    # 训练模型
    model = Word2Vec(
        sentences,
        vector_size=vector_size,
        window=window,
        min_count=min_count,
        workers=4,
        sg=1  # 使用skip-gram
    )
    
    return model

def text_to_vector(text, model, vector_size=100):
    """
    将文本转换为向量
    """
    words = text.split()
    vectors = []
    
    for word in words:
        if word in model.wv:
            vectors.append(model.wv[word])
    
    if vectors:
        return np.mean(vectors, axis=0)
    else:
        return np.zeros(vector_size)
```

## 三、模型训练

### 1. 朴素贝叶斯分类器

```python
from sklearn.naive_bayes import MultinomialNB
from sklearn.metrics import accuracy_score, classification_report, confusion_matrix

def train_naive_bayes(X_train, y_train, X_test, y_test):
    """
    训练朴素贝叶斯分类器
    """
    # 创建模型
    nb_model = MultinomialNB(alpha=1.0)
    
    # 训练模型
    nb_model.fit(X_train, y_train)
    
    # 预测
    y_pred = nb_model.predict(X_test)
    
    # 评估
    accuracy = accuracy_score(y_test, y_pred)
    print(f"朴素贝叶斯准确率: {accuracy:.4f}")
    print(classification_report(y_test, y_pred))
    
    return nb_model
```

### 2. 支持向量机

```python
from sklearn.svm import SVC
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler

def train_svm(X_train, y_train, X_test, y_test):
    """
    训练支持向量机
    """
    # 创建管道
    svm_pipeline = Pipeline([
        ('scaler', StandardScaler(with_mean=False)),  # 标准化
        ('svm', SVC(kernel='rbf', C=1.0, gamma='scale', probability=True))
    ])
    
    # 训练模型
    svm_pipeline.fit(X_train, y_train)
    
    # 预测
    y_pred = svm_pipeline.predict(X_test)
    
    # 评估
    accuracy = accuracy_score(y_test, y_pred)
    print(f"SVM准确率: {accuracy:.4f}")
    print(classification_report(y_test, y_pred))
    
    return svm_pipeline
```

### 3. 随机森林

```python
from sklearn.ensemble import RandomForestClassifier

def train_random_forest(X_train, y_train, X_test, y_test):
    """
    训练随机森林
    """
    # 创建模型
    rf_model = RandomForestClassifier(
        n_estimators=100,
        max_depth=10,
        min_samples_split=5,
        min_samples_leaf=2,
        random_state=42
    )
    
    # 训练模型
    rf_model.fit(X_train, y_train)
    
    # 预测
    y_pred = rf_model.predict(X_test)
    
    # 评估
    accuracy = accuracy_score(y_test, y_pred)
    print(f"随机森林准确率: {accuracy:.4f}")
    print(classification_report(y_test, y_pred))
    
    return rf_model
```

## 四、模型优化

### 1. 网格搜索调参

```python
from sklearn.model_selection import GridSearchCV

def optimize_svm(X_train, y_train):
    """
    使用网格搜索优化SVM参数
    """
    # 参数网格
    param_grid = {
        'svm__C': [0.1, 1, 10, 100],
        'svm__gamma': ['scale', 'auto', 0.001, 0.01, 0.1, 1],
        'svm__kernel': ['rbf', 'linear']
    }
    
    # 创建基础管道
    pipeline = Pipeline([
        ('scaler', StandardScaler(with_mean=False)),
        ('svm', SVC(probability=True))
    ])
    
    # 网格搜索
    grid_search = GridSearchCV(
        pipeline,
        param_grid,
        cv=5,
        scoring='accuracy',
        n_jobs=-1,
        verbose=1
    )
    
    # 训练
    grid_search.fit(X_train, y_train)
    
    print(f"最佳参数: {grid_search.best_params_}")
    print(f"最佳得分: {grid_search.best_score_:.4f}")
    
    return grid_search.best_estimator_
```

### 2. 交叉验证

```python
from sklearn.model_selection import cross_val_score

def cross_validate_model(model, X, y, cv=5):
    """
    交叉验证评估模型
    """
    scores = cross_val_score(model, X, y, cv=cv, scoring='accuracy')
    
    print(f"交叉验证得分: {scores}")
    print(f"平均得分: {scores.mean():.4f} (+/- {scores.std() * 2:.4f})")
    
    return scores
```

## 五、完整代码实现

```python
def main():
    """
    主函数：完整的情感分析流程
    """
    # 1. 加载和预处理数据
    print("1. 加载数据...")
    data = load_data('sentiment140.csv')
    
    # 简化标签：0->0(负面), 4->1(正面)
    data['target'] = data['target'].replace(4, 1)
    
    # 清洗文本
    print("2. 清洗文本...")
    data['clean_text'] = data['text'].apply(clean_text)
    data['processed_text'] = data['clean_text'].apply(preprocess_text)
    
    # 2. 特征提取
    print("3. 特征提取...")
    X, vectorizer = extract_tfidf_features(data['processed_text'])
    y = data['target']
    
    # 3. 划分数据集
    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=42, stratify=y
    )
    
    # 4. 模型训练和评估
    print("4. 模型训练...")
    
    # 朴素贝叶斯
    print("\n=== 朴素贝叶斯 ===")
    nb_model = train_naive_bayes(X_train, y_train, X_test, y_test)
    
    # SVM
    print("\n=== 支持向量机 ===")
    svm_model = train_svm(X_train, y_train, X_test, y_test)
    
    # 随机森林
    print("\n=== 随机森林 ===")
    rf_model = train_random_forest(X_train, y_train, X_test, y_test)
    
    # 5. 模型优化
    print("\n5. 模型优化...")
    best_svm = optimize_svm(X_train, y_train)
    
    # 6. 最终评估
    print("\n6. 最终评估...")
    final_pred = best_svm.predict(X_test)
    final_accuracy = accuracy_score(y_test, final_pred)
    print(f"最终准确率: {final_accuracy:.4f}")
    
    return best_svm, vectorizer

if __name__ == "__main__":
    model, vectorizer = main()
```

## 六、模型应用

### 预测新文本

```python
def predict_sentiment(text, model, vectorizer):
    """
    预测单个文本的情感
    """
    # 预处理
    clean = clean_text(text)
    processed = preprocess_text(clean)
    
    # 特征提取
    features = vectorizer.transform([processed])
    
    # 预测
    prediction = model.predict(features)[0]
    probability = model.predict_proba(features)[0]
    
    sentiment = "正面" if prediction == 1 else "负面"
    confidence = max(probability)
    
    return sentiment, confidence

# 使用示例
text = "I really love this movie! It's amazing!"
sentiment, confidence = predict_sentiment(text, model, vectorizer)
print(f"文本: {text}")
print(f"情感: {sentiment}")
print(f"置信度: {confidence:.4f}")
```

### 批量预测

```python
def batch_predict(texts, model, vectorizer):
    """
    批量预测文本情感
    """
    results = []
    
    for text in texts:
        sentiment, confidence = predict_sentiment(text, model, vectorizer)
        results.append({
            'text': text,
            'sentiment': sentiment,
            'confidence': confidence
        })
    
    return pd.DataFrame(results)

# 使用示例
test_texts = [
    "I hate this product, it's terrible!",
    "This is the best thing ever!",
    "It's okay, nothing special.",
    "Absolutely love it! Highly recommend!"
]

results = batch_predict(test_texts, model, vectorizer)
print(results)
```

## 七、结果分析

### 模型性能对比

| 模型 | 准确率 | 精确率 | 召回率 | F1-score |
|------|--------|--------|--------|----------|
| 朴素贝叶斯 | 0.82 | 0.81 | 0.83 | 0.82 |
| SVM | 0.85 | 0.86 | 0.84 | 0.85 |
| 随机森林 | 0.83 | 0.82 | 0.85 | 0.83 |
| 优化后SVM | **0.88** | **0.89** | **0.87** | **0.88** |

### 混淆矩阵分析

```python
import matplotlib.pyplot as plt
import seaborn as sns

def plot_confusion_matrix(y_true, y_pred, labels=['负面', '正面']):
    """
    绘制混淆矩阵
    """
    cm = confusion_matrix(y_true, y_pred)
    
    plt.figure(figsize=(8, 6))
    sns.heatmap(cm, annot=True, fmt='d', cmap='Blues', 
                xticklabels=labels, yticklabels=labels)
    plt.title('混淆矩阵')
    plt.xlabel('预测标签')
    plt.ylabel('真实标签')
    plt.show()
    
    return cm
```

## 八、项目总结

### 主要成果
1. **高准确率**：最终模型达到88%的准确率
2. **完整流程**：从数据预处理到模型部署的完整方案
3. **多模型对比**：系统比较了不同算法的性能
4. **实用性强**：可直接应用于实际项目

### 关键技术点
1. **数据预处理**：文本清洗、标准化处理
2. **特征工程**：TF-IDF特征提取、词向量表示
3. **模型选择**：多种机器学习算法对比
4. **参数优化**：网格搜索调参、交叉验证

### 改进方向
1. **深度学习**：尝试LSTM、BERT等深度学习模型
2. **特征优化**：添加更多文本特征，如句法特征
3. **数据增强**：使用数据增强技术提高模型泛化能力
4. **多分类**：扩展为细粒度情感分类

### 实际应用场景
1. **社交媒体监控**：监测品牌声誉
2. **客户反馈分析**：分析用户评价
3. **市场研究**：了解消费者情感
4. **新闻舆情分析**：跟踪公众舆论

## 参考资料

1. Sentiment140数据集：http://help.sentiment140.com/
2. NLTK文档：https://www.nltk.org/
3. Scikit-learn文档：https://scikit-learn.org/
4. Gensim文档：https://radimrehurek.com/gensim/

---

**项目成果**：这个情感分析项目不仅达到了88%的高准确率，更重要的是建立了完整的机器学习项目开发流程。

## 代码仓库

完整代码已开源：[sentiment-analysis-project](https://github.com/wendy7756/sentiment-analysis)