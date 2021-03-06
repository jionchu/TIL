# POS (Part of Speech) Tagging
품사 (명사, 동사, 형용사) tagging

- 태깅정보를 이용하여 더 많은 정보를 추출하자

### 1) 태깅(Tagging)
- 원시 말뭉치에서 중의성을 해소하여 부가적인 언어 정보를 부착하는데, 이 정보를 **태그**(tag)라고 하며, 이러한 행위를 태깅이라 한다.
- 한편 태깅을 수행하는 컴퓨터 프로그램을 **태거**(tagger)라고 한다.
- 태그가 부착된 말뭉치를 **부착말뭉치**(tagged corpus)라고 한다.

### 2) Part of Speech Tagging
Jon saw the saw and decided to take it to the table.  
NNP VBD DT  NN  CC  VBD TO  VB  PRR IN  DT  NN

### 3) Full parsing vs Shallow parsing
- Information Extraction을 하기 위해, Full Parsing은 어려우므로, Shallow Parsing을 하여 부분 정보라도 일단 파악함

### 4) 품사 태깅 방법에 관한 두 학파간의 이견
- Rule-Based: Human crafted rules based on lexical and other lignuistic knowledge.
- Learning-Based: Trained on human annotated corpora like the Penn Treebank.

- Statistical models: Hidden Markov Model(HMM), Maximum Entrop Markov Model(MEMM), Conditional Random Field(CRF)
- Rule learning: Transformation Based Learning(TBL)

### 5) POS Tagging Example
#### ㄱ. pos_tag
```python
import nltk
s = ['time','flies','like','an','arrow']
nltk.pos_tag(s)
```
```
[('time', 'NN'),
 ('flies', 'NNS'),
 ('like', 'IN'),
 ('an', 'DT'),
 ('arrow', 'NN')]
```
POS Tagging 제대로 못함
#### ㄴ. tagset='universal'
```python
nltk.pos_tag(s, tagset='universal')
```
```
[('time', 'NOUN'),
 ('flies', 'NOUN'),
 ('like', 'ADP'),
 ('an', 'DET'),
 ('arrow', 'NOUN')]
```
#### ㄷ. tagset 설명
```python
nltk.help.upenn_tagset()
```

## 1. Using a Tagger
### 1) pos_tag()
생각보다 성능은 좋지 않음

```python
import nltk
from nltk.corpus import *
text = nltk.word_tokenize('They refused to permit us to obtain the refuse permit')
nltk.pos_tag(text)
```
```
[('They', 'PRP'),
 ('refused', 'VBD'),
 ('to', 'TO'),
 ('permit', 'VB'),
 ('us', 'PRP'),
 ('to', 'TO'),
 ('obtain', 'VB'),
 ('the', 'DT'),
 ('refuse', 'NN'),
 ('permit', 'NN')]
```

## 2. Tagged Corpora
### 1) str2tuple()
```python
tagged_token = nltk.tag.str2tuple('fly/NN')
tagged_token
```
```
('fly', 'NN')
```

### 2) tagged_words()
품사 리스트가 tuple로 반환됨
#### ㄱ. tagset='universal'
```python
nltk.corpus.brown.tagged_words()
```
```
[('The', 'AT'), ('Fulton', 'NP-TL'), ...]
```
```python
nltk.corpus.brown.tagged_words(tagset='universal')
```
```
[('The', 'DET'), ('Fulton', 'NOUN'), ...]
```

#### ㄴ. others
```python
print(nltk.corpus.nps_chat.tagged_words())
```
```
[('now', 'RB'), ('im', 'PRP'), ('left', 'VBD'), ...]
```
```python
nltk.corpus.conll2000.tagged_words()
```
```
[('Confidence', 'NN'), ('in', 'IN'), ('the', 'DT'), ...]
```
```python
nltk.corpus.treebank.tagged_words()
```
```
[('Pierre', 'NNP'), ('Vinken', 'NNP'), (',', ','), ...]
```

