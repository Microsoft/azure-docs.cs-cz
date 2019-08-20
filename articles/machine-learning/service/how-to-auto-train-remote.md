---
title: Automatizované vzdálené výpočetní cíle pro automatizované na ML
titleSuffix: Azure Machine Learning service
description: Naučte se vytvářet modely pomocí automatizovaného strojového učení na Azure Machine Learning vzdálených výpočetních cílů pomocí služby Azure Machine Learning
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 7/12/2019
ms.openlocfilehash: 3c3205b64803ac4ee67997ef546ffd64c89f23b4
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624830"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Trénování modelů pomocí automatizovaných strojového učení v cloudu

Ve službě Azure Machine Learning vyzkoušejte svůj model pro různé typy výpočetní prostředky, které spravujete. Cílem výpočtů může být místní počítač nebo prostředek v cloudu.

Experimenty ve strojovém učení můžete snadno škálovat nebo škálovat přidáním dalších výpočetních cílů, například Azure Machine Learning COMPUTE (AmlCompute). AmlCompute je spravovaná výpočetní infrastruktura, která umožňuje snadno vytvořit výpočetní prostředí s jedním uzlem nebo několika uzly.

V tomto článku se dozvíte, jak vytvořit model pomocí automatizovaného ML s AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Jak se liší vzdálené a místní?

V tomto kurzu se naučíte[model klasifikace pomocí automatizovaného strojového učení](tutorial-auto-train-models.md)a naučíte se používat místní počítač k výuce modelu pomocí automatizovaného ml. Pracovní postup, když místně školení platí také pro i vzdálených cílů. Však s vzdálený výpočetní automatizované iterací experimentů v ML jsou spouštěny asynchronně. Tato funkce umožňuje zrušit konkrétní iteraci, podívejte se na stav provádění nebo pokračovat v práci na ostatní buňky v poznámkovém bloku Jupyter. Chcete-li se naučit vzdáleně, vytvořte nejprve vzdálené výpočetní cíle, jako je AmlCompute. Potom nakonfigurujte vzdáleného prostředku a odeslání kódu existuje.

V tomto článku se dozvíte o dalších krocích potřebných ke spuštění automatizovaného experimentu ML na vzdáleném AmlCompute cíli. Objekt workspace `ws`, v tomto kurzu se používá v rámci sem kód.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Vytvořit prostředek

Vytvořte cíl AmlCompute v pracovním prostoru (`ws`), pokud ještě neexistuje.

**Časový odhad**: Vytvoření cíle AmlCompute trvá přibližně 5 minut.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl"  # Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            # for GPU, use "STANDARD_NC6"
                                                            # vm_priority = 'lowpriority', # optional
                                                            max_nodes=6)
compute_target = ComputeTarget.create(
    ws, amlcompute_cluster_name, provisioning_config)

# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(
    show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Teď můžete použít `compute_target` jako vzdálený výpočetní cílový objekt.

Mezi omezení názvu clusteru patří:
+ Musí být kratší než 64 znaků.
+ Nesmí obsahovat žádný z následujících znaků: `\` ~! @ # $ % ^ & * () = + _ [] {} \\ \\ |;: \' \\", < > /?. `

## <a name="access-data-using-tabulardataset-function"></a>Přístup k datům pomocí funkce TabularDataset

Definovány X a y jako `TabularDataset`s, které jsou předány do automatizovaného ml v AutoMLConfig. `from_delimited_files`ve výchozím nastavení nastaví `infer_column_types` hodnotu true, která bude automaticky odvodit typ sloupce. 

Pokud chcete ručně nastavit typy sloupců, můžete nastavit `set_column_types` argument tak, aby ručně nastavil typ každého sloupce. V následující ukázce kódu data pocházejí z balíčku skriptu sklearn.

```python
# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd

data_train = datasets.load_digits()

pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

X = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/X_train.csv'))
y = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/y_train.csv'))

```

## <a name="create-run-configuration"></a>Vytvořit konfiguraci spuštění

Aby byly k dispozici závislosti pro skript get_data. py, definujte `RunConfiguration` objekt, který `CondaDependencies`je definován. Použijte tento objekt pro `run_configuration` parametr v. `AutoMLConfig`

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config = RunConfiguration(framework="python")
run_config.target = compute_target
run_config.environment.docker.enabled = True
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

dependencies = CondaDependencies.create(
    pip_packages=["scikit-learn", "scipy", "numpy"])
