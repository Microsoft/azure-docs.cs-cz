---
title: Vytváření automatizovaných experimentů ML
titleSuffix: Azure Machine Learning
description: Automatizované machine learning vybere algoritmus pro vás a generuje modelu připravené na nasazení. Další možnosti, které můžete použít ke konfiguraci automatické se strojovým učením.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 4d4a3eae9ea3931ceb720785bbf458f54689be6e
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213518"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurace automatizovaných experimentů ML v Pythonu

V této příručce se dozvíte, jak definovat různé konfigurační nastavení pro automatizované experimenty strojového učení pomocí [sady Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Automatizované machine learning za vás vybere algoritmus a hyperparameters a generuje modelu připravené na nasazení. Existuje několik možností, které můžete použít ke konfiguraci automatické se strojovým učením.

Příklady automatických experimentů strojového učení najdete v [kurzu: Školení modelu klasifikace pomocí automatizovaného strojového](tutorial-auto-train-models.md) učení nebo [výukových modelů pomocí automatizovaného strojového učení v cloudu](how-to-auto-train-remote.md).

Možnosti konfigurace je k dispozici ve službě automatizované machine learning:

* Vyberte typ experimentu: Klasifikace, regrese nebo prognózování časových řad
* Zdroj dat, formát a načítat data
* Vyberte cílové výpočetní prostředky: místní nebo vzdálené
* Automatizované strojového učení nastavení testu
* Spuštění automatizované experimentu strojového učení
* Zkoumání metrik model
* Registrace a nasazení modelu

Pokud dáváte přednost žádnému způsobu použití kódu, můžete také [vytvářet automatizované experimenty strojového učení v Azure Portal](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Vyberte typ testu

Než začnete experimentu, byste měli určit druh machine learning problému jsou řešení. Automatizované machine learning podporuje typy úloh klasifikace, regrese a Prognózování.

Automatizované machine learning podporuje tyto algoritmy během automatizace a ladění procesu. Jako uživatel není nutné lze určit algoritmus.

Klasifikace | Regrese | Prognózování časových řad
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
[Třídění DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN lineární třídění](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineární regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineární regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Pomocí stochastického sestupu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

`task` Použijte parametr`AutoMLConfig` v konstruktoru k určení typu experimentu.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>Zdroj dat a formát
Automatizované machine learning podporuje data, která se nachází v místním počítači nebo v cloudu, jako je Azure Blob Storage. Data lze načíst do scikit-informace podporovaných datových formátů. Můžete číst data do:
* Pole Numpy X (funkce) a y (Cílová proměnná nebo také popis)
* Pandas dataframe

>[!Important]
> Požadavky na školicí data:
>* Data musí být v tabulkovém formátu.
>* Hodnota, kterou chcete odhadnout (cílový sloupec), musí být k dispozici v datech.

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

Pro vzdálená spuštění je potřeba, aby data byla přístupná ze vzdálených výpočtů. To se dá udělat tak, že se data nahrávají do úložiště dat.

Tady je příklad použití `datastore`:

```python
    import pandas as pd
    from sklearn import datasets

    data_train = datasets.load_digits()

    pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
    pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)
```

### <a name="define-dprep-references"></a>Definovat odkazy na dprep

Definujte odkaz X a y jako dprep, který se bude předávat do automatizovaného `AutoMLConfig` objektu Machine Learning, který bude vypadat přibližně takto:

```python

    X = dprep.auto_read_file(path=ds.path('digitsdata/X_train.csv'))
    y = dprep.auto_read_file(path=ds.path('digitsdata/y_train.csv'))


    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 path = project_folder,
                                 run_configuration=conda_run_config,
                                 X = X,
                                 y = y,
                                 **automl_settings
                                )
```

## <a name="train-and-validation-data"></a>Trénování a ověřování dat

Můžete určit samostatný vlak a sadu ověřování přímo v `AutoMLConfig` metodě.

### <a name="k-folds-cross-validation"></a>K přeložení křížové ověření

Použití `n_cross_validations` nastavení určuje počet křížové ověření. Trénovací datové sady se náhodně rozdělit na `n_cross_validations` složení stejnou velikost. Při každé křížového ověření round jeden složení se použije pro ověřování modelů trénovaných na zbývající složení. Tento proces se opakuje pro `n_cross_validations` zaokrouhlí dokud každý fold se jednou nepoužije jako sada ověření. Průměrné skóre napříč všemi `n_cross_validations` zaokrouhlí se ohlásí, a odpovídající modelu budou retrained na celém trénovací datové sady.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Křížové ověření Monte Carlo (opakované náhodné dílčí vzorkování)

Použít `validation_size` určit procento trénovací datové sady, který se má použít pro ověření a potom použijte `n_cross_validations` určit počet křížové ověření. Při každé křížové ověření round podmnožinu velikost `validation_size` budou náhodně vybrány pro ověřování modelů trénovaných na zbývající data. Nakonec se stanoví skóre průměr všech `n_cross_validations` zaokrouhlí se ohlásí, a odpovídající modelu budou retrained na celém trénovací datové sady. Monte Carlo se pro prognózování časových řad nepodporuje.

### <a name="custom-validation-dataset"></a>Vlastní ověření datové sady

Použijte vlastní ověřovací datovou sadu, pokud není přijatelné náhodné rozdělení, obvykle data časových řad nebo nevyvážená data. Můžete zadat vlastní ověření datové sady. Ověření datové sadě zadán namísto náhodného datové sady se vyhodnotí modelu.

## <a name="compute-to-run-experiment"></a>Výpočetní prostředky pro spuštění experimentu

Dále určete, kde bude Trénink modelu. Automatické experimentu strojového učení a trénování můžete spustit na následujících výpočetních možností:
*   Místní počítač například místní pracovní plocha nebo přenosný počítač – obecně Pokud máte malé datové sady a jsou stále ve fázi průzkumu.
*   Vzdálený počítač v cloudu – [Azure Machine Learning spravovat Compute](concept-compute-target.md#amlcompute) je spravovaná služba, která umožňuje trénování modelů strojového učení na clusterech virtuálních počítačů Azure.

Příklady poznámkových bloků s místními a vzdálenými výpočetními cíli najdete na [webu GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) .

*   Cluster Azure Databricks ve vašem předplatném Azure. Další podrobnosti najdete tady: [nastavení Azure Databricks clusteru pro automatizované ml](how-to-configure-environment.md#azure-databricks) .

Příklady poznámkových bloků s Azure Databricks najdete na [webu GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) .

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurovat nastavení testu

Existuje několik možností, které můžete použít ke konfiguraci vašeho automatizované experimentu strojového učení. Tyto parametry jsou nastavené po vytvoření instance `AutoMLConfig` objektu. Úplný seznam parametrů naleznete v tématu [Třída AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) .

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

Tři různé `task` hodnoty parametrů určují seznam modelů, které se mají použít.  Pomocí parametrů `blacklist` nebo můžete dále upravit iterace s dostupnými modely, které chcete zahrnout nebo vyloučit. `whitelist` Seznam podporovaných modelů lze nalézt ve [třídě SupportedModels](https://docs.microsoft.com/en-us/python/api/azureml-train-automl/azureml.train.automl.constants.supportedmodels?view=azure-ml-py).

### <a name="primary-metric"></a>Primární metriku
Primární metrika; Jak je znázorněno v předchozích příkladech Určuje metriku, která se má použít během školení modelu pro optimalizaci. Primární metrika, kterou můžete vybrat, je určená typem úlohy, kterou zvolíte. Níže je uveden seznam dostupných metrik.

Přečtěte si o konkrétních definicích těchto informací v seznámení s [automatizovanými výsledky strojového učení](how-to-understand-automated-ml.md).

|Klasifikace | Regrese | Prognózování časových řad
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Předzpracování dat & featurization

V každém automatizovaném experimentu Machine Learning se vaše data [automaticky škálují a normalizují](concept-automated-ml.md#preprocess) , aby se algoritmy lépe prováděly.  Můžete ale také povolit další předzpracování/featurization, například chybějící hodnoty imputac, Encoding a transformes. [Přečtěte si další informace o tom, co je zahrnuté featurization](how-to-create-portal-experiments.md#preprocess).

Chcete-li povolit tuto featurization `"preprocess": True` , zadejte [ `AutoMLConfig` pro třídu](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

> [!NOTE]
> Automatické kroky před zpracováním strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselnou atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky před zpracováním během školení.

### <a name="time-series-forecasting"></a>Prognózování časových řad
Pro typ úkolu prognózy časových řad máte k definování další parametry.
1. time_column_name – tento parametr je povinný, který definuje název sloupce ve školicích datech obsahujících řadu data a času.
1. max_horizon – definuje dobu, kterou chcete předpovědět na základě periodicity školicích dat. Například pokud máte školicí data s denním intervalem, můžete definovat, jak dlouho chcete, aby model mohl vyškolit.
1. grain_column_names – definuje názvy sloupců, které obsahují data jednotlivých časových řad ve vašich školicích datech. Pokud například vytváříte předpověď prodeje konkrétní značky podle obchodu, definovali byste sloupce úložiště a značky jako sloupce zrnitosti.

Podívejte se na příklad níže uvedených nastavení, příklad poznámkového bloku je k dispozici [zde](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

### <a name="ensemble"></a>Konfigurace kompletu

Modely kompletu jsou ve výchozím nastavení povolené a zobrazují se jako poslední iterace v automatizovaném spuštění strojového učení. Aktuálně podporované metody kompletování jsou hlasovací a Stacked. Hlasování je implementováno jako měkký hlas pomocí vážených průměrů a implementace skládání používá 2 implementaci vrstvy, kde první vrstva má stejné modely jako hlasovacího kompletu, a druhý model vrstvy se používá k nalezení optimální kombinace modely z první vrstvy. Pokud používáte modely ONNX **nebo** Pokud máte povolenou možnost vysvětlování modelu, bude zablokování zakázané a budou využívány jenom hlasovací prvky.

Existuje více výchozích argumentů, které lze zadat jako `kwargs` `AutoMLConfig` v objektu pro změnu výchozího chování kompletování zásobníku.

* `stack_meta_learner_type`: meta-učí se model vyškolený na výstupu jednotlivých heterogenní modelů. Výchozí metaznačky jsou `LogisticRegression` pro úlohy klasifikace (nebo `LogisticRegressionCV` Pokud je povolené křížové ověřování) a `ElasticNet` pro úlohy regrese/předpovědi (nebo `ElasticNetCV` Pokud je povolené křížové ověřování). Tento parametr může být jeden z `LogisticRegression`následujících řetězců:, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor`, nebo `LinearRegression`.
* `stack_meta_learner_train_percentage`: určuje podíl sady školení (při výběru typu výuky a ověření školení), který má být rezervován pro školení meta-učí. Výchozí hodnota je `0.2`.
* `stack_meta_learner_kwargs`: volitelné parametry, které mají být předána inicializátoru meta-učí. Tyto parametry a typy parametrů zrcadlí hodnoty z odpovídajícího konstruktoru modelu a jsou předávány do konstruktoru modelu.

Následující kód ukazuje příklad určení chování vlastního kompletování v `AutoMLConfig` objektu.

```python
ensemble_settings = {
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        **ensemble_settings
        )
```

Školení kompletu je ve výchozím nastavení povolené, ale je možné ho zakázat pomocí `enable_voting_ensemble` logických parametrů a `enable_stack_ensemble` .

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Spusťte experiment

Pro automatizované ml vytvoříte `Experiment` objekt, který je pojmenovaný objekt `Workspace` v, který se používá ke spouštění experimentů.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Odeslání experimentu ke spuštění a generovat model. Předání `AutoMLConfig` k `submit` metoda ke generování modelu.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Závislosti jsou nejprve nainstalována na nový počítač.  Může trvat až 10 minut, než se zobrazí výstup.
>Nastavení `show_output` k `True` výsledků ve výstupu se zobrazí se v konzole.

### <a name="exit-criteria"></a>Výstupní kritéria
K dokončení experimentu můžete definovat několik možností.
1. Žádná kritéria – Pokud nedefinujete žádné parametry ukončení, bude experiment pokračovat, dokud nebude u primární metriky proveden žádný další postup.
1. Počet iterací – můžete definovat počet iterací pro spuštění experimentu. Volitelně můžete přidat iteration_timeout_minutes a definovat časový limit v minutách pro každou iteraci.
1. Ukončit po uplynutí doby, kdy se v nastavení použije experiment_timeout_minutes, můžete určit, jak dlouho má experiment pokračovat v běhu.
1. Ukončit po dosažení skóre – pomocí experiment_exit_score se můžete rozhodnout pro dokončení experimentu po dosažení skóre na základě vaší primární metriky.

### <a name="explore-model-metrics"></a>Zkoumání metrik model

Pokud se nacházíte v poznámkovém bloku, můžete zobrazit výsledky školení v widgetu nebo v případě potřeby. Zobrazit [sledovat a posuzovat modely](how-to-track-experiments.md#view-run-details) další podrobnosti.

## <a name="understand-automated-ml-models"></a>Principy automatizovaných modelů ML

Libovolný model vytvořený pomocí automatizovaného ML obsahuje následující kroky:
+ Automatizovaná technologie funkcí (Pokud předzpracování = true)
+ Škálování/normalizace a algoritmy s hodnotami měřiče

Pro získání těchto informací z výstupu fitted_model z automatizovaného ML je transparentní.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Automatizovaná technologie funkcí

Podívejte se na seznam předzpracovaných a [automatizovaných funkcí](concept-automated-ml.md#preprocess) , které se objeví při předběžném zpracování = true.

Vezměte v úvahu tento příklad:
+ K dispozici jsou 4 vstupní funkce: A (číselná), B (číselná), C (číselná), D (DateTime)
+ Číselná funkce C je vyřazena, protože se jedná o sloupec ID se všemi jedinečnými hodnotami.
+ Numerické funkce a a B chybějící hodnoty, a proto jsou ve významu imputované.
+ Funkce DateTime D je natrénuje na 11 různých funkcí navržených pro analýzu.

Tato 2 rozhraní API použijte v prvním kroku namontovaného modelu, abyste lépe pochopili víc.  Podívejte se na [Tento ukázkový Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ Rozhraní API 1 `get_engineered_feature_names()` : vrátí seznam navržených názvů funkcí.

  Použití:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Tento seznam obsahuje všechny inženýrské názvy funkcí.

  >[!Note]
  >Použijte ' timeseriestransformer ' pro Task = ' Forecasting ', jinak použijte ' datatransformer ' pro ' regresi ' nebo ' Classification '.

+ Rozhraní API 2 `get_featurization_summary()` : Vrátí souhrn featurization pro všechny vstupní funkce.

  Použití:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Použijte ' timeseriestransformer ' pro Task = ' Forecasting ', jinak použijte ' datatransformer ' pro ' regresi ' nebo ' Classification '.

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
   |RawFeatureName|Název funkce nebo sloupce vstupu ze zadané datové sady.|
   |TypeDetected|Byl zjištěn datový typ vstupní funkce.|
   |Odpojení|Určuje, zda byla vstupní funkce vyřazena nebo použita.|
   |EngineeringFeatureCount|Počet funkcí generovaných pomocí transformací technologie automatizovaného zpracování funkcí|
   |Transformace|Seznam transformací použitých u vstupních funkcí k vygenerování navržených funkcí|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Škálování/normalizace a algoritmy s hodnotami měřiče:

Chcete-li pochopit hodnoty škálování/normalizace a algoritmů a parametrů pro kanál, použijte fitted_model. Steps. [Další informace o škálování/normalizaci](concept-automated-ml.md#preprocess). Tady je ukázkový výstup:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Další podrobnosti získáte pomocí této pomocné funkce uvedené v [tomto ukázkovém poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(fitted_model)
```

Následuje příklad výstupu kanálu pomocí konkrétního algoritmu (LogisticRegression s RobustScalar, v tomto případě).

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

## <a name="explain-the-model-interpretability"></a>Vysvětlení modelu (výklad)

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

Pokud chcete zobrazit důležitost funkcí pomocí objektu Run, zobrazte si adresu URL:

```
automl_run.get_portal_url()
```

Graf důležitost funkcí v pracovním prostoru můžete vizualizovat v Azure Portal nebo na [cílové stránce pracovního prostoru (Preview)](https://ml.azure.com). Graf je také zobrazen při použití `RunDetails` [widgetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) v poznámkovém bloku. Další informace o grafech najdete v tématu popisujícím [automatizované výsledky strojového učení](how-to-understand-automated-ml.md).

```Python
from azureml.widgets import RunDetails
RunDetails(automl_run).show()
```

![Funkce význam grafu](./media/how-to-configure-auto-train/feature-importance.png)

Další informace o tom, jak je možné povolit vysvětlení modelu a důležitost funkcí v jiných oblastech sady SDK mimo automatizované strojové učení, najdete v článku [konceptu](machine-learning-interpretability-explainability.md) o výkladu.

## <a name="next-steps"></a>Další kroky

Další informace o [jak a kde nasadit model](how-to-deploy-and-where.md).

Přečtěte si další informace o [tom, jak vytvořit regresní model pomocí automatizovaného strojového učení](tutorial-auto-train-models.md) nebo [jak pomocí automatizovaného strojového učení na vzdáleném prostředku vyškolit](how-to-auto-train-remote.md).
