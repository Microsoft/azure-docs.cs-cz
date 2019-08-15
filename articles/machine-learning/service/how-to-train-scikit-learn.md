---
title: Výuka modelů strojového učení pomocí scikit
titleSuffix: Azure Machine Learning service
description: Naučte se spouštět scikit výukové skripty v podnikovém měřítku pomocí třídy skriptu sklearn Estimator v Azure Machine Learning. Ukázkové skripty klasifikují obrázky v sadě Iris pro sestavení modelu strojového učení na základě datové sady Iris scikit-učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: 5c44f4ed5cd6488520bf6dd99f502f33049b9b17
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963003"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Sestavování scikitch modelů s využitím škálování pomocí služby Azure Machine Learning

V tomto článku se dozvíte, jak spustit školicí skripty scikit v podnikovém měřítku pomocí třídy [skriptu sklearn Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) v Azure Machine Learning. 

V ukázkových skriptech v tomto článku se používají ke klasifikaci imagí květů Iris k sestavení modelu machine learningu založeného na [datové sadě Iris](https://archive.ics.uci.edu/ml/datasets/iris)scikit-učení.

Bez ohledu na to, jestli provedete výukový model Machine Learning scikit z provozu nebo do cloudu převedete existující model, můžete použít Azure Machine Learning k horizontálnímu navýšení kapacity Open-Source školicích úloh pomocí elastických výpočetních prostředků pro Cloud. Pomocí Azure Machine Learning můžete sestavovat, nasazovat, používat verzi a monitorovat modely produkčního prostředí.

## <a name="prerequisites"></a>Požadavky

Spusťte tento kód v jednom z těchto prostředí:
 - Virtuální počítač s poznámkovým blokem Azure Machine Learning – nemusíte stahovat nebo instalovat

    - Dokončete [kurz: Nastavte prostředí a pracovní](tutorial-1st-experiment-sdk-setup.md) prostor pro vytvoření vyhrazeného serveru poznámkového bloku předem načteného pomocí sady SDK a ukázkového úložiště.
    - Ve složce školení ukázek na serveru poznámkového bloku najděte dokončený a rozbalený Poznámkový blok tak, že přejdete do tohoto adresáře: **How-to-use-azureml > training > výukový-skriptu sklearn-Intune-Deploy-with-** složka.

 - Váš vlastní server Jupyter Notebook

    - [Nainstalujte sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - Stáhnout datovou sadu a ukázkový soubor skriptu 
        - [datová sada Iris](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Dokončenou [Jupyter notebook verzi](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) tohoto průvodce najdete na stránce ukázek na GitHubu. Poznámkový blok obsahuje rozšířený oddíl, který pokrývá inteligentní ladění parametrů a načítá nejlepší model podle primární metriky.

## <a name="set-up-the-experiment"></a>Nastavení experimentu

Tato část nastavuje experiment pro školení načtením požadovaných balíčků Pythonu, inicializací pracovního prostoru, vytvořením experimentu a nahráním školicích dat a školicích skriptů.

### <a name="import-packages"></a>Import balíčků

Nejdřív importujte nezbytné knihovny Pythonu.

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

[Pracovní prostor služby Azure Machine Learning](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte. V sadě Python SDK máte přístup k artefaktům pracovního prostoru vytvořením [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektu.

Vytvořte objekt pracovního prostoru ze `config.json` souboru vytvořeného v [části požadavky](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Vytvoření experimentu ve strojovém učení

Vytvořte experiment a složku, do které se budou ukládat skripty pro školení. V tomto příkladu vytvořte experiment nazvaný "skriptu sklearn-Iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Nahrát datovou sadu a skripty

[Úložiště](how-to-access-data.md) dat je místo, kde se můžou data ukládat a přistupovat jejich připojením k cíli výpočtů nebo jejich zkopírováním. Každý pracovní prostor poskytuje výchozí úložiště dat. Nahrajte data a školicí skripty do úložiště dat, aby k nim bylo možné snadno přistup během školení.

1. Vytvořte adresář pro vaše data.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Nahrajte datovou sadu Iris do výchozího úložiště dat.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Nahrajte školicí skript `train_iris.py`scikit-učení.

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>Vytvořit nebo získat cíl výpočtů

Vytvořte výpočetní cíl pro úlohu scikit-učení, na které se má spustit. Scikit – informace podporují jenom jeden uzel a výpočetní výkon procesoru.

Následující kód vytvoří Azure Machine Learning spravované COMPUTE (AmlCompute) pro výpočetní prostředek vzdáleného školení. Vytváření AmlCompute trvá přibližně 5 minut. Pokud je AmlCompute s tímto názvem již ve vašem pracovním prostoru, tento kód přeskočí proces vytváření.

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

Další informace o výpočetních cílech najdete v článku [co je cílový výpočetní cíl](concept-compute-target.md) .

## <a name="create-a-scikit-learn-estimator"></a>Vytvoření scikit-učit Estimator

[Scikit-Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) poskytuje jednoduchý způsob, jak spustit výukovou úlohu s scikitmi postupy na výpočetním cíli. Je implementována prostřednictvím [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) třídy, kterou lze použít k podpoře školení procesoru s jedním uzlem.

Pokud váš školicí skript potřebuje ke spuštění další balíčky PIP nebo Conda, můžete mít balíčky nainstalované ve výsledné imagi Docker tím, že předáte jejich názvy pomocí `pip_packages` argumentů a. `conda_packages`

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

## <a name="submit-a-run"></a>Odeslat běh

[Objekt Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) poskytuje rozhraní k historii spuštění, když je úloha spuštěná a po jejím dokončení.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Po spuštění se spustí v následujících fázích:

- Připravuje se: Obrázek Docker se vytvoří podle TensorFlow Estimator. Obrázek se nahraje do registru kontejneru v pracovním prostoru a v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spuštění a dají se zobrazit ke sledování průběhu.

- **Škálování:** Cluster se pokusí o horizontální navýšení kapacity, pokud Batch AI cluster vyžaduje více uzlů pro spuštění běhu, než je aktuálně k dispozici.

- **Spuštěno**: Všechny skripty ve složce skriptu se nahrají do cílového výpočetního prostředí, úložiště dat se připojí nebo zkopírují a entry_script se spustí. Výstupy z stdout a složky./logs se streamují do historie spuštění a dají se použít k monitorování běhu.

- **Následné zpracování**: Složka s příponou./Outputs se zkopíruje do historie spuštění.

## <a name="save-and-register-the-model"></a>Uložení a registrace modelu

Po proškolení modelu ho můžete uložit a zaregistrovat do svého pracovního prostoru. Registrace modelu umožňuje ukládat a modelovat vaše modely do svého pracovního prostoru, aby bylo možné zjednodušit [správu modelů a nasazení](concept-model-management-and-deployment.md).

Přidáním následujícího kódu do školicího skriptu, train_iris. py, uložte model. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Zaregistrujte model do svého pracovního prostoru pomocí následujícího kódu.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Další postup


V tomto článku jste si vyškole a zaregistrovali model Keras ve službě Azure Machine Learning. Pokud se chcete dozvědět, jak model nasadit, pokračujte na náš článek nasazení modelu.

> [!div class="nextstepaction"]
> [Jak a kde nasadit modely](how-to-deploy-and-where.md)
* [Sledovat spustit metriky během cvičení](how-to-track-experiments.md)
* [Vyladění hyperparameters](how-to-tune-hyperparameters.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)
* [Referenční architektura distribuovaného školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
