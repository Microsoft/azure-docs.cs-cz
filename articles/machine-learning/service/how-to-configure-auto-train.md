---
title: Konfigurace automatizovaného se strojovým učením
titleSuffix: Azure Machine Learning service
description: Automatizované machine learning vybere algoritmus pro vás a generuje modelu připravené na nasazení. Další možnosti, které můžete použít ke konfiguraci automatické se strojovým učením.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: e1dd0cf995d7d9c263e49735decc5573107b1add
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140162"
---
# <a name="configure-automated-machine-learning-experiments"></a>Konfigurace automatizovaného se strojovým učením

Automatizované machine learning za vás vybere algoritmus a hyperparameters a generuje modelu připravené na nasazení. Existuje několik možností, které můžete použít ke konfiguraci automatické se strojovým učením. V této příručce se dozvíte, jak definovat různá nastavení konfigurace.

Chcete-li zobrazit příklady automatizované experimentů machine learningu, naleznete v tématu [kurz: Vyškolíme model klasifikace pomocí automatizovaných strojového učení](tutorial-auto-train-models.md) nebo [trénování modelů pomocí automatizovaných strojového učení v cloudu](how-to-auto-train-remote.md).

Možnosti konfigurace je k dispozici ve službě automatizované machine learning:

* Vyberte typ testu: klasifikace, regrese nebo Prognózování
* Zdroj dat, formát a načítat data
* Vyberte cílové výpočetní prostředky: místní nebo vzdálené
* Automatizované strojového učení nastavení testu
* Spuštění automatizované experimentu strojového učení
* Zkoumání metrik model
* Registrace a nasazení modelu

## <a name="select-your-experiment-type"></a>Vyberte typ testu
Než začnete experimentu, byste měli určit druh machine learning problému jsou řešení. Automatizované machine learning podporuje typy úloh klasifikace, regrese a Prognózování. 

Technologie automatizovaného strojového učení, které jsou obecně dostupné, **Prognózování je stále ve verzi public preview.**

Automatizované machine learning podporuje tyto algoritmy během automatizace a ladění procesu. Jako uživatel není nutné lze určit algoritmus.

