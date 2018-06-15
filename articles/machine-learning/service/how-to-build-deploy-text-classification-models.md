---
title: Sestavení a nasazení modelu klasifikace text pomocí Azure Machine Learning balíčku pro Analýza textu.
description: Zjistěte, jak pro vytváření, trénování, otestovat a nasadit model klasifikace text pro Analýza textu pomocí balíček Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/07/2018
ms.openlocfilehash: a970bd889e6994833b2e34adc90af594f9db4d6b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939701"
---
# <a name="build-and-deploy-text-classification-models-with-azure-machine-learning"></a>Vytváření a nasazování text klasifikační modely pomocí Azure Machine Learning

V tomto článku se dozvíte trénování a nasazení modelu klasifikace textu s **Azure Machine Learning balíček pro Analýza textu** (AMLPTA). Cílem text klasifikace je přiřadit k části textu na jeden nebo více předdefinované třídy nebo kategorie. Tento text může být například dokument, aktuální článek, vyhledávací dotaz, e-mailu, tweet, lístky žádostí o podporu.

Existují široký aplikace klasifikace text jako třeba: 
+ Kategorizaci novinových článcích a obsah přenosu zprávy do témata
+ Uspořádání webových stránek do hierarchické kategorií 
+ Filtrování e-mailu nevyžádané pošty
+ Analýza subjektivního hodnocení
+ Predikci záměru uživatele volaná dotazy vyhledávání
+ Směrování lístky žádostí o podporu
+ Analýza názory zákazníků 

Text klasifikace model vytváření a nasazení pracovního postupu pro model s AMLPTA vypadá takto:

1. Načtení dat
2. Trénování modelu
3. Použít třídění 
4. Vyhodnocení výkonu modelu
5. Uložení kanálu
6. Testování kanálu
8. Model nasadit jako webovou službu

