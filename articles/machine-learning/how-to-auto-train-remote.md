---
title: Automatizované vzdálené výpočetní cíle pro automatizované na ML
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet modely pomocí automatizovaného strojového učení na Azure Machine Learning vzdálených výpočetních cílů s Azure Machine Learning
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.custom: how-to, automl
ms.date: 03/09/2020
ms.openlocfilehash: d6d21acc685ba840b585ada43e59230fdd73787f
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796376"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Trénování modelů pomocí automatizovaného strojového učení v cloudu



V Azure Machine Learning můžete svůj model vyškolit na různých typech výpočetních prostředků, které spravujete. Cílem výpočtů může být místní počítač nebo prostředek v cloudu.

Experimenty ve strojovém učení můžete snadno škálovat nebo škálovat přidáním dalších výpočetních cílů, například Azure Machine Learning COMPUTE (AmlCompute). AmlCompute je spravovaná výpočetní infrastruktura, která umožňuje snadno vytvářet výpočetní prostředky s jedním nebo několika uzly.

V tomto článku se dozvíte, jak vytvořit model pomocí automatizovaného ML s AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Jak se vzdálené liší od místních?

K dispozici jsou další funkce při použití vzdáleného výpočetního cíle.  Další podrobnosti najdete v tématu [místní a vzdálené výpočetní cíle](concept-automated-ml.md#local-remote).

V tomto kurzu se naučíte[model klasifikace pomocí automatizovaného strojového učení](tutorial-auto-train-models.md)a naučíte se používat místní počítač k výuce modelu pomocí automatizovaného ml. Pracovní postup, který se lokálně školení týká, se také vztahuje na vzdálené cíle. Chcete-li se naučit vzdáleně, vytvořte nejprve vzdálené výpočetní cíle, jako je AmlCompute. Potom nakonfigurujete vzdálený prostředek a odešlete svůj kód.

V tomto článku se dozvíte o dalších krocích potřebných ke spuštění automatizovaného experimentu ML na vzdáleném AmlCompute cíli. Objekt pracovního prostoru `ws` z tohoto kurzu se používá v celém kódu zde.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Vytvoření prostředku

Vytvořte [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?preserve-view=true&view=azure-ml-py) cíl v pracovním prostoru ( `ws` ), pokud ještě neexistuje.

**Časový odhad**: Vytvoření cíle AmlCompute trvá přibližně 5 minut.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Nyní můžete `compute_target` objekt použít jako cíl vzdáleného výpočtu.

Mezi omezení názvu clusteru patří:
+ Musí být kratší než 64 znaků.
+ Nelze zahrnout žádný z následujících znaků: `\` ~! @ # $% ^ & * () = + _ [] {} \\ \\ |;: \' \\ ",  < > /?. `

## <a name="access-data-using-tabulardataset-function"></a>Přístup k datům pomocí funkce TabularDataset

Definováno training_data jako [`TabularDataset`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) a popisek, který se předává do automatizovaného ml v [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py) . `TabularDataset`Metoda `from_delimited_files` ve výchozím nastavení nastaví na `infer_column_types` hodnotu true, která bude automaticky odvodit typ sloupce. 

Pokud chcete ručně nastavit typy sloupců, můžete nastavit `set_column_types` argument tak, aby ručně nastavil typ každého sloupce. V následující ukázce kódu pocházejí data z balíčku skriptu sklearn.

```python
from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd
import os

# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists('project_folder'):
    os.makedirs('project_folder')

X = pd.DataFrame(data_train.data[100:,:])
y = pd.DataFrame(data_train.target[100:])

# merge X and y
label = "digit"
X[label] = y

training_data = X

training_data.to_csv('data/digits.csv')
ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

training_data = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/digits.csv'))
```

## <a name="configure-experiment"></a>Konfigurovat experiment
Zadejte nastavení pro `AutoMLConfig` .  (Podívejte se na [úplný seznam parametrů](how-to-configure-auto-train.md#configure-experiment) a jejich možné hodnoty.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "experiment_timeout_minutes" : 20,
    "enable_early_stopping" : True,
    "iteration_timeout_minutes": 10,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "max_concurrent_iterations": 10,
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             training_data=training_data,
                             label_column_name=label,
                             **automl_settings,
                             )
```

## <a name="submit-training-experiment"></a>Odeslat experiment pro školení

Nyní odešlete konfiguraci pro automatický výběr algoritmu, parametrů technologie Hyper a výukového modelu.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'Tutorial-automl-remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Zobrazí se výstup podobný následujícímu příkladu:

```output
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
```

## <a name="explore-results"></a>Prozkoumat výsledky

Můžete použít stejnou [pomůcku Jupyter](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) , jak je znázorněno v [kurzu školení](tutorial-auto-train-models.md#explore-the-results) pro zobrazení grafu a tabulky výsledků.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Tady je statický obrázek pomůcky.  V poznámkovém bloku můžete kliknutím na libovolný řádek v tabulce zobrazit vlastnosti spuštění a výstupní protokoly pro daný běh.   K zobrazení grafu každé dostupné metriky pro každou iteraci můžete použít také rozevírací seznam nad grafem.

![tabulka pomůcky](./media/how-to-auto-train-remote/table.png)
![diagram pomůcky](./media/how-to-auto-train-remote/plot.png)

Widget zobrazí adresu URL, pomocí které můžete zobrazit a prozkoumat jednotlivé podrobnosti o spuštění.  

Pokud nejste v Jupyter poznámkovém bloku, můžete zobrazit adresu URL z samotného spuštění:

```
remote_run.get_portal_url()
```

Ve vašem pracovním prostoru jsou k dispozici stejné informace.  Další informace o těchto výsledcích najdete v tématu [vyhodnocení výsledků automatizovaného strojového učení](how-to-understand-automated-ml.md).

## <a name="example"></a>Příklad

Následující [Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb) znázorňuje koncepty v tomto článku.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

* Naučte [se konfigurovat nastavení pro automatické školení](how-to-configure-auto-train.md).
* Další informace [najdete v tématu](how-to-machine-learning-interpretability-automl.md) povolení funkcí interpretace modelu v rámci automatizovaných experimentů ml.