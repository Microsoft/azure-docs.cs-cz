---
title: Vyškolit modely strojového učení scikit-learn
titleSuffix: Azure Machine Learning
description: Zjistěte, jak spustit vaše školicí skripty scikit-learn v podnikovém měřítku pomocí třídy odhadu Azure Machine Learning SKlearn. Ukázkové skripty klasifikují obrazy květin iris a vytvářejí model strojového učení založený na datové sadě duhovky scikit-learn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: bdd2cc400c3df75742689258caea8cb87ee8ccc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942255"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Vytvářejte modely scikit-learn ve velkém měřítku pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak spustit vaše školicí skripty scikit-learn v podnikovém měřítku pomocí třídy odhadu Azure Machine Learning [SKlearn.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 

Příklad skripty v tomto článku se používají ke klasifikaci iris květinové obrázky k vytvoření modelu strojového učení založené na scikit-learn [iris datové sady](https://archive.ics.uci.edu/ml/datasets/iris).

Ať už trénujete model scikit-learn pro strojové učení od základu nebo přinášíte do cloudu existující model, můžete pomocí Azure Machine Learning škálovat úlohy školení s otevřeným zdrojovým kódem pomocí elastických cloudových výpočetních prostředků. Pomocí Azure Machine Learning můžete vytvářet, nasazovat, nastavovat a monitorovat produkční modely.

## <a name="prerequisites"></a>Požadavky

Spusťte tento kód v některém z těchto prostředí:
 - Výpočetní instance Azure Machine Learning – není nutné žádné stahování ani instalace

    - Dokončete [kurz: Instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) a vytvořte vyhrazený notebook ový server s předinstalovaným sadou SDK a ukázkovým úložištěm.
    - Ve složce školení ukázky na serveru notebooku, najít dokončené a rozšířené poznámkový blok přechodem do tohoto adresáře: **how-to-use-azureml > ml-frameworks > scikit-learn > školení > vlak-hyperparameter-tune-deploy-with-sklearn** složky.

 - Váš vlastní Jupyter Notebook server

    - [Nainstalujte azure machine learning sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - Stažení souboru dat a ukázkového skriptu 
        - [datová sada duhovky](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Můžete také najít vyplněnou [verzi jupyterského poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) této příručky na stránce ukázek GitHub. Poznámkový blok obsahuje rozšířenou sekci, která zahrnuje inteligentní hyperparametrické ladění a načítání nejlepšího modelu podle primárních metrik.

## <a name="set-up-the-experiment"></a>Nastavení experimentu

Tato část nastaví trénovací experiment načtením požadovaných balíčků pythonu, inicializací pracovního prostoru, vytvořením experimentu a nahráním trénovacích dat a trénovacích skriptů.

### <a name="import-packages"></a>Import balíčků

Nejprve importujte potřebné knihovny Pythonu.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicializaci pracovního prostoru

[Pracovní prostor Azure Machine Learning](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje vám centralizované místo pro práci se všemi artefakty, které vytvoříte. V pythonu SDK můžete přistupovat k artefaktům [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) pracovního prostoru vytvořením objektu.

Vytvořte objekt pracovního `config.json` prostoru ze souboru vytvořeného v [části požadavky](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Vytvoření experimentu se strojovým učením

Vytvořte experiment a složku pro uložení školicích skriptů. V tomto příkladu vytvořte experiment s názvem "sklearn-iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Příprava školicího skriptu

V tomto kurzu je pro vás již k dispozici školicí skript **train_iris.py.** V praxi byste měli být schopni přijmout všechny vlastní trénovací skript, jak je a spustit jej s Azure ML bez nutnosti měnit kód.

Pokud chcete používat možnosti sledování a metrik Azure ML, přidejte do školicího skriptu malé množství kódu Azure ML.  Školicí skript **train_iris.py** ukazuje, jak protokolovat některé `Run` metriky do vašeho Azure ML spustit pomocí objektu ve skriptu.

Poskytnutý trénovací skript používá `iris = datasets.load_iris()` ukázková data z funkce.  Pro vlastní data může být nutné použít kroky, jako je [například nahrát datovou sadu a skripty,](how-to-train-keras.md#data-upload) aby byla data k dispozici během školení.

Zkopírujte školicí skript **train_iris.py** do adresáře projektu.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Vytvoření nebo získání výpočetního cíle

Vytvořte výpočetní cíl pro úlohu scikit-learn, na které se můžete spustit. Scikit-learn podporuje pouze jeden uzel, CPU výpočetní techniky.

Následující kód vytvoří spravované výpočetní prostředky Azure Machine Learning (AmlCompute) pro váš výpočetní prostředek pro vzdálené školení. Vytvoření AmlCompute trvá přibližně 5 minut. Pokud AmlCompute s tímto názvem je již ve vašem pracovním prostoru, tento kód přeskočí proces vytváření.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Další informace o výpočetních cílech najdete v článku, [co je cíl výpočetního cíle.](concept-compute-target.md)

## <a name="create-a-scikit-learn-estimator"></a>Vytvoření odhadu scikit-learn

[Scikit-learn odhad poskytuje](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) jednoduchý způsob, jak zahájit scikit-learn školení práci na výpočetní cíl. Je implementována [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) prostřednictvím třídy, která může být použita pro podporu školení procesoru s jedním uznami.

Pokud váš školicí skript potřebuje další pip nebo conda balíčky ke spuštění, můžete mít `pip_packages` `conda_packages` balíčky nainstalované na výsledné image docker ujeho předávání jejich názvy prostřednictvím a argumenty.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```


Další informace o přizpůsobení prostředí Pythonu najdete v [tématu Vytváření a správa prostředí pro školení a nasazení](how-to-use-environments.md). 

## <a name="submit-a-run"></a>Odeslat běh

Run [Objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) poskytuje rozhraní pro historii spuštění, zatímco úloha je spuštěna a po dokončení.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Při spuštění prochází následujícími fázemi:

- **Příprava**: Obrázek dockeru je vytvořen podle odhadu TensorFlow. Bitová kopie se nahraje do registru kontejnerů pracovního prostoru a uloží do mezipaměti pro pozdější spuštění. Protokoly jsou také přenášeny do historie spuštění a lze je zobrazit za účelem sledování průběhu.

- **Škálování:** Cluster se pokusí vertikálně navýšit kapacitu, pokud cluster Batch AI vyžaduje ke spuštění spuštění více uzlů, než je aktuálně k dispozici.

- **Spuštění**: Všechny skripty ve složce skriptu jsou odeslány do výpočetního cíle, úložiště dat jsou připojena nebo zkopírována a entry_script se spustí. Výstupy ze složky stdout a ./logs jsou přenášeny do historie spuštění a lze je použít ke sledování běhu.

- **Post-Processing**: Složka ./výstupy spuštění je zkopírována do historie spuštění.

## <a name="save-and-register-the-model"></a>Uložení a registrace modelu

Po vyškolení modelu jej můžete uložit a zaregistrovat do pracovního prostoru. Registrace modelu umožňuje ukládat a nakládat s modely v pracovním prostoru a zjednodušit [tak správu a nasazení modelu](concept-model-management-and-deployment.md).

Přidejte následující kód do školicího skriptu, train_iris.py, chcete-li model uložit. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Zaregistrujte model do pracovního prostoru pomocí následujícího kódu. Zadáním parametrů `model_framework`, `model_framework_version`, `resource_configuration`a , bez kódu nasazení modelu bude k dispozici. To umožňuje přímo nasadit model jako webovou službu [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) z registrovaného modelu a objekt definuje výpočetní prostředek pro webovou službu.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Nasazení

Model, který jste právě zaregistrovali, můžete nasadit přesně stejným způsobem jako jakýkoli jiný registrovaný model v Azure Machine Learning, bez ohledu na to, který odhad jste použili pro školení. Postup nasazení obsahuje oddíl o registraci modelů, ale můžete přeskočit přímo k [vytvoření výpočetního cíle](how-to-deploy-and-where.md#choose-a-compute-target) pro nasazení, protože již máte registrovaný model.

### <a name="preview-no-code-model-deployment"></a>(Náhled) Nasazení modelu bez kódu

Namísto tradiční trasy nasazení můžete také použít funkci nasazení bez kódu (náhled) pro scikit-learn. Nasazení modelu bez kódu je podporováno pro všechny předdefinované typy modelů scikit-learn. Registrací modelu, jak je `model_framework`znázorněno výše, pomocí , `model_framework_version`a `resource_configuration` parametry, můžete jednoduše použít statickou [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) funkci k nasazení modelu.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Poznámka: Tyto závislosti jsou zahrnuty v předem sestavené scikit-learn odvození kontejneru.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Úplné [návody na](how-to-deploy-and-where.md) nasazení v Azure Machine Learning do větší hloubky.


## <a name="next-steps"></a>Další kroky

V tomto článku jste vyškolili a zaregistrovali model scikit-learn a dozvěděli jste se o možnostech nasazení. Další informace o Azure Machine Learning najdete v těchto dalších článcích.

* [Sledovat metriky běhu během tréninku](how-to-track-experiments.md)
* [Ladění hyperparametrů](how-to-tune-hyperparameters.md)
* [Referenční architektura pro distribuované školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
