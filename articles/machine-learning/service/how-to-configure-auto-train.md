---
title: Konfigurovat vaše automatizované experimentu strojového učení – Azure Machine Learning
description: Automatizované machine learning vybere algoritmus pro vás a generuje modelu připravené na nasazení. Další možnosti, které můžete použít ke konfiguraci automatické se strojovým učením.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 33126c094a55bc57edd49a54fbc4f5acd7401998
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079000"
---
# <a name="configure-your-automated-machine-learning-experiment"></a>Konfigurovat vaše automatizované experimentu strojového učení

Automatizované machine learning (automatizované ML) vybere algoritmus a hyperparameters za vás a generuje modelu připravené na nasazení. Model si můžete stáhnout i dál přizpůsobit. Existuje několik možností, které můžete použít ke konfiguraci automatické ML experimentů. V této příručce se dozvíte, jak definovat různá nastavení konfigurace.

Chcete-li zobrazit příklady automatizované ML, naleznete v tématu [kurz: Vyškolíme model klasifikace pomocí automatizovaných strojového učení](tutorial-auto-train-models.md) nebo [trénování modelů pomocí automatizovaných strojového učení v cloudu](how-to-auto-train-remote.md).

Možnosti konfigurace je k dispozici ve službě automatizované machine learning:

* Vyberte typ testu, například klasifikace, regrese 
* Zdroj dat, formát a načítat data
* Vyberte vaše cílové výpočetní prostředí (místní nebo vzdálené)
* Automatické nastavení experimentu ML
* Spustit automatizované experimentu ML
* Zkoumání metrik model
* Registrace a nasazení modelu

## <a name="select-your-experiment-type"></a>Vyberte typ testu
Než začnete experimentu, byste měli určit druh machine learning problému jsou řešení. Automatizované ML podporuje dvě kategorie učení: klasifikačních a regresních. Automatizované ML podporuje tyto algoritmy během automatizace a ladění procesu. Jako uživatel není nutné lze určit algoritmus.
Klasifikace | Regrese
--|--
sklearn.linear_model. LogisticRegression | sklearn.linear_model. ElasticNet
sklearn.linear_model. SGDClassifier  | sklearn.ensemble.GradientBoostingRegressor
sklearn.naive_bayes. BernoulliNB | sklearn.tree.DecisionTreeRegressor
sklearn.naive_bayes. MultinomialNB | sklearn.neighbors.KNeighborsRegressor
sklearn.svm.SVC | sklearn.linear_model. LassoLars
sklearn.svm.LinearSVC | sklearn.linear_model. SGDRegressor
sklearn.calibration.CalibratedClassifierCV |    sklearn.ensemble.RandomForestRegressor
sklearn.neighbors.KNeighborsClassifier |    sklearn.ensemble.ExtraTreesRegressor
sklearn.tree.DecisionTreeClassifier |   lightgbm. LGBMRegressor
sklearn.ensemble.RandomForestClassifier |
sklearn.ensemble.ExtraTreesClassifier   |
sklearn.ensemble.GradientBoostingClassifier |
lightgbm. LGBMClassifier |


## <a name="data-source-and-format"></a>Zdroj dat a formát
Automatizované ML podporuje data, která se nachází v místním počítači nebo v cloudu v Azure Blob Storage. Data lze načíst do scikit-informace podporovaných datových formátů. Může číst data do polí Numpy 1) X (funkce) a y (Cílová proměnná nebo také popis) nebo 2) Pandas dataframe. 

Příklady:

1.  Numpy pole

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data 
    y_digits = digits.target
    ```

2.  Pandas dataframe

    ```python
    Import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Načíst data pro spouštění experimentů na vzdálený výpočetní

Pokud používáte vzdálený výpočetní ke spuštění experimentu, načítání dat musí být zabalené v skript pythonu samostatné `get_data()`. Tento skript je spuštěn na vzdálený výpočetní, ve kterém se spouští automatizované experimentu ML. `get_data` eliminuje nutnost načíst data při přenosu pro každou iteraci. Bez `get_data`, experimentu selže, když jste spustili vzdálený výpočetní prostředky.

Tady je příklad `get_data`:

