---
title: Výuka modelů strojového učení pomocí scikit
titleSuffix: Azure Machine Learning
description: Naučte se spouštět scikit výukové skripty v podnikovém měřítku pomocí třídy skriptu sklearn Estimator v Azure Machine Learning. Ukázkové skripty klasifikují obrázky v sadě Iris pro sestavení modelu strojového učení na základě datové sady Iris scikit-učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: ec1ea8bac35906969f051a70c44bd6f0685dc942
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489431"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Sestavujte modely scikit s využitím škálování pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak spustit školicí skripty scikit v podnikovém měřítku pomocí třídy [skriptu sklearn Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) v Azure Machine Learning. 

V ukázkových skriptech v tomto článku se používají ke klasifikaci imagí květů Iris k sestavení modelu machine learningu založeného na [datové sadě Iris](https://archive.ics.uci.edu/ml/datasets/iris)scikit-učení.

Bez ohledu na to, jestli provedete výukový model Machine Learning scikit z provozu nebo do cloudu převedete existující model, můžete použít Azure Machine Learning k horizontálnímu navýšení kapacity Open-Source školicích úloh pomocí elastických výpočetních prostředků pro Cloud. Pomocí Azure Machine Learning můžete sestavovat, nasazovat, používat verzi a monitorovat modely produkčního prostředí.

## <a name="prerequisites"></a>Předpoklady

Spusťte tento kód v jednom z těchto prostředí:
 - Azure Machine Learning výpočetní instance – nepotřebujete žádné soubory ke stažení nebo instalaci

    - Dokončete [kurz: instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) pro vytvoření vyhrazeného serveru poznámkového bloku předem načteného se sadou SDK a s ukázkovým úložištěm.
    - Ve složce školení ukázek na serveru pro Poznámkový blok Najděte dokončený a rozbalený Poznámkový blok tak, že přejdete na tento adresář: **How-to->-azureml ml-framework > scikit-učení > školení >** složka.

 - Váš vlastní server Jupyter Notebook

    - [Nainstalujte sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - Stáhnout datovou sadu a ukázkový soubor skriptu 
        - [datová sada Iris](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Dokončenou [Jupyter notebook verzi](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) tohoto průvodce najdete na stránce ukázek na GitHubu. Poznámkový blok obsahuje rozšířený oddíl, který pokrývá inteligentní ladění parametrů a načítá nejlepší model podle primární metriky.

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

[Azure Machine Learning pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte. V sadě Python SDK můžete získat přístup k artefaktům pracovního prostoru vytvořením objektu [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Vytvořte objekt pracovního prostoru ze souboru `config.json` vytvořeného v [části požadavky](#prerequisites).

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

### <a name="prepare-training-script"></a>Příprava školicího skriptu

V tomto kurzu jste si už pro vás poskytli školicí skript **train_iris. py** . V praxi byste měli být schopni vzít libovolný vlastní školicí skript a spustit ho s Azure ML bez nutnosti upravovat kód.

Pokud chcete používat funkce sledování a metrik Azure ML, přidejte do školicího skriptu malý objem kódu Azure ML.  Školicí skript **train_iris. py** ukazuje, jak protokolovat některé metriky do Azure ml běhu pomocí objektu `Run` ve skriptu.

Zadaný školicí skript používá ukázková data z funkce `iris = datasets.load_iris()`.  Pro vlastní data možná budete muset použít kroky, jako je například [nahrát datovou sadu a skripty](how-to-train-keras.md#data-upload) k zpřístupnění dat během školení.

Zkopírujte školicí skript **train_iris. py** do adresáře projektu.

```
import shutil
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

Pokud váš školicí skript potřebuje ke spuštění další balíčky PIP nebo Conda, můžete je nainstalovat ve výsledné imagi Docker předáním jejich názvů prostřednictvím `pip_packages` a `conda_packages`ch argumentů.

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

- **Příprava**: obrázek Docker se vytvoří podle TensorFlow Estimator. Obrázek se nahraje do registru kontejneru v pracovním prostoru a v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spuštění a dají se zobrazit ke sledování průběhu.

- **Škálování**: cluster se pokusí o horizontální navýšení kapacity, pokud Batch AI cluster vyžaduje více uzlů pro spuštění běhu, než je aktuálně k dispozici.

- **Spuštěno**: všechny skripty ve složce skriptu se nahrají do cílového výpočetního prostředí, úložiště dat se připojí nebo zkopírují a entry_script se spustí. Výstupy z stdout a složky./logs se streamují do historie spuštění a dají se použít k monitorování běhu.

- **Následné zpracování**: složka./Outputs se v běhu kopíruje do historie spuštění.

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

## <a name="next-steps"></a>Další kroky


V tomto článku jste si vyškole a zaregistrovali Keras model na Azure Machine Learning. Pokud se chcete dozvědět, jak model nasadit, pokračujte na náš článek nasazení modelu.

> [!div class="nextstepaction"]
> [Jak a kde nasadit modely](how-to-deploy-and-where.md)
* [Sledovat metriky spuštění během školení](how-to-track-experiments.md)
* [Ladit parametry](how-to-tune-hyperparameters.md)
* [Nasazení trained model](how-to-deploy-and-where.md)
* [Referenční architektura distribuovaného školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
