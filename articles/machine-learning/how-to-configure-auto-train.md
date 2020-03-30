---
title: Vytváření automatizovaných experimentů ML
titleSuffix: Azure Machine Learning
description: Automatizované strojové učení vybere algoritmus pro vás a generuje model připravený k nasazení. Seznamte se s možnostmi, které můžete použít ke konfiguraci automatizovaných experimentů strojového učení.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 4cf940e38a84ea2eeb1896c8f7c628c8d5734374
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247125"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurace experimentů automatizovaného strojového učení v Pythonu
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V této příručce se dozvíte, jak definovat různá nastavení konfigurace vašich experimentů automatizovaného strojového učení pomocí sady [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Automatizované strojové učení vybere algoritmus a hyperparametry pro vás a generuje model připravený k nasazení. Existuje několik možností, které můžete použít ke konfiguraci automatizovaných experimentů strojového učení.

Příklady automatizovaných experimentů strojového učení najdete v [tématu Výuka: Trénování klasifikačního modelu pomocí automatizovaného strojového učení](tutorial-auto-train-models.md) nebo [Trénování modelů s automatizovaným strojovým učením v cloudu](how-to-auto-train-remote.md).

Možnosti konfigurace dostupné v automatizovaném strojovém učení:

* Vyberte typ experimentu: Klasifikace, Regrese nebo Prognóza časových řad
* Zdroj dat, formáty a načítání dat
* Vyberte si svůj výpočetní cíl: místní nebo vzdálený
* Nastavení automatizovaného experimentu strojového učení
* Spuštění automatizovaného experimentu strojového učení
* Prozkoumejte metriky modelu
* Registrace a nasazení modelu

Pokud dáváte přednost žádné prostředí kódu, můžete také [vytvořit automatizované experimenty strojového učení ve studiu Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md).

## <a name="select-your-experiment-type"></a>Výběr typu experimentu

Než začnete experiment, měli byste určit druh problému strojového učení, který řešíte. Automatizované strojové učení podporuje typy úloh klasifikace, regrese a prognózování. Další informace o [typech úkolů](how-to-define-task-type.md).

Automatizované strojové učení podporuje následující algoritmy během procesu automatizace a ladění. Jako uživatel není nutné zadat algoritmus.

Classification | Regrese | Prognózování časové řady
|-- |-- |--
[Logistická regrese](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastická síť](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastická síť](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Lehký GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lehký GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lehký GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Zesílení přechodu](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Zesílení přechodu](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Zesílení přechodu](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#regression)|[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nejbližší sousedé](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nejbližší sousedé](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nejbližší sousedé](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineární SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Klasifikace vektorů podpory (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stochastický gradient nísestup (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastický gradient nísestup (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Náhodný les](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Náhodný les](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Náhodný les](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extrémně randomizované stromy](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extrémně randomizované stromy](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extrémně randomizované stromy](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN třídění](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Lineární klasifikátor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineární regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineární regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naivní Bayesovy klasifikátory](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Rychlý lineární regresor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Stochastický gradient nísestup (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Online přechod sestup urychlovač](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prorok](https://facebook.github.io/prophet/docs/quick_start.html)
|[Averaged Perceptron Třídění](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||PrognózaTCN
|[Lineární klasifikátor SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

Pomocí `task` parametru `AutoMLConfig` v konstruktoru určete typ experimentu.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Zdroj dat a formát

Automatizované strojové učení podporuje data, která se nacházejí na místní ploše nebo v cloudu, jako je Azure Blob Storage. Data lze číst do **Pandas DataFrame** nebo **Azure Machine Learning TabularDataset**.  [Další informace o datových sadách](how-to-create-register-datasets.md).

Požadavky na údaje o školení:
- Data musí být v tabulkové podobě.
- Hodnota předpovědět, cílový sloupec, musí být v datech.

Následující příklady kódu ukazují, jak ukládat data v těchto formátech.

* TabularDataset

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Pandas datový rám

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Načtení dat pro spuštěný experiment na vzdálených výpočetních zdrojích

Pro vzdálené spuštění musí být trénovací data přístupná ze vzdáleného výpočetního prostředí. Třída [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) v sadě SDK zpřístupňuje funkce:

* snadný přenos dat ze statických souborů nebo zdrojů adres URL do pracovního prostoru
* zpřístupňujte svá data trénovacím skriptům při spuštění na cloudových výpočetních prostředcích

Podívejte se [na návody](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) pro `Dataset` příklad použití třídy k připojení dat k vašemu výpočetnímu cíli.

## <a name="train-and-validation-data"></a>Údaje o trénování a ověření

Můžete zadat samostatné vlakové a ověřovací `AutoMLConfig` sady přímo v konstruktoru.

### <a name="k-folds-cross-validation"></a>Křížová validace K-Folds

Pomocí `n_cross_validations` nastavení můžete zadat počet křížových ověření. Sada dat školení bude náhodně `n_cross_validations` rozdělena do záhybů stejné velikosti. Během každého kola křížové validace bude jeden z záhybů použit pro validaci modelu trénovaného na zbývajících záhybech. Tento proces se `n_cross_validations` opakuje pro kola, dokud každý záhyb je použit jednou jako sada ověření. Budou vykázány `n_cross_validations` průměrné skóre ve všech kolech a odpovídající model bude přeškolen na celou sadu trénovacích dat.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo Cross Validace (opakované náhodné dílčí vzorkování)

Slouží `validation_size` k určení procenta trénovací datové sady, která `n_cross_validations` by měla být použita pro ověření, a slouží k určení počtu křížových ověření. Během každého křížového ověřovacího kola `validation_size` bude náhodně vybrána podmnožina velikosti pro ověření modelu trénovaného na zbývajících datech. Nakonec budou vykázány `n_cross_validations` průměrné skóre ve všech kolech a odpovídající model bude přeškolen na celou sadu dat školení. Monte Carlo není podporováno pro prognózování časových řad.

### <a name="custom-validation-dataset"></a>Vlastní datová sada ověření

Vlastní ověřovací datovou sadu použijte, pokud náhodné rozdělení není přijatelné, obvykle data časových řad nebo nevyvážená data. Můžete zadat vlastní datovou sadu ověření. Model bude vyhodnocen podle ověřovací datové sady zadané namísto náhodné datové sady.

## <a name="compute-to-run-experiment"></a>Výpočetní prostředí pro spuštění experimentu

Dále určete, kde bude model trénovaný. Automatizovaný experiment školení strojového učení můžete spustit na následující chod výpočetních funkcí:
* Místní počítač, například místní stolní počítač nebo přenosný počítač – obecně platí, když máte malou datovou sadu a jste stále ve fázi průzkumu.
* Vzdálený počítač v cloudu – [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) je spravovaná služba, která umožňuje trénovat modely strojového učení v clusterech virtuálních počítačů Azure.

  Podívejte se na tento [web GitHubu,](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) kde najdete příklady poznámkových bloků s místními a vzdálenými výpočetními cíli.

* Cluster Azure Databricks ve vašem předplatném Azure. Další podrobnosti najdete zde – [Nastavení clusteru Azure Databricks pro automatizovanou ml.](how-to-configure-environment.md#azure-databricks)

  Podívejte se na tento [web GitHubu,](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) kde najdete příklady poznámkových bloků s Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurace nastavení experimentu

Existuje několik možností, které můžete použít ke konfiguraci automatizovaného experimentu strojového učení. Tyto parametry jsou nastaveny vytvořením instance objektu. `AutoMLConfig` Úplný seznam parametrů naleznete ve [třídě AutoMLConfig.](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)

Možné příklady:

1. Klasifikační experiment s použitím AUC vážený jako primární metrika s minutami časového času experimentu nastavenými na 30 minut a 2 přeložení křížového ověření.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. Níže je uveden příklad regresního experimentu, který končí po 60 minutách s pěti validačními křížky.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['kNN regressor'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

Tři různé `task` hodnoty parametrů (třetí typ `forecasting`úlohy je a `regression` používá podobný fond algoritmů jako úkoly) určují seznam modelů, které mají být aplikovány. Pomocí `whitelist` parametrů `blacklist` or můžete dále upravovat iterace s dostupnými modely, které mají být zahrnuty nebo vyloučeny. Seznam podporovaných modelů naleznete na [stránce SupportedModels Class](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) for ([Klasifikace](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [Prognózování](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)a [Regrese).](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)

Aby se zabránilo selhání časového limitu experimentu, služba `experiment_timeout_minutes` ověření automatizované ml bude vyžadovat, aby byla nastavena na minimálně 15 minut nebo 60 minut, pokud řádek podle velikosti sloupce překročí 10 milionů.

### <a name="primary-metric"></a>Primární metrika
Primární metrika určuje metriku, která se má použít při trénování modelu pro optimalizaci. Dostupné metriky, které můžete vybrat, jsou určeny vybraným typem úkolu a v následující tabulce jsou uvedeny platné primární metriky pro každý typ úkolu.

|Classification | Regrese | Prognózování časové řady
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Informace o konkrétních definicích těchto metrik najdete v [části Principy výsledků automatizovaného strojového učení](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Data featurization

V každém automatizovaném experimentu strojového učení se vaše data [automaticky škálují a normalizují,](concept-automated-ml.md#preprocess) aby pomohla *určitým* algoritmům, které jsou citlivé na funkce, které jsou v různých měřítkách.  Můžete však také povolit další featurization, jako jsou chybějící hodnoty imputace, kódování a transformace. [Další informace o tom, co featurization je zahrnuta](how-to-use-automated-ml-for-ml-models.md#featurization).

Při konfiguraci experimentů můžete povolit `featurization`rozšířené nastavení . V následující tabulce jsou uvedena přijatá [ `AutoMLConfig` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)nastavení pro featurization ve třídě .

|Konfigurace featurizace | Popis |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Označuje, že by měl být použit vlastní krok featurization. [Přečtěte si, jak přizpůsobit featurization](how-to-configure-auto-train.md#customize-feature-engineering).|
|`"featurization": 'off'`| Označuje krok featurization by neměl být proveden automaticky.|
|`"featurization": 'auto'`| Označuje, že jako součást předběžného zpracování jsou automaticky [prováděna svodidla dat a kroky featurization.](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options)|

> [!NOTE]
> Automatizované kroky strojového učení featurization (normalizace funkcí, zpracování chybějících dat, převod textu na číselné atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi, stejné kroky featurization použít během trénování se použijí na vstupní data automaticky.

### <a name="time-series-forecasting"></a>Prognózování časové řady
Úloha `forecasting` časové řady vyžaduje další parametry v objektu konfigurace:

1. `time_column_name`: Povinný parametr, který definuje název sloupce v trénovacích datech obsahujících platnou časovou řadu.
1. `max_horizon`: Definuje dobu, kterou chcete předpovědět na základě periodicity dat školení. Například pokud máte trénovací data s denní čas zrna, definujete, jak daleko ve dnech chcete model trénovat.
1. `grain_column_names`: Definuje název sloupců, které obsahují data jednotlivých časových řad v trénovacích datech. Pokud například předpovídáte prodej určité značky podle obchodu, definujete jako sloupce zrna sloupce obchodu a značky. Pro každé zrno nebo seskupení budou vytvořeny samostatné časové řady a prognózy. 

Příklady nastavení použitých níže naleznete v [ukázkovém poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

automl_config = AutoMLConfig(task = 'forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=20,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble-configuration"></a><a name="ensemble"></a>Konfigurace souboru

Modely souborů jsou ve výchozím nastavení povoleny a zobrazují se jako iterace konečného spuštění v automatizovaném spuštění strojového učení. V současné době podporované ensemble metody jsou hlasování a stohování. Hlasování je implementováno jako měkké hlasování pomocí vážených průměrů a implementace stohování používá dvouvrstvou implementaci, kde první vrstva má stejné modely jako hlasovací soubor a model druhé vrstvy se používá k nalezení optimální kombinace modely z první vrstvy. Pokud používáte modely ONNX **nebo** máte povolenou vysvětlitelnost modelu, stohování bude zakázáno a bude využito pouze hlasování.

Existuje více výchozí argumenty, které `kwargs` mohou `AutoMLConfig` být poskytnuty jako v objektu změnit výchozí chování zásobníku souboru.

* `stack_meta_learner_type`: meta-learner je model vyškolený na výstupu jednotlivých heterogenních modelů. Výchozí meta-learners `LogisticRegression` jsou pro `LogisticRegressionCV` úkoly klasifikace (nebo `ElasticNet` pokud křížové ověření je povoleno) `ElasticNetCV` a pro regresní /prognostické úkoly (nebo pokud je povoleno křížové ověření). Tento parametr může být jeden z `LogisticRegression` `LogisticRegressionCV`následujících řetězců: `LightGBMClassifier`, , , `ElasticNet` `ElasticNetCV`, `LightGBMRegressor`, , , nebo `LinearRegression`.
* `stack_meta_learner_train_percentage`: specifikuje podíl výcvikové sady (při výběru typu vlaku a validace výcviku), který má být vyhrazen pro školení metastudera. Výchozí hodnota `0.2`je .
* `stack_meta_learner_kwargs`: volitelné parametry pro předání inicializace metastudenta. Tyto parametry a typy parametrů zrcadlí parametry a typy parametrů z odpovídajícího konstruktoru modelu a jsou předány konstruktoru modelu.

Následující kód ukazuje příklad určení chování vlastního `AutoMLConfig` souboru v objektu.

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
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

Školení souboru je ve výchozím nastavení povoleno, `enable_voting_ensemble` `enable_stack_ensemble` ale lze jej zakázat pomocí a logických parametrů.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Spustit experiment

Pro automatické ML vytvoříte `Experiment` objekt, který je `Workspace` pojmenovaný objekt v slouží ke spuštění experimentů.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Odešlete experiment, aby se spustil a vygeneroval model. `AutoMLConfig` Předat metodu `submit` pro generování modelu.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Závislosti jsou nejprve nainstalovány v novém počítači.  Výstup může trvat až 10 minut.
>Nastavení `show_output` `True` na výsledky výstupu se zobrazí na konzoli.

### <a name="exit-criteria"></a>Kritéria ukončení
Existuje několik možností, které můžete definovat pro ukončení experimentu.
1. Žádná kritéria: Pokud nedefinujete žádné parametry ukončení, experiment bude pokračovat, dokud nebude dosaženo dalšího pokroku v primární metrice.
1. Ukončit po delší době: Použití `experiment_timeout_minutes` v nastavení umožňuje definovat, jak dlouho v minutách by měl experiment pokračovat v běhu.
1. Ukončit po dosažení skóre: `experiment_exit_score` Pomocí dokončí experiment po dosažení primární metriky skóre.

### <a name="explore-model-metrics"></a>Prozkoumejte metriky modelu

Výsledky školení můžete zobrazit ve widgetu nebo v aktivním režimu, pokud jste v poznámkovém bloku. Další podrobnosti najdete v [tématu Sledování a vyhodnocení modelů.](how-to-track-experiments.md#view-run-details)

## <a name="understand-automated-ml-models"></a>Principy automatizovaných modelů ML

Všechny modely vyrobené pomocí automatizovaného ml zahrnuje následující kroky:
+ Automatizované technické funkce `"featurization": 'auto'`(pokud)
+ Změna měřítka/normalizace a algoritmus s hodnotami hyperparametrů

Díky tomu je transparentní získat tyto informace z fitted_model výstup z automatizovaného ML.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Automatické inženýring funkcí

Podívejte se na seznam předzpracování a `"featurization": 'auto'`automatické funkce [inženýrství,](concept-automated-ml.md#preprocess) které se stane, když .

Vezměme si tento příklad:
+ K dispozici jsou čtyři vstupní funkce: A (Numeric), B (Numeric), C (Numeric), D (DateTime)
+ Číselný prvek C je vynechán, protože se jedná o sloupec ID se všemi jedinečnými hodnotami.
+ Číselné prvky A a B mají chybějící hodnoty, a proto jsou imputovány průměrnou
+ DateTime funkce D je featurized do 11 různých inženýrských funkcí

Pomocí těchto 2 API v prvním kroku namontovaného modelu porozumíte více.  Viz [tento ukázkový poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ ROZHRANÍ API `get_engineered_feature_names()` 1: vrátí seznam názvů navržených funkcí.

  Použití:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Tento seznam obsahuje všechny názvy konstrukčních prvků.

  >[!Note]
  >Použijte 'timeseriestransformer' pro task='forecasting', jinak použijte 'datatransformer' pro 'regrese' nebo 'klasifikace' úkol.

+ ROZHRANÍ API `get_featurization_summary()` 2: vrátí souhrn featurization pro všechny vstupní funkce.

  Použití:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Použijte 'timeseriestransformer' pro task='forecasting', jinak použijte 'datatransformer' pro 'regrese' nebo 'klasifikace' úkol.

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
   |RawFeatureName|Vstupní název prvku/sloupce z poskytnuté datové sady.|
   |TypeDetected|Detekovaný datový typ vstupní funkce.|
   |Klesl|Označuje, zda byla vstupní funkce vynechána nebo použita.|
   |EngineeringFeatureCount|Počet funkcí generovaných pomocí automatických transformací funkce.|
   |Transformace|Seznam transformací použitých na vstupní prvky pro generování konstrukčních prvků.|
   
### <a name="customize-feature-engineering"></a>Přizpůsobení technické úpravy funkcí
Chcete-li přizpůsobit `"featurization": FeaturizationConfig`technické funkce, zadejte .

Podporované přizpůsobení zahrnuje:

|Přizpůsobení|Definice|
|--|--|
|Aktualizace účelu sloupce|Přepsat typ prvku pro zadaný sloupec.|
|Aktualizace parametrů transformátoru |Aktualizovat parametry pro zadaný transformátor. V současné době podporuje Imputer (průměr, nejčastější & medián) a HashOneHotEncoder.|
|Přetažení sloupců |Sloupce k poklesu z výkonuurized.|
|Blokové transformátory| Blokové transformátory, které mají být použity při procesu featurization.|

Vytvořte objekt FeaturizationConfig pomocí volání rozhraní API:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Změna měřítka/normalizace a algoritmus s hodnotami hyperparametrů:

Chcete-li porozumět hodnotám měřítka/normalizace a algoritmu/hyperparametrů pro kanál, použijte fitted_model.steps. [Další informace o změně měřítka/normalizace](concept-automated-ml.md#preprocess). Zde je ukázkový výstup:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Chcete-li získat další podrobnosti, použijte tuto pomocnou funkci uvedenou v [tomto ukázkovém poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

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

Následující ukázkový výstup je pro kanál pomocí konkrétní algoritmus (LogisticRegression s RobustScalar, v tomto případě).

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

### <a name="predict-class-probability"></a>Předpovědět pravděpodobnost třídy

Modely vyrobené pomocí automatizovaného ML všechny mají obálkové objekty, které zrcadlí funkce z jejich open source původu třídy. Většina objektů obálky klasifikačního modelu vrácených automatizovaným ml implementovat `predict_proba()` funkci, která přijímá pole jako nebo řídké matice data vzorek funkce (hodnoty X) a vrátí n-dimenzionální pole každého vzorku a jeho pravděpodobnost příslušné třídy.

Za předpokladu, že jste načetli nejlepší spustit a vybavené `predict_proba()` modelu pomocí stejných volání shora, můžete volat přímo z vybaveného modelu, dodává `X_test` vzorek v příslušném formátu v závislosti na typu modelu.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Pokud základní model nepodporuje `predict_proba()` funkci nebo formát je nesprávný, bude vyvolána výjimka specifická pro třídu modelu. Viz [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) a [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) referenční dokumenty příklady, jak je tato funkce implementována pro různé typy modelů.

<a name="explain"></a>

## <a name="model-interpretability"></a>Interpretovatelnost modelů

Interpretace modelu umožňuje pochopit, proč vaše modely předpovědi a základní funkce hodnoty důležitosti. Sada SDK obsahuje různé balíčky pro povolení funkcí interpretace modelu, a to jak v době školení, tak v době odvození, pro místní i nasazené modely.

Podívejte [se na návody](how-to-machine-learning-interpretability-automl.md) pro ukázky kódu o tom, jak povolit funkce interpretovatelnosti konkrétně v rámci automatizovaných experimentů strojového učení.

Obecné informace o tom, jak lze povolit vysvětlení modelu a důležitost funkcí v jiných oblastech sady SDK mimo automatizované strojové učení, naleznete v článku [konceptu](how-to-machine-learning-interpretability.md) o interpretaci.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [tom, jak a kde nasadit model](how-to-deploy-and-where.md).

Další informace o [tom, jak trénovat regresní model pomocí automatizovaného strojového učení](tutorial-auto-train-models.md) nebo [jak trénovat pomocí automatizovaného strojového učení na vzdáleném zdroji](how-to-auto-train-remote.md).