run_config.environment.python.conda_dependencies = dependencies
```

Další příklad tohoto návrhového vzoru najdete v tomto ukázkovém poznámkovém [bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) .

## <a name="configure-experiment"></a>Konfigurace testu
Zadejte nastavení pro `AutoMLConfig`.  (Viz [úplný seznam parametrů](how-to-configure-auto-train.md#configure-experiment) a jejich možných hodnot.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             )
```

### <a name="enable-model-explanations"></a>Povolit vysvětlení modelu

Nastavit volitelný `model_explainability` parametr `AutoMLConfig` konstruktoru. Kromě toho objekt datového rámce ověření musí být předán jako parametr `X_valid` používat funkci explainability modelu.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             model_explainability=True,
                             X_valid=X_test
                             )
```

## <a name="submit-training-experiment"></a>Odeslat výukového experimentu

Teď odešlete konfigurace k automatickému výběru algoritmus, hyper parametry a trénování modelu.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Zobrazí se výstup podobný následujícímu příkladu:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************

     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>Prozkoumejte výsledky

Můžete použít stejnou pomůcku [Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) , jak je znázorněno v [kurzu školení](tutorial-auto-train-models.md#explore-the-results) pro zobrazení grafu a tabulky výsledků.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Tady je statický obrázek pomůcky.  V poznámkovém bloku můžete kliknutím na kterýkoli řádek v tabulce najdete ve vlastnostech spuštění a výstupních protokolů pro spuštění.   Rozevírací seznam nad grafem můžete také použít k zobrazení grafu jednotlivé dostupné metriky pro každou iteraci.

![tabulka pomůcky](./media/how-to-auto-train-remote/table.png)
![diagram pomůcky](./media/how-to-auto-train-remote/plot.png)

Ve widgetu zobrazí adresu URL můžete zobrazit a prozkoumat podrobnosti o spuštění jednotlivých.  

Pokud nejste v Jupyter poznámkovém bloku, můžete zobrazit adresu URL z samotného spuštění:

```
remote_run.get_portal_url()
```

Ve vašem pracovním prostoru jsou k dispozici stejné informace.  Další informace o těchto výsledcích najdete v tématu [vysvětlení výsledků automatizovaného strojového učení](how-to-understand-automated-ml.md).

### <a name="view-logs"></a>Zobrazení protokolů

Najít protokoly na datové VĚDY v rámci `/tmp/azureml_run/{iterationid}/azureml-logs`.

## <a name="explain"></a>Nejlepší vysvětlení modelu

Načítání modelu vysvětlení dat umožňuje zobrazit podrobné informace o modelech pro zvýšení transparentnosti do co běží na back endu. V tomto příkladu spustíte modelu vysvětlení pouze pro nejlepší přizpůsobení modelu. Při spuštění pro všechny modely v kanálu, způsobí spoustu času spuštění. Obsahuje informace o modelu vysvětlení:

* shap_values: Vysvětlení informací vygenerovaných Shap lib.
* expected_values: Očekávaná hodnota modelu použitá pro sadu X_train dat.
* overall_summary: Hodnoty důležitosti funkce na úrovni modelu seřazené v sestupném pořadí.
* overall_imp: Názvy funkcí seřazené ve stejném pořadí jako v overall_summary.
* per_class_summary: Hodnoty důležitosti funkcí na úrovni třídy jsou seřazené v sestupném pořadí. K dispozici pouze pro případ klasifikace.
* per_class_imp: Názvy funkcí seřazené ve stejném pořadí jako v per_class_summary. K dispozici pouze pro případ klasifikace.

Chcete-li vybrat nejlepší kanál ze své iterace použijte následující kód. `get_output` Metoda vrátí nejlepší spuštění a vybavené model pro poslední přizpůsobit vyvolání.

```python
best_run, fitted_model = remote_run.get_output()
```

Import `retrieve_model_explanation` funkci a spustit na nejlepší model.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

Tisk výsledků `best_run` vysvětlení proměnných, které chcete zobrazit.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

Tisk `best_run` vysvětlení souhrnu proměnné výsledkem následující výstup.

![Výstup na konzole explainability modelu](./media/how-to-auto-train-remote/expl-print.png)

Funkce význam prostřednictvím widgetu uživatelského rozhraní, jakož i ve webovém uživatelském rozhraní na webu Azure portal můžete také vizualizovat ve vašem pracovním prostoru.

![Model explainability uživatelského rozhraní](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Příklad

Poznámkový blok [How-to-use-AzureML/Automated-Machine-Learning/Remote-amlcompute/auto-ml-Remote-amlcompute. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) ukazuje koncepty v tomto článku.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup

Přečtěte si [jak nakonfigurovat nastavení pro automatické školení](how-to-configure-auto-train.md).
