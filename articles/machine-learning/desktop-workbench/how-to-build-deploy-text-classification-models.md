---
title: Sestavit a nasadit model klasifikace textu s použitím balíčku Azure Machine Learning pro analýzu textu.
description: Zjistěte, jak sestavit, trénování, testování a nasadit model klasifikace textu s použitím balíčku Azure Machine Learning pro analýzu textu.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/07/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 73f95280cc02b6f891c4ef67cd11084768d7d282
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853009"
---
# <a name="build-and-deploy-text-classification-models-with-azure-machine-learning"></a>Sestavování a nasazování modelů klasifikace textu pomocí služby Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


V tomto článku se dozvíte jak pro trénování a nasadit model klasifikace textu s **Azure Machine Learning balíček pro rozhraní Text Analytics** (AMLPTA). Cílem klasifikace textu je přiřadit část textu na jeden nebo více předdefinovaných tříd nebo kategorií. Tento text může být například dokument, zpravodajskému článku, vyhledávací dotaz, e-mail, tweet, lístky podpory.

Existují různé aplikace klasifikace textu, jako: 
+ Zařazení novinové články a příspěvky při přenosu obsahu do témat
+ Uspořádání webových stránek do hierarchických kategorií 
+ Filtrování spamu
+ Analýza mínění
+ Předpověď záměru uživatele z vyhledávacích dotazů
+ Směrování lístky žádostí o podporu
+ Analýza názorů zákazníků 

Text klasifikace modelu sestavení a nasazení pracovního postupu pro model s AMLPTA vypadá takto:

1. Načtení dat
2. Trénování modelu
3. Použít třídění 
4. Vyhodnocení výkonu modelu
5. Uložení kanálu
6. Test kanálu
8. Tento model nasadíme jako webovou službu

