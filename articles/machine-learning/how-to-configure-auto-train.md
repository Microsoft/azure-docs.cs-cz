---
title: Vytváření experimentů automatizovaného strojového učení
titleSuffix: Azure Machine Learning
description: Automatický strojové učení vybere algoritmus pro vás a vygeneruje model připravený k nasazení. Seznamte se s možnostmi, které můžete použít ke konfiguraci automatizovaných experimentů strojového učení.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: seodec18
ms.openlocfilehash: c183c179200738566d0794ba23582f16068013b6
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722843"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurace experimentů automatizovaného strojového učení v Pythonu
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V této příručce se dozvíte, jak definovat různé konfigurační nastavení pro automatizované experimenty strojového učení pomocí [sady Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Automatický strojové učení vybere pro vás algoritmus a parametry a vygeneruje model připravený pro nasazení. K dispozici je několik možností, pomocí kterých můžete nakonfigurovat automatizované experimenty strojového učení.

Příklady automatických experimentů strojového učení najdete v tématu [kurz: výuka modelu klasifikace pomocí automatizovaného strojového učení](tutorial-auto-train-models.md) nebo [výukových modelů pomocí automatizovaného strojového učení v cloudu](how-to-auto-train-remote.md).

Možnosti konfigurace dostupné v automatizovaném strojovém učení:

* Vyberte typ experimentu: klasifikace, regrese nebo prognózování časových řad.
* Zdroj dat, formáty a načítání dat
* Výběr cílového výpočetního prostředí: místní nebo vzdálené
* Automatické nastavení experimentování ve strojovém učení
* Spuštění automatizovaného experimentu machine learningu
* Prozkoumat metriky modelu
* Registrace a nasazení modelu

Pokud dáváte přednost žádnému způsobu použití kódu, můžete [v Azure Machine Learning Studiu vytvářet i automatizované experimenty strojového učení](how-to-use-automated-ml-for-ml-models.md).

## <a name="select-your-experiment-type"></a>Výběr typu experimentu

Než začnete experimentovat, měli byste určit druh problému strojového učení, který řešíte. Automatizované Machine Learning podporuje typy úloh klasifikace, regrese a prognózy. Přečtěte si další informace o [typech úloh](how-to-define-task-type.md).

Automatizované Machine Learning podporuje během procesu automatizace a optimalizace následující algoritmy. Jako uživatel není nutné zadávat algoritmus.

> [!NOTE]
> Pokud plánujete exportovat vytvořené modely automl do [modelu ONNX](concept-onnx.md), je možné převést pouze ty algoritmy označené znakem * na formát ONNX. Přečtěte si další informace o [převodu modelů na ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Všimněte si také, že ONNX podporuje v tuto chvíli pouze úlohy klasifikace a regrese. 

Classification | Regrese | Prognózování časové řady
|-- |-- |--
[Logistická regrese](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastická síť](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastická síť](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Lehký GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Lehký GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Lehký GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Zvýšení barevného přechodu](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Zvýšení barevného přechodu](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Zvýšení barevného přechodu](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#regression)
[K nejbližším sousedním sousedům](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K nejbližším sousedním sousedům](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K nejbližším sousedním sousedům](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineární SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS laso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Podpora klasifikace vektoru (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Stochastickéhoový přechod klesá (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Stochastickéhoový přechod klesá (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Náhodná doménová struktura](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Náhodná doménová struktura](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Náhodný les](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extrémně náhodné stromy](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extrémně náhodné stromy](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extrémně náhodné stromy](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Třídění DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier) |[DNN regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN lineární třídění](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineární regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor) |[Lineární regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Rychlé lineární regresor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Automatické ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Stochastickéhoový přechod klesá (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* |[Online gradient regresor klesání](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[Průměrný Perceptron klasifikátor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[Lineární třídění SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)* ||

Použijte `task` parametr v `AutoMLConfig` konstruktoru k určení typu experimentu.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Zdroj dat a formát

Automatizované Machine Learning podporuje data, která se nachází na místním počítači nebo v cloudu, jako je například Azure Blob Storage. Data lze číst do **PANDAS dataframe** nebo do **Azure Machine Learning TabularDataset**.  [Přečtěte si další informace o datových sadách](how-to-create-register-datasets.md).

Požadavky na školicí data:
- Data musí být v tabulkovém formátu.
- Hodnota pro předpověď, cílový sloupec musí být v datech.

Následující příklady kódu ukazují, jak ukládat data v těchto formátech.

* TabularDataset

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* PANDAS – datový rámec

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Načtení dat pro spuštění experimentu na vzdálených COMPUTE

Pro vzdálená spuštění musí být školicí data dostupná ze vzdáleného výpočetního prostředí. Třída [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) v sadě SDK zpřístupňuje funkce:

* snadné přenos dat ze statických souborů nebo zdrojů URL do vašeho pracovního prostoru
* zpřístupnění dat pro školicí skripty při spuštění na cloudových výpočetních prostředcích

Podívejte se [na](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) příklad použití `Dataset` třídy pro připojení dat k cíli služby Compute.

## <a name="train-and-validation-data"></a>Data o školeních a ověřováních

V konstruktoru můžete určit samostatné sady vlaků a ověřovacích sad přímo `AutoMLConfig` .

### <a name="k-folds-cross-validation"></a>K skládání křížového ověřování

Pomocí `n_cross_validations` nastavení určete počet křížků. Sada školicích dat bude náhodně rozdělena na `n_cross_validations` skládání stejné velikosti. Během každého kulatého kulatého kole se jedno ze skládání použije pro ověření modelu vyškolených ve zbývajících skládáních. Tento proces se opakuje `n_cross_validations` , dokud se každé skládání nepoužije jako nastavené pro ověření. Průměrné skóre `n_cross_validations` pro všechna zaokrouhlení budou hlášena a odpovídající model bude převedený na celou sadu dat školení.

Přečtěte si další informace o tom, jak autoML používá křížové ověřování, aby se [předešlo navýšení modelů](concept-manage-ml-pitfalls.md#prevent-over-fitting)
### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Křížové ověření Monte Carlo (opakované náhodné dílčí vzorkování)

Slouží `validation_size` k zadání procenta pro datovou sadu školení, která se má použít k ověření, a použijte `n_cross_validations` k určení počtu meziplatných. Během každého kulatého kruhového ověřování se náhodně vybere podmnožina velikosti `validation_size` pro ověření modelu vyškolených u zbývajících dat. Nakonec bude oznámeno průměrné skóre všech `n_cross_validations` zaokrouhlení a odpovídající model bude převedený na celou datovou sadu školení. Monte Carlo se pro prognózování časových řad nepodporuje.

### <a name="custom-validation-dataset"></a>Vlastní ověřovací datová sada

Použijte vlastní ověřovací datovou sadu, pokud není přijatelné náhodné rozdělení, obvykle data časových řad nebo nevyvážená data. Můžete zadat vlastní ověřovací datovou sadu. Model bude vyhodnocen proti zadané datové sadě ověřování místo náhodné datové sady.

## <a name="compute-to-run-experiment"></a>Výpočetní prostředí pro spuštění experimentu

Dále určete, kde bude model vyškolený. Automatický experiment pro školení ve službě Machine Learning může běžet na následujících možnostech výpočtů:
* Váš místní počítač, jako je například místní plocha nebo přenosný počítač – obecně platí, že pokud máte malou datovou sadu a stále jste ve fázi průzkumu.
* Vzdálený počítač v cloudu – [Azure Machine Learning Managed COMPUTE](concept-compute-target.md#amlcompute) je spravovaná služba, která umožňuje naučit modely strojového učení v clusterech virtuálních počítačů Azure. 

  Příklady poznámkových bloků s místními a vzdálenými výpočetními cíli najdete na tomto [webu GitHubu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) .

* Cluster Azure Databricks ve vašem předplatném Azure. Další podrobnosti najdete tady: [nastavení Azure Databricks clusteru pro automatizované ml](how-to-configure-environment.md#azure-databricks) .

  Příklady poznámkových bloků s Azure Databricks najdete na tomto [webu GitHubu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) .

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurace nastavení experimentu

K dispozici je několik možností, pomocí kterých můžete nakonfigurovat automatický experiment strojového učení. Tyto parametry jsou nastaveny vytvořením instance `AutoMLConfig` objektu. Úplný seznam parametrů naleznete v tématu [Třída AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) .

Možné příklady:

1. Experiment s klasifikací pomocí AUC váže jako primární metrika s časovým limitem experimentu nastaveným na 30 minut a 2 přeložení křížového ověřování.

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
2. Níže je uveden příklad regresní experimentu, který se ukončí po 60 minutách a pět přeložení ověřování.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

Tři různé `task` hodnoty parametrů (třetí typ úlohy je `forecasting` a jako úlohy používá podobný fond algoritmů `regression` ) určují seznam modelů, které se mají použít. Pomocí `whitelist` parametrů nebo `blacklist` můžete dále upravit iterace s dostupnými modely, které chcete zahrnout nebo vyloučit. Seznam podporovaných modelů lze nalézt ve [třídě SupportedModels](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) pro ([klasifikace](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [Prognózování](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)a [regresi](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)).

Aby se zabránilo neúspěšným časovým limitům experimentů, bude služba ověřování automatizovaného ML vyžadovat, aby `experiment_timeout_minutes` byla nastavená na minimálně 15 minut, nebo 60 minut, pokud řádek podle velikosti sloupce překračuje 10 000 000.

### <a name="primary-metric"></a>Primární metrika
Primární metrika určuje metriku, která se má použít během školení modelu pro optimalizaci. Dostupné metriky můžete vybrat podle typu úlohy, kterou zvolíte, a v následující tabulce jsou uvedeny platné primární metriky pro každý typ úkolu.

|Classification | Regrese | Prognózování časové řady
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Přečtěte si o konkrétních definicích těchto metrik v seznámení s [automatizovanými výsledky strojového učení](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Featurization dat

U každého automatizovaného experimentu strojového učení se vaše data [automaticky škálují a normalizují](concept-automated-ml.md#preprocess) tak, aby pomohly *určité* algoritmy, které jsou citlivé na funkce, které jsou v různých měřítkech.  Můžete ale také povolit další featurization, například chybějící hodnoty imputac, Encoding a transformes. [Přečtěte si další informace o tom, co je zahrnuté featurization](how-to-use-automated-ml-for-ml-models.md#featurization).

Při konfiguraci experimentů můžete povolit rozšířené nastavení `featurization` . V následující tabulce jsou uvedena přijímaná nastavení pro featurization ve [třídě AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

|Konfigurace Featurization | Popis |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indikuje, že by se měl použít upravený krok featurization. [Přečtěte si, jak přizpůsobit featurization](how-to-configure-auto-train.md#customize-feature-engineering).|
|`"featurization": 'off'`| Indikuje, že krok featurization by se neměl provádět automaticky.|
|`"featurization": 'auto'`| Označuje, že v rámci předběžného zpracování se [kroky guardrails a featurization](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options) provádějí automaticky.|

> [!NOTE]
> Automatické kroky featurization strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselnou atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky featurization, jaké jste použili během školení.

### <a name="time-series-forecasting"></a>Prognózování časové řady
Úloha časové řady `forecasting` vyžaduje další parametry v objektu Configuration:

1. `time_column_name`: Požadovaný parametr, který definuje název sloupce ve školicích datech obsahujících platnou časovou řadu.
1. `max_horizon`: Definuje dobu, po kterou chcete předpovědět na základě periodicity školicích dat. Například pokud máte školicí data s denním intervalem, můžete definovat, jak dlouho chcete, aby model mohl vyškolit.
1. `grain_column_names`: Definuje názvy sloupců, které obsahují data jednotlivých časových řad ve vašich školicích datech. Pokud například vytváříte předpověď prodeje konkrétní značky podle obchodu, definovali byste sloupce úložiště a značky jako sloupce zrnitosti. Pro každou zrnitost/seskupení se vytvoří oddělující časová řada a prognózy. 

Příklady nastavení použitých níže najdete v [ukázkovém poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

### <a name="ensemble-configuration"></a><a name="ensemble"></a>Konfigurace kompletu

Modely kompletu jsou ve výchozím nastavení povolené a zobrazují se jako poslední iterace v automatizovaném spuštění strojového učení. Aktuálně podporované metody kompletování jsou hlasovací a Stacked. Hlasování je implementováno jako měkký hlas pomocí vážených průměrů a implementace skládání používá dvě implementace vrstvy, kde první vrstva má stejné modely jako hlasovacího kompletu, a druhý model vrstvy se používá k nalezení optimální kombinace modelů z první vrstvy. Pokud používáte modely ONNX **nebo** Pokud máte povolenou možnost vysvětlování modelu, bude zablokování zakázané a budou využívány jenom hlasovací prvky.

Existuje více výchozích argumentů, které lze zadat jako `kwargs` v `AutoMLConfig` objektu pro změnu výchozího chování kompletu.

* `ensemble_download_models_timeout_sec`: Během generování modelu VotingEnsemble a StackEnsemble se stáhnou několik namontovaných modelů z předchozích podřízených běhů. Pokud se setkáte s touto chybou: `AutoMLEnsembleException: Could not find any models for running ensembling` , možná bude potřeba poskytnout více času pro stažení modelů. Výchozí hodnota je 300 sekund pro stažení těchto modelů paralelně a neexistuje maximální časový limit. Pokud je potřeba víc času, nakonfigurujte tento parametr s vyšší hodnotou než 300 s. **Poznámka**: Pokud je dosažen časový limit a jsou staženy modely, pak ensembling pokračuje s libovolným počtem modelů, které se stáhly (není nutné, aby všechny modely byly staženy do konce tohoto časového limitu).

Následující parametry platí pouze pro modely StackEnsemble: 

* `stack_meta_learner_type`: meta-učí se model vyškolený na výstupu jednotlivých heterogenních modelů. Výchozí metaznačky jsou `LogisticRegression` pro úlohy klasifikace (nebo `LogisticRegressionCV` Pokud je povolené křížové ověřování) a `ElasticNet` pro úlohy regrese/předpovědi (nebo `ElasticNetCV` Pokud je povolené křížové ověřování). Tento parametr může být jeden z následujících řetězců: `LogisticRegression` , `LogisticRegressionCV` , `LightGBMClassifier` , `ElasticNet` , `ElasticNetCV` , `LightGBMRegressor` , nebo `LinearRegression` .

* `stack_meta_learner_train_percentage`: určuje podíl sady školení (při výběru typu výuky a ověření školení), který má být rezervován pro školení meta-učí. Výchozí hodnota je `0.2` . 

* `stack_meta_learner_kwargs`: volitelné parametry, které mají být předána inicializátoru meta-učí. Tyto parametry a typy parametrů zrcadlí parametry a typy parametrů z odpovídajícího konstruktoru modelu a jsou předávány do konstruktoru modelu.

Následující kód ukazuje příklad určení chování vlastního kompletování v `AutoMLConfig` objektu.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
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

Školení kompletu je ve výchozím nastavení povolené, ale je možné ho zakázat pomocí `enable_voting_ensemble` `enable_stack_ensemble` logických parametrů a.

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

Pro automatizované ML vytvoříte `Experiment` objekt, který je pojmenovaný objekt v, který se `Workspace` používá ke spouštění experimentů.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Odešlete experiment, aby se spustil a vygeneroval model. Předáním `AutoMLConfig` metodě k `submit` vygenerování modelu.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Závislosti jsou nejprve nainstalovány v novém počítači.  Může trvat až 10 minut, než se zobrazí výstup.
>Nastavení `show_output` na `True` výstup se zobrazí v konzole nástroje.

### <a name="exit-criteria"></a><a name="exit"></a>Výstupní kritéria

Existuje několik možností, které můžete definovat pro ukončení experimentu.
1. Žádná kritéria: Pokud nedefinujete žádné parametry ukončení, bude experiment pokračovat, dokud nebude u primární metriky proveden žádný další postup.
1. Ukončit po určité době: pomocí `experiment_timeout_minutes` v nastavení můžete určit, jak dlouho má experiment pokračovat v běhu.
1. Ukončit po dosažení skóre: pomocí `experiment_exit_score` se dokončí experiment po dosažení primárního skóre metriky.

### <a name="explore-model-metrics"></a>Prozkoumat metriky modelu

Pokud se nacházíte v poznámkovém bloku, můžete zobrazit výsledky školení v widgetu nebo v případě potřeby. Další podrobnosti najdete v tématu [sledování a vyhodnocení modelů](how-to-track-experiments.md#view-run-details) .

## <a name="understand-automated-ml-models"></a>Principy automatizovaných modelů ML

Libovolný model vytvořený pomocí automatizovaného ML obsahuje následující kroky:
+ Automatizovaná technologie funkcí (Pokud `"featurization": 'auto'` )
+ Škálování/normalizace a algoritmy s použitím hodnot parametrů

Pro získání těchto informací z fitted_modelho výstupu z automatizovaného ML je transparentní.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Automatizovaná technologie funkcí

Podívejte se na seznam předzpracovaných a [automatizovaných funkcí](concept-automated-ml.md#preprocess) , ke kterým dojde, když `"featurization": 'auto'` .

Vezměte v úvahu tento příklad:
+ Existují čtyři vstupní funkce: A (číselná), B (číselná), C (číselná), D (DateTime).
+ Číselná funkce C je vyřazena, protože se jedná o sloupec ID se všemi jedinečnými hodnotami.
+ Numerické funkce a a B chybějící hodnoty a jsou proto imputované středníky.
+ Funkce DateTime D je natrénuje na 11 různých funkcí navržených pro analýzu.

Tato 2 rozhraní API použijte v prvním kroku namontovaného modelu, abyste lépe pochopili víc.  Podívejte se na [Tento ukázkový Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ Rozhraní API 1: `get_engineered_feature_names()` vrátí seznam navržených názvů funkcí.

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

+ Rozhraní API 2: `get_featurization_summary()` Vrátí souhrn featurization pro všechny vstupní funkce.

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
   
### <a name="customize-feature-engineering"></a>Přizpůsobení technologie funkcí
K přizpůsobení technologie funkcí zadejte  `"featurization": FeaturizationConfig` .

Podporované vlastní nastavení zahrnuje:

|Přizpůsobení|Definice|
|--|--|
|Aktualizace pro účely sloupce|Přepište typ funkce pro zadaný sloupec.|
|Aktualizace parametrů transformátoru |Aktualizuje parametry pro zadaný transformátor. V současné době podporuje Imputac (střední, nejčastější & medián) a HashOneHotEncoder.|
|Odkládací sloupce |Sloupce, které se mají odpustit z natrénuje|
|Blokovat transformátory| Blokuje transformátory, které se mají použít na featurization procesu.|

Vytvoření objektu FeaturizationConfig pomocí volání rozhraní API:
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

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Škálování/normalizace a algoritmy pomocí hodnot parametrů:

Pro pochopení hodnoty škálování/normalizace a algoritmu/parametrů pro kanál použijte fitted_model. Steps. [Další informace o škálování/normalizaci](concept-automated-ml.md#preprocess). Zde je ukázkový výstup:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Další podrobnosti získáte pomocí této pomocné funkce: 

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


print_model(model)
```

Následující vzorový výstup je určený pro kanál pomocí konkrétního algoritmu (LogisticRegression s RobustScalar, v tomto případě).

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

### <a name="predict-class-probability"></a>Předpověď pravděpodobnosti třídy

Modely vytvořené pomocí automatizovaných ML mají objekty obálky, které zrcadlí funkci ze své Open Source třídy původu. Většina objektů obálky modelu klasifikace vrácených pomocí automatizovaného ML implementuje `predict_proba()` funkci, která přijímá ukázku dat typu pole nebo zhuštěné matrice vašich funkcí (hodnot X) a vrací n-dimenzionální pole každé ukázky a její příslušnou pravděpodobnost třídy.

Za předpokladu, že jste načetli nejlepší běh a namontovaný model pomocí stejných volání výše, můžete zavolat `predict_proba()` přímo z namontovaného modelu a zadat `X_test` ukázku v příslušném formátu v závislosti na typu modelu.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Pokud podkladový model nepodporuje `predict_proba()` funkci nebo je nesprávný formát, bude vyvolána výjimka specifická pro třídu modelu. Příklady toho, jak je tato funkce implementovaná pro různé typy modelů, najdete v referenční dokumentaci k [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) a [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) .

<a name="explain"></a>

## <a name="model-interpretability"></a>Interpretovatelnost modelů

Možnost interpretace modelu vám umožní pochopit, proč se vaše modely předpovědi, a základní hodnoty důležitosti funkcí. Sada SDK obsahuje různé balíčky pro povolení funkcí pro interpretaci modelu, a to jak v rámci školení, tak i v době odvození pro místní a nasazené modely.

Informace o tom, jak v rámci automatizované experimenty strojového učení povolit funkce pro interpretaci, najdete v tématu [postupy](how-to-machine-learning-interpretability-automl.md) pro ukázky kódu.

Obecné informace o tom, jak je možné povolit vysvětlení modelu a důležitost funkcí v jiných oblastech sady SDK mimo automatizované strojové učení, najdete v článku [konceptu](how-to-machine-learning-interpretability.md) o výkladu.

## <a name="next-steps"></a>Další kroky

+ Přečtěte si další informace o [tom, jak a kde model nasadit](how-to-deploy-and-where.md).

+ Přečtěte si další informace o [tom, jak vytvořit regresní model pomocí automatizovaného strojového učení](tutorial-auto-train-models.md) nebo [jak pomocí automatizovaného strojového učení na vzdáleném prostředku vyškolit](how-to-auto-train-remote.md).
+ Naučte se, jak proškolit několik modelů pomocí autoML v [mnoha akcelerátorech řešení modelů](https://aka.ms/many-models).
