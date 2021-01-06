---
title: Vytváření experimentů automatizovaného strojového učení
titleSuffix: Azure Machine Learning
description: Naučte se definovat zdroje dat, výpočty a nastavení konfigurace pro automatizované experimenty strojového učení.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: 054d18337e50a367cf1f6f004f4e1d1652c7751e
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954396"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurace experimentů automatizovaného strojového učení v Pythonu


V této příručce se dozvíte, jak definovat různé konfigurační nastavení pro automatizované experimenty strojového učení pomocí [sady Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py). Automatický strojové učení vybere pro vás algoritmus a parametry a vygeneruje model připravený pro nasazení. K dispozici je několik možností, pomocí kterých můžete nakonfigurovat automatizované experimenty strojového učení.

Příklady automatických experimentů strojového učení najdete v tématu [kurz: výuka modelu klasifikace pomocí automatizovaného strojového učení](tutorial-auto-train-models.md) nebo [výukových modelů pomocí automatizovaného strojového učení v cloudu](how-to-auto-train-remote.md).

Možnosti konfigurace dostupné v automatizovaném strojovém učení:

* Vyberte typ experimentu: klasifikace, regrese nebo prognózování časových řad.
* Zdroj dat, formáty a načítání dat
* Výběr cílového výpočetního prostředí: místní nebo vzdálené
* Automatické nastavení experimentování ve strojovém učení
* Spuštění experimentu automatizovaného strojového učení
* Prozkoumat metriky modelu
* Registrace a nasazení modelu