Najdete [balíček referenční dokumentaci](https://aka.ms/aml-packages/text) pro podrobné referenční dokumentace pro každý modul a třídy.

Ukázkový kód v tomto článku se používá scikit-informace kanálu.

## <a name="prerequisites"></a>Požadavky 

1. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

1. Následující účty a aplikace musí mít nastavení a instalaci:
   - Účet experimentování ve službě Machine Learning Azure 
   - Účet správy modelů Azure Machine Learning
   - Nainstalovanou aplikaci Azure Machine Learning Workbench

   Pokud tyto tři jsou ještě vytvořen nebo nainstalován, postupujte [Quickstart pro Azure Machine Learning a Workbench instalace](../desktop-workbench/quickstart-installation.md) článku. 

1. Musíte nainstalovat balíček Azure Machine Learning pro analýzu textu. Zjistěte, jak [instalaci tohoto balíčku zde](https://aka.ms/aml-packages/text).


## <a name="sample-data-and-jupyter-notebook"></a>Ukázková data a aplikace Jupyter notebook

### <a name="get-the-jupyter-notebook"></a>Získání poznámkového bloku Jupyter

Vyzkoušejte si to sami. Stáhněte Poznámkový blok a sami ho spusťte.

> [!div class="nextstepaction"]
> [Získání poznámkového bloku Jupyter](https://aka.ms/aml-packages/text/notebooks/text_classification_sentiment_data)

### <a name="download-and-explore-the-sample-data"></a>Stažení a prozkoumání ukázková data
V následujícím příkladu [datovou sadu 20 diskusních skupin](http://qwone.com/~jason/20Newsgroups/) , který je k dispozici prostřednictvím scikit-další knihovny ukazují, jak vytvořit klasifikátor text pomocí Azure Machine Learning balíčku pro analýzu textu. 

Datová sada 20 diskusních skupin má asi 18 000 diskusní příspěvky na 20 dalších tématech rozdělit do dvou podskupin: jeden pro trénování a druhou pro hodnocení výkonu. Rozdělit do trénování a testování je založena na každé datum příspěvek zprávu, zda se před nebo po určitém datu.

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

### <a name="set-the-location-of-the-data"></a>Nastavit umístění dat
Nastavte umístění, kam jste stáhli data v parametru dir data. Můžete také použít vlastní data, vstupní datová sada musí být formát soubor *.tsv.

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
 
 Data se skládá z popisku a text
    
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>label</th>
      <th>text</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7</td>
      <td>Od: v064mb9k@ubvmsd.cc.buffalo.edu (NEIL B....</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5</td>
      <td>Od: Miller Rick &lt; rick@ee.uwm.edu &gt;\nSubject:...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>Z: mathew &lt; mathew@mantis.co.uk &gt;\nSubject: R...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>17</td>
      <td>Od: bakken@cs.arizona.edu \nSub (Dave Bakken)...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>19</td>
      <td>Od: livesey@solntze.wpd.sgi.com (Jon Livesey...</td>
    </tr>
  </tbody>
</table>
</div>

Získejte shodu mezi kategorií a jejich název.

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

Nyní můžete vytvořit histogram předběžné zkoumání diagram třídy frekvence do školení a testovací datové sady. 

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

Při spuštění poznámkového bloku Jupypter vykreslení se zobrazí po spuštění předchozího bloku kódu.


## <a name="train-the-model"></a>Trénování modelu

### <a name="specify-scikit-learn-algorithm-and-define-the-text-classifier"></a>Zadejte scikit-další algoritmus a definovat třídění text

Tento krok zahrnuje školení scikit-informace model klasifikace textu pomocí jedné oproti Rest učení algoritmu logistické regrese.

Úplný seznam poznatky, najdete [inteligentních algoritmů Scikit](http://scikit-learn.org/stable/supervised_learning) dokumentaci.

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
    
### <a name="fit-the-model"></a>Přizpůsobení modelu

Použijte výchozí parametry balíčku. Ve výchozím nastavení extrahuje třídění text:
+ Unigrams aplikace Word a bigrams
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


Při školení, musí obsahovat text a popisek sloupce. Zatímco textového sloupce je potřeba pro předpovědi. 

### <a name="examine-and-set-the-parameters-of-the-different-pipeline-steps"></a>Prozkoumání a nastavte parametry kroky jiném kanálu
    
Předtím, než právě modelu se obvykle, nastavte parametry. 

***Příklad uvedený s text_word_ngrams*** 

Následující ukázky kódu ukazují, jak pro trénování modelu s použitím výchozí parametry kanálu a modelu. 

Chcete-li zjistit, jaké parametry jsou součástí "text_word_ngrams", použijte [get_step_param_names_by_name](/python/api/msft-tatk/tatk.core.base_text_model.basetextmodel#get-step-param-names-by-name). Tato funkce vrací parametry, jako je například malými písmeny input_col output_col a tak dále. 

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

V případě potřeby můžete změnit výchozí parametry.  Následujícím kódem můžete změnit rozsah extrahované znak n gramy z (4,4) na (3,4) k extrakci znaků tri gramy a 4 g:

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
V případě potřeby můžete optimalizovat výkon modelů opětovným spuštěním model přizpůsobení krokem. stačí k revidované parametry:

```python
import os
params_file_path = os.path.join(data_dir, "params.tsv")
text_classifier.export_params(params_file_path)
```

## <a name="apply-the-classifier"></a>Použít třídění

Použijte klasifikátor trénovaného text na základě testovací datové ke generování třídy předpovědí:

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
      <th>label</th>
      <th>text</th>
      <th>pravděpodobnosti</th>
      <th>Předpověď</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7</td>
      <td>Od: v064mb9k@ubvmsd.cc.buffalo.edu (NEIL B....</td>
      <td>[0.0165036341329 0.0548664746458, 0.020549685...</td>
      <td>12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5</td>
      <td>Od: Miller Rick &lt; rick@ee.uwm.edu &gt;\nSubject:...</td>
      <td>[0.025145498995 0.125877400021, 0.03947047877...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>Z: mathew &lt; mathew@mantis.co.uk &gt;\nSubject: R...</td>
      <td>[0.67566338235 0.0150749738583, 0.00992439163...</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>17</td>
      <td>Od: bakken@cs.arizona.edu \nSub (Dave Bakken)...</td>
      <td>[0.146063943868 0.00232465192179, 0.002442807...</td>
      <td>18</td>
    </tr>
    <tr>
      <th>4</th>
      <td>19</td>
      <td>Od: livesey@solntze.wpd.sgi.com (Jon Livesey...</td>
      <td>[0.670712265297 0.017332269703, 0.01062429663...</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="evaluate-model-performance"></a>Vyhodnocení výkonu modelu
[Vyhodnocení modulu](/python/api/msft-tatk/tatk.evaluation) vyhodnotí přesnost třídění trénovaného text na základě testovací datové. Vyhodnocení funkce generuje chybovou matici a nabízí F1 – makro skóre.

```python
 text_classifier.evaluate(df_test)          
```

    TextClassifier ::evaluate ==> start
    Time taken: 0.0 mins
    TextClassifier ::evaluate ==> end
    

Vykreslení v agregaci, přidali bez matice normalizace pro vizualizaci.


```python
evaluator.plot_confusion_matrix(normalize=False,
                                title='Confusion matrix, without normalization', 
                                print_confusion_matrix=False,
                                figsize=(8,8),
                                colors=None)
```

Chybová matice bez normalizace
    
Při spuštění poznámkového bloku se zobrazí chybová matice



Zobrazení normalizovaná chybová matice pro vizualizaci.


```python
evaluator.plot_confusion_matrix(normalize=True,
                                title='Normalized Confusion matrix', 
                                print_confusion_matrix=False,
                                figsize=(8,8),
                                colors=None)
```

    Normalized confusion matrix
   
Při spuštění poznámkového bloku se zobrazí chybová matice

## <a name="save-the-pipeline"></a>Uložení kanálu
Uložení kanálu zařazení do souboru zip. N gramy ngrams aplikace word a znak navíc uložte jako textové soubory.

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
Načte klasifikace kanálu a ngrams aplikace word a znak n gramy pro odvozování:

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
    

## <a name="test-the-pipeline"></a>Test kanálu

K vyhodnocení testovací datové sady, platí kanálu klasifikace načíst text:

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

## <a name="operationalization-deploy-and-consume"></a>Operacionalizace: nasazení a využití

V této části nasadíte kanálu text klasifikaci pomocí služby Azure Machine Learning web [CLI aplikace Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Potom můžete používání této webové služby pro trénování a vyhodnocování.

**Přihlaste se k předplatnému Azure pomocí Azure CLI**

Pomocí [Azure](https://azure.microsoft.com/) účet s platným předplatným, přihlaste se pomocí následujícího příkazu rozhraní příkazového řádku:
<br>`az login`

+ Pokud chcete přepnout do jiného předplatného Azure, použijte příkaz:
<br>`az account set --subscription [your subscription name]`

+ Pokud chcete zobrazit aktuální účet správy modelů, použijte příkaz:
  <br>`az ml account modelmanagement show`

**Vytvoření a nastavení prostředí pro nasazení**

Stačí nastavit prostředí pro nasazení jednou. Pokud nemáte ještě, nastavte si nyní pomocí prostředí pro nasazení [tyto pokyny](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

1. Ujistěte se, že vaše prostředí Azure Machine Learning, účet služby Správa modelů a skupina prostředků se nacházejí ve stejné oblasti.

1. Stáhněte si konfigurační soubor nasazení z úložiště objektů Blob a uloží do místního prostředí:

   ```python
   # Download the deployment config file from Blob storage `url` and save it locally under `file_name`:
   deployment_config_file_url = 'https://aztatksa.blob.core.windows.net/dailyrelease/tatk_deploy_config.yaml'
   deployment_config_file_path=os.path.join(resources_dir, 'tatk_deploy_config.yaml')
   import urllib.request
   urllib.request.urlretrieve(deployment_config_file_url, deployment_config_file_path)
   ```

1. Aktualizujte konfigurační soubor nasazení, které jste si stáhli tak, aby odrážel vaše prostředky:

   ```python
   web_service_name = 'please type your web service name'
   working_directory= os.path.join(resources_dir, 'deployment') 

   web_service = text_classifier.deploy(web_service_name= web_service_name, 
                          config_file_path=deployment_config_file_path,
                          working_directory= working_directory)  
   ```

1. Vzhledem k tomu, že úspěšně nasadil trénovaného modelu vyvolejte hodnoticí webové služby na novou datovou sadu:

   ```python
   print("Service URL: {}".format(web_service._service_url))
   print("Service Key: {}".format(web_service._api_key))
   ```

1. Načtěte webové služby v každém okamžiku pomocí jeho názvu:

   ```python
   from tatk.operationalization.csi.csi_web_service import CsiWebService
   url = "<please type the service URL here>"
   key = "<please type the service Key here>"
   web_service = CsiWebService(url, key)
   ```

1. Testování webové služby s tělem dva e-mailů z 20 newsgrpoups datové sady:

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

Další informace o Azure Machine Learning balíčku pro analýzu textu v těchto článcích:

+ Přečtěte si [balíček přehled](https://aka.ms/aml-packages/text).

+ Prozkoumejte [referenční dokumentaci](https://aka.ms/aml-packages/text) pro tento balíček.

+ Další informace o [další balíčky Python pro Azure Machine Learning](reference-python-package-overview.md).