Obrátit [balíček referenční dokumentaci k nástroji](https://aka.ms/aml-packages/text) pro podrobné referenční informace pro každý modul a třída.

Ukázkový kód v tomto článku používá scikit-další kanálu.

## <a name="prerequisites"></a>Požadavky 

1. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

1. Následující účty a aplikace musí být nastavení a nainstalován:
   - Účet Azure Machine Learning experimentování 
   - Účet Azure Machine Learning Model správy
   - Nainstalovanou aplikaci Azure Machine Learning Workbench

   Pokud tyto tři jsou ještě vytvořen nebo nainstalován, postupujte podle kroků [Azure Machine Learning Quickstart a Workbench instalace](../service/quickstart-installation.md) článku. 

1. Balíček Azure Machine Learning pro Analýza textu musí být nainstalován. Zjistěte, jak [instalaci tohoto balíčku zde](https://aka.ms/aml-packages/text).


## <a name="sample-data-and-jupyter-notebook"></a>Ukázková data a poznámkového bloku Jupyter

### <a name="get-the-jupyter-notebook"></a>Získat poznámkového bloku Jupyter

Vyzkoušejte si to sami. Stáhněte poznámkového bloku a spusťte ho sami.

> [!div class="nextstepaction"]
> [Získat poznámkového bloku Jupyter](https://aka.ms/aml-packages/text/notebooks/text_classification_sentiment_data)

### <a name="download-and-explore-the-sample-data"></a>Stáhněte si a prozkoumejte ukázková data
Následující příklad používá [datovou sadu 20 diskusní skupiny](http://qwone.com/~jason/20Newsgroups/) , je k dispozici prostřednictvím scikit-další knihovny a ukazují, jak vytvoření klasifikátoru v textu pro Analýza textu pomocí Azure Machine Learning balíčku. 

Datová sada 20 diskusní skupiny má přibližně 18 000 diskusní skupiny příspěvcích na 20 dalších tématech rozdělené do dvou podmnožin: jeden pro školení a jinou pro testování výkonu. Rozdělení do vlaku a testování podle každé datum post zprávu zda před nebo po určitém datu.

```python
# Import Packages 
# Use Azure Machine Learning history magic to control history collection
# History is off by default, options are "on", "off", or "show"
#%azureml history on
%matplotlib inline
# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
import os

logger = get_azureml_logger()

# Log cell runs into run history
logger.log('Cell','Set up run')
# from tatk.utils import load_newsgroups_data, data_dir, dictionaries_dir, models_dir
import pip
pip.main(["show", "azureml-tatk"])
```

### <a name="set-the-location-of-the-data"></a>Nastavte umístění dat
Nastavte umístění, kam jste stáhli data v parametru dir data. Můžete také použít vlastní data, vstupní datovou sadu musí být formát souboru *.tsv.

```python
import os
import pandas as pd

#set the working directory where to save the training data files
resources_dir = os.path.join(os.path.expanduser("~"), "tatk", "resources")
data_dir = os.path.join(os.path.expanduser("~"), "tatk", "data")

from sklearn.datasets import fetch_20newsgroups
twenty_train = fetch_20newsgroups(data_home=data_dir, subset='train')
X_train, y_train = twenty_train.data, twenty_train.target
df_train = pd.DataFrame({"text":X_train, "label":y_train})

twenty_test = fetch_20newsgroups(data_home=data_dir, subset='test')
X_test, y_test = twenty_test.data, twenty_test.target   
df_test = pd.DataFrame({"text":X_test, "label":y_test})
    
# Training Dataset Location
#training_file_path = <specify-your-own-training-data-file-path-here>
# df_train = pd.read_csv(training_file_path,
#                        sep = '\t',                        
#                        header = 0, names= <specify-your-column-name-list-here>)
df_train.head()
print("df_train.shape= {}".format(df_train.shape))

# Test Dataset Location
#test_file_path = <specify-your-own-test-data-file-path-here>
# df_test = pd.read_csv(test_file_path,
#                       sep = '\t',                        
#                       header = 0, names= <specify-your-column-name-list-here>)

print("df_test.shape= {}".format(df_test.shape))
df_test.head()
```
    df_train.shape= (11314, 2)
    df_test.shape= (7532, 2)
 
 Data se skládá z popisek a textové
    
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Popisek</th>
      <th>text</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7</td>
      <td>Z: v064mb9k@ubvmsd.cc.buffalo.edu (NEIL B....</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5</td>
      <td>Z: Lukeš Rick &lt; rick@ee.uwm.edu &gt;\nSubject:...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>Z: mathew &lt; mathew@mantis.co.uk &gt;\nSubject: R....</td>
    </tr>
    <tr>
      <th>3</th>
      <td>17</td>
      <td>Z: bakken@cs.arizona.edu \nSub (Dave Bakken)...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>19</td>
      <td>Z: livesey@solntze.wpd.sgi.com (Jon Livesey...</td>
    </tr>
  </tbody>
</table>
</div>

Získáte shodu mezi kategorií a jejich název.

```python
int_to_categories = pd.DataFrame({'category':range(20), 'category_name': list(twenty_train.target_names)})
int_to_categories 
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>category_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>ALT.atheism</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>comp.Graphics</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>comp.OS.MS windows.misc</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>comp.sys.IBM.PC.hardware</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>comp.sys.Mac.hardware</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>comp.Windows.x</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>Misc.forsale</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>rec.Autos</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>rec.motorcycles</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>rec.Sport.Baseball</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>rec.Sport.hockey</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>SCI.crypt</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>SCI.Electronics</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>SCI.med</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>SCI.Space</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15</td>
      <td>soc.religion.Christian</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16</td>
      <td>Talk.politics.guns</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17</td>
      <td>Talk.politics.mideast</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18</td>
      <td>Talk.politics.Misc</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19</td>
      <td>Talk.religion.Misc</td>
    </tr>
  </tbody>
</table>
</div>

Nyní můžete vytvořit histogram výkresu předběžné zkoumání četnosti – třída v školení a testovací datové sady. 

```python
import numpy as np
import math
from matplotlib import pyplot as plt

data = df_train["label"].values
labels = set(data)
print(labels)
bins = range(len(labels)+1) 

#plt.xlim([min(data)-5, max(data)+5])

plt.hist(data, bins=bins, alpha=0.8)
plt.title('training data distribution over the class labels)')
plt.xlabel('class label')
plt.ylabel('frequency')
plt.grid(True)
plt.show()

data = df_test["label"].values
labels = set(data)
print(labels)
bins = range(len(labels)+1) 

#plt.xlim([min(data)-5, max(data)+5])

plt.hist(data, bins=bins, alpha=0.8)
plt.title('test data distribution over the class labels)')
plt.xlabel('class label')
plt.ylabel('frequency')
plt.grid(True)
plt.show()
```

Při spuštění Jupypter poznámkového bloku, zobrazí se po provedení předchozí blok kódu pozemků.


## <a name="train-the-model"></a>Trénování modelu

### <a name="specify-scikit-learn-algorithm-and-define-the-text-classifier"></a>Zadejte scikit-další algoritmus a definovat třídění textu

Tento krok zahrnuje, školení scikit-další text klasifikace model pomocí jedné versus Rest Logistic Regression algoritmus učení.

Úplný seznam learnings, najdete v části [inteligentních algoritmů Scikit](http://scikit-learn.org/stable/supervised_learning) dokumentaci.

```python
from sklearn.linear_model import LogisticRegression
import tatk
from tatk.pipelines.text_classification.text_classifier import TextClassifier

log_reg_learner =  LogisticRegression(penalty='l2', dual=False, tol=0.0001, 
                            C=1.0, fit_intercept=True, intercept_scaling=1, 
                            class_weight=None, random_state=None, 
                            solver='lbfgs', max_iter=100, multi_class='ovr',
                            verbose=1, warm_start=False, n_jobs=3) 

#train the model a text column "tweets"
text_classifier = TextClassifier(estimator=log_reg_learner, 
                                text_cols = ["text"], 
                                label_cols = ["label"], 
#                                 numeric_cols = None,
#                                 cat_cols = None, 
                                extract_word_ngrams=True, extract_char_ngrams=True)

```

    TextClassifier::create_pipeline ==> start
    :: number of jobs for the pipeline : 12
    0   text_nltk_preprocessor
    1   text_word_ngrams
    2   text_char_ngrams
    3   assembler
    4   learner
    TextClassifier::create_pipeline ==> end
    
### <a name="fit-the-model"></a>Podle modelu

Použijte výchozí parametry balíčku. Ve výchozím nastavení extrahuje text třídění:
+ Word unigrams a bigrams
+ Znak 4 g

```python
text_classifier.fit(df_train)        
```
   
    TextClassifier::fit ==> start
    schema: col=label:I4:0 col=text:TX:1 header+
    NltkPreprocessor::tatk_fit_transform ==> start
    NltkPreprocessor::tatk_fit_transform ==> end     Time taken: 0.08 mins
    NGramsVectorizer::tatk_fit_transform ==> startNGramsVectorizer::tatk_fit_transform ==> start
    
                vocabulary size=216393
    NGramsVectorizer::tatk_fit_transform ==> end     Time taken: 0.41 mins
                vocabulary size=67230
    NGramsVectorizer::tatk_fit_transform ==> end     Time taken: 0.49 mins
    VectorAssembler::transform ==> start, num of input records=11314
    (11314, 216393)
    (11314, 67230)
    all_features::
    (11314, 283623)
    Time taken: 0.06 mins
    VectorAssembler::transform ==> end
    LogisticRegression::tatk_fit ==> start
    
    [Parallel(n_jobs=3)]: Done  20 out of  20 | elapsed:  2.4min finished
    
    LogisticRegression::tatk_fit ==> end     Time taken: 2.4 mins
    Time taken: 3.04 mins
    TextClassifier::fit ==> end

    TextClassifier(add_index_col=False, callable_proprocessors_list=None,
            cat_cols=None, char_hashing_original=False, col_prefix='tmp_00_',
            decompose_n_grams=False, detect_phrases=False,
            dictionary_categories=None, dictionary_file_path=None,
            embedding_file_path=None, embedding_file_path_fastText=None,
            estimator=None, estimator_vectorizers_list=None,
            extract_char_ngrams=True, extract_word_ngrams=True,
            label_cols=['label'], numeric_cols=None,
            pos_tagger_vectorizer=False,
            preprocessor_dictionary_file_path=None, regex_replcaement='',
            replace_regex_pattern=None, scale_numeric_cols=False,
            text_callable_list=None, text_cols=['text'], text_regex_list=None,
            weight_col=None)


Během cvičení, musí mít text a popisek sloupce. Když pro předpovědi je potřeba jenom textový sloupec. 

### <a name="examine-and-set-the-parameters-of-the-different-pipeline-steps"></a>Zkontrolovat a nastavit parametry kroky jiném kanálu
    
Předtím, než podle modelu se obvykle nastavte parametry. 

***Příklad s text_word_ngrams*** 

Následující ukázky kódu ukazují, jak pro trénování modelu pomocí výchozí parametry kanálu a modelu. 

Pokud chcete zobrazit, jaké parametry jsou zahrnuté pro "text_word_ngrams", použijte [get_step_param_names_by_name](https://docs.microsoft.com/python/api/tatk.core.base_text_model.basetextmodel). Tato funkce vrátí parametry, jako je například malými písmeny, input_col, output_col a tak dále. 

```python
text_classifier.get_step_param_names_by_name("text_word_ngrams")
```

    ['min_df',
     'strip_accents',
     'max_df',
     'decode_error',
     'max_features',
     'binary',
     'input',
     'vocabulary',
     'analyzer',
     'token_pattern',
     'encoding',
     'use_idf',
     'save_overwrite',
     'output_col',
     'stop_words',
     'sublinear_tf',
     'input_col',
     'lowercase',
     'ngram_range',
     'preprocessor',
     'tokenizer',
     'hashing',
     'dtype',
     'norm',
     'smooth_idf',
     'n_hashing_features']

Dále zkontrolujte hodnoty parametru pro "text_char_ngrams":

```python
text_classifier.get_step_params_by_name("text_char_ngrams")        
```
    {'analyzer': 'char_wb',
     'binary': False,
     'decode_error': 'strict',
     'dtype': numpy.float32,
     'encoding': 'utf-8',
     'hashing': False,
     'input': 'content',
     'input_col': 'NltkPreprocessor5283a730506549cc880f074e750607b0',
     'lowercase': True,
     'max_df': 1.0,
     'max_features': None,
     'min_df': 3,
     'n_hashing_features': None,
     'ngram_range': (4, 4),
     'norm': 'l2',
     'output_col': 'NGramsVectorizer8eb11031f6b64eaaad9ff0fd3b0f5b80',
     'preprocessor': None,
     'save_overwrite': True,
     'smooth_idf': True,
     'stop_words': None,
     'strip_accents': None,
     'sublinear_tf': False,
     'token_pattern': '(?u)\\b\\w\\w+\\b',
     'tokenizer': None,
     'use_idf': True,
     'vocabulary': None}

V případě potřeby můžete změnit výchozí parametry.  Následující kód můžete změnit rozsah extrahované znak n gram z (4,4) na (3,4) k extrakci znak tři gram a 4 g:

```python
text_classifier.set_step_params_by_name("text_char_ngrams", ngram_range =(3,4)) 
text_classifier.get_step_params_by_name("text_char_ngrams")
```
    {'analyzer': 'char_wb',
     'binary': False,
     'decode_error': 'strict',
     'dtype': numpy.float32,
     'encoding': 'utf-8',
     'hashing': False,
     'input': 'content',
     'input_col': 'NltkPreprocessor5283a730506549cc880f074e750607b0',
     'lowercase': True,
     'max_df': 1.0,
     'max_features': None,
     'min_df': 3,
     'n_hashing_features': None,
     'ngram_range': (3, 4),
     'norm': 'l2',
     'output_col': 'NGramsVectorizer8eb11031f6b64eaaad9ff0fd3b0f5b80',
     'preprocessor': None,
     'save_overwrite': True,
     'smooth_idf': True,
     'stop_words': None,
     'strip_accents': None,
     'sublinear_tf': False,
     'token_pattern': '(?u)\\b\\w\\w+\\b',
     'tokenizer': None,
     'use_idf': True,
     'vocabulary': None}

### <a name="export-the-parameters-to-a-file"></a>Exportovat do souboru parametrů
V případě potřeby můžete optimalizovat výkon modelu opětovným spuštěním modelu hodí krok s revidovaný parametry:

```python
import os
params_file_path = os.path.join(data_dir, "params.tsv")
text_classifier.export_params(params_file_path)
```

## <a name="apply-the-classifier"></a>Použít třídění

Použijte klasifikátor vyškolení text na základě testovací datové ke generování předpovědi třídy:

```python
 df_test = text_classifier.predict(df_test)
```

    TextClassifier ::predict ==> start
    NltkPreprocessor::tatk_transform ==> start
    NltkPreprocessor::tatk_transform ==> end     Time taken: 0.05 mins
    NGramsVectorizer::tatk_transform ==> startNGramsVectorizer::tatk_transform ==> start
    
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.15 mins
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.37 mins
    VectorAssembler::transform ==> start, num of input records=7532
    (7532, 216393)
    (7532, 67230)
    all_features::
    (7532, 283623)
    Time taken: 0.03 mins
    VectorAssembler::transform ==> end
    LogisticRegression::tatk_predict_proba ==> start
    LogisticRegression::tatk_predict_proba ==> end   Time taken: 0.01 mins
    LogisticRegression::tatk_predict ==> start
    LogisticRegression::tatk_predict ==> end     Time taken: 0.01 mins
    Time taken: 0.46 mins
    TextClassifier ::predict ==> end
    Order of Labels in predicted probabilities saved to attribute label_order of the class object
    
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Popisek</th>
      <th>text</th>
      <th>pravděpodobnostech</th>
      <th>Předpověď</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7</td>
      <td>Z: v064mb9k@ubvmsd.cc.buffalo.edu (NEIL B....</td>
      <td>[0.0165036341329, 0.0548664746458, 0.020549685...</td>
      <td>12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5</td>
      <td>Z: Lukeš Rick &lt; rick@ee.uwm.edu &gt;\nSubject:...</td>
      <td>[0.025145498995, 0.125877400021, 0.03947047877...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>Z: mathew &lt; mathew@mantis.co.uk &gt;\nSubject: R....</td>
      <td>[0.67566338235, 0.0150749738583, 0.00992439163...</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>17</td>
      <td>Z: bakken@cs.arizona.edu \nSub (Dave Bakken)...</td>
      <td>[0.146063943868, 0.00232465192179, 0.002442807...</td>
      <td>18</td>
    </tr>
    <tr>
      <th>4</th>
      <td>19</td>
      <td>Z: livesey@solntze.wpd.sgi.com (Jon Livesey...</td>
      <td>[0.670712265297, 0.017332269703, 0.01062429663...</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="evaluate-model-performance"></a>Vyhodnocení výkonu modelu
[Modul vyhodnocení](https://docs.microsoft.com/python/api/tatk.evaluation) vyhodnotí přesnost třídění vyškolení text na testovací datové sady. Funkce evaluate nedorozuměním matice generuje a poskytuje skóre makro F1.

```python
 text_classifier.evaluate(df_test)          
```

    TextClassifier ::evaluate ==> start
    Time taken: 0.0 mins
    TextClassifier ::evaluate ==> end
    

Vykreslení nedorozuměním bez matice normalizace pro vizualizaci.


```python
evaluator.plot_confusion_matrix(normalize=False,
                                title='Confusion matrix, without normalization', 
                                print_confusion_matrix=False,
                                figsize=(8,8),
                                colors=None)
```

Matice nejasnostem, bez normalizaci
    
Při spuštění poznámkového bloku nedorozuměním matice se zobrazí



Vykreslení normalizovaný nedorozuměním matice pro vizualizaci.


```python
evaluator.plot_confusion_matrix(normalize=True,
                                title='Normalized Confusion matrix', 
                                print_confusion_matrix=False,
                                figsize=(8,8),
                                colors=None)
```

    Normalized confusion matrix
   
Při spuštění poznámkového bloku nedorozuměním matice se zobrazí

## <a name="save-the-pipeline"></a>Uložení kanálu
Klasifikace kanálu uložte do souboru zip. N gram word ngrams a znak rovněž uložte jako textové soubory.

```python
import os
working_dir = os.path.join(data_dir, 'outputs')  
if not os.path.exists(working_dir):
    os.makedirs(working_dir)

# you can save the trained model as a folder or a zip file
model_file = os.path.join(working_dir, 'sk_model.zip')    
text_classifier.save(model_file)
# %azureml upload outputs/models/sk_model.zip

```
    BaseTextModel::save ==> start
    TatkPipeline::save ==> start
    Time taken: 0.28 mins
    TatkPipeline::save ==> end
    Time taken: 0.38 mins
    BaseTextModel::save ==> end
    
```python
# for debugging, you can save the word n-grams vocabulary to a text file
word_vocab_file_path = os.path.join(working_dir, 'word_ngrams_vocabulary.tsv')
text_classifier.get_step_by_name("text_word_ngrams").save_vocabulary(word_vocab_file_path) 
# %azureml upload outputs/dictionaries/word_ngrams_vocabulary.pkl

# for debugging, you can save the character n-grams vocabulary to a text file
char_vocab_file_path = os.path.join(working_dir, 'char_ngrams_vocabulary.tsv')
text_classifier.get_step_by_name("text_char_ngrams").save_vocabulary(char_vocab_file_path) 
# %azureml upload outputs/dictionaries/char_ngrams_vocabulary.pkl
```

    save_vocabulary ==> start
    saving 216393 n-grams ...
    Time taken: 0.01 mins
    save_vocabulary ==> end
    save_vocabulary ==> start
    saving 67230 n-grams ...
    Time taken: 0.0 mins
    save_vocabulary ==> end
 
## <a name="load-the-pipeline"></a>Načíst kanálu
Zatížení klasifikace kanálu a word ngrams a znak n gram pro inferencing:

```python
# in order to deploy the trained model, you have to load the zip file of the classifier pipeline
loaded_text_classifier = TextClassifier.load(model_file)

from tatk.feature_extraction import NGramsVectorizer
word_ngram_vocab = NGramsVectorizer.load_vocabulary(word_vocab_file_path)
char_ngram_vocab = NGramsVectorizer.load_vocabulary(char_vocab_file_path)
```
    BaseTextModel::load ==> start
    TatkPipeline::load ==> start
    Time taken: 0.14 mins
    TatkPipeline::load ==> end
    Time taken: 0.15 mins
    BaseTextModel::load ==> end
    loading 216393 n-grams ...
    loading 67230 n-grams ...
    

## <a name="test-the-pipeline"></a>Testování kanálu

Abyste mohli vyhodnotit testovací datové sady, použít kanálu klasifikace načíst text:

```python
predictions = loaded_text_classifier.predict(df_test)
loaded_evaluator = loaded_text_classifier.evaluate(predictions)
loaded_evaluator.get_metrics('macro_f1')
```
    TextClassifier ::predict ==> start
    NltkPreprocessor::tatk_transform ==> start
    NltkPreprocessor::tatk_transform ==> end     Time taken: 0.05 mins
    NGramsVectorizer::tatk_transform ==> startNGramsVectorizer::tatk_transform ==> start
    
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.14 mins
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.36 mins
    VectorAssembler::transform ==> start, num of input records=7532
    (7532, 216393)
    (7532, 67230)
    all_features::
    (7532, 283623)
    Time taken: 0.03 mins
    VectorAssembler::transform ==> end
    LogisticRegression::tatk_predict_proba ==> start
    LogisticRegression::tatk_predict_proba ==> end   Time taken: 0.01 mins
    LogisticRegression::tatk_predict ==> start
    LogisticRegression::tatk_predict ==> end     Time taken: 0.01 mins
    Time taken: 0.45 mins
    TextClassifier ::predict ==> end
    Order of Labels in predicted probabilities saved to attribute label_order of the class object
    TextClassifier ::evaluate ==> start
    Time taken: 0.0 mins
    TextClassifier ::evaluate ==> en
    
    0.82727029243808903

## <a name="operationalization-deploy-and-consume"></a>Operationalization: nasazení a využívat

V této části nasadíte kanálu klasifikace text jako k Azure Machine Learning webové služby pomocí [příkazového řádku Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Potom využívají webové služby pro školení a vyhodnocování.

**Přihlaste se k předplatnému Azure pomocí rozhraní příkazového řádku Azure**

Pomocí [Azure](https://azure.microsoft.com/) účet s platným předplatným, přihlaste se pomocí rozhraní příkazového řádku následující příkaz:
<br>`az login`

+ Chcete-li přepnout do jiného předplatného Azure, použijte příkaz:
<br>`az account set --subscription [your subscription name]`

+ Pokud chcete zobrazit aktuální účet správy modelu, použijte příkaz:
  <br>`az ml account modelmanagement show`

**Vytvoření a nastavení prostředí pro nasazení**

Stačí nastavit prostředí pro nasazení jednou. Pokud nemáte ještě, nastavit prostředí pro nasazení teď pomocí [tyto pokyny](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

1. Zkontrolujte, že vaše prostředí Azure Machine Learning, model správy účtů a skupin prostředků, které jsou umístěny ve stejné oblasti.

1. Stáhněte si konfigurační soubor nasazení nástroje z úložiště objektů Blob a uložit místně:

   ```python
   # Download the deployment config file from Blob storage `url` and save it locally under `file_name`:
   deployment_config_file_url = 'https://aztatksa.blob.core.windows.net/dailyrelease/tatk_deploy_config.yaml'
   deployment_config_file_path=os.path.join(resources_dir, 'tatk_deploy_config.yaml')
   import urllib.request
   urllib.request.urlretrieve(deployment_config_file_url, deployment_config_file_path)
   ```

1. Aktualizujte konfigurační soubor nasazení, které jste stáhli, aby odrážela vaše prostředky:

   ```python
   web_service_name = 'please type your web service name'
   working_directory= os.path.join(resources_dir, 'deployment') 

   web_service = text_classifier.deploy(web_service_name= web_service_name, 
                          config_file_path=deployment_config_file_path,
                          working_directory= working_directory)  
   ```

1. Vzhledem k tomu, že pro cvičný model je úspěšně nasazena, vyvolání vyhodnocování webovou službu na nová datová sada:

   ```python
   print("Service URL: {}".format(web_service._service_url))
   print("Service Key: {}".format(web_service._api_key))
   ```

1. Načte později pomocí jeho názvu webové služby:

   ```python
   from tatk.operationalization.csi.csi_web_service import CsiWebService
   url = "<please type the service URL here>"
   key = "<please type the service Key here>"
   web_service = CsiWebService(url, key)
   ```

1. Spolu s textem dva e-mailů prováděné z datové sady 20 newsgrpoups testovací webové služby:

   ```python
   # Example input data for scoring
   import json
   dict1 ={}
   dict1["recordId"] = "a1" 
   dict1["data"]= {}
   dict1["data"]["text"] = """
   I'd be interested in a copy of this code if you run across it.
   (Mail to the author bounced)
    > / hpldsla:comp.graphics / email-address-removed / 12:53 am  May 13,
    1993 /
    > I fooled around with this problem a few years ago, and implemented a
    > simple method that ran on a PC.
    > was very simple - about 40 or 50 lines of code.
    . . .
    > Somewhere I still have it
    > and could dig it out if there was interest.
   """
   
   dict2 ={}
   dict2["recordId"] = "b2"
   dict2["data"] ={}
   dict2["data"]["text"] = """
   >>Could the people discussing recreational drugs such as mj, lsd, mdma, etc.,
   >>take their discussions to alt.drugs? Their discussions will receive greatest
   >>contribution and readership there. The people interested in strictly
   >>"smart drugs" (i.e. Nootropics) should post to this group. The two groups
   >>(alt.drugs & alt.psychoactives) have been used interchangably lately.
   >>I do think that alt.psychoactives is a deceiving name. alt.psychoactives
   >>is supposedly the "smart drug" newsgroup according to newsgroup lists on
   >>the Usenet. Should we establish an alt.nootropics or alt.sdn (smart drugs &
   >>nutrients)? I have noticed some posts in sci.med.nutrition regarding
   >>"smart nutrients." We may lower that groups burden as well.
   >   

   I was wondering if a group called 'sci.pharmacology' would be relevent.
   This would be used for a more formal discussion about pharmacological
   issues (pharmacodynamics, neuropharmacology, etc.)      

   Just an informal proposal (I don't know anything about the net.politics
   for adding a newsgroup, etc.)

   """

   dict_list =[dict1, dict2]
   data ={}
   data["values"] = dict_list
   input_data_json_str = json.dumps(data)
   print (input_data_json_str)
   prediction = web_service.score(input_data_json_str)
   prediction
   ```

   ```
   {"values": [{"recordId": "a1", "data": {"text": "\nI'd be interested in a copy of this code if you run across it.\n(Mail to the author bounced)\n > / hpldsla:comp.graphics / email-address-removed / 12:53 am  May 13,\n 1993 /\n > I fooled around with this problem a few years ago, and implemented a\n > simple method that ran on a PC.\n > was very simple - about 40 or 50 lines of code.\n . . .\n > Somewhere I still have it\n > and could dig it out if there was interest.\n"}}, {"recordId": "b2", "data": {"text": "\n>>Could the people discussing recreational drugs such as mj, lsd, mdma, etc.,\n>>take their discussions to alt.drugs? Their discussions will receive greatest\n>>contribution and readership there. The people interested in strictly\n>>\"smart drugs\" (i.e. Nootropics) should post to this group. The two groups\n>>(alt.drugs & alt.psychoactives) have been used interchangably lately.\n>>I do think that alt.psychoactives is a deceiving name. alt.psychoactives\n>>is supposedly the \"smart drug\" newsgroup according to newsgroup lists on\n>>the Usenet. Should we establish an alt.nootropics or alt.sdn (smart drugs &\n>>nutrients)? I have noticed some posts in sci.med.nutrition regarding\n>>\"smart nutrients.\" We may lower that groups burden as well.\n>\n\nI was wondering if a group called 'sci.pharmacology' would be relevent.\nThis would be used for a more formal discussion about pharmacological\nissues (pharmacodynamics, neuropharmacology, etc.)\n\nJust an informal proposal (I don't know anything about the net.politics\nfor adding a newsgroup, etc.)\n\n"}}]}
   F1 2018-05-02 00:10:58,272 INFO Web service scored. 
    
   '{"values": [{"recordId": "b2", "data": {"text": "\\n>>Could the people discussing recreational drugs such as mj, lsd, mdma, etc.,\\n>>take their discussions to alt.drugs? Their discussions will receive greatest\\n>>contribution and readership there. The people interested in strictly\\n>>\\"smart drugs\\" (i.e. Nootropics) should post to this group. The two groups\\n>>(alt.drugs & alt.psychoactives) have been used interchangably lately.\\n>>I do think that alt.psychoactives is a deceiving name. alt.psychoactives\\n>>is supposedly the \\"smart drug\\" newsgroup according to newsgroup lists on\\n>>the Usenet. Should we establish an alt.nootropics or alt.sdn (smart drugs &\\n>>nutrients)? I have noticed some posts in sci.med.nutrition regarding\\n>>\\"smart nutrients.\\" We may lower that groups burden as well.\\n>\\n\\nI was wondering if a group called \'sci.pharmacology\' would be relevent.\\nThis would be used for a more formal discussion about pharmacological\\nissues (pharmacodynamics, neuropharmacology, etc.)\\n\\nJust an informal proposal (I don\'t know anything about the net.politics\\nfor adding a newsgroup, etc.)\\n\\n", "class": 13}}, {"recordId": "a1", "data": {"text": "\\nI\'d be interested in a copy of this code if you run across it.\\n(Mail to the author bounced)\\n > / hpldsla:comp.graphics / email-address-removed / 12:53 am  May 13,\\n 1993 /\\n > I fooled around with this problem a few years ago, and implemented a\\n > simple method that ran on a PC.\\n > was very simple - about 40 or 50 lines of code.\\n . . .\\n > Somewhere I still have it\\n > and could dig it out if there was interest.\\n", "class": 1}}]}'
   ```


## <a name="next-steps"></a>Další postup

Další informace o Azure Machine Learning balíčku pro Analýza textu v těchto článcích:

+ Pro čtení [balíček přehled a zjistěte, jak ji nainstalovat](https://aka.ms/aml-packages/text).

+ Prozkoumejte [referenční dokumentace](https://aka.ms/aml-packages/text) pro tento balíček.

+ Další informace o [další balíčky Python pro Azure Machine Learning](reference-python-package-overview.md).

