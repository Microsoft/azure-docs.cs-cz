---
title: Automatizované cíle vzdálený výpočetní ML
titleSuffix: Azure Machine Learning service
description: Naučte se sestavovat modely využití automatizovaných strojového učení na Data Science virtuální cílové výpočetní prostředí vzdáleného počítače (DSVM) pomocí služby Azure Machine Learning
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 18b2b3df2748392b12b60517604478b120871754
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256056"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Trénování modelů pomocí automatizovaných strojového učení v cloudu

Ve službě Azure Machine Learning vyzkoušejte svůj model pro různé typy výpočetní prostředky, které spravujete. Cílové výpočetní prostředí může být místní počítač nebo počítač v cloudu.

Můžete snadno vertikálně navýšit kapacitu nebo horizontální navýšení kapacity experimentu s Machine learningem tak, že přidáte další výpočetní cíle. Výpočetní target – možnosti zahrnují založeného na Ubuntu dat virtuálního počítače VĚDY nebo Azure Machine Learning Compute. Datové VĚDY je přizpůsobená image virtuálního počítače v cloudu Azure Microsoftu vytvořená speciálně pro datových věd. Obsahuje mnoho oblíbených datové vědy a dalších nástrojů, funkcí a je předem nakonfigurované.  

V tomto článku se naučíte sestavit model využívající automatizované ML na datové VĚDY.

## <a name="how-does-remote-differ-from-local"></a>Jak se liší vzdálené a místní?

Tento kurz "[Vyškolíme model klasifikace pomocí automatizovaných strojového učení](tutorial-auto-train-models.md)" vás naučí, jak použijete k natrénování modelu pomocí automatizovaných ML místního počítače.  Pracovní postup, když místně školení platí také pro i vzdálených cílů. Však s vzdálený výpočetní automatizované iterací experimentů v ML jsou spouštěny asynchronně. Tato funkce umožňuje zrušit konkrétní iteraci, podívejte se na stav provádění nebo pokračovat v práci na ostatní buňky v poznámkovém bloku Jupyter. K trénování vzdáleně, je nejprve vytvořit cílové vzdálené výpočetní prostředí jako je například DSVM Azure.  Potom nakonfigurujte vzdáleného prostředku a odeslání kódu existuje.

Tento článek popisuje další kroky potřebné ke spuštění na vzdáleném DSVM automatizované experimentu ML.  Objekt workspace `ws`, v tomto kurzu se používá v rámci sem kód.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Vytvořit prostředek

Vytvoření datové VĚDY v pracovním prostoru (`ws`) Pokud ještě neexistuje. Pokud byla dříve vytvořena datové VĚDY, tento kód přeskočí v procesu vytváření a načte existující podrobnosti prostředku do `dsvm_compute` objektu.  

**Časový odhad**: Vytvoření virtuálního počítače trvá přibližně 5 minut.

```python
from azureml.core.compute import DsvmCompute

dsvm_name = 'mydsvm' #Name your DSVM
try:
    dsvm_compute = DsvmCompute(ws, dsvm_name)
    print('found existing dsvm.')
except:
    print('creating new dsvm.')
    # Below is using a VM of SKU Standard_D2_v2 which is 2 core machine. You can check Azure virtual machines documentation for additional SKUs of VMs.
    dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
    dsvm_compute = DsvmCompute.create(ws, name = dsvm_name, provisioning_configuration = dsvm_config)
    dsvm_compute.wait_for_completion(show_output = True)
```

Teď můžete použít `dsvm_compute` jako vzdálený výpočetní cílový objekt.

Omezení přístupu názvem DSVM patří:
+ Musí být kratší než 64 znaků.  
+ Nesmí obsahovat žádný z následujících znaků: `\` ~! @ # $ % ^ & * () = + _ [] {} \\ \\ |;: \' \\", < > /?. `

