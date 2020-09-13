---
title: Odeslání školicího běhu do cíle výpočetní technologie
titleSuffix: Azure Machine Learning
description: Naučte svůj model strojového učení v různých školicích prostředích (cíle výpočtů). Mezi školicími prostředími můžete snadno přepínat. Spusťte školení místně. Pokud potřebujete horizontální navýšení kapacity, přepněte na cloudový cíl výpočtů.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: da48b593b8f645566b2f9775fabc5d8e62e625b6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661569"
---
# <a name="submit-a-training-run-to-a-compute-target"></a>Odeslání školicího běhu do cíle výpočetní technologie

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak pomocí různých školicích prostředí ([cílů výpočtů](concept-compute-target.md)) naučit svůj model Machine Learning.

Po školení je běžné spustit na místním počítači a později spustit tento školicí skript na jiném cílovém výpočetním prostředí. Pomocí Azure Machine Learning můžete skript spustit na různých výpočetních cílech, aniž byste museli měnit školicí skript.

Vše, co potřebujete udělat, je definovat prostředí pro každý cíl výpočtů v rámci **Konfigurace spuštění skriptu**.  Pak, pokud chcete spustit experiment pro školení na jiném cílovém výpočetním prostředí, zadejte konfiguraci spuštění pro výpočetní výkon.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes
* [Sada SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* [Azure Machine Learning pracovní prostor](how-to-manage-workspace.md),`ws`
* Cíl výpočtů, `my_compute_target` .  Vytvořte cíl výpočtů pomocí:
  * [Python SDK](how-to-create-attach-compute-sdk.md) 
  * [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Co je konfigurace spuštění skriptu?

Odešlete experiment školení s objektem [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) .  Tento objekt obsahuje:

* **source_directory**: zdrojový adresář, který obsahuje školicí skript.
* **skript**: identifikace školicího skriptu
* **run_config**: [Konfigurace spuštění](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true), která zase definuje, kde se bude probíhat školení. V rámci `run_config` zadáte cílový výpočetní výkon a prostředí, které se má použít při spuštění školicího skriptu.  

## <a name="whats-an-environment"></a>Co je prostředí?

Azure Machine Learning [prostředí](concept-environments.md) jsou zapouzdření prostředí, ve kterém se vaše školení ve službě Machine Learning stane. Určují balíčky Pythonu, proměnné prostředí a nastavení softwaru kolem vašich školicích a vyhodnocovacích skriptů. Určují také časy spuštění (Python, Spark nebo Docker).  

Prostředí jsou specifikována v  `run_config` objektu uvnitř `ScriptRunConfig` .

## <a name="train-your-model"></a><a id="submit"></a>Trénování modelu

Vzor kódu pro odeslání školicího běhu je stejný pro všechny typy výpočetních cílů:

1. Vytvoření experimentu ke spuštění
1. Vytvořte prostředí, ve kterém se skript spustí.
1. Vytvoření konfigurace spuštění skriptu, která odkazuje na cíl a prostředí výpočtů
1. Odeslat běh
1. Počkat na dokončení běhu

Nebo můžete:

* Odešlete experiment s `Estimator` objektem, jak je znázorněno v [modelech vlak ml pomocí odhady](how-to-train-ml-models.md).
* Odešlete HyperDrive spuštění pro [ladění pomocí parametrů](how-to-tune-hyperparameters.md).
* Odešlete experiment prostřednictvím [rozšíření vs Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Vytvoření experimentu

Vytvořte experiment v pracovním prostoru.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'

experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="create-an-environment"></a>Vytvoření prostředí

Podaná prostředí obsahují kolekce balíčků Pythonu, které jsou ve výchozím nastavení k dispozici ve vašem pracovním prostoru. Tato prostředí se zálohují pomocí imagí Docker uložených v mezipaměti, což snižuje náklady na přípravu na spuštění. Pro vzdálený cíl výpočetní služby můžete použít některé z těchto oblíbených prostředí, která zahájí:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
my_environment = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Další informace a podrobnosti o prostředích najdete v tématu [vytvoření & použití softwarových prostředí v Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a>Místní cíl výpočtů

Pokud je váš cíl výpočetní služby vaším **místním počítačem**, zodpovídáte za zajištění dostupnosti všech potřebných balíčků v prostředí Pythonu, ve kterém se skript spouští.  Použijte `python.user_managed_dependencies` k použití aktuálního prostředí Python (nebo Pythonu na cestě, kterou zadáte).

```python
from azureml.core import Environment

# Editing a run configuration property on-fly.
my_environment = Environment("user-managed-env")

my_environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#my_environment.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-script-run-configuration"></a>Vytvořit konfiguraci spuštění skriptu

Teď, když máte výpočetní cíl ( `compute_target` ) a prostředí ( `my_environment` ), vytvořte konfiguraci spuštění skriptu, která spustí školicí skript (), který `train.py` se nachází ve vašem `project_folder` adresáři:

```python
from azureml.core import ScriptRunConfig

script_run_config = ScriptRunConfig(source_directory=project_folder, script='train.py')

# Set compute target
script_run_config.run_config.target = my_compute_target

# Set environment.   If you don't do this, a default environment will be created.
script_run_config.run_config.environment = my_environment
```

Můžete také chtít nastavit rozhraní pro svůj běh.

* Pro cluster HDI:
    ```python
    src.run_config.framework = "pyspark"
    ```

* Pro vzdálený virtuální počítač:
    ```python
    src.run_config.framework = "python"
    ```

## <a name="submit-the-experiment"></a>Odeslání experimentu

```python
run = experiment.submit(config=script_run_config)
```

> [!IMPORTANT]
> Po odeslání školicího cvičení se vytvoří snímek adresáře, který obsahuje vaše školicí skripty, a pošle se do cíle služby Compute. Je také uložen jako součást experimentu v pracovním prostoru. Pokud změníte soubory a znovu odešlete spuštění, nahrají se jenom změněné soubory.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Další informace o snímcích najdete v tématu [snímky](concept-azure-machine-learning-architecture.md#snapshots).


<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Sledování a integrace Git

Když spustíte školicí kurz, kde zdrojový adresář je místní úložiště Git, informace o úložišti se ukládají v historii spuštění. Další informace najdete v tématu [integrace Gitu pro Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Příklady poznámkových bloků

Příklady školení s různými cíli výpočtů najdete v těchto poznámkových blocích:
* [postupy – použití – AzureML/školení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [kurzy/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

* [Kurz: výuka modelu](tutorial-train-models-with-aml.md) používá ke školení modelu spravovaný výpočetní cíl.
* Přečtěte si, jak [efektivně ladit parametry](how-to-tune-hyperparameters.md) pro vytváření lepších modelů.? zobrazení = Azure-ml-py&Preserve-View = true)
* Jakmile budete mít školený model, zjistěte, [jak a kde nasadit modely](how-to-deploy-and-where.md).
* Zobrazit odkaz na sadu SDK [třídy RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py&preserve-view=true)
* [Použití Azure Machine Learning s Azure Virtual Networks](how-to-enable-virtual-network.md)