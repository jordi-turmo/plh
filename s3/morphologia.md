class: center, middle

## Processament del Llenguatge Humà

# Lab.3: nivell lèxic - morfologia

<br>

### Gerard Escudero

## Grau en Intel·ligència Artificial

<br>

![:scale 75%](../fib.png)

---
class: left, middle, inverse

# Sumari

- .cyan[Documentació]

  - .cyan[Morfosintaxi i lematització]

  - Models morfosintàctics

- Exercici

  - *Hidden Markov Models*

- Pràctica

  - *Sentiment Polarity* i detecció d'opinions

---

## *Pen Treebank Part-of-Speech tags* (morfo-sintaxi)

.small[
.cols5050[
.col1[
| Etiqueta | Descripció |
|----------|------------|
| CC  | Coordinating conjunction |
| CD  | Cardinal number |
| DT  | Determiner |
| EX  | Existential there |
| FW  | Foreign word |
| IN  | Preposition or subordinating conjunction |
| JJ  | Adjective |
| JJR | Adjective, comparative |
| JJS | Adjective, superlative |
| LS  | List item marker |
| MD  | Modal |
| NN  | Noun, singular or mass |
| NNS | Noun, plural |
| NNP | Proper noun, singular |
| NNPS | Proper noun, plural |
| PDT | Predeterminer |
| POS | Possessive ending |
| PRP | Personal pronoun |
]
.col2[
| Etiqueta | Descripció |
|----------|------------|
| PRP$ | Possessive pronoun |
| RB  | Adverb |
| RBR | Adverb, comparative |
| RBS | Adverb, superlative |
| RP  | Particle |
| SYM | Symbol |
| TO  | to |
| UH  | Interjection |
| VB  | Verb, base form |
| VBD | Verb, past tense |
| VBG | Verb, gerund or present participle |
| VBN | Verb, past participle |
| VBP | Verb, non-3rd person singular present |
| VBZ | Verb, 3rd person singular present |
| WDT | Wh-determiner |
| WP  | Wh-pronoun |
| WP$ | Possessive wh-pronoun |
| WRB | Wh-adverb |
]]]

---

# Nivell lèxic a NLTK

### Requeriments

```
import nltk
nltk.download('averaged_perceptron_tagger')
```

### Etiquetatge morfo-sintàctic

```
words = ['women', 'played', 'with', 'small', 'children', 'happily']
nltk.pos_tag(words)

👉 [('women', 'NNS'),
    ('played', 'VBD'),
    ('with', 'IN'),
    ('small', 'JJ'),
    ('children', 'NNS'),
    ('happily', 'RB')]
```

---

# Lematització en NLTK

### Requeriments

```
nltk.download('wordnet')
nltk.download('omw-1.4')

wnl = nltk.stem.WordNetLemmatizer()

def lemmatize(p):
  d = {'NN': 'n', 'NNS': 'n', 
       'JJ': 'a', 'JJR': 'a', 'JJS': 'a', 
       'VB': 'v', 'VBD': 'v', 'VBG': 'v', 'VBN': 'v', 'VBP': 'v', 'VBZ': 'v', 
       'RB': 'r', 'RBR': 'r', 'RBS': 'r'}
  if p[1] in d:
    return wnl.lemmatize(p[0], pos=d[p[1]])
  return p[0]
```

### Lematització

```
[lemmatize(pair) for pair in pairs]

👉 ['woman', 'play', 'with', 'small', 'child', 'happily']
```

---

# Nivell lèxic a spaCy

### Requeriments

```
import spacy
!python -m spacy download ca_core_news_sm
nlp = spacy.load("ca_core_news_sm")
```

### Nivell lèxic

```
doc = nlp("L'Arnau té un gos negre.")
[(token.text, token.pos_, token.lemma_, token.is_stop) for token in doc]

👉 [("L'", 'DET', 'el', False),
    ('Arnau', 'PROPN', 'Arnau', False),
    ('té', 'VERB', 'tenir', False),
    ('un', 'DET', 'un', True),
    ('gos', 'NOUN', 'gos', False),
    ('negre', 'ADJ', 'negre', False),
    ('.', 'PUNCT', '.', False)]
```