>[!Warning]
>Pokud se vytvoření nezdaří a zobrazí se zpráva o způsobilosti nákupní Marketplace:
>    1. Přejděte na web [Azure Portal](https://portal.azure.com).
>    1. Začněte vytvářet DSVM 
>    1. Vyberte "Chcete vytvořit prostřednictvím kódu programu" Povolit programové vytváření
>    1. Ukončíte bez vytvoření virtuálního počítače
>    1. Znovu spustit kód pro vytváření.

Tento kód nevytváří uživatelské jméno nebo heslo pro datové VĚDY, pro kterého je zřízené. Pokud se chcete připojit přímo k virtuálnímu počítači, přejděte [webu Azure portal](https://portal.azure.com) Vytvořte přihlašovací údaje.  

### <a name="attach-existing-linux-dsvm"></a>Připojit existující DSVM Linux

Můžete také připojit existující DSVM Linux jako cílové výpočetní prostředí. Tento příklad využívá existující DSVM, ale nevytvoří nový prostředek.

> [!NOTE]
>
> Následující kód používá `RemoteCompute` cílové třídy připojit existující virtuální počítač jako vaše cílové výpočetní prostředí.
> `DsvmCompute` Třídy se přestanou používat v budoucích vydaných verzích a místo toho použití tohoto vzoru návrhu.

Spusťte následující kód k vytvoření cílové výpočetní prostředí z již existujících DSVM Linux.

```python
from azureml.core.compute import ComputeTarget, RemoteCompute 

attach_config = RemoteCompute.attach_configuration(username='<username>',
                                                   address='<ip_adress_or_fqdn>',
                                                   ssh_port=22,
                                                   private_key_file='./.ssh/id_rsa')
compute_target = ComputeTarget.attach(workspace=ws,
                                      name='attached_vm',
                                      attach_configuration=attach_config)

compute_target.wait_for_completion(show_output=True)
```

Teď můžete použít `compute_target` jako vzdálený výpočetní cílový objekt.

## <a name="access-data-using-getdata-file"></a>Přístup k datům pomocí souboru get_data

Poskytnutí přístupu vzdálený prostředek na trénovací data. Pro automatizované strojovým učením běžící na vzdálené výpočetní prostředky, data, musí být načtena pomocí `get_data()` funkce.  

Pokud chcete poskytnout přístup, musíte mít:
+ Vytvořit soubor obsahující get_data.py `get_data()` – funkce 
* Umístit tento soubor v adresáři dostupné jako absolutní cestu 

Kód pro čtení dat z úložiště objektů blob nebo místní disk v souboru get_data.py může zapouzdřit. V následující ukázce kódu data pocházejí z balíčku skriptu sklearn.

>[!Warning]
>Pokud používáte vzdálený výpočetní, pak je nutné použít `get_data()` kde se provádí transformaci dat. Pokud je potřeba nainstalovat další knihovny pro transformace dat v rámci get_data(), existují další kroky, kterými se můžete řídit. Odkazovat [auto-ml přípravy ukázkový poznámkový blok](https://aka.ms/aml-auto-ml-data-prep ) podrobnosti.


```python
# Create a project_folder if it doesn't exist
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

#Write the get_data file.
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-experiment"></a>Konfigurace testu

Zadejte nastavení pro `AutoMLConfig`.  (Viz [úplný seznam parametrů](how-to-configure-auto-train.md#configure-experiment) a jejich možných hodnot.)

V nastavení `run_configuration` nastavena `run_config` objektu, který obsahuje nastavení a konfigurace pro datové VĚDY.  

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
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                            )
```

### <a name="enable-model-explanations"></a>Povolit vysvětlení modelu

Nastavit volitelný `model_explainability` parametr `AutoMLConfig` konstruktoru. Kromě toho objekt datového rámce ověření musí být předán jako parametr `X_valid` používat funkci explainability modelu.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                             model_explainability=True,
                             X_valid = X_test
                            )
```

## <a name="submit-training-experiment"></a>Odeslat výukového experimentu

Teď odešlete konfigurace k automatickému výběru algoritmus, hyper parametry a trénování modelu.

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
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

Můžete použít stejné widgetu Jupyter jako v [školicí kurz](tutorial-auto-train-models.md#explore-the-results) graf a tabulka výsledků.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```
Tady je statický obrázek pomůcky.  V poznámkovém bloku můžete kliknutím na kterýkoli řádek v tabulce najdete ve vlastnostech spuštění a výstupních protokolů pro spuštění.   Rozevírací seznam nad grafem můžete také použít k zobrazení grafu jednotlivé dostupné metriky pro každou iteraci.

![tabulka pomůcky](./media/how-to-auto-train-remote/table.png)
![diagram pomůcky](./media/how-to-auto-train-remote/plot.png)

Ve widgetu zobrazí adresu URL můžete zobrazit a prozkoumat podrobnosti o spuštění jednotlivých.
 
### <a name="view-logs"></a>Zobrazení protokolů

Najít protokoly na datové VĚDY v rámci `/tmp/azureml_run/{iterationid}/azureml-logs`.

## <a name="best-model-explanation"></a>Vysvětlení nejlepší model

Načítání modelu vysvětlení dat umožňuje zobrazit podrobné informace o modelech pro zvýšení transparentnosti do co běží na back endu. V tomto příkladu spustíte modelu vysvětlení pouze pro nejlepší přizpůsobení modelu. Při spuštění pro všechny modely v kanálu, způsobí spoustu času spuštění. Obsahuje informace o modelu vysvětlení:

* shap_values: Vysvětlení informací generovaných okno lib
* expected_values: Očekávaná hodnota použitý k nastavení X_train dat modelu.
* overall_summary: Hodnoty význam úroveň funkce modelu seřazeny v sestupném pořadí
* overall: Názvy funkcí seřazeny ve stejném pořadí jako overall_summary
* per_class_summary: Na úrovni funkce význam hodnoty třídy seřazeny v sestupném pořadí. K dispozici jenom pro případ klasifikace
* per_class: Názvy funkcí seřazeny ve stejném pořadí jako per_class_summary. K dispozici jenom pro případ klasifikace

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

## <a name="example"></a>Příklad:

[How-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb) Poznámkový blok ukazuje koncepty v tomto článku. 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup

Přečtěte si [jak nakonfigurovat nastavení pro automatické školení](how-to-configure-auto-train.md).
