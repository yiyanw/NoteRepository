# NLP常用的库

## nltk 

natural language took kit

https://www.nltk.org/

包含了很多nlp领域经典的数据集

## Spacy

https://spacy.io/

comp5046 lab7 讲dependency parser的时候用到了这个库

```python
import spacy
#PrettyTable is a Python library for generating attractive ASCII tables.
from prettytable import PrettyTable

#load the spacy api with the pre-trained statistical models for English. English multi-task CNN trained on the OntoNotes corpus
nlp = spacy.load("en_core_web_sm")

#define the sentence
sentence = "The woman showed a wellshaped smile in the dark."
parse = nlp(sentence)

x = PrettyTable()
#define column names
x.field_names = ["TokenID", "Token", "HeadID", "Dependency"]

#spaCy does not provide the fake ROOT token so add a row for the fake Root
x.add_row([0,"ROOT",0,"-"])

#parsing recursively
for token in parse:
  if token.dep_=="ROOT":
    x.add_row([token.i+1,token.text,"0",token.dep_])
  else:  
    x.add_row([token.i+1,token.text,token.head.i+1,token.dep_])
    
print("Parsing Result with Spacy API")
#printing the table
print(x)

#spaCy comes with a built-in visualisation suite
from spacy import displacy

doc = nlp(sentence)
displacy.render(doc, style='dep', jupyter=True, options={'distance':90}) 	
```

## conllu

校验dependency parser的lib

这个库会根据universal dependencies来校验parser的好坏





# Other

## prettyTable

https://pypi.org/project/prettytable/

顾名思义：可以把数据以更好看的形式print出来