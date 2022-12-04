# MapReduce例子讲解

例子参考代码：

[https://github.com/Yelp/mrjob/tree/master/mrjob/examples](https://github.com/Yelp/mrjob/tree/master/mrjob/examples)

## 单词记数

> [https://github.com/Yelp/mrjob/blob/master/mrjob/examples/mr\_word\_freq\_count.py](https://github.com/Yelp/mrjob/blob/master/mrjob/examples/mr\_word\_freq\_count.py)&#x20;

统计文本中每个单词出现的总次数

```
class MRWordFreqCount(MRJob):

    def mapper(self, _, line): # 输入为每一行文本
        for word in WORD_RE.findall(line): 
        # WORD_RE.findall(line): 是把一行文本拆分成单词
            yield (word.lower(), 1)  # map的输出key是单词，value是1

    def combiner(self, word, counts):
        yield (word, sum(counts)) #combiner阶段先把同一台服务器的相同单词的合并一下，并求单词的总数。

    def reducer(self, word, counts):
        yield (word, sum(counts)) # 将相同key的单词求和。
```

## 统计行数、单词和字符个数

> [https://github.com/Yelp/mrjob/blob/master/mrjob/examples/mr\_wc.py](https://github.com/Yelp/mrjob/blob/master/mrjob/examples/mr\_wc.py)

统计文本中总共有多少行，有多少个单词，有多少个字符

```
class MRWordCountUtility(MRJob):

    def __init__(self, *args, **kwargs):
        super(MRWordCountUtility, self).__init__(*args, **kwargs)
        # 先初始化chars、words、lines分别存在字符数、单词数和行数。
        self.chars = 0
        self.words = 0
        self.lines = 0

    def mapper(self, _, line):
        # Don't actually yield anything for each line. Instead, collect them
        # and yield the sums when all lines have been processed. The results
        # will be collected by the reducer.
        # 每一不单词输出结果，把每一行的字符数、单词数和行数累加到相应的变量中
        self.chars += len(line) + 1  # +1 for newline，统计字条个数
        self.words += sum(1 for word in line.split() if word.strip()) # 统计单词数
        self.lines += 1 # 统计行数

    def mapper_final(self):
        # 所有的行都遍历完后执行，分别把chars、words和lines作为key，提交给reducers去执行。
        yield('chars', self.chars)
        yield('words', self.words)
        yield('lines', self.lines)

    def reducer(self, key, values):
        yield(key, sum(values))
```

## 找出出现次数最多的几个单词

> [https://github.com/Yelp/mrjob/blob/master/mrjob/examples/mr\_most\_used\_word.py](https://github.com/Yelp/mrjob/blob/master/mrjob/examples/mr\_most\_used\_word.py)

