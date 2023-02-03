class: center, middle

## Processament del Llenguatge Humà

# Lab.5: nivell lèxic - semàntica - <br> desambiguació de sentits

### Gerard Escudero

## Grau en Intel·ligència Artificial

<br>

![:scale 75%](../fib.png)

---
class: left, middle, inverse

# Outline

- .cyan[Documentació]

  - .cyan[Lesk amb NLTK]

  - UKB amb TextServer

- Pràctica

  - UKB i SentiWornet

---

# Lesk in NLTK

**Requeriments**:

```python3
import nltk
nltk.download('wordnet')
nltk.download('omw-1.4')
```

**Ús**:

```python3
context = ['I', 'went', 'to', 'the', 'bank', 'to', 'deposit', 'money', '.']

synset = nltk.wsd.lesk(context, 'bank', 'n')
```

**Resultat**:

```python3
synset.name(), synset.definition()
👉
('savings_bank.n.02',
 'a container (usually with a slot in the top) for keeping money at home')
```

---
class: left, middle, inverse

# Outline

- .cyan[Documentació]

  - .brown[Lesk amb NLTK]

  - .cyan[UKB amb TextServer]

- Pràctica

  - UKB i SentiWornet

---

# UKB amb TextServer I

**Requeriments**:

```python3
from google.colab import drive
import sys
import json

drive.mount('/content/drive')
sys.path.insert(0, '/content/drive/My Drive/Colab Notebooks/plh')
from textserver import TextServer
```
**Ús**:

```python3
ts = TextServer('usuari', 'passwd', 'senses')
ctnt = ts.query("L'Arnau té un gos. Se l'estima molt.")
```

---

# UKB amb TextServer II

**Resultat**:

```python3
load = lambda r: json.loads(r.encode('utf-8'))
pars = lambda r: [p for p in r['paragraphs']]
sents = lambda p: [s for s in p['sentences']]

info = lambda s: [(t['form'],t['lemma'],t['tag'],t['pos'],t['wn']) 
            if 'wn' in t else (t['form'],t['lemma'],t['tag'],t['pos']) 
            for t in s['tokens']]
```

```python3
list(map(info, sents(pars(load(ctnt))[0])))
👉
[[("L'", 'el', 'DA0CS0', 'determiner'),
  ('Arnau', 'arnau', 'NP00O00', 'noun'),
  ('té', 'tenir', 'VMIP3S0', 'verb', '02205098-v'),
  ('un', 'un', 'DI0MS0', 'determiner'),
  ('gos', 'gos', 'NCMS000', 'noun', '02084071-n'),
  ('.', '.', 'Fp', 'punctuation')],
 [('Se', 'es', 'P00CN00', 'pronoun'),
  ("l'", 'el', 'PP3CSA0', 'pronoun'),
  ('estima', 'estimar', 'VMIP3S0', 'verb', '02256109-v'),
  ('molt', 'molt', 'RG', 'adverb', '00059171-r'),
  ('.', '.', 'Fp', 'punctuation')]]
```

---
class: left, middle, inverse

# Outline

- .brown[Documentació]

  - .brown[Lesk amb NLTK]

  - .brown[UKB amb TextServer]

- .cyan[Pràctica]

  - .cyan[Detecció d'opinions]

---

# Detecció d'opinions (pràctica 2.b)

#### Enunciat

* Implementeu un detector d'opinions positives o negatives no supervisat
  1. Apliqueu l'UKB per obtenir els synsets de les paraules
  2. Obtingueu els valors SentiWordnet de cada synset

* Utilitzeu com a dades el/els conjunts de test que hagueu utilitzat a la pràctica 2.a

* Penseu en com podeu combinar aquests valors per obtenir un resultat

* Penseu que fareu si el synset no hi és a SentiWordnet

* Penseu quines categories utilitzareu:
  - només adjectius
  - noms, adjectius i adverbis
  - noms, adjectius, verbs i adverbis

* Analitzeu els resultats i compareu-los amb els de la part supervisada