### 3) Universal Tag Sets
![universal tag sets](https://github.com/jionchu/TIL/blob/master/AI/images/universal_tag_sets.PNG)  

### 4) 브라운 뉴스 카테고리에서 가장 빈번하게 출현하는 품사 리스트하기
```python
brown_news_tagged = nltk.corpus.brown.tagged_words(categories='news',tagset='universal')
tag_fd = nltk.FreqDist(tag for (word, tag) in brown_news_tagged)
tag_fd.most_common()
```
```
[('NOUN', 30654),
 ('VERB', 14399),
 ('ADP', 12355),
 ('.', 11928),
 ('DET', 11389),
 ('ADJ', 6706),
 ('ADV', 3349),
 ('CONJ', 2717),
 ('PRON', 2535),
 ('PRT', 2264),
 ('NUM', 2166),
 ('X', 92)]
```
```python
tag_fd.plot(cumulative=True)
```

### 5) 명사 앞에 가장 자주 출현하는 단어 찾기
(a[0],a[1]) : ('The','DET'), ('Fulton','NP') ...  
(a,b) : ('N','DET'), ('NP','N') ...  
```python
word_tag_pairs = nltk.bigrams(brown_news_tagged)
noun_preceders = [a[1] for (a,b) in word_tag_pairs if b[1] == 'NOUN']
fdist = nltk.FreqDist(noun_preceders)
[tag for (tag, _) in fdist.most_common()]
```
```
['NOUN',
 'DET',
 'ADJ',
 'ADP',
 '.',
 'VERB',
 'CONJ',
 'NUM',
 'ADV',
 'PRT',
 'PRON',
 'X']
```

### 6) 뉴스 카테고리에서 가장 빈번한 동사군들 찾기
```python
wsj = nltk.corpus.treebank.tagged_words(tagset='universal')
word_tag_fd = nltk.FreqDist(wsj)
[wt[0] for (wt,_) in word_tag_fd.most_common() if wt[1] == 'VERB']
```
```
['is',
 'said',
 'was',
 'are',
 'be',
 'has',
 'have',
 'will',
 'says',
 'would',
 'were',
 'had',
 'been',
 'could',
 "'s",
 'can',
 'do',
 ...
]
```
#### ㄱ. 'yield'가 어떤 품사로 몇 번 쓰였는지
```python
cfd1 = nltk.ConditionalFreqDist(wsj)
cfd1['yield'].most_common()
```
```
[('VERB', 28), ('NOUN', 20)]
```
#### ㄴ. 'cut'이 어떤 품사로 몇 번 쓰였는지
```python
cfd1['cut'].most_common()
```
```
[('VERB', 25), ('NOUN', 3)]
```

### 7) VBD(과거형) and VBN(과거분사형) 태그를 동시에 가지는 단어를 찾고, 그 주변 텍스트 살펴보기
```python
[w for w in cfd1.conditions() if 'VBD' in cfd1[w] and 'VBN' in cfd1[w]]
```
```
['Asked', 'accelerated', 'accepted', 'accused', 'acquired', added', ... ]
```
```python
idx1 = wsj.index(('kicked', 'VBD'))
wsj[idx1-4:idx1+1]
```
```
[('While', 'IN'), ('program', 'NN'), ('trades', 'NNS'), ('swiftly', 'RB'), ('kicked', 'VBD')]
```
```python
idx2 = wsj.index(('kicked', 'VBN'))
wsj[idx2-4:idx2+1]
```
```
[('head', 'NN'), ('of', 'IN'), ('state', 'NN'), ('has', 'VBZ'), ('kicked', 'VBN')]
```

### 8) tagset 중 'NN'으로 시작하는 태그와 관련 단어들 리스트하기 (brown tagset)
```python
def findtags(tag_prefix, tagged_text):
    cfd = nltk.ConditionalFreqDist((tag, word) for (word, tag) in tagged_text if tag.startswith(tag_prefix))
    return dict((tag, cfd[tag].most_common(5)) for tag in cfd.conditions())

tagdict = findtags('NN', nltk.corpus.brown.tagged_words(categories='news'))
for tag in sorted(tagdict):
    print(tag, tagdict[tag])
```
```
NN [('year', 137), ('time', 97), ('state', 88), ('week', 85), ('man', 72)]
NN$ [("year's", 13), ("world's", 8), ("state's", 7), ("nation's", 6), ("city's", 6)]
NN$-HL [("Golf's", 1), ("Navy's", 1)]
NN$-TL [("President's", 11), ("Administration's", 3), ("Army's", 3), ("League's", 3), ("University's", 3)]
NN-HL [('sp.', 2), ('problem', 2), ('Question', 2), ('cut', 2), ('party', 2)]
NN-NC [('ova', 1), ('eva', 1), ('aya', 1)]
NN-TL [('President', 88), ('House', 68), ('State', 59), ('University', 42), ('City', 41)]
NN-TL-HL [('Fort', 2), ('Mayor', 1), ('Commissioner', 1), ('City', 1), ('Oak', 1)]
NNS [('years', 101), ('members', 69), ('people', 52), ('sales', 51), ('men', 46)]
NNS$ [("children's", 7), ("women's", 5), ("men's", 3), ("janitors'", 3), ("taxpayers'", 2)]
NNS$-HL [("Dealers'", 1), ("Idols'", 1)]
NNS$-TL [("Women's", 4), ("States'", 3), ("Giants'", 2), ("Princes'", 1), ("Bombers'", 1)]
NNS-HL [('Wards', 1), ('deputies', 1), ('bonds', 1), ('aspects', 1), ('Decisions', 1)]
NNS-TL [('States', 38), ('Nations', 11), ('Masters', 10), ('Communists', 9), ('Rules', 9)]
NNS-TL-HL [('Nations', 1)]
```

### 9) 단어로 검색하는 것보다 POS 태그로 검색하는 것이 나은 경우
#### ㄱ. often 단어의 사용법에 대해 연구
```python
brown_learned_text = nltk.corpus.brown.words(categories='learned')
sorted(set(b for (a,b) in nltk.bigrams(brown_learned_text) if a == 'often'))
```
```
[',',
 '.',
 'accomplished',
 'analytically',
 'appear',
 'apt',
 'associated',
 'assuming',
 'became',
 'become',
 'been',
 'began',
 'call',
 'called',
 'carefully',
 'chose',
 'classified',
 'colorful',
 'composed',
 'contain',
 'differed',
 'difficult',
 'encountered',
 'enough',
 'equate',
 'extremely',
 'found',
 'happens',
 'have',
 'ignored',
 'in',
 'involved',
 'more',
 'needed',
 'nightly',
 'observed',
 'of',
 'on',
 'out',
 'quite',
 'represent',
 'responsible',
 'revamped',
 'seclude',
 'set',
 'shortened',
 'sing',
 'sounded',
 'stated',
 'still',
 'sung',
 'supported',
 'than',
 'to',
 'when',
 'work']
```
```python
brown_lrnd_tagged = nltk.corpus.brown.tagged_words(categories='learned', tagset='universal')
tags = [b[1] for (a,b) in nltk.bigrams(brown_lrnd_tagged) if a[0] == 'often']
fd = nltk.FreqDist(tags)
fd.tabulate()
```
```
VERB  ADV  ADP  ADJ    .  PRT 
  37    8    7    6    4    2 
```

#### ㄴ. "<Verb> to <Verb>" 구문 형태 모두 찾아내기
```python
from nltk.corpus import brown
import nltk

def process(sentence):
    for (w1, t1), (w2, t2), (w3, t3) in nltk.trigrams(sentence):
        if (t1.startswith('V') and t2=='TO' and t3.startswith('V')):
            print(w1, w2, w3)

for tagged_sent in brown.tagged_sents():
    process(tagged_sent)
```
```
combined to achieve
continue to place
serve to protect
wanted to wait
allowed to place
expected to become
expected to approve
expected to make
intends to make
seek to set
...
```

## 3. Mapping Words to Properties Using Python Dictionaries

### 1) Defining Dictionary
- create a dictionary
```python
pos = {'colorless':'ADJ', 'ideas':'N', 'sleep':'V','furiously':'ADV'}
pos = dict(colorless='ADJ', ideas='N', sleep='V', furiously='ADV')
```
- dictionary keys must be immutable types

```python
import nltk
pos = {'colorless':'ADJ', 'ideas':'N', 'sleep':'V','furiously':'ADV'}
pos['colorless']
```
```
'ADJ'
```
```python
pos['sleep']
```
```
'V'
```
```python
pos['blog']
```
```
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-7-14268bca594a> in <module>
----> 1 pos('blog')

TypeError: 'dict' object is not callable
```

### 2) Default dictionary
```python
from collections import defaultdict
pos = defaultdict(lambda:'NOUN')
pos['ideas'] = 'N'
pos
```
```
defaultdict(<function __main__.<lambda>()>, {'ideas': 'N'})
```
```python
pos['ideas']
```
```
'N'
```
```python
pos['blog']
```
```
'NOUN'
```

### 3) 품사 개수 세기
#### ㄱ. 전체 품사 개수 세기
```python
from collections import defaultdict
counts = defaultdict(int)
from nltk.corpus import brown
for (word, tag) in brown.tagged_words(categories=
'news', tagset='universal'):
    counts[tag] += 1
```
#### ㄴ. 특정 품사의 개수 알아내기
```python
counts['NOUN']
```
```
30654
```

#### ㄷ. 개수가 많은 순서대로 품사 정렬하기
```python
from operator import itemgetter
sorted(counts.items(), key=itemgetter(1), reverse=True)
```
```
[('NOUN', 30654),
 ('VERB', 14399),
 ('ADP', 12355),
 ('.', 11928),
 ('DET', 11389),
 ('ADJ', 6706),
 ('ADV', 3349),
 ('CONJ', 2717),
 ('PRON', 2535),
 ('PRT', 2264),
 ('NUM', 2166),
 ('X', 92)]
```
```python
[t for t,c in sorted(counts.items(), key=itemgetter(1), reverse=True)]
```
```
['NOUN',
 'VERB',
 'ADP',
 '.',
 'DET',
 'ADJ',
 'ADV',
 'CONJ',
 'PRON',
 'PRT',
 'NUM',
 'X']
```
```python
counts.items()
```
```
dict_items([('DET', 11389), ('NOUN', 30654), ('ADJ', 6706), ('VERB', 14399), ('ADP', 12355), ('.', 11928), ('ADV', 3349), ('CONJ', 2717), ('PRT', 2264), ('PRON', 2535), ('NUM', 2166), ('X', 92)])
```

### 4) Complex Keys and Values
```python
pos = defaultdict(lambda: defaultdict(int))
brown_news_tagged = brown.tagged_words(categories='news', tagset='universal')
for ((w1, t1), (w2, t2)) in nltk.bigrams(brown_news_tagged):
    pos[(t1, w2)][t2] += 1

// right라는 단어 앞에 DET(관사)가 있는 경우 right의 품사는 무엇인가
pos[('DET', 'right')]
```
```
defaultdict(<class 'int'>, {'ADJ': 11, 'NOUN': 5})
```

### 5) Index words according to their last two letters
```python
last_letters = defaultdict(list)
words = nltk.corpus.words.words('en')
for word in words:
    key = word[-2:]
    last_letters[key].append(word)

last_letters['ly']
```
```
['abactinally',
 'abandonedly',
 'abasedly',
 'abashedly',
 'abashlessly',
 'abbreviately',
 'abdominally',
 'abhorrently',
 ...
]
```
```python
last_letters['zy']
```
```
['blazy',
 'bleezy',
 'blowzy',
 'boozy',
 'breezy',
 'bronzy',
 'buzzy',
 'Chazy',
 'cozy',
 'crazy',
 ...
]
```

## 4. Automatic Tagging

### 1) tagged_sents()
```python
import nltk
from nltk.corpus import *

brown.words()
```
```
['The', 'Fulton', 'County', 'Grand', 'Jury', 'said', ...]
```
```python
brown.tagged_words()
```
```
[('The', 'AT'), ('Fulton', 'NP-TL'), ...]
```
```python
brown.tagged_words(tagset='universal')
```
```
[('The', 'DET'), ('Fulton', 'NOUN'), ...]
```

#### ㄱ. 단어가 어떤 품사로 많이 사용되었는지 확인
```python
brown_tagged = brown.tagged_words(tagset='universal')
cfd = nltk.ConditionalFreqDist(brown_tagged)
```
```python
cfd['time']
```
```
FreqDist({'NOUN': 1555, 'VERB': 1})
```
```python
cfd['flies']
```
```
FreqDist({'NOUN': 7, 'VERB': 4})
```

#### ㄴ. 카테고리별 태깅하기
```python
from nltk.corpus import brown
brown_tagged_sents = brown.tagged_sents(categories='news')
brown_sents = brown.sents(categories='news')
```

### 2) POS tagging classes in NLTK
- **DefaultTagger**
- **RegexpTagger**
- **UnigramTagger**

### 3) Default Tagger
- 모든 tag를 하나로 통일
#### ㄱ. 모든 tag를 'NN'으로 통일하기
```python
tags = [tag for (word, tag) in brown.tagged_words(categories='news')]
nltk.FreqDist(tags).max()
```
```
'NN'
```
```python
raw = 'I do not like green eggs and ham, I do not like them Sam I am!'
tokens = nltk.word_tokenize(raw)
default_tagger = nltk.DefaultTagger('NN')
default_tagger.tag(tokens)
```
```
[('I', 'NN'),
 ('do', 'NN'),
 ('not', 'NN'),
 ('like', 'NN'),
 ('green', 'NN'),
 ('eggs', 'NN'),
 ('and', 'NN'),
 ('ham', 'NN'),
 (',', 'NN'),
 ('I', 'NN'),
 ('do', 'NN'),
 ('not', 'NN'),
 ('like', 'NN'),
 ('them', 'NN'),
 ('Sam', 'NN'),
 ('I', 'NN'),
 ('am', 'NN'),
 ('!', 'NN')]
```
- 당연히 품사 정확도는 낮음
```python
default_tagger.evaluate(brown_tagged_sents)
```
```
0.13089484257215028
```

### 4) Regular Expression Tagger
```python
patterns = [
    (r'.*ing$','VBG'),
    (r'.*ed$','VBD'),
    (r'.*es$','VBZ'),
    (r'.*ould$','MD'),
    (r'.*\'s$','NN$'),
    (r'.*s$','NNS'),
    (r'^-?[0-9]+(.[0-9]+)?$','CD'),
    (r'.*','NN')# default
]
regexp_tagger = nltk.RegexpTagger(patterns)
regexp_tagger.tag(brown_sents[3])
```
```
[('``', 'NN'),
 ('Only', 'NN'),
 ('a', 'NN'),
 ('relative', 'NN'),
 ('handful', 'NN'),
 ('of', 'NN'),
 ('such', 'NN'),
 ('reports', 'NNS'),
 ('was', 'NNS'),
 ('received', 'VBD'),
 ("''", 'NN'),
 (',', 'NN'),
 ('the', 'NN'),
 ('jury', 'NN'),
 ('said', 'NN'),
 (',', 'NN'),
 ('``', 'NN'),
 ('considering', 'VBG'),
 ('the', 'NN'),
 ('widespread', 'NN'),
 ('interest', 'NN'),
 ('in', 'NN'),
 ('the', 'NN'),
 ('election', 'NN'),
 (',', 'NN'),
 ('the', 'NN'),
 ('number', 'NN'),
 ('of', 'NN'),
 ('voters', 'NNS'),
 ('and', 'NN'),
 ('the', 'NN'),
 ('size', 'NN'),
 ('of', 'NN'),
 ('this', 'NNS'),
 ('city', 'NN'),
 ("''", 'NN'),
 ('.', 'NN')]
```
- DefaultTagger보다는 정확도가 높음
```python
regexp_tagger.evaluate(brown_tagged_sents)
```
```
0.20326391789486245
```

### 5) Unigram Tagger (Lookup Tagger)
```python
fd = nltk.FreqDist(brown.words(categories='news'))
cfd = nltk.ConditionalFreqDist(brown.tagged_words(categories='news'))
most_freq_words = fd.most_common(100)
likely_tags = dict((word, cfd[word].max()) for (word, _) in most_freq_words)
baseline_tagger = nltk.UnigramTagger(model=likely_tags)
baseline_tagger.evaluate(brown_tagged_sents)
```
```
0.45578495136941344
```

### 6) Conbining Taggers: backoff tagging
Unigram tagger에서 most_common으로 가장 많이 사용된 일부의 단어들만 찾을 수 있으므로 backoff를 이용하여 default tag를 설정해준다.  
```python
baseline_tagger = nltk.UnigramTagger(model=likely_tags,backoff=nltk.DefaultTagger('NN'))
baseline_tagger.evaluate(brown_tagged_sents)
```
```
0.5817769556656125
```

## 5. N-Gram Tagging
### 1) Unigram Tagging: training()
```python
from nltk.corpus import brown
brown_tagged_sents = brown.tagged_sents(categories='news')
brown_sents = brown.sents(categories='news')
unigram_tagger = nltk.UnigramTagger(brown_tagged_sents)
unigram_tagger.tag(brown_sents[2007])
```
```
[('Various', 'JJ'),
 ('of', 'IN'),
 ('the', 'AT'),
 ('apartments', 'NNS'),
 ('are', 'BER'),
 ('of', 'IN'),
 ('the', 'AT'),
 ('terrace', 'NN'),
 ('type', 'NN'),
 (',', ','),
 ('being', 'BEG'),
 ('on', 'IN'),
 ('the', 'AT'),
 ('ground', 'NN'),
 ('floor', 'NN'),
 ('so', 'QL'),
 ('that', 'CS'),
 ('entrance', 'NN'),
 ('is', 'BEZ'),
 ('direct', 'JJ'),
 ('.', '.')]
```
```python
unigram_tagger.evaluate(brown_tagged_sents)
```
```
0.9349006503968017
```

### 2) Typical Steps of Supervised Learning
![supervised learning](https://github.com/jionchu/TIL/blob/master/AI/images/supervised_learning.PNG)  

```python
size = int(len(brown_tagged_sents)*0.9)
train_sents = brown_tagged_sents[:size]
test_sents = brown_tagged_sents[size:]
size
```
```
4160
```
```python
unigram_tagger = nltk.UnigramTagger(train_sents)
unigram_tagger.evaluate(test_sents)
```
```
0.8121200039868434
```
train data와 test data를 구분해야 정확하게 성능을 평가할 수 있음  

### 3) General N-Gram Tagging
- 위의 unigram tagger는 단어만으로 품사를 판단함. 문맥을 보고 판단하는 N-Gram tagging
- 앞에 있는 단어들의 품사를 확인

#### ㄱ. Bigram
```python
bigram_tagger = nltk.BigramTagger(train_sents)
bigram_tagger.tag(brown_sents[2007])
```
```
[('Various', 'JJ'),
 ('of', 'IN'),
 ('the', 'AT'),
 ('apartments', 'NNS'),
 ('are', 'BER'),
 ('of', 'IN'),
 ('the', 'AT'),
 ('terrace', 'NN'),
 ('type', 'NN'),
 (',', ','),
 ('being', 'BEG'),
 ('on', 'IN'),
 ('the', 'AT'),
 ('ground', 'NN'),
 ('floor', 'NN'),
 ('so', 'CS'),
 ('that', 'CS'),
 ('entrance', 'NN'),
 ('is', 'BEZ'),
 ('direct', 'JJ'),
 ('.', '.')]
```
```python
unseen_sents = brown_sents[4203]
bigram_tagger.tag(unseen_sents)
```
```
[('The', 'AT'),
 ('population', 'NN'),
 ('of', 'IN'),
 ('the', 'AT'),
 ('Congo', 'NP'),
 ('is', 'BEZ'),
 ('13.5', None),
 ('million', None),
 (',', None),
 ('divided', None),
 ('into', None),
 ('at', None),
 ('least', None),
 ('seven', None),
 ('major', None),
 ('``', None),
 ('culture', None),
 ('clusters', None),
 ("''", None),
 ('and', None),
 ('innumerable', None),
 ('tribes', None),
 ('speaking', None),
 ('400', None),
 ('separate', None),
 ('dialects', None),
 ('.', None)]
```
```python
bigram_tagger.evaluate(test_sents)
```
```
0.10206319146815508
```
정확도가 확 떨어짐  
-> bigram을 적용하면 일부 data만 training됨  
-> unseen data가 너무 많은 것

### 4) Combining Tagger
bigram(가장 정확한 것)이 처리하지 못하는 것은 unigram이, unigram이 처리하지 못하는 것은 default tagger가 처리함
```python
t0 = nltk.DefaultTagger('NN')
t1 = nltk.UnigramTagger(train_sents, backoff=t0)
t2 = nltk.BigramTagger(train_sents, backoff=t1)
t2.evaluate(test_sents)
```
```
0.8452108043456593
```

### 5) Pickling
- Storing taggers (지금까지 학습시킨 것을 저장)

- 저장하기
```python
from pickle import dump
output = open('t2.pkl', 'wb')
dump(t2, output, -1)
output.close()
```
- 불러오기
```python
from pickle import load
input = open('t2.pkl','rb')
tagger = load(input)
input.close()
```

## 6. Transformation-Based Tagging
### 1) Brill Tagger
데이터가 많지 않을 때  
학습 도중 여러 가지 rele을 적용하면서 mistake를 고쳐 나감  
- (a) Replace NN with VB when the previous word is TO
- (b) Replace TO with IN when the next tag is NNS