Klasifikace | Regrese | Prognózování
|-- |-- |--
[Logistické regrese](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastické Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastické Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Pomocí stochastického sestupu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Světlý GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Světlý GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Přechodu zvýšení skóre](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Přechodu zvýšení skóre](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Vektorové podpory C klasifikace (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#regression)|[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#regression)
[Lineární SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[K nejbližší okolí](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K nejbližší okolí](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[K nejbližší okolí](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors)|[LARS laso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS laso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Pomocí stochastického sestupu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Pomocí stochastického sestupu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Náhodné doménové struktury](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Náhodné doménové struktury](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Náhodné doménové struktury](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Velmi náhodnou stromů](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Velmi náhodnou stromů](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Velmi náhodnou stromů](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Přechodu zvýšení skóre](https://scikit-learn.org/stable/modules/ensemble.html#classification)|
[Světlý GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|


## <a name="data-source-and-format"></a>Zdroj dat a formát
Automatizované machine learning podporuje data, která se nachází v místním počítači nebo v cloudu, jako je Azure Blob Storage. Data lze načíst do scikit-informace podporovaných datových formátů. Můžete číst data do:
* Pole Numpy X (funkce) a y (Cílová proměnná nebo také popis)
* Pandas dataframe 

Příklady:

*   Numpy pole

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data 
    y_digits = digits.target
    ```

*   Pandas dataframe

    ```python
    import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Načíst data pro spouštění experimentů na vzdálený výpočetní

Pokud používáte vzdálený výpočetní ke spuštění experimentu, načítání dat musí být zabalené v skript pythonu samostatné `get_data()`. Tento skript je spuštěn na vzdálený výpočetní, ve kterém se spouští Automatické experimentu strojového učení. `get_data` eliminuje nutnost načíst data při přenosu pro každou iteraci. Bez `get_data`, experimentu selže, když jste spustili vzdálený výpočetní prostředky.

Tady je příklad `get_data`:

```python
%%writefile $project_folder/get_data.py 
import pandas as pd 
from sklearn.model_selection import train_test_split 
from sklearn.preprocessing import LabelEncoder 
def get_data(): # Burning man 2016 data 
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42) 
    return { "X" : df, "y" : y }
```

Ve vaší `AutoMLConfig` objektu, můžete zadat `data_script` parametr a zadejte cestu k `get_data` souboru skriptu, podobně jako následující:

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

`get_data` skript může vrátit:

Klíč | Typ |    Vzájemně se vylučuje s | Popis
---|---|---|---
X | Pandas Dataframe nebo Numpy pole | data_train, popisek, sloupce |  Všechny funkce k trénování s
Y | Pandas Dataframe nebo Numpy pole |   label   | Popisek dat pro trénování s. Pro klasifikaci by měl být pole celých čísel.
X_valid | Pandas Dataframe nebo Numpy pole   | data_train, popisek | _Volitelné_ všechny funkce má provést ověření. Pokud není zadán, X je rozdělená mezi trénování a ověření
y_valid |   Pandas Dataframe nebo Numpy pole | data_train, popisek | _Volitelné_ popisku dat má provést ověření. Pokud není zadán, y je rozdělená mezi trénování a ověření
sample_weight | Pandas Dataframe nebo Numpy pole |   data_train, popisek, sloupce| _Volitelné_ váženou hodnotu pro každý vzorek. Používá se, když chcete přiřadit různé váhy na základě datových bodů 
sample_weight_valid | Pandas Dataframe nebo Numpy pole | data_train, popisek, sloupce |    _Volitelné_ váženou hodnotu pro každý vzorek ověření. Pokud není zadán, sample_weight je rozdělená mezi trénování a ověření
data_train |    Pandas Dataframe |  X, y, X_valid, y_valid |    Všechna data (funkce a popisek), tak moct trénovat s
label | řetězec  | X, y, X_valid, y_valid |  Představuje sloupec v data_train popisek
Sloupce | pole řetězců  ||  _Volitelné_ seznamu povolených IP adres sloupců používali pro funkce
cv_splits_indices   | Pole celých čísel ||  _Volitelné_ seznamu indexů pro rozdělení dat pro křížové ověření

### <a name="load-and-prepare-data-using-dataprep-sdk"></a>Načíst a připravit data pomocí sady SDK přípravy
Automatizované se strojovým učením podporuje načítání dat a transformace pomocí sady SDK přípravy. Pomocí sady SDK poskytuje možnost

>* Načtení z mnoha typů souborů s analýzy odvozování parametrů (kódování, oddělovač, hlavičky)
>* Převod typu pomocí odvození během načítání souboru
>* Podpora připojení pro MS SQL Server a úložiště Azure Data Lake
>* Přidání sloupce pomocí výrazu
>* Dává chybějící hodnoty
>* Odvození sloupce podle příkladu
>* Filtrování
>* Vlastních transformací Pythonu

Další informace o datech přípravu sdk naleznete [postup přípravy dat pro modelování článku](how-to-load-data.md). Tady je příklad, načítání dat pomocí sady sdk pro data přípravu. 
```python
# The data referenced here was pulled from `sklearn.datasets.load_digits()`.
simple_example_data_root = 'https://dprepdata.blob.core.windows.net/automl-notebook-data/'
X = dprep.auto_read_file(simple_example_data_root + 'X.csv').skip(1)  # Remove the header row.
# You can use `auto_read_file` which intelligently figures out delimiters and datatypes of a file.

# Here we read a comma delimited file and convert all columns to integers.
y = dprep.read_csv(simple_example_data_root + 'y.csv').to_long(dprep.ColumnSelector(term='.*', use_regex = True))
```

## <a name="train-and-validation-data"></a>Trénování a ověřování dat

Můžete zadat samostatné trénování a ověření nastavení prostřednictvím get_data() nebo přímo v `AutoMLConfig` metody.

## <a name="cross-validation-split-options"></a>Možností rozdělení, křížového ověření

### <a name="k-folds-cross-validation"></a>K přeložení křížové ověření

Použití `n_cross_validations` nastavení určuje počet křížové ověření. Trénovací datové sady se náhodně rozdělit na `n_cross_validations` složení stejnou velikost. Při každé křížového ověření round jeden složení se použije pro ověřování modelů trénovaných na zbývající složení. Tento proces se opakuje pro `n_cross_validations` zaokrouhlí dokud každý fold se jednou nepoužije jako sada ověření. Průměrné skóre napříč všemi `n_cross_validations` zaokrouhlí se ohlásí, a odpovídající modelu budou retrained na celém trénovací datové sady.

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Monte Carlo ke zjištění křížové ověření (označovaný také jako Opakuje se náhodný dílčí vzorkování)

Použít `validation_size` určit procento trénovací datové sady, který se má použít pro ověření a potom použijte `n_cross_validations` určit počet křížové ověření. Při každé křížové ověření round podmnožinu velikost `validation_size` budou náhodně vybrány pro ověřování modelů trénovaných na zbývající data. Nakonec se stanoví skóre průměr všech `n_cross_validations` zaokrouhlí se ohlásí, a odpovídající modelu budou retrained na celém trénovací datové sady.

### <a name="custom-validation-dataset"></a>Vlastní ověření datové sady

Použít datovou sadu vlastní ověřování, pokud náhodného dělení není přijatelná (obvykle časové řady dat nebo imbalanced dat). Můžete zadat vlastní ověření datové sady. Ověření datové sadě zadán namísto náhodného datové sady se vyhodnotí modelu.

## <a name="compute-to-run-experiment"></a>Výpočetní prostředky pro spuštění experimentu

Dále určete, kde bude Trénink modelu. Automatické experimentu strojového učení a trénování můžete spustit na následujících výpočetních možností:
*   Místní počítač například místní pracovní plocha nebo přenosný počítač – obecně Pokud máte malé datové sady a jsou stále ve fázi průzkumu.
*   Vzdálený počítač v cloudu – [Azure Machine Learning spravovat Compute](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) je spravovaná služba, která umožňuje trénování modelů strojového učení na clusterech virtuálních počítačů Azure.

Zobrazit [webu Github](https://github.com/Azure/MachineLearningNotebooks/tree/master/automl) například cílových výpočetních prostředí poznámkové bloky s místním a vzdáleným.

<a name='configure-experiment'/>

## <a name="configure-your-experiment-settings"></a>Konfigurovat nastavení testu

Existuje několik možností, které můžete použít ke konfiguraci vašeho automatizované experimentu strojového učení. Tyto parametry jsou nastavené po vytvoření instance `AutoMLConfig` objektu.

Možné příklady:

1.  Klasifikace experimentu s využitím AUC váha jako primární metriku s maximální dobou 12 000 sekund na iterace experimentu na konec až 50 iterací a 2 přeložení křížového ověření.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        X=X, 
        y=y,
        n_cross_validations=2)
    ```
2.  Tady je příklad sady experiment regrese k ukončení po 100 iterací, s každou iterací trvá až 600 sekund s 5 ověření pro různé složení.

    ````python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        primary_metric='r2_score',
        X=X, 
        y=y,
        n_cross_validations=5)
    ````

Tato tabulka shrnuje nastavení parametrů, které jsou k dispozici pro experiment a jejich výchozí hodnoty.

Vlastnost |  Popis | Výchozí hodnota
--|--|--
`task`  |Zadejte typ problému machine learning. Povolené hodnoty jsou <li>Klasifikace</li><li>Regrese</li><li>Prognózování</li>    | Žádný |
`primary_metric` |Metrika, kterou chcete optimalizovat při vytváření modelu. Například pokud zadáte jako primary_metric přesnost, automatizované strojového učení vyhledá najít model s nejvyšší přesností. Můžete zadat pouze jeden primary_metric na experiment. Povolené hodnoty jsou <br/>**Klasifikace**:<br/><li> accuracy  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**Regrese**: <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score  </li> | Pro klasifikaci: přesnost <br/>Pro regresní: spearman_correlation <br/> |
`experiment_exit_score` |   Nastavení cílové hodnoty pro váš primary_metric. Po nalezení modelu, který splňuje cíl primary_metric, automatizované strojového učení se zastaví, iterace a ukončí experimentu. Pokud tato hodnota není nastavená (výchozí), automatické experimentu strojového učení a bude nadále spuštěna počet iterací podle iterace. Přebírá hodnotu double. Pokud cíl nikdy dosáhne, bude pokračovat automatizovaná strojového učení, dokud nebude dosaženo počtu iterací podle iterace.| Žádný
`iterations` |Maximální počet průchodu cyklem. Každá iterace je rovna trénovací úlohu, která vede kanálu. Kanál je předzpracování dat a modelu. Chcete-li získat model vysoce kvalitní, použijte 250 nebo více    | 100
`max_concurrent_iterations`|    Maximální počet opakování při paralelním spuštění. Toto nastavení funguje jenom pro vzdálené výpočetní prostředky.|   1
`max_cores_per_iteration`   | Určuje, kolik jader na cílové výpočetní prostředí se použije k natrénování jeden kanál. Pokud tento algoritmus mohou využívat více jader, to zvyšuje výkon na počítači s více jádry. Můžete ho nastavit na hodnotu -1 použití všech dostupných jader v počítači.|  1
`iteration_timeout_minutes` |   Omezuje množství času (v minutách), trvá konkrétní iteraci. Pokud iterace překročí zadanou hodnotu, bude zrušen danou iteraci. Pokud není nastavena, pak iterace běží nepřetržitě až do dokončení. |   Žádný
`n_cross_validations`   |Počet rozdělení křížových ověření| Žádný
`validation_size`   |Velikost ověření nastavit jako procento všech ukázka školení.|  Žádný
`preprocess` | True nebo False <br/>Hodnota TRUE povolí experimentovat provádět předběžného zpracování na vstupu. Tady je podmnožinou předběžného zpracování<li>Chybějící Data: Uplatňuje číselných dat s průměrem, Text se většina výskyt chybí </li><li>Hodnoty zařazené do kategorií: Pokud datový typ je číselné a počet jedinečných hodnot je méně než 5 procentech, převede do jedné hot kódování </li><li>Atd. pro úplný seznam kontrolu [úložiště GitHub](https://aka.ms/aml-notebooks)</li><br/>Poznámka: Pokud je zhuštěný dat nelze použít předzpracování = true |  False | 
`blacklist_models`  | Automatizované experimentu strojového učení a má mnoho různých algoritmů, které se pokusí. Konfigurace vyloučení určitých algoritmů z experimentu. Je užitečné, pokud jste si vědomi, že algoritmy nefungují dobře u datové sady. S výjimkou algoritmů můžete ušetřit vám výpočetní prostředky a školení čas.<br/>Povolené hodnoty pro klasifikaci<br/><li>LogisticRegression</li><li>SGD</li><li>MultinomialNaiveBayes</li><li>BernoulliNaiveBayes</li><li>SVM</li><li>LinearSVM</li><li>KNN</li><li>DecisionTree</li><li>RandomForest</li><li>ExtremeRandomTrees</li><li>LightGBM</li><li>GradientBoosting</li><li>TensorFlowDNN</li><li>TensorFlowLinearClassifier</li><br/>Povolené hodnoty pro regresní<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li><br/>Povolené hodnoty pro prognózování<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li>|   Žádný
`whitelist_models`  | Automatizované experimentu strojového učení a má mnoho různých algoritmů, které se pokusí. Konfigurace zahrnují určitých algoritmů pro experiment. Je užitečné, pokud jste si vědomi, že algoritmy fungují dobře u datové sady. <br/>Povolené hodnoty pro klasifikaci<br/><li>LogisticRegression</li><li>SGD</li><li>MultinomialNaiveBayes</li><li>BernoulliNaiveBayes</li><li>SVM</li><li>LinearSVM</li><li>KNN</li><li>DecisionTree</li><li>RandomForest</li><li>ExtremeRandomTrees</li><li>LightGBM</li><li>GradientBoosting</li><li>TensorFlowDNN</li><li>TensorFlowLinearClassifier</li><br/>Povolené hodnoty pro regresní<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li><br/>Povolené hodnoty pro prognózování<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li>|  Žádný
`verbosity` |Určuje úroveň protokolování s informacemi, které se nejvíce podrobné a kritické přičemž nejmenší. Úroveň podrobností má stejné hodnoty, jak jsou definovány v protokolování balíček pythonu. Povolené hodnoty jsou:<br/><li>logging.INFO</li><li>protokolování. UPOZORNĚNÍ</li><li>protokolování. CHYBA</li><li>protokolování. KRITICKÁ</li>  | logging.INFO</li> 
`X` | Všechny funkce k trénování s |  Žádný
`y` |   Popisek dat pro trénování s. Pro klasifikaci by měl být pole celých čísel.|  Žádný
`X_valid`|_Volitelné_ všechny funkce má provést ověření. Pokud není zadán, X je rozdělená mezi trénování a ověření |   Žádný
`y_valid`   |_Volitelné_ popisku dat má provést ověření. Pokud není zadán, y je rozdělená mezi trénování a ověření    | Žádný
`sample_weight` |   _Volitelné_ váženou hodnotu pro každý vzorek. Používá se, když chcete přiřadit různé váhy na základě datových bodů |   Žádný
`sample_weight_valid`   |   _Volitelné_ váženou hodnotu pro každý vzorek ověření. Pokud není zadán, sample_weight je rozdělená mezi trénování a ověření   | Žádný
`run_configuration` |   RunConfiguration objektu.  Používá pro vzdálené spuštění. |Žádný
`data_script`  |    Cesta k souboru, který obsahuje metodu get_data.  Vyžaduje se pro vzdálené spuštění.   |Žádný
`model_explainability` | _Volitelné_ True nebo False <br/>  Hodnota TRUE povolí experimentovat provádět funkce důležitosti pro každou iteraci. Metoda explain_model() konkrétní iteraci slouží také povolit funkci důležitost na vyžádání pro danou iteraci po dokončení testu. | False
`enable_ensembling`|Příznak pro povolení iterace ensembling po dokončení všech iterací.|
`ensemble_iterations`|Počet iterací, během kterých jsme zvolili vybavené kanálu jako součást konečné skupiny stromů.|

## <a name="data-pre-processing-and-featurization"></a>Předběžné zpracování dat a snadné

Pokud používáte `preprocess=True`, následující data předběžného zpracování kroky se provádějí automaticky za vás:
1.  Velkou mohutností či žádné funkce odchylka
    * Vyřaďte ze sady pro trénování a ověření funkce s žádnou užitečnou informaci. Patří mezi funkce s všechny hodnoty, které chybí, stejnou hodnotu napříč všemi řádky nebo s velmi vysokou kardinalitu (například hodnoty hash ID nebo identifikátory GUID).
1.  Chybí hodnota imputace
    *   Numerické funkce dává chybějící hodnoty pomocí průměr hodnot ve sloupci.
    *   Funkce zařazené do kategorií dává chybějící hodnoty pomocí nejčastěji se vyskytující hodnotu.
1.  Generovat další funkce
    * Pro funkce data a času: rok, měsíc, den, den v týdnu, den roku, čtvrtletí, týden v roce, hodinu, minutu, sekundu.
    * Pro textové funkce: termín frekvenci podle slovo unigram bi gramy a tri gramy, počet vektorizaci.
1.  Transformace a kódování
    * Číselné funkce s velmi malým počtem jedinečných hodnot transformována do kategorií funkce.
    * V závislosti na mohutnosti zařazené do kategorií funkcí proveďte popisek kódování nebo (algoritmu hash) jeden horkou kódování.

## <a name="run-experiment"></a>Spusťte experiment

Odeslání experimentu ke spuštění a generovat model. Předání `AutoMLConfig` k `submit` metoda ke generování modelu.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Závislosti jsou nejprve nainstalována na nový počítač.  Může trvat až 10 minut, než se zobrazí výstup.
>Nastavení `show_output` k `True` výsledků ve výstupu se zobrazí se v konzole.


## <a name="explore-model-metrics"></a>Zkoumání metrik model
Ve widgetu nebo vložené můžete zobrazit výsledky, pokud jste v poznámkovém bloku. Zobrazit [sledovat a posuzovat modely](how-to-track-experiments.md#view-run-details) další podrobnosti.


### <a name="classification-metrics"></a>Klasifikace metriky
Tyto metriky jsou uloženy v každé iteraci pro úlohu klasifikace.

|Primární metriku|Popis|Výpočet|Další parametry
--|--|--|--|--|
AUC_Macro| AUC je oblasti pod křivkou charakteristiku provozní příjemce. Makro je aritmetický průměr AUC pro každou třídu.  | [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Průměrná = "makro"|
AUC_Micro| AUC je oblasti pod křivkou charakteristiku provozní příjemce. Micro je vypočítán globably kombinací pravdivě pozitivní i falešně pozitivních výsledků z každé třídě| [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Průměrná = "micro"|
AUC_Weighted  | AUC je oblasti pod křivkou charakteristiku provozní příjemce. Váha je aritmetický průměr skóre pro každou třídu váženo podle počtu true instancí každé třídy| [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Průměrná = "váha"
accuracy|Přesnost je procento předpokládané popisky, které přesně odpovídají true popisky. |[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Žádný|
average_precision_score_macro|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Aritmetický průměr přesnost skóre každé třídy je – makro|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "makro"|
average_precision_score_micro|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Micro je vypočítán globálně pomocí kombinace pravdivě pozitivní i falešně pozitivních výsledků při každé odříznutí|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "micro"|
average_precision_score_weighted|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Váha je aritmetický průměr přesnost průměrné skóre pro každou třídu váženo podle počtu true instancí každé třídy|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "váha"|
balanced_accuracy|Vyvážený přesnost je aritmetický průměr o odvolání pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "makro"|
f1_score_macro|F1 skóre je průměr přesnosti a odvolání. Aritmetický průměr skóre F1 pro každou třídu je – makro|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "makro"|
f1_score_micro|F1 skóre je průměr přesnosti a odvolání. Micro je vypočítán globálně výpočtem celkového pravdivě pozitivní, falešně negativní a počet falešně pozitivních výsledků|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "micro"|
f1_score_weighted|F1 skóre je průměr přesnosti a odvolání. Vážený průměr třídy frekvence skóre F1 pro každou třídu|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "váha"|
log_loss|Toto je ztráta funkce použitá v logistické regrese (multinomial) a rozšíření jako jsou neuronové sítě definované jako záporné pravděpodobnost protokolu true popisků zadaný pravděpodobnostní třídění předpovědi. Jeden vzorek hodnotou true, pokud popisek yt v {0,1} a odhad pravděpodobnosti yp této yt = 1, ztráta protokolu je – protokolování P (yt&#124;yp) =-(yt log(yp) + (1 - yt) protokolu (1 - yp))|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Žádný|
norm_macro_recall|Normalizovaná si možná Vzpomínáte – makro je makro si možná Vzpomínáte, normalizovaná tak, aby náhodná výkonu má skóre 0 a ideální výkon má skóre 1. Toho dosáhnete pomocí norm_macro_recall: = (recall_score_macro - R) /(1-R), kde R je očekávané hodnotě recall_score_macro pro náhodné predikcí (tj, R = 0,5 pro binární klasifikaci) a R=(1/C) problémů klasifikace C – třída|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "makro" a potom (recall_score_macro - R) /(1-R), kde R je očekávané hodnotě recall_score_macro pro náhodné predikcí (tj, R = 0,5 pro binární klasifikaci) a R=(1/C) problémů klasifikace C – třída|
precision_score_macro|Přesnost je procento prvky označeny jako určité třídy, které jsou ve skutečnosti v dané třídě. Aritmetický průměr přesnosti pro každou třídu je – makro|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "makro"|
precision_score_micro|Přesnost je procento prvky označeny jako určité třídy, které jsou ve skutečnosti v dané třídě. Micro je vypočítán globálně výpočtem celkového pravdivě pozitivní a počet falešně pozitivních výsledků|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "micro"|
precision_score_weighted|Přesnost je procento prvky označeny jako určité třídy, které jsou ve skutečnosti v dané třídě. Váha je aritmetický průměr přesnosti pro každou třídu váženo podle počtu true instancí každé třídy|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "váha"|
recall_score_macro|Odvolání je procento prvky ve skutečnosti určité třídy, které jsou správně označené. Aritmetický průměr o odvolání pro každou třídu je – makro|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "makro"|
recall_score_micro|Odvolání je procento prvky ve skutečnosti určité třídy, které jsou správně označené. Micro je vypočítán globálně výpočtem celkového pravdivě pozitivní, falešně negativní|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "micro"|
recall_score_weighted|Odvolání je procento prvky ve skutečnosti určité třídy, které jsou správně označené. Váha je aritmetický průměr o odvolání pro každou třídu váženo podle počtu true instancí každé třídy|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "váha"|
weighted_accuracy|Vážený přesnost je přesnost, kde je rovna podíl true instancí v tomto příkladu true třídě váhou na každý příklad|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight je rovna poměr této třídy pro každý prvek v cílovém vektor|

### <a name="regression-and-forecasting-metrics"></a>Regrese a Prognózování metriky
Tyto metriky jsou uloženy v každé iteraci pro regresní nebo Prognózování úloh.

|Primární metriku|Popis|Výpočet|Další parametry
--|--|--|--|--|
explained_variance|Bylo vysvětleno odchylky je poměr, ke kterému matematické modelu účty pro variantu dané datové sady. Je, že procento snížení odchylku původní data, která mají odchylku chyby. Pokud chyby je 0, je roven bylo vysvětleno variance.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Žádný|
r2_score|R2 je koeficient stanovení nebo snížení procenta v porovnání s modelem směrný plán, který zobrazí průměr kvadratických chyb. Pokud chyby je 0, je roven bylo vysvětleno variance.|[Výpočet](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Žádný|
spearman_correlation|Korelace spearman je nonparametric míra monotonicity vztah mezi dvěma datovými sadami. Na rozdíl od korelace Pearson korelace Spearman nepředpokládá, že oba datové sady mají obvykle distribuovat. Stejně jako ostatní koeficienty korelace ten se pohybuje mezi -1 a + 1 hodnotou 0 zdání žádnou souvislost. Korelací mezi -1 nebo + 1 implikují přesné monotónní relace. Pozitivní korelace neznamená, že jako x zvýšení zločinců se stejně y. Negativní korelace znamenají, že jako x zvyšují v pořadí, y snižuje.|[Výpočet](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Žádný|
mean_absolute_error|To znamenat, že absolutní chyba je očekávaná hodnota absolutní hodnota rozdílu mezi cílem a do predikce.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Žádný|
normalized_mean_absolute_error|Normalizovaná střední absolutní chyba je střední absolutní chyba rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Rozdělit podle rozsahu dat|
median_absolute_error|Střední absolutní chyba je Medián všech absolutních rozdílů mezi cílem a do predikce. To je robustní odlehlé hodnoty.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Žádný|
normalized_median_absolute_error|Normalizovaná střední absolutní chyba je střední absolutní chyba rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Rozdělit podle rozsahu dat|
root_mean_squared_error|Kořenové bude kvadratická chyba odmocninu očekávané kvadratickému rozdílu mezi cílem a do predikce.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Žádný|
normalized_root_mean_squared_error|Normalizovaná kořenové bude kvadratická chyba kořenové střední kvadratické chyby rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Rozdělit podle rozsahu dat|
root_mean_squared_log_error|Kořenové znamenat protokolu kvadratických chyb je odmocninu očekávané kvadratická chyba logaritmické|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Žádný|
normalized_root_mean_squared_log_error|Chyba mean ve čtverci protokolu Noramlized kořenový se kořenový mean ve čtverci protokolu chybu rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Rozdělit podle rozsahu dat|

## <a name="explain-the-model"></a>Vysvětlují modelu

Technologie automatizovaného strojového učení, které jsou obecně dostupné, **explainability funkce modelu je stále ve verzi public preview.**

Automatizované machine learning umožňuje pochopit důležité funkce.  Během procesu trénování můžete získat význam globální funkce pro model.  Pro scénáře klasifikace můžete také získat důležité funkce na úrovni třídy.  Je nutné zadat ověření datové sady (X_valid) Chcete-li získat funkce význam.

Existují dva způsoby, jak generovat funkci význam.

*   Až se experiment dokončí, můžete použít `explain_model` metodu na kteroukoli iteraci.

    ```
    from azureml.train.automl.automlexplainer import explain_model
    
    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)
    
    #Overall feature importance
    print(overall_imp)
    print(overall_summary) 
    
    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary) 
    ```

*   Chcete-li zobrazit funkci význam pro všechny iterace, nastavte `model_explainability` příznak `True` v AutoMLConfig.  

    ```
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train, 
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    Až to bude hotové, můžete načíst význam funkce pro konkrétní iteraci retrieve_model_explanation metody.

    ```
    from azureml.train.automl.automlexplainer import retrieve_model_explanation
    
    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)
    
    #Overall feature importance
    print(overall_imp)
    print(overall_summary) 
    
    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary) 
    ```

Můžete vizualizovat graf význam funkce ve vašem pracovním prostoru na webu Azure Portal. Grafu se zobrazí také při použití poznámkového bloku Jupyter widgetu. Další informace o grafech najdete [Azure ML ukázkové poznámkové bloky článku.](samples-notebooks.md)

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![Funkce význam grafu](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>Další postup

Další informace o [jak a kde nasadit model](how-to-deploy-and-where.md).

Další informace o [trénování modelu klasifikaci pomocí služby machine learning automatizovaná](tutorial-auto-train-models.md) nebo [trénování pomocí automatizované strojového učení na vzdálený prostředek](how-to-auto-train-remote.md). 
