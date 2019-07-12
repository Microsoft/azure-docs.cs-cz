---
title: Trénování a zaregistrujte scikit-další modely
titleSuffix: Azure Machine Learning service
description: V tomto článku se dozvíte, jak trénovat a registrovat scikit-informace model pomocí služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 06/30/2019
ms.custom: seodec18
ms.openlocfilehash: c9e983f7981c1155964617694d2cce86aba741b7
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840022"
---
# <a name="train-and-register-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Trénování a registraci Scikit poučení modely ve velkém měřítku ve službě Azure Machine Learning

Tento článek popisuje, jak pro trénování a zaregistrujte model Scikit informace s použitím služby Azure Machine Learning. Využívá Oblíbené [datovou sadu Iris](https://archive.ics.uci.edu/ml/datasets/iris) ke klasifikaci iris květinu imagí s vlastním [scikit-informace](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) třídy.

Scikit informace je open source výpočetní platforma běžně používá pro machine learning. Pomocí služby Azure Machine Learning můžete rychle škálovat úlohy trénování open source pomocí elastických cloudových výpočetních prostředků. Můžete také sledovat tréninkových spuštění, verze modely nasazení modelů a mnoho dalšího.

Ať už vyvíjíte modelu Scikit poučení ze základů nebo existující model spojili do cloudu, můžete pomocí služby Azure Machine Learning sestavovat modely připravené pro produkční prostředí.

## <a name="prerequisites"></a>Požadavky

Tento kód spusťte v jedné z těchto prostředí:
 - Azure Machine Learning poznámkového bloku virtuálního počítače – žádné soubory ke stažení nebo instalace

    - Dokončení [rychlý start založené na cloudu Poznámkový blok](quickstart-run-cloud-notebook.md) vytvořit poznámkový blok vyhrazený server už načtené pomocí sady SDK a ukázkové úložiště.
    - Ve složce samples na serveru Poznámkový blok, najít poznámkový blok dokončené a rozšířená tak, že přejdete do tohoto adresáře: **postupy-k-použití azureml > Školení > train-hyperparameter-tune-deploy-with-sklearn** složky.

 - Váš vlastní server poznámkového bloku Jupyter

    - [Nainstalujte aplikaci Azure Machine Learning sady SDK pro Python](setup-create-workspace.md#sdk)
    - [Vytvořte konfigurační soubor pracovního prostoru](setup-create-workspace.md#write-a-configuration-file)
    - [Stáhněte si ukázkový soubor skriptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn) `train_iris.py`
    - Můžete také vyhledat dokončené [Poznámkový blok Jupyter verze](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-sklearn.ipynb) tohoto průvodce na stránce ukázky na Githubu. Poznámkový blok obsahuje oddíl rozšířené pokrývající inteligentní hyperparameter ladění a načítání nejlepší model podle primární metriky.

## <a name="set-up-the-experiment"></a>Nastavení testu

Tato část výukového experimentu nastaví načtením balíčky požadované pythonu, inicializuje se pracovní prostor, vytvoření experimentu a nahrávání trénovacích dat a trénovací skripty.

### <a name="import-packages"></a>Import balíčků

Nejprve importujte nezbytné knihovny jazyka Python.

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

### <a name="initialize-a-workspace"></a>Inicializovat pracovní prostor

[Pracovního prostoru služby Azure Machine Learning](concept-workspace.md) je prostředek nejvyšší úrovně služby. To vám poskytne centrální místo, kde můžete pracovat s všechny artefakty, které vytvoříte. V sadě Python SDK, dostanete artefakty pracovního prostoru tak, že vytvoříte [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektu.

Vytvoření objektu z pracovního prostoru `config.json` soubor vytvořený v [části s předpoklady](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Vytvoření experimentu

Vytvoření experimentu a složku pro uložení trénovací skripty. V tomto příkladu vytvoření experimentu nazývá "skriptu sklearn iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Nahrání datové sady a skripty

[Datastore](how-to-access-data.md) je místo, kde můžete data ukládají a získávají připojením nebo kopírování dat do cílového výpočetního prostředí. Každý pracovní prostor poskytuje výchozí úložiště. Nahrání dat a skriptů školení k úložišti dat tak, aby k nim mohou snadno přistupovat během cvičení.

1. Vytvořte adresář pro vaše data.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Nahrajte datovou sadu iris do výchozího úložiště.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Nahrávání Scikit poučení cvičný skript `train_iris.py`.

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>Vytvořit nebo získat cílové výpočetní prostředí

Vytvořte cílové výpočetní prostředí pro vaši úlohu Scikit informace pro spuštění na. Scikit další pouze podporuje jeden uzel, výpočetní využití procesoru.

Následující kód vytvoří spravovat Azure Machine Learning compute (AmlCompute) pro vaše vzdálené školení výpočetních prostředků. Vytvoření AmlCompute trvá přibližně 5 minut. Pokud AmlCompute s tímto názvem již ve vašem pracovním prostoru, přeskočí tento kód v procesu vytváření.

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

Další informace o cílových výpočetních prostředí najdete v článku [co je cílové výpočetní prostředí](concept-compute-target.md) článku.

## <a name="create-a-scikit-learn-estimator"></a>Vytvoření odhaduje se Scikit poučení

[Scikit poučení estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) poskytuje jednoduchý způsob spouštění Scikit informace trénovací úlohu na cílové výpočetní prostředí. Se implementuje pomocí [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) třídy, které lze použít pro podporu vzdělávání procesoru jedním uzlem.

Pokud váš skript školení potřebuje další pip nebo conda balíčky ke spuštění, můžete mít balíčky, které jsou nainstalovány na výsledný image dockeru předáním názvů prostřednictvím `pip_packages` a `conda_packages` argumenty.

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

## <a name="submit-a-run"></a>Odeslat spustit

[Spustit objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) poskytuje rozhraní pro historie spuštění, když úloha běží, a po jejím dokončení.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Jak spustit provádí, prochází následujících fází:

- **Příprava**: Podle TensorFlow odhaduje se vytvoří image dockeru. Na obrázku je odeslat do registru kontejnerů pracovního prostoru a uložená v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spouštění a lze je zobrazit můžete sledovat průběh.

- **Škálování:** Cluster se pokusí vertikálně navýšit kapacitu, pokud cluster Batch AI vyžaduje ke spuštění spustit, než je aktuálně k dispozici více uzlů.

- **Spuštění**: Všechny skripty ve složce script se nahrají do cílové výpočetní prostředí, úložiště dat se připojí, tedy zkopírují a entry_script provádí. Výstupy z výstupu stdout a. / složky protokolů se streamují do historie spouštění a slouží k monitorování spuštění.

- **Následné zpracování**: . / Výstupy, složka spuštění se kopíruje do historie spuštění.

## <a name="save-and-register-the-model"></a>Uložit a zaregistrujte model

Když jsme natrénovali model, můžete uložit a zaregistrujte ho do pracovního prostoru. Registrace modelu vám umožní úložiště a verzí vašich modelů v pracovním prostoru pro zjednodušení [model nasazení a správu](concept-model-management-and-deployment.md).

Přidejte následující kód do skriptu školení, train_iris.py uložit model. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Zaregistrujte model do pracovního prostoru s následujícím kódem.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Další postup

V tomto článku školení a zaregistrován modelu Scikit informace o službě Azure Machine Learning.

* Informace o nasazení modelu najdete dál v našich [model nasazení](how-to-deploy-and-where.md) článku.

* [Vyladění hyperparameters](how-to-tune-hyperparameters.md)

* [Sledovat spustit metriky během cvičení](how-to-track-experiments.md)