- [Etiquetes dels models](https://spacy.io/models/ca)

---

# Nivell lèxic a TextServer

### Entrada

```
"L'Arnau té un gos. Se l'estima molt."
```

### Sortida

```
[[("L'", 'el', 'DA0CS0', 'determiner'),
  ('Arnau', 'arnau', 'NP00O00', 'noun'),
  ('té', 'tenir', 'VMIP3S0', 'verb'),
  ('un', 'un', 'DI0MS0', 'determiner'),
  ('gos', 'gos', 'NCMS000', 'noun'),
  ('.', '.', 'Fp', 'punctuation')],
 [('Se', 'es', 'P00CN00', 'pronoun'),
  ("l'", 'el', 'DA0CS0', 'determiner'),
  ('estima', 'estimar', 'VMIP3S0', 'verb'),
  ('molt', 'molt', 'RG', 'adverb'),
  ('.', '.', 'Fp', 'punctuation')]]
```

---

# Ús del TextServer

```python3
load = lambda r: json.loads(r.encode('utf-8'))
pars = lambda r: [p for p in r['paragraphs']]
sents = lambda p: [s for s in p['sentences']]
decode = lambda x: bytes(x,'latin1').decode('utf-8')
info = lambda s: [(t['form'],t['lemma'],t['tag'],t['pos']) for t in s['tokens']]

from google.colab import drive
import sys
import json

drive.mount('/content/drive')
sys.path.insert(0, '/content/drive/My Drive/Colab Notebooks/plh')
from textserver import TextServer

ts = TextServer('usuari', 'passwd', 'morpho')
ctnt = ts.query("L'Arnau té un gos. Se l'estima molt.")

list(map(info, sents(pars(load(ctnt))[0])))
```

---
class: left, middle, inverse

# Sumari

- .cyan[Documentació]

  - .brown[Morfosintaxi i lematització]

  - .cyan[Models morfosintàctics]

- Exercici

  - *Hidden Markov Models*

- Pràctica

  - *Sentiment Polarity* i detecció d'opinions

---

# Models morfosintàctics

### *Part-of-Speech taggers*

- Estadístics:

  - *Hidden Markov Models*

  - *Conditional Random Fields*

  - *TnT*, *Perceptron*

  - ...

- Basats en regles: *Brill*

---

# Hidden Markov Models I

### Requeriments

```
import nltk
nltk.download('treebank')
```

### Penn Treebank

```
len(nltk.corpus.treebank.tagged_sents()) 👉 3914

nltk.corpus.treebank.tagged_sents()[1]
👉 [('Mr.', 'NNP'),
    ('Vinken', 'NNP'),
    ('is', 'VBZ'),
    ('chairman', 'NN'),
    ('of', 'IN'),
    ('Elsevier', 'NNP'),
    ('N.V.', 'NNP'),
    (',', ','),
    ('the', 'DT'),
    ('Dutch', 'NNP'),
    ('publishing', 'VBG'),
    ('group', 'NN'),
    ('.', '.')]
```

---

# Hidden Markov Models II

### Aprenent el model

```
train = nltk.corpus.treebank.tagged_sents()[:3000]
test = nltk.corpus.treebank.tagged_sents()[3000:]

trainer = nltk.tag.hmm.HiddenMarkovModelTrainer()
HMM = trainer.train_supervised(train)

HMM.accuracy(test) 👉 0.36844377293330455
```

### Guardant el model

```
import dill
from google.colab import drive

drive.mount('/content/drive')

with open('/content/drive/My Drive/models/hmmTagger.dill', 'wb') as f:
    dill.dump(HMM, f)
```


---

# Hidden Markov Models III

### Aplicació del model

```
with open('/content/drive/My Drive/models/hmmTagger.dill', "rb") as f:
    tagger = dill.load(f)

tagger.tag(['the', 'men', 'attended', 'to', 'the', 'meetings'])
👉 [('the', 'DT'),
    ('men', 'NNS'),
    ('attended', 'VBD'),
    ('to', 'TO'),
    ('the', 'DT'),
    ('meetings', 'NNS')]
```

---

# Conditional Random Fields

```python3
!pip install python-crfsuite
from google.colab import drive
import nltk

nltk.download('treebank')
train = nltk.corpus.treebank.tagged_sents()[:3000]
test = nltk.corpus.treebank.tagged_sents()[3000:]

drive.mount('/content/drive')
model = nltk.tag.CRFTagger()
model.train(train,'/content/drive/My Drive/models/crfTagger.mdl')

model.accuracy(test)  👉  0.9474638463198791
```

```python3
tagger = nltk.tag.CRFTagger()
tagger.set_model_file('/content/drive/My Drive/models/crfTagger.mdl')
tagger.tag(['the', 'men', 'attended', 'to', 'the', 'meetings'])

👉  [('the', 'DT'),
     ('men', 'NNS'),
     ('attended', 'VBD'),
     ('to', 'TO'),
     ('the', 'DT'),
     ('meetings', 'NNS')]
```

---
class: left, middle, inverse

# Sumari

- .brown[Documentació]

  - .brown[Morfosintaxi i lematització]

  - .brown[Models morfosintàctics]

- .cyan[Exercici]

  - .cyan[*Hidden Markov Models*]

- Pràctica

  - *Sentiment Polarity* i detecció d'opinions

---

# Hidden Markov Models (exercici 2)

#### Recursos

- [Corpus](resources/tagged.ca.tgz) / [wikicorpus](https://www.cs.upc.edu/~nlp/wikicorpus/)

#### Enunciat

- Entrenau un *tagger* per al català

- Utilitzeu el conjunt de dades de dalt

- Definiu el protocol i mesures de validació que creieu més convenients i apliqueu-los

- Analitzeu els resultats

---
class: left, middle, inverse

# Sumari

- .brown[Documentació]

  - .brown[Morfosintaxi i lematització]

  - .brown[Models morfosintàctics]

- .brown[Exercici]

  - .brown[*Hidden Markov Models*]

- .cyan[Pràctica]

  - .cyan[*Sentiment Polarity* i detecció d'opinions]

---

# Sentiment analysis data

.blue[Pendent: fer-ho supervisat amb xarxes neuronals o obert]

.blue[sklearn vectorizer i Gradient Boosting]

#### NLTK’s Movie Reviews Corpus

* polarity corpus

  - 1000 positive examples

  - 1000 negative examples

---

* use in NLTK

```python3
from nltk.corpus import movie_reviews as mr

mr.fileids('pos')   # list of exemples:
        # ['pos/cv000_29590.txt', ...]

mr.words('pos/cv000_29590.txt')   # exemple as list of words:
        # ['films', 'adapted', ...]
```

*  System requirements:

```python3
import nltk
nltk.download('movie_reviews')
```


