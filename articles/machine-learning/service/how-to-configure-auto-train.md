---
title: Vytváření automatizovaných experimentů v ML
titleSuffix: Azure Machine Learning service
description: Automatizované machine learning vybere algoritmus pro vás a generuje modelu připravené na nasazení. Další možnosti, které můžete použít ke konfiguraci automatické se strojovým učením.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 3fcc1926d580007750e7e1f5a3de06ef6578e1b5
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957458"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurace automatizovaného experimentů v ML v Pythonu

V této příručce, zjistěte, jak definovat různá nastavení konfigurace z vašeho automatizované se strojovým učením s [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Automatizované machine learning za vás vybere algoritmus a hyperparameters a generuje modelu připravené na nasazení. Existuje několik možností, které můžete použít ke konfiguraci automatické se strojovým učením.

Chcete-li zobrazit příklady automatizované experimentů machine learningu, naleznete v tématu [kurzu: Trénování modelu klasifikace automatizované machine Learning](tutorial-auto-train-models.md) nebo [trénování modelů pomocí automatizovaných strojového učení v cloudu](how-to-auto-train-remote.md).

Možnosti konfigurace je k dispozici ve službě automatizované machine learning:

* Vyberte typ testu: Klasifikace, regrese nebo předpovědi časové řady
* Zdroj dat, formát a načítat data
* Vyberte cílové výpočetní prostředky: místní nebo vzdálené
* Automatizované strojového učení nastavení testu
* Spuštění automatizované experimentu strojového učení
* Zkoumání metrik model
* Registrace a nasazení modelu

Pokud dáváte přednost žádný kód prostředí, můžete také [vaše automatizované se strojovým učením na webu Azure Portal vytvořit](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Vyberte typ testu

Než začnete experimentu, byste měli určit druh machine learning problému jsou řešení. Automatizované machine learning podporuje typy úloh klasifikace, regrese a Prognózování.

Automatizované machine learning podporuje tyto algoritmy během automatizace a ladění procesu. Jako uživatel není nutné lze určit algoritmus. Během cvičení jsou k dispozici DNN algoritmy, automatizované ML nesestaví DNN modely.

Klasifikace | Regrese | Vytváření prognóz časových řad
|-- |-- |--
[Logistické regrese](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastické Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastické Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Světlý GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Světlý GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Světlý GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Přechodu zvýšení skóre](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Přechodu zvýšení skóre](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Přechodu zvýšení skóre](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#regression)|[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#regression)
[K nejbližší okolí](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K nejbližší okolí](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K nejbližší okolí](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineární SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS laso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS laso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Vektorové podpory C klasifikace (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Pomocí stochastického sestupu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Pomocí stochastického sestupu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Náhodné doménové struktury](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Náhodné doménové struktury](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Náhodné doménové struktury](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Velmi náhodnou stromů](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Velmi náhodnou stromů](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Velmi náhodnou stromů](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Třídění DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Regresor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regresor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Lineární třídění DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regresor lineární](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regresor lineární](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Pomocí stochastického sestupu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|


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
    from sklearn.model_selection import train_test_split
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    y = df["Label"]
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

Klíč | Typ | Vzájemně se vylučuje s    | Popis
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

### <a name="load-and-prepare-data-using-data-prep-sdk"></a>Načíst a připravit data pomocí přípravy dat sady SDK
Automatizované se strojovým učením podporuje načítání dat a transformace pomocí přípravy dat sady SDK. Pomocí sady SDK poskytuje možnost

>* Načtení z mnoha typů souborů s analýzy odvozování parametrů (kódování, oddělovač, hlavičky)
>* Převod typu pomocí odvození během načítání souboru
>* Podpora připojení pro MS SQL Server a úložiště Azure Data Lake
>* Přidání sloupce pomocí výrazu
>* Dává chybějící hodnoty
>* Odvození sloupce podle příkladu
>* Filtrování
>* Vlastních transformací Pythonu

Další informace o datech přípravu sdk naleznete [postup přípravy dat pro modelování článku](how-to-load-data.md).
Tady je příklad, načítání dat pomocí sady sdk pro data přípravu.
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

### <a name="k-folds-cross-validation"></a>K přeložení křížové ověření

Použití `n_cross_validations` nastavení určuje počet křížové ověření. Trénovací datové sady se náhodně rozdělit na `n_cross_validations` složení stejnou velikost. Při každé křížového ověření round jeden složení se použije pro ověřování modelů trénovaných na zbývající složení. Tento proces se opakuje pro `n_cross_validations` zaokrouhlí dokud každý fold se jednou nepoužije jako sada ověření. Průměrné skóre napříč všemi `n_cross_validations` zaokrouhlí se ohlásí, a odpovídající modelu budou retrained na celém trénovací datové sady.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo křížové ověření (opakované náhodné dílčí vzorkování)

Použít `validation_size` určit procento trénovací datové sady, který se má použít pro ověření a potom použijte `n_cross_validations` určit počet křížové ověření. Při každé křížové ověření round podmnožinu velikost `validation_size` budou náhodně vybrány pro ověřování modelů trénovaných na zbývající data. Nakonec se stanoví skóre průměr všech `n_cross_validations` zaokrouhlí se ohlásí, a odpovídající modelu budou retrained na celém trénovací datové sady. Monte Carlo ke zjištění nepodporuje prognózy časových řad.

### <a name="custom-validation-dataset"></a>Vlastní ověření datové sady

Použijte vlastní ověřovací datové sady, pokud není přijatelná náhodného dělení, obvykle dat časových řad nebo imbalanced data. Můžete zadat vlastní ověření datové sady. Ověření datové sadě zadán namísto náhodného datové sady se vyhodnotí modelu.

## <a name="compute-to-run-experiment"></a>Výpočetní prostředky pro spuštění experimentu

Dále určete, kde bude Trénink modelu. Automatické experimentu strojového učení a trénování můžete spustit na následujících výpočetních možností:
*   Místní počítač například místní pracovní plocha nebo přenosný počítač – obecně Pokud máte malé datové sady a jsou stále ve fázi průzkumu.
*   Vzdálený počítač v cloudu – [Azure Machine Learning spravovat Compute](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) je spravovaná služba, která umožňuje trénování modelů strojového učení na clusterech virtuálních počítačů Azure.

Zobrazit [webu GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) například cílových výpočetních prostředí poznámkové bloky s místním a vzdáleným.

*   Cluster Azure Databricks ve vašem předplatném Azure. Tady – další podrobnosti můžete najít [Azure Databricks nastavení clusteru pro automatizované ML](how-to-configure-environment.md#azure-databricks)

Zobrazit [webu GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) například poznámkové bloky s Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurovat nastavení testu

Existuje několik možností, které můžete použít ke konfiguraci vašeho automatizované experimentu strojového učení. Tyto parametry jsou nastavené po vytvoření instance `AutoMLConfig` objektu. Zobrazit [AutoMLConfig třídy](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) zobrazit úplný seznam parametrů.

Možné příklady:

1.  Klasifikace experimentu s využitím AUC váha jako primární metriku s maximální dobou 12 000 sekund na iterace experimentu na konec až 50 iterací a 2 přeložení křížového ověření.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  Tady je příklad sady experiment regrese k ukončení po 100 iterací, s každou iterací trvá až 600 sekund s 5 ověření pro různé složení.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

Třemi hlavními `task` hodnoty parametrů určit seznam algoritmy, které chcete použít.  Použití `whitelist` nebo `blacklist` parametry pokročilejší úpravy iteracím dostupné algoritmy, které mají zahrnout nebo vyloučit. Seznam podporovaných modelech najdete na [SupportedAlgorithms třídy](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Primární metriku
Primární metric; jak je znázorněno výše uvedených příkladech určuje metriky, které se použijí při cvičení modelu pro optimalizaci. Primární metriky, které můžete vybrat se určuje podle typu úkolu, který zvolíte. Níže je seznam dostupných metrik.

|Klasifikace | Regrese | Vytváření prognóz časových řad
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Předzpracování dat a snadné

V každé automatizované experimentu strojového učení, vaše data jsou [automaticky škálovat a normalized](concept-automated-ml.md#preprocess) umožňující výkonné algoritmy.  Ale můžete také povolit další předzpracování/snadné, jako je například chybějící hodnoty imputace, kódování a transformace. [Další informace o tom, jaké snadné je součástí](how-to-create-portal-experiments.md#preprocess).

Chcete-li povolit tuto snadné, zadejte `"preprocess": True` pro [ `AutoMLConfig` třídy](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

### <a name="time-series-forecasting"></a>Vytváření prognóz časových řad
Pro typ Prognózování úloh čas řady je nutné definovat další parametry.
1. time_column_name – Toto je povinný parametr, který definuje název sloupce do řady obsahující datum a čas trénovací data.
1. max_horizon – Určuje dobu, kterou chcete předpovědět navýšení kapacity podle periodicitu trénovací data. Například pokud máte trénovacích dat s zrna denní dobu, můžete definovat jak daleko out ve dnech modelu pro trénování.
1. grain_column_names – ten definuje název sloupce, které obsahují data jednotlivých časových řad v trénovací data. Například pokud prognózy prodeje konkrétní značku ve storu byste definovali úložiště a značky sloupců jako sloupců intervalem.

Viz příklad těchto nastavení se používá pod, například Poznámkový blok je k dispozici [tady](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task='forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

## <a name="run-experiment"></a>Spusťte experiment

Odeslání experimentu ke spuštění a generovat model. Předání `AutoMLConfig` k `submit` metoda ke generování modelu.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Závislosti jsou nejprve nainstalována na nový počítač.  Může trvat až 10 minut, než se zobrazí výstup.
>Nastavení `show_output` k `True` výsledků ve výstupu se zobrazí se v konzole.

### <a name="exit-criteria"></a>Výstupní kritéria
Existuje několik možností můžete definovat na dokončení experimentu.
1. Žádná kritéria – Pokud nedefinujete některý ukončete parametry testu bude pokračovat, dokud se žádné další krok se provádí na primární metriku.
1. Počet iterací - definujete počet iterací pro experiment ke spuštění. Můžete si volitelné přidat iteration_timeout_minutes k definování časového limitu během několika minut za každé iterace.
1. Ukončit po dobu - pomocí experiment_timeout_minutes v nastavení, které můžete definovat dobu v minutách by experiment pokračovat v běhu.
1. Ukončete po skóre se dosáhlo - pomocí experiment_exit_score, které můžete provést experiment po skóre na základě primární metriky se dosáhlo.

### <a name="explore-model-metrics"></a>Zkoumání metrik model

Ve widgetu nebo vložené můžete zobrazit výsledky školení, pokud jste v poznámkovém bloku. Zobrazit [sledovat a posuzovat modely](how-to-track-experiments.md#view-run-details) další podrobnosti.

## <a name="understand-automated-ml-models"></a>Vysvětlení automatizované modelů ML

Všechny modely vytvořené pomocí automatizovaných ML zahrnuje následující kroky:
+ Automatické vytváření funkcí (Pokud se předběžné zpracování = True)
+ Škálování/normalizace a algoritmus hypermeter hodnotami

Usnadňujeme pro získání těchto informací z fitted_model výstup z automatizovaných ML transparentní.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Vytváření automatizovaných funkcí

Zobrazit seznam předběžného zpracování a [automatizované vytváření funkcí](concept-automated-ml.md#preprocess) , který se stane, když předzpracování = True.

Podívejte se například:
+ Existují 4 vstupní funkce: (Číselné) B (číselné), (číselné) C, D (DateTime)
+ Číselné funkce C je zahozena, protože se jedná o sloupec ID se všemi hodnotami jedinečnými
+ Chybí některé hodnoty číselné funkce A a B a proto jsou uložené ve střední hodnotu
+ Funkce DateTime D je natrénuje do 11 různých funkcí inženýrství

Použijte tyto 2 rozhraní API v prvním kroku vybavené modelu lépe porozumět.  Zobrazit [tento poznámkový blok ukázka](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ Rozhraní API 1: `get_engineered_feature_names()` vrátí seznam názvů inženýrství funkcí.

  Použití:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Tento seznam obsahuje všechny názvy inženýrství funkce.

  >[!Note]
  >Použít "timeseriestransformer" pro úkol = Prognózování, použijte jiný "datatransformer' pro"regrese"nebo"klasifikaci"úloh.

+ Rozhraní API 2: `get_featurization_summary()` Vrátí souhrn pro všechny vstupní funkce snadné.

  Použití:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Použít "timeseriestransformer" pro úkol = Prognózování, použijte jiný "datatransformer' pro"regrese"nebo"klasifikaci"úloh.

  Výstup:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   Kde:

   |Výstup|Definice|
   |----|--------|
   |RawFeatureName|Název vstupu funkce/sloupce z datové sady k dispozici.|
   |TypeDetected|Zjištěné datovým typem vstupu funkce.|
   |Vyřadit|Označuje, zda vstupní funkce byla zahozena, nebo použít.|
   |EngineeringFeatureCount|Mnoho funkcí, které jsou generovány pomocí transformace engineering automatická funkce.|
   |Transformace|Seznam transformací použít k zadání funkce, které chcete generovat inženýrství funkce.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Škálování/normalizace a algoritmus hypermeter hodnotami:

Chcete-li pochopit, jaké hodnoty škálování/normalizace a algoritmus/hyperparameter pro kanál, použijte fitted_model.steps. [Další informace o škálování/normalizace](concept-automated-ml.md#preprocess). Tady je ukázkový výstup:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Další podrobnosti získáte pomocí této funkce pomocné rutiny ukazuje [tento poznámkový blok ukázka](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

```python
from pprint import pprint
def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(fitted_model)
```

Zde je ukázkový výstup pro kanálu pomocí konkrétní algoritmus (LogisticRegression s RobustScalar, v tomto případě).

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

<a name="explain"></a>

## <a name="explain-the-model-interpretability"></a>Vysvětlují modelu (interpretability)

Automatizované machine learning umožňuje pochopit důležité funkce.  Během procesu trénování můžete získat význam globální funkce pro model.  Pro scénáře klasifikace můžete také získat důležité funkce na úrovni třídy.  Je nutné zadat ověření datové sady (X_valid) Chcete-li získat funkce význam.

Existují dva způsoby, jak generovat funkci význam.

*   Až se experiment dokončí, můžete použít `explain_model` metodu na kteroukoli iteraci.

    ```python
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

    ```python
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

    ```python
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

Můžete vizualizovat graf význam funkce ve vašem pracovním prostoru na webu Azure Portal. Grafu se zobrazí také při použití poznámkového bloku Jupyter widgetu. Další informace o grafech najdete [článku poznámkových bloků ukázka Azure Machine Learning service.](samples-notebooks.md)

```Python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![Funkce význam grafu](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>Další postup

Další informace o [jak a kde nasadit model](how-to-deploy-and-where.md).

Další informace o [jak vyškolíme model pomocí automatizovaných strojového učení regrese](tutorial-auto-train-models.md) nebo [trénování pomocí automatizované strojového učení na vzdálený prostředek](how-to-auto-train-remote.md).