```python
%%writefile $project_folder/get_data.py 
import pandas as pd 
from sklearn.model_selection 
import train_test_split 
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

`get_data` skript může vrátit následující:
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

## <a name="train-and-validation-data"></a>Trénování a ověřování dat

Můžete zadat samostatné trénování a ověření nastavení prostřednictvím get_data() nebo přímo v `AutoMLConfig` metody.

## <a name="cross-validation-split-options"></a>Možností rozdělení, křížového ověření

### <a name="k-folds-cross-validation"></a>K přeložení křížové ověření

Použití `n_cross_validations` nastavení určuje počet křížové ověření. Trénovací datové sady se náhodně rozdělit na `n_cross_validations` složení stejnou velikost. Při každé křížového ověření round jeden složení se použije pro ověřování modelů trénovaných na zbývající složení. Tento proces se opakuje pro `n_cross_validations` zaokrouhlí dokud každý fold se jednou nepoužije jako sada ověření. Nakonec se stanoví skóre průměr všech `n_cross_validations` zaokrouhlí se ohlásí, a odpovídající modelu budou retrained na celém trénovací datové sady.

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Monte Carlo ke zjištění křížové ověření (označovaný také jako Opakuje se náhodný dílčí vzorkování)

Použít `validation_size` určit procento trénovací datové sady, který se má použít pro ověření a potom použijte `n_cross_validations` určit počet křížové ověření. Při každé křížové ověření round podmnožinu velikost `validation_size` budou náhodně vybrány pro ověřování modelů trénovaných na zbývající data. Nakonec se stanoví skóre průměr všech `n_cross_validations` zaokrouhlí se ohlásí, a odpovídající modelu budou retrained na celém trénovací datové sady.

### <a name="custom-validation-dataset"></a>Vlastní ověření datové sady

Použít datovou sadu vlastní ověřování, pokud náhodného dělení není přijatelná (obvykle časové řady dat nebo imbalanced dat). Díky tomu můžete určit vlastní ověření datové sady. Ověření datové sadě zadán namísto náhodného datové sady se vyhodnotí modelu.

## <a name="compute-to-run-experiment"></a>Výpočetní prostředky pro spuštění experimentu

Dále určete, kde bude Trénink modelu. Automatizované výukového experimentu ML běží na cílové výpočetní prostředí, které jste sami vlastnili a spravovali. 

Výpočetní možnosti podporované jsou následující:
1.  Místní počítač například místní pracovní plocha nebo přenosný počítač – obecně Pokud máte malé datové sady a jsou stále ve fázi průzkumu.
2.  Vzdálený počítač v cloudu – [virtuální počítač Azure datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) s Linuxem – máte velkou datovou sadu a chcete vertikálně navýšit kapacitu na velké počítač, který je k dispozici v cloudu Azure. 
3.  Cluster Azure Batch AI – A spravovat cluster, který můžete nastavit pro horizontální navýšení kapacity a automatizovaných ML iterací běží paralelně. 


## <a name="configure-your-experiment-settings"></a>Konfigurovat nastavení testu

Existuje několik knoflíky, které můžete použít ke konfiguraci automatické experimentu ML. Tyto parametry jsou nastavené po vytvoření instance `AutoMLConfig` objektu.

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
`task`  |Zadejte typ problému machine learning. Povolené hodnoty jsou <li>Klasifikace</li><li>Regrese</li>    | Žádný |
`primary_metric` |Metrika, kterou chcete optimalizovat při vytváření modelu. Například pokud zadáte jako primary_metric přesnost, automatizované ML vypadá najít model s nejvyšší přesností. Můžete zadat pouze jeden primary_metric na experiment. Povolené hodnoty jsou <br/>**Klasifikace**:<br/><li> accuracy  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**Regrese**: <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score    </li> | Pro klasifikaci: přesnost <br/>Pro regresní: spearman_correlation <br/> |
`exit_score` |  Nastavení cílové hodnoty pro váš primary_metric. Po nalezení modelu, který splňuje cíl primary_metric, automatizované ML zastaví, iterace a ukončí experimentu. Pokud tato hodnota není nastavená (výchozí), bude nadále spuštěna počet iterací podle iterace experimentu ML automatizované. Přebírá hodnotu double. Pokud cíl nikdy dosáhne, bude pokračovat automatizované ML, dokud nebude dosaženo počtu iterací podle iterace.|   Žádný
`iterations` |Maximální počet průchodu cyklem. Každá iterace je rovna trénovací úlohu, která vede kanálu. Kanál je předzpracování dat a modelu. Chcete-li získat model vysoce kvalitní použít 250 nebo více | 100
`Concurrent_iterations`|    Maximální počet opakování běžet paralelně. Toto nastavení funguje jenom pro vzdálené výpočetní prostředky.|    1
`max_cores_per_iteration`   | Určuje, kolik jader na cílové výpočetní prostředí se použije k natrénování jeden kanál. Pokud tento algoritmus mohou využívat více jader, to zvyšuje výkon na počítači s více jádry. Můžete ho nastavit na hodnotu -1 použití všech dostupných jader v počítači.|  1
`max_time_sec` |    Omezuje množství času (v sekundách), trvá konkrétní iteraci. Pokud iterace překročí zadanou hodnotu, bude zrušen danou iteraci. Pokud není nastavena, pak iterace běží nepřetržitě až do dokončení. |   Žádný
`n_cross_validations`   |Počet rozdělení křížových ověření| Žádný
`validation_size`   |Velikost ověření nastavit jako procento všech ukázka školení.|  Žádný
`preprocess` | True nebo False <br/>Hodnota TRUE povolí experimentovat provádět předběžného zpracování na vstupu. Tady je podmnožinou předběžného zpracování<li>Chybějící Data: Uplatňuje chybí data číselná s průměrem, Text se většina occurance </li><li>Hodnoty zařazené do kategorií: Pokud datový typ je číselné a počet jedinečných hodnot je méně než 5 procentech, převede do jedné hot kódování </li><li>Atd. pro úplný seznam kontrolu [úložiště GitHub](https://aka.ms/aml-notebooks)</li><br/>Poznámka: Pokud je zhuštěný dat nelze použít předzpracování = true |  False | 
`blacklist_algos`   | Automatizované experimentu ML má mnoho různých algoritmů, které se pokusí. Konfigurujte automatické ML vyloučení určitých algoritmů z experimentu. Je užitečné, pokud jste si vědomi, že algoritmy nefungují dobře u datové sady. S výjimkou algoritmů můžete ušetřit vám výpočetní prostředky a školení čas.<br/>Povolené hodnoty pro klasifikaci<br/><li>Logistické regrese</li><li>SGD třídění</li><li>MultinomialNB</li><li>BernoulliNB</li><li>SVM</li><li>LinearSVM</li><li>kNN</li><li>DT</li><li>RF</li><li>Navíc stromů</li><li>přechodu zvýšení skóre</li><li>lgbm_classifier</li><br/>Povolené hodnoty pro regresní<br/><li>Elastic net</li><li>Regresor přechodu zvýšení skóre</li><li>Regresor DT</li><li>regresor kNN</li><li>Laso lars</li><li>Regresor SGD</li><li>Regresor RF</li><li>regresor navíc stromů</li>|   Žádný
`verbosity` |Určuje úroveň protokolování s informacemi, které se nejvíce podrobné a kritické přičemž nejmenší.<br/>Povolené hodnoty jsou:<br/><li>logging.INFO</li><li>protokolování. UPOZORNĚNÍ</li><li>protokolování. CHYBA</li><li>protokolování. KRITICKÁ</li>  | logging.INFO</li> 
`X` | Všechny funkce k trénování s |  Žádný
`y` |   Popisek dat pro trénování s. Pro klasifikaci by měl být pole celých čísel.|  Žádný
`X_valid`|_Volitelné_ všechny funkce má provést ověření. Pokud není zadán, X je rozdělená mezi trénování a ověření |   Žádný
`y_valid`   |_Volitelné_ popisku dat má provést ověření. Pokud není zadán, y je rozdělená mezi trénování a ověření    | Žádný
`sample_weight` |   _Volitelné_ váženou hodnotu pro každý vzorek. Používá se, když chcete přiřadit různé váhy na základě datových bodů |   Žádný
`sample_weight_valid`   |   _Volitelné_ váženou hodnotu pro každý vzorek ověření. Pokud není zadán, sample_weight je rozdělená mezi trénování a ověření   | Žádný
`run_configuration` |   RunConfiguration objektu.  Používá pro vzdálené spuštění. |Žádný
`data_script`  |    Cesta k souboru, který obsahuje metodu get_data.  Vyžaduje se pro vzdálené spuštění.   |Žádný


## <a name="run-experiment"></a>Spusťte experiment

Dále jsme můžete spustit experiment ke spuštění a generovat model pro nás. Předání `AutoMLConfig` k `submit` metoda ke generování modelu.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Závislosti se nejprve instalují na nový DSVM.  Může trvat až 10 minut, než se zobrazí výstup.
>Nastavení `show_output` True výsledky ve výstupu se zobrazí se v konzole.


## <a name="explore-model-metrics"></a>Zkoumání metrik model
Ve widgetu nebo vložené můžete zobrazit výsledky, pokud jste v poznámkovém bloku. Zobrazte podrobnosti "Sledovat a vyhodnocení modelů". (ujistěte se, že obsah AML obsahuje důležité informace pro automatizované ML)

Tyto metriky jsou uloženy v každé iteraci
* AUC_macro
* AUC_micro
* AUC_weighted
* accuracy
* average_precision_score_macro
* average_precision_score_micro
* average_precision_score_weighted
* balanced_accuracy
* f1_score_macro
* f1_score_micro
* f1_score_weighted
* log_loss
* norm_macro_recall
* precision_score_macro
* precision_score_micro
* precision_score_weighted
* recall_score_macro
* recall_score_micro
* recall_score_weighted
* weighted_accuracy

## <a name="next-steps"></a>Další postup

Další informace o [jak a kde nasadit model](how-to-deploy-and-where.md).

Další informace o [jak vyškolíme model klasifikace pomocí automatizované ML](tutorial-auto-train-models.md) nebo [trénování pomocí automatizované ML vzdáleného prostředku](how-to-auto-train-remote.md). 