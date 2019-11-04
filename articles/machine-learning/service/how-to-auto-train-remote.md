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
ms.date: 11/04/2019
ms.openlocfilehash: 4276a713e62f96cc5340fc7be0e8391939d32342
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497323"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Výuka modelů pomocí automatizovaného strojového učení v cloudu

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V Azure Machine Learning můžete svůj model vyškolit na různých typech výpočetních prostředků, které spravujete. Cílem výpočtů může být místní počítač nebo prostředek v cloudu.

Experimenty ve strojovém učení můžete snadno škálovat nebo škálovat přidáním dalších výpočetních cílů, například Azure Machine Learning COMPUTE (AmlCompute). AmlCompute je spravovaná výpočetní infrastruktura, která umožňuje snadno vytvořit výpočetní prostředí s jedním uzlem nebo několika uzly.

V tomto článku se dozvíte, jak vytvořit model pomocí automatizovaného ML s AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Jak se vzdálené liší od místních?

V tomto kurzu se naučíte[model klasifikace pomocí automatizovaného strojového učení](tutorial-auto-train-models.md)a naučíte se používat místní počítač k výuce modelu pomocí automatizovaného ml. Pracovní postup, který se lokálně školení týká, se také vztahuje na vzdálené cíle. Při vzdáleném výpočetním prostředí se ale automatizované iterace experimentů provádějí asynchronně. Tato funkce umožňuje zrušit konkrétní iteraci, sledovat stav spuštění nebo pokračovat v práci na jiných buňkách v poznámkovém bloku Jupyter. Chcete-li se naučit vzdáleně, vytvořte nejprve vzdálené výpočetní cíle, jako je AmlCompute. Potom nakonfigurujete vzdálený prostředek a odešlete svůj kód.

V tomto článku se dozvíte o dalších krocích potřebných ke spuštění automatizovaného experimentu ML na vzdáleném AmlCompute cíli. Objekt pracovního prostoru `ws`v tomto kurzu se používá v celém kódu zde.

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

Nyní můžete objekt `compute_target` použít jako cíl vzdáleného výpočtu.

Mezi omezení názvu clusteru patří:
+ Musí být kratší než 64 znaků.
+ Nelze zahrnout žádný z následujících znaků: `\` ~! @ # $% ^ & * () = + _ [] {} \\\\ |; : \' \\", < >/?. `

## <a name="access-data-using-tabulardataset-function"></a>Přístup k datům pomocí funkce TabularDataset

Definovány X a y jako `TabularDataset`s, které jsou předány do automatizovaného ML v AutoMLConfig. `from_delimited_files` ve výchozím nastavení nastaví `infer_column_types` na hodnotu true, která bude automaticky odvodit typ sloupce. 

Pokud chcete ručně nastavit typy sloupců, můžete nastavit argument `set_column_types` tak, aby ručně nastavil typ každého sloupce. V následující ukázce kódu pocházejí data z balíčku skriptu sklearn.

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

Aby byly k dispozici závislosti pro skript get_data. py, definujte objekt `RunConfiguration` s definovaným `CondaDependencies`. Použijte tento objekt pro parametr `run_configuration` v `AutoMLConfig`.

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

Další příklad tohoto návrhového vzoru najdete v tomto [ukázkovém poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) .

## <a name="configure-experiment"></a>Konfigurovat experiment
Zadejte nastavení pro `AutoMLConfig`.  (Podívejte se na [úplný seznam parametrů](how-to-configure-auto-train.md#configure-experiment) a jejich možné hodnoty.)

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

Nastavte volitelný parametr `model_explainability` v konstruktoru `AutoMLConfig`. Kromě toho musí být objekt dataframe ověřování předán jako parametr `X_valid` pro použití funkce vysvětlení modelu.

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

## <a name="submit-training-experiment"></a>Odeslat experiment pro školení

Nyní odešlete konfiguraci pro automatický výběr algoritmu, parametrů technologie Hyper a výukového modelu.

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


## <a name="explore-results"></a>Prozkoumat výsledky

Můžete použít stejnou [pomůcku Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) , jak je znázorněno v [kurzu školení](tutorial-auto-train-models.md#explore-the-results) pro zobrazení grafu a tabulky výsledků.

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

Ve vašem pracovním prostoru jsou k dispozici stejné informace.  Další informace o těchto výsledcích najdete v tématu [vysvětlení výsledků automatizovaného strojového učení](how-to-understand-automated-ml.md).

### <a name="view-logs"></a>Zobrazení protokolů

Vyhledejte v DSVM protokoly v části `/tmp/azureml_run/{iterationid}/azureml-logs`.

## <a name="explain"></a>Nejlepší vysvětlení modelu

Získání podrobných informací o modelech vám umožní zobrazit podrobné informace o modelech a zvýšit tak transparentnost na to, co běží na back-endu. V tomto příkladu můžete spustit vysvětlení modelu pouze pro model nejlépe vyhovující. Pokud spustíte pro všechny modely v kanálu, bude to mít za následek významnou dobu běhu. Informace o vysvětlení modelu zahrnují:

* shap_values: Vysvětlení informací generovaných nástrojem Shap lib.
* expected_values: očekávaná hodnota modelu použitá pro sadu dat X_train.
* overall_summary: hodnoty důležitosti funkcí na úrovni modelu seřazené v sestupném pořadí.
* overall_imp: názvy funkcí seřazené ve stejném pořadí jako v overall_summary.
* per_class_summary: hodnoty důležitosti funkce na úrovni třídy jsou seřazené v sestupném pořadí. K dispozici pouze pro případ klasifikace.
* per_class_imp: názvy funkcí seřazené ve stejném pořadí jako v per_class_summary. K dispozici pouze pro případ klasifikace.

Použijte následující kód k výběru nejlepšího kanálu z iterací. Metoda `get_output` vrátí nejlepší běh a namontovaný model pro poslední vyvolání.

```python
best_run, fitted_model = remote_run.get_output()
```

Naimportujte funkci `retrieve_model_explanation` a spusťte na nejvhodnějším modelu.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

Vytiskněte výsledky `best_run` proměnných vysvětlení, které chcete zobrazit.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

Tisk `best_run` vysvětlení souhrnných proměnných vede k následujícímu výstupu.

![Výstup konzoly pro vysvětlení modelu](./media/how-to-auto-train-remote/expl-print.png)

Můžete také vizualizovat důležitost funkcí prostřednictvím uživatelského rozhraní widgetu nebo ve vašem pracovním prostoru v [Azure Machine Learning Studiu](https://ml.azure.com). 

![Uživatelské rozhraní pro vysvětlení modelu](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Příklad:

Poznámkový blok [How-to-use-AzureML/Automated-Machine-Learning/Remote-amlcompute/auto-ml-Remote-amlcompute. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) ukazuje koncepty v tomto článku.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

Naučte [se konfigurovat nastavení pro automatické školení](how-to-configure-auto-train.md).