Pokud dáváte přednost žádnému způsobu použití kódu, můžete [v Azure Machine Learning Studiu vytvářet i automatizované experimenty strojového učení](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Požadavky

Pro tento článek potřebujete, 
* Pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit pracovní prostor, přečtěte si téma [vytvoření Azure Machine Learningho pracovního prostoru](how-to-manage-workspace.md).

* Sada SDK Azure Machine Learning Pythonu je nainstalovaná.
    Pro instalaci sady SDK můžete buď 
    * Vytvořte výpočetní instanci, která automaticky nainstaluje sadu SDK a je předem nakonfigurovaná pro pracovní postupy ML. Další informace najdete v tématu [Vytvoření a Správa výpočetní instance Azure Machine Learning](how-to-create-manage-compute-instance.md) . 

    * [Nainstalujte `automl` balíček sami](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment), včetně [výchozí instalace](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py#default-install) sady SDK.

## <a name="select-your-experiment-type"></a>Výběr typu experimentu

Než začnete experimentovat, měli byste určit druh problému strojového učení, který řešíte. Automatizované Machine Learning podporuje typy úloh `classification` , `regression` a `forecasting` . Přečtěte si další informace o [typech úloh](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

Následující kód používá `task` parametr v `AutoMLConfig` konstruktoru k určení typu experimentu jako `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Zdroj a formát dat

Automatizované strojové učení podporuje data nacházející se na místním desktopu nebo v cloudu, například ve službě Azure Blob Storage. Data lze číst do **PANDAS dataframe** nebo do **Azure Machine Learning TabularDataset**. [Další informace o datových sadách](how-to-create-register-datasets.md)

Požadavky na trénovací data:
- Data musí být v tabulkovém formátu.
- Hodnota pro předpověď, cílový sloupec musí být v datech.

**Pro vzdálené experimenty** musí být školicí data dostupná ze vzdáleného výpočetního prostředí. AutoML při práci ve vzdáleném výpočetním prostředí přijímá pouze [tabulkové datové sady Azure Machine Learning](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py). 

Datové sady Azure Machine Learning zveřejňují funkce umožňující:

* Data můžete snadno přenést ze statických souborů nebo zdrojů URL do svého pracovního prostoru.
* Zpřístupnění dat pro trénovací skripty při využití cloudových výpočetních prostředků Příklad [](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) použití `Dataset` třídy pro připojení dat ke vzdálenému cíli výpočtů najdete v tématu výuka s datovými sadami.

Následující kód vytvoří TabularDataset z webové adresy URL. Příklady vytváření datových sad z jiných zdrojů, jako jsou místní soubory a úložiště dat, najdete v tématu věnovaném [Vytvoření TabularDatasets](how-to-create-register-datasets.md#create-a-tabulardataset) .

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Pro účely místních výpočetních experimentů** doporučujeme PANDAS dataframes pro rychlejší časy zpracování.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Školení, ověřování a testování dat

Můžete určit samostatné **školicí a ověřovací sady** přímo v `AutoMLConfig` konstruktoru. Přečtěte si další informace o [tom, jak nakonfigurovat rozdělení dat a vzájemné ověřování](how-to-configure-cross-validation-data-splits.md) pro AutoML experimenty. 

Pokud explicitně nezadáte `validation_data` `n_cross_validation` parametr nebo, AutoML použije výchozí techniky k určení, jak se provádí ověřování. Toto určení závisí na počtu řádků v datové sadě přiřazené k vašemu `training_data` parametru. 

|&nbsp;Velikost dat &nbsp; školení| Technika ověřování |
|---|-----|
|**Větší &nbsp; než &nbsp; 20 000 &nbsp; řádků**| Je použito rozdělení dat pro vlak nebo ověření. Ve výchozím nastavení se jako sada ověření provede 10% počáteční sady dat školení. Pak se tato sada ověření používá pro výpočet metrik.
|**Menší &nbsp; než &nbsp; 20 000 &nbsp; řádků**| Je použit přístup pro křížové ověřování. Výchozí počet skládání závisí na počtu řádků. <br> **Pokud je datová sada menší než 1 000 řádků**, použije se 10 skládání. <br> **Pokud jsou řádky mezi 1 000 a 20 000**, budou použity tři skládání.

V tuto chvíli potřebujete pro vyhodnocení modelu zadat vlastní **testovací data** . Příklad kódu, který přináší vlastní testovací data pro vyhodnocení modelu, najdete v části **test** [tohoto poznámkového bloku Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).

## <a name="compute-to-run-experiment"></a>Výpočetní prostředí pro spuštění experimentu

Dále určete, kde bude model vyškolený. Ke spuštění výukového experimentu automatizovaného strojového učení je možné využít následující výpočetní možnosti. Seznamte se s [výhodami a nevýhodami místních a vzdálených výpočetních možností](concept-automated-ml.md#local-remote). 

* Váš **místní** počítač, jako je místní plocha nebo přenosný počítač – obecně platí, že když máte malou datovou sadu a pořád jste ve fázi průzkumu. Příklad místního výpočetního prostředí najdete v [tomto poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb). 
 
* **Vzdálený** počítač v cloudu – [Azure Machine Learning Managed COMPUTE](concept-compute-target.md#amlcompute) je spravovaná služba, která umožňuje naučit modely strojového učení v clusterech virtuálních počítačů Azure. 

    Příklad použití vzdáleného spravovaného výpočetního prostředí služby Azure Machine Learning najdete v [tomto poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). 

* **Cluster Azure Databricks** ve vašem předplatném Azure. Další podrobnosti najdete v [nastavení Azure Databricks clusteru pro automatizované ml](how-to-configure-databricks-automl-environment.md). Příklady poznámkových bloků s Azure Databricks najdete na tomto [webu GitHubu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) .

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurace nastavení experimentu

K dispozici je několik možností, pomocí kterých můžete nakonfigurovat automatický experiment strojového učení. Tyto parametry jsou nastaveny vytvořením instance `AutoMLConfig` objektu. Úplný seznam parametrů naleznete v tématu [Třída AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) .

Možné příklady:

1. Experiment s klasifikací pomocí AUC váže jako primární metrika s časovým limitem experimentu nastaveným na 30 minut a 2 přeložení křížového ověřování.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. V následujícím příkladu je regresní experiment nastavený na konec po 60 minutách s pěti více než 5 ověřovacími skládáními.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. Úlohy předpovědi vyžadují další nastavení. Další podrobnosti najdete v článku věnovaném [Vytvoření modelu prognózy Time-Series](how-to-auto-train-forecast.md) . 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'drop_column_names': ['logQuantity'],
        'forecast_horizon': n_test_periods
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
    
### <a name="supported-models"></a>Podporované modely

Automatizované strojové učení během procesu automatizace a optimalizace zkouší různé modely a algoritmy. Jako uživatel není nutné zadávat algoritmus. 

Tři různé `task` hodnoty parametrů určují seznam algoritmů neboli modelů, které se mají použít. Pomocí `allowed_models` parametrů nebo `blocked_models` můžete dále upravit iterace s dostupnými modely, které chcete zahrnout nebo vyloučit. 

Následující tabulka shrnuje podporované modely podle typu úkolu. 

> [!NOTE]
> Pokud plánujete exportovat vytvořené modely automl do [modelu ONNX](concept-onnx.md), je možné převést pouze ty algoritmy označené znakem * na formát ONNX. Přečtěte si další informace o [převodu modelů na ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Všimněte si také, že ONNX podporuje v tuto chvíli pouze úlohy klasifikace a regrese. 

Klasifikace | Regrese | Prognózování časové řady
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
[Průměrný Perceptron klasifikátor](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[Online gradient regresor klesání](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Automatické ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Rychlé lineární regresor](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[Stochastickéhoový přechod klesá (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Lineární třídění SVM](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Primární metrika
`primary metric`Parametr určuje metriku, která se má použít během školení modelu pro optimalizaci. Dostupné metriky můžete vybrat podle typu úlohy, kterou zvolíte, a v následující tabulce jsou uvedeny platné primární metriky pro každý typ úkolu.

Přečtěte si o konkrétních definicích těchto metrik v seznámení s [automatizovanými výsledky strojového učení](how-to-understand-automated-ml.md).

|Klasifikace | Regrese | Prognózování časové řady
|--|--|--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-featurization"></a>Featurization dat

U každého automatizovaného experimentu strojového učení se vaše data automaticky škálují a normalizují tak, aby pomohly *určité* algoritmy, které jsou citlivé na funkce, které jsou v různých měřítkech. Toto škálování a normalizace se označují jako featurization. Další podrobnosti a příklady kódu naleznete [v tématu Featurization v AutoML](how-to-configure-auto-features.md#) . 

Při konfiguraci experimentů ve vašem `AutoMLConfig` objektu můžete nastavení povolit nebo zakázat `featurization` . V následující tabulce jsou uvedena přijímaná nastavení pro featurization v [objektu AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Konfigurace Featurization | Popis |
| ------------- | ------------- |
|`"featurization": 'auto'`| Označuje, že v rámci předběžného zpracování se [kroky guardrails a featurization](how-to-configure-auto-features.md#featurization) provádějí automaticky. **Výchozí nastavení**.|
|`"featurization": 'off'`| Indikuje, že krok featurization se neprovádí automaticky.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indikuje, že by se měl použít upravený krok featurization. [Přečtěte si, jak přizpůsobit featurization](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Automatické kroky featurization strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselnou atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky featurization, jaké jste použili během školení.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a>Konfigurace kompletu

Modely kompletu jsou ve výchozím nastavení povolené a zobrazují se jako poslední iterace spuštění v AutoML spuštění. V současné době se podporují **VotingEnsemble** a **StackEnsemble** . 

Hlasovací aplikace implementuje měkké hlasování, které používá vážené průměry. Implementace skládání používá dvě implementace vrstvy, kde první vrstva má stejné modely jako hlasovacího kompletu, a druhý model vrstvy se používá k nalezení optimální kombinace modelů z první vrstvy. 

Pokud používáte modely ONNX **nebo** máte povolené vyjasnění modelu, je zablokování zakázané a využívají se jenom hlasovací.

Školení kompletu se dá zakázat pomocí `enable_voting_ensemble` `enable_stack_ensemble` logických parametrů a.

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

Chcete-li změnit výchozí chování kompletování, existuje více výchozích argumentů, které lze zadat jako `kwargs` v `AutoMLConfig` objektu.

> [!IMPORTANT]
>  Následující parametry nejsou explicitními parametry třídy AutoMLConfig. 

* `ensemble_download_models_timeout_sec`: Během generování modelu **VotingEnsemble** a **StackEnsemble** se stáhnou několik namontovaných modelů z předchozích podřízených běhů. Pokud se setkáte s touto chybou: `AutoMLEnsembleException: Could not find any models for running ensembling` , možná bude potřeba poskytnout více času pro stažení modelů. Výchozí hodnota je 300 sekund pro stažení těchto modelů paralelně a neexistuje maximální časový limit. Pokud je potřeba víc času, nakonfigurujte tento parametr s vyšší hodnotou než 300 s. 

  > [!NOTE]
  >  Pokud je dosažen časový limit a jsou staženy modely, pak ensembling pokračuje s libovolným počtem modelů, které stáhl. Není nutné, aby všechny modely byly staženy, aby byly dokončeny v rámci tohoto časového limitu.

Následující parametry platí pouze pro modely **StackEnsemble** : 

* `stack_meta_learner_type`: meta-učí se model vyškolený na výstupu jednotlivých heterogenních modelů. Výchozí metaznačky jsou `LogisticRegression` pro úlohy klasifikace (nebo `LogisticRegressionCV` Pokud je povolené křížové ověřování) a `ElasticNet` pro úlohy regrese/předpovědi (nebo `ElasticNetCV` Pokud je povolené křížové ověřování). Tento parametr může být jeden z následujících řetězců: `LogisticRegression` , `LogisticRegressionCV` , `LightGBMClassifier` , `ElasticNet` , `ElasticNetCV` , `LightGBMRegressor` , nebo `LinearRegression` .

* `stack_meta_learner_train_percentage`: určuje podíl sady školení (při výběru typu výuky a ověření školení), který má být rezervován pro školení meta-učí. Výchozí hodnota je `0.2`. 

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

<a name="exit"></a> 

### <a name="exit-criteria"></a>Výstupní kritéria

Existuje několik možností, které můžete v AutoMLConfig definovat pro ukončení experimentu.

|Kritéria| Popis
|----|----
Žádná &nbsp; kritéria | Pokud nedefinujete žádné parametry ukončení, experiment pokračuje, dokud neproběhne další postup u primární metriky.
Po &nbsp; &nbsp; delší &nbsp; &nbsp; dobu| Pomocí `experiment_timeout_minutes` Možnosti v nastavení můžete určit, jak dlouho má experiment běžet v řádu minut. <br><br> Aby se zabránilo chybám při experimentování, je k dispozici minimálně 15 minut nebo 60 minut, pokud řádek podle velikosti sloupce překračuje 10 000 000.
Bylo &nbsp; &nbsp; dosaženo skóre &nbsp; &nbsp;| Použijte `experiment_exit_score` dokončí experiment po dosažení zadaného primárního skóre metriky.

## <a name="run-experiment"></a>Spustit experiment

Pro automatizované ML vytvoříte `Experiment` objekt, který je pojmenovaný objekt v, který se `Workspace` používá ke spouštění experimentů.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
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

### <a name="multiple-child-runs-on-clusters"></a>Několik podřízených spuštění v clusterech

Automatizovaná spuštění experimentů v rámci služby můžete provádět na clusteru, který už spouští jiný experiment. Časování však závisí na tom, kolik uzlů cluster má, a pokud jsou tyto uzly k dispozici pro spuštění jiného experimentu.

Každý uzel v clusteru funguje jako jednotlivý virtuální počítač (VM), který může provádět jeden školicí běh. pro automatizované ML to znamená podřízený běh. Pokud jsou všechny uzly zaneprázdněné, nový experiment se zařadí do fronty. Pokud ale existují bezplatné uzly, na novém experimentu se v dostupných uzlech/virtuálních počítačích spustí paralelně automatizovaná podřízená rutina ML.

Aby bylo možné spravovat podřízená spuštění a při jejich provádění, doporučujeme vytvořit vyhrazený cluster na jeden experiment a porovnat počet `max_concurrent_iterations` experimentů s počtem uzlů v clusteru. Tímto způsobem použijete všechny uzly clusteru ve stejnou dobu s počtem souběžných podřízených spuštění/iterací, které chcete.

Nakonfigurujte  `max_concurrent_iterations` v `AutoMLConfig` objektu. Pokud není nakonfigurovaný, pak ve výchozím nastavení je povolený jenom jeden souběžný podřízený běh nebo iterace na jeden experiment.  

## <a name="explore-models-and-metrics"></a>Prozkoumejte modely a metriky

Pokud se nacházíte v poznámkovém bloku, můžete zobrazit výsledky školení v widgetu nebo v případě potřeby. Další podrobnosti najdete v tématu [sledování a vyhodnocení modelů](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs) .

Přečtěte si téma [vyhodnocení výsledků automatických experimentů strojového učení](how-to-understand-automated-ml.md) pro definice a příklady grafů výkonu a metriky, které jsou k dispozici pro jednotlivé spuštění. 

Chcete-li získat souhrn featurization a pochopit, jaké funkce byly přidány do konkrétního modelu, přečtěte si téma [transparentnost featurization](how-to-configure-auto-features.md#featurization-transparency). 

> [!NOTE]
> Algoritmy automatizované ML mají podstatu, která může způsobit mírnou variaci v konečném skóre Doporučené metriky modelu, jako je přesnost. Automatizované ML také provádí operace s daty, jako je rozdělení výukového testu, rozdělení vlaku-ověření nebo křížové ověřování v případě potřeby. Takže pokud spustíte experiment se stejným nastavením konfigurace a primární metrikou víckrát, pravděpodobně se vám v každém experimentu v důsledku těchto faktorů zobrazí variace konečný výsledek metriky. 

## <a name="register-and-deploy-models"></a>Registrace a nasazení modelů

Podrobnosti o tom, jak stáhnout nebo zaregistrovat model pro nasazení do webové služby, najdete v tématu [jak a kde nasadit model](how-to-deploy-and-where.md).

<a name="explain"></a>

## <a name="model-interpretability"></a>Interpretovatelnost modelů

Možnost interpretace modelu vám umožní pochopit, proč se vaše modely předpovědi, a základní hodnoty důležitosti funkcí. Sada SDK obsahuje různé balíčky pro povolení funkcí pro interpretaci modelu, a to jak v rámci školení, tak i v době odvození pro místní a nasazené modely.

Informace o tom, jak v rámci automatizované experimenty strojového učení povolit funkce pro interpretaci, najdete v tématu [postupy](how-to-machine-learning-interpretability-automl.md) pro ukázky kódu.

Obecné informace o tom, jak je možné povolit vysvětlení modelu a důležitost funkcí v jiných oblastech sady SDK mimo automatizované strojové učení, najdete v článku [konceptu](how-to-machine-learning-interpretability.md) o výkladu.

> [!NOTE]
> ForecastTCN model není aktuálně podporován klientem vysvětlení. Tento model nevrátí řídicí panel vysvětlení, pokud je vrácen jako nejlepší model, a nepodporuje spuštění vysvětlení na vyžádání.

## <a name="troubleshooting"></a>Řešení potíží

* **Nedávná aktualizace `AutoML` závislostí na novější verze bude mít za následek porušení kompatibility**: od verze 1.13.0 sady SDK se modely nenačte do starších sad SDK z důvodu nekompatibility mezi staršími verzemi, které jsme připnuli v předchozích balíčcích, a novějšími verzemi, které teď zapnete. Zobrazí se chyba, například:
  * Modul nebyl nalezen: ex. `No module named 'sklearn.decomposition._truncated_svd` ,
  * Chyby importu: ex. `ImportError: cannot import name 'RollingOriginValidator'` ,
  * Chyby atributů: ex. `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  Pokud chcete tento problém obejít, proveďte jeden z následujících dvou kroků v závislosti na `AutoML` verzi školení pro sadu SDK:
    * Pokud `AutoML` je vaše verze školení SDK větší než 1.13.0, budete potřebovat `pandas == 0.25.1` a `sckit-learn==0.22.1` . Pokud dojde k neshodě verzí, upgradujte scikit-učí nebo PANDAS na správnou verzi, jak je znázorněno níže:
      
      ```bash
         pip install --upgrade pandas==0.25.1
         pip install --upgrade scikit-learn==0.22.1
      ```
      
    * Pokud `AutoML` je vaše verze školení SDK menší nebo rovna 1.12.0, budete potřebovat `pandas == 0.23.4` a `sckit-learn==0.20.3` . Pokud se neshoduje verze, downgrade scikit-učí nebo PANDAS na správnou verzi, jak je znázorněno níže:
  
      ```bash
        pip install --upgrade pandas==0.23.4
        pip install --upgrade scikit-learn==0.20.3
      ```

* **Nasazení se nezdařilo**: pro verze <= 1.18.0 sady SDK, může základní image vytvořená pro nasazení selhat s následující chybou: "Chyba při importu: nelze importovat název `cached_property` z `werkzeug` ". 

  Problém můžete vyřešit podle následujících kroků:
  1. Stažení balíčku modelu
  2. Rozbalit balíček
  3. Nasazení pomocí prostředků unzip

* **Předpověď na pozici R2 je vždycky nulová**: k tomuto problému dochází, pokud mají poskytnuté školicí údaje časovou řadu, která obsahuje stejnou hodnotu pro poslední `n_cv_splits`  +  `forecasting_horizon` datové body. Pokud je tento model očekáván v časové řadě, můžete přepínat primární metriku na normalizovaný základní průměrnou chybu.
 
* **TensorFlow**: od verze 1.5.0 sady SDK služba automatizovaného strojového učení neinstaluje modely TensorFlow ve výchozím nastavení. Pokud chcete nainstalovat TensorFlow a používat ho s automatizovanými experimenty ML, nainstalujte TensorFlow = = 1.12.0 prostřednictvím CondaDependecies. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```

* **Grafy experimentů**: binární klasifikační grafy (přesnost-odvolání, ROC, křivka získání atd.) zobrazené v automatizovaných iteracích experimentu se nevykreslují správně v uživatelském rozhraní od 4/12. V grafu jsou v současné době zobrazeny inverzní výsledky, kde je lepší provádět modely s nižšími výsledky. Řešení je v šetření.

* **Datacihly ruší automatizované spuštění strojového učení**: když v Azure Databricks používáte automatizované funkce machine learningu, ke zrušení spuštění a spuštění nového experimentu restartujte cluster Azure Databricks.

* **Datacihly >10 iterací pro automatizované strojové učení**: v případě automatizovaného nastavení strojového učení, pokud máte více než 10 iterací, nastavte `show_output` na hodnotu `False` při odeslání běhu.

* **Widget datacihly pro sadu Azure Machine Learning SDK a automatizované Machine Learning**: pomůcka SDK pro Azure Machine Learning není v poznámkovém bloku datacihly podporovaná, protože poznámkové bloky nemůžou analyzovat widgety HTML. Widget můžete zobrazit na portálu pomocí tohoto kódu Pythonu v buňce s Azure Databricksm poznámkového bloku:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup se nezdařila**: 
    * Ve Windows spusťte automl_setup z příkazového řádku Anaconda. Pomocí tohoto odkazu [nainstalujete Miniconda](https://docs.conda.io/en/latest/miniconda.html).
    * Zajistěte, aby byl nainstalován conda 64, nikoli 32-bit spuštěním `conda info` příkazu. `platform`Měla by být `win-64` pro Windows nebo `osx-64` pro Mac.
    * Ujistěte se, že je nainstalovaný conda 4.4.10 nebo novější. Verzi můžete ověřit pomocí příkazu `conda -V` . Pokud máte nainstalovanou předchozí verzi, můžete ji aktualizovat pomocí příkazu: `conda update conda` .
    * Linux `gcc: error trying to exec 'cc1plus'`
      *  Pokud `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` dojde k chybě, nainstalujte základy sestavení pomocí příkazu `sudo apt-get install build-essential` .
      * Pokud chcete automl_setup vytvořit nové prostředí Conda, předejte nový název jako první parametr. Zobrazit existující prostředí conda pomocí `conda env list` a odebrat je pomocí `conda env remove -n <environmentname>` .
      
* **automl_setup_linux. sh se nezdařila**: Pokud se v Ubuntu Linux automl_setup_linus. sh, došlo k chybě: `unable to execute 'gcc': No such file or directory`-
  1. Ujistěte se, že jsou povolené Odchozí porty 53 a 80. Na virtuálním počítači Azure to můžete udělat z Azure Portal tím, že vyberete virtuální počítač a kliknete na sítě.
  2. Spusťte příkaz: `sudo apt-get update`
  3. Spusťte příkaz: `sudo apt-get install build-essential --fix-missing`
  4. Spustit `automl_setup_linux.sh` znovu

* **konfigurace. ipynb se nezdařila**:
  * V případě místních conda nejdříve zajistěte, aby byl úspěšně spuštěn automl_setup.
  * Ujistěte se, že je subscription_id správná. Subscription_id v Azure Portal Najděte tak, že vyberete všechny služby a potom předplatné. Znaky "<" a ">" by neměly být zahrnuty do hodnoty subscription_id. Například `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` má platný formát.
  * Zajistěte přístup k předplatnému pro přispěvatele nebo vlastníka.
  * Ověřte, zda je oblast jednou z podporovaných oblastí: `eastus2` , `eastus` , `westcentralus` , `southeastasia` , `westeurope` , `australiaeast` , `westus2` , `southcentralus` .
  * Ověřte přístup k oblasti pomocí Azure Portal.
  
* nepovedlo **`import AutoMLConfig` se: ve** verzi automatizovaného strojového učení pro 1.0.76 byly provedeny změny balíčků, které před aktualizací na novou verzi vyžadují odinstalaci předchozí verze. Pokud `ImportError: cannot import name AutoMLConfig` dojde k chybě po upgradu z verze sady SDK před 1.0.76 na verzi v 1.0.76 nebo novější, vyřešte tuto chybu spuštěním: `pip uninstall azureml-train automl` a potom `pip install azureml-train-auotml` . Skript automl_setup. cmd to provede automaticky. 

* **Workspace.from_config** Chyba: Pokud volání ws = Workspace.from_config () selhává –
  1. Ujistěte se, že Poznámkový blok Configuration. ipynb byl úspěšně spuštěn.
  2. Pokud se Poznámkový blok spouští ze složky, která není ve složce, ve které `configuration.ipynb` bylo spuštěno, zkopírujte aml_config složky a config.jssouboru, který obsahuje do nové složky. Workspace.from_config přečte config.jspro složku poznámkového bloku nebo její nadřazenou složku.
  3. Pokud se používá nové předplatné, skupina prostředků, pracovní prostor nebo oblast, ujistěte se, že jste `configuration.ipynb` Poznámkový blok znovu spustili. Přímá změna config.jsna přímo bude fungovat jenom v případě, že pracovní prostor už existuje v zadané skupině prostředků v rámci zadaného předplatného.
  4. Pokud chcete změnit oblast, změňte pracovní prostor, skupinu prostředků nebo předplatné. `Workspace.create` pracovní prostor nebude vytvořen ani aktualizován, pokud již existuje, i když je zadaná oblast odlišná.
  
* **Ukázkový Poznámkový blok se nezdařil**: Pokud ukázkový Poznámkový blok selhává s chybou, že vlastnost, metoda nebo knihovna neexistuje:
  * Ujistěte se, že je v Jupyter Notebook vybraná správná jádro. Jádro se zobrazí v pravém horním rohu stránky poznámkového bloku. Výchozí hodnota je azure_automl. Jádro se uloží jako součást poznámkového bloku. Pokud tedy přepnete na nové prostředí Conda, budete muset vybrat nové jádro v poznámkovém bloku.
      * V případě Azure Notebooks by měl být Python 3,6. 
      * V místních prostředích conda by měl být název prostředí Conda, který jste zadali v automl_setup.
  * Zajistěte, aby byl Poznámkový blok pro verzi sady SDK, kterou používáte. Verzi sady SDK můžete kontrolovat vykonáním `azureml.core.VERSION` Jupyter notebook buňky. Předchozí verzi ukázkových poznámkových bloků můžete stáhnout z GitHubu tak, že kliknete na `Branch` tlačítko, vyberete `Tags` kartu a pak vyberete verzi.

* **`import numpy` v systému Windows** došlo k chybě. v některých prostředích systému Windows se zobrazí chyba při načítání numpy s nejnovější verzí jazyka Python 3.6.8. Pokud se tento problém zobrazí, vyzkoušejte Python verze 3.6.7.

* nepovedlo **`import numpy` se: Podívejte** se na verzi TensorFlow v prostředí automatizovaného conda ml. Podporované verze jsou < 1,13. Pokud je verze >= 1,13, odinstalujte TensorFlow z prostředí. Můžete si ověřit verzi TensorFlow a odinstalovat následujícím způsobem:
  1. Spusťte příkazové prostředí, aktivujte prostředí Conda, ve kterém jsou nainstalované automatizované balíčky ml.
  2. Zadejte `pip freeze` a vyhledejte `tensorflow` , pokud se nachází, uvedená verze by měla být < 1,13
  3. Pokud uvedená verze není podporovanou verzí, `pip uninstall tensorflow` v příkazovém prostředí a zadejte y pro potvrzení.
  
 * **Spuštění selhalo `jwt.exceptions.DecodeError` s**: přesná chybová zpráva: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

    U verzí <= 1.17.0 sady SDK může instalace způsobit nepodporovanou verzi PyJWT. Ověřte verzi PyJWT v prostředí automatizovaného conda ml. Podporované verze jsou < 2.0.0. Verzi PyJWT můžete ověřit následujícím způsobem:
    1. Spusťte příkazové prostředí, aktivujte prostředí Conda, ve kterém jsou nainstalované automatizované balíčky ml.
    2. Zadejte `pip freeze` a vyhledejte `PyJWT` , pokud se nachází, uvedená verze by měla být < 2.0.0

    Pokud uvedená verze není podporovanou verzí:
    1. Zvažte upgrade na nejnovější verzi sady AutoML SDK: `pip install -U azureml-sdk[automl]` .
    2. Pokud to není životaschopné, odinstalujte PyJWT z prostředí a nainstalujte správnou verzi následujícím způsobem:
        - `pip uninstall PyJWT` v příkazovém prostředí a zadejte `y` pro potvrzení.
        - Nainstalujte pomocí `pip install 'PyJWT<2.0.0'` .

## <a name="next-steps"></a>Další kroky

+ Přečtěte si další informace o [tom, jak a kde model nasadit](how-to-deploy-and-where.md).

+ Přečtěte si další informace o [tom, jak vytvořit regresní model pomocí automatizovaného strojového učení](tutorial-auto-train-models.md) nebo [jak pomocí automatizovaného strojového učení na vzdáleném prostředku vyškolit](how-to-auto-train-remote.md).

+ Naučte se, jak proškolit několik modelů pomocí AutoML v [mnoha akcelerátorech řešení modelů](https://aka.ms/many-models).
