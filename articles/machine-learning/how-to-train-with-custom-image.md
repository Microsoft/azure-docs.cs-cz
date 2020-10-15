---
title: Výuka modelu pomocí vlastní image Docker
titleSuffix: Azure Machine Learning
description: Naučte se naučit modely s vlastními imagemi Docker v Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 13a88b327b5ba56b52cd4f08d9c7fae5d653ed38
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92095938"
---
# <a name="train-a-model-using-a-custom-docker-image"></a>Výuka modelu pomocí vlastní image Docker

V tomto článku se dozvíte, jak používat vlastní image Docker při výuce modelů pomocí Azure Machine Learning. 

Ukázkové skripty v tomto článku se používají ke klasifikaci PET imagí vytvořením sítě konvoluční neuronové. 

I když Azure Machine Learning poskytuje výchozí základní image Docker, můžete k určení konkrétní základní image použít taky Azure Machine Learning prostředí, jako je například jedna ze sady udržovaných [základních imagí Azure ml](https://github.com/Azure/AzureML-Containers) nebo vlastní [Image](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). Vlastní základní image umožňují pečlivě spravovat závislosti a při provádění školicích úloh udržovat užší kontrolu nad verzemi komponent. 

## <a name="prerequisites"></a>Předpoklady 
Spusťte tento kód v jednom z těchto prostředí:
* Azure Machine Learning výpočetní instance – nepotřebujete žádné soubory ke stažení nebo instalaci
    * Dokončete [kurz: instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) pro vytvoření vyhrazeného serveru poznámkového bloku předem načteného se sadou SDK a s ukázkovým úložištěm.
    * V úložišti s [příklady](https://github.com/Azure/azureml-examples)Azure Machine Learning Najděte dokončený Poznámkový blok tak, že přejdete do tohoto adresáře: **poznámkové bloky > fastai > vlak-domácích-resnet34. ipynb** 

* Váš vlastní server Jupyter Notebook
    * Vytvořte [konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    * [Sada Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true). 
    * [Azure Container Registry](/azure/container-registry) nebo jiný registr Docker, který je přístupný na internetu.

## <a name="set-up-the-experiment"></a>Nastavení experimentu 
Tato část nastavuje experiment pro školení tím, že inicializuje pracovní prostor, vytvoří experiment a nahraje školicí data a školicí skripty.

### <a name="initialize-a-workspace"></a>Inicializovat pracovní prostor
[Azure Machine Learning pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte. V sadě Python SDK máte přístup k artefaktům pracovního prostoru vytvořením [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) objektu.

Vytvořte objekt pracovního prostoru ze `config.json` souboru vytvořeného v [části požadavky](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Příprava skriptů
Pro tento kurz se [tady](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py)poskytuje školicí skript **Train.py** . V praxi můžete použít libovolný vlastní školicí skript, jak je, a spustit ho pomocí Azure Machine Learning.

### <a name="define-your-environment"></a>Definice prostředí
Vytvořte objekt prostředí a povolte Docker. 

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

Zadaný základní obrázek níže podporuje knihovnu fast.ai, která umožňuje distribuované funkce hloubkového učení. Další informace najdete v tématu [Fast.AI dockerhubu](https://hub.docker.com/u/fastdotai). 

Pokud používáte vlastní image Docker, možná jste už prostředí Python nastavili správně. V takovém případě nastavte `user_managed_dependencies` příznak na hodnotu true, aby bylo možné využít vestavěné prostředí Python vlastní image. Ve výchozím nastavení vytvoří Azure ML prostředí conda se závislostmi, které jste zadali, a spustí běh v tomto prostředí namísto použití jakýchkoli knihoven Pythonu, které jste nainstalovali na základní image.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

Pokud chcete použít image z privátního registru kontejnerů, který není ve vašem pracovním prostoru, musíte použít `docker.base_image_registry` k zadání adresy úložiště a uživatelského jména a hesla:

```python
# Set the container registry information
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

Je také možné použít vlastní souboru Dockerfile. Tento postup použijte v případě, že potřebujete instalovat balíčky mimo Python jako závislosti a nezapomeňte nastavit základní image na žádná.

```python 
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

Další informace o vytváření a správě prostředí Azure ML najdete v tématu věnovaném [vytváření & používání softwarových prostředí](how-to-use-environments.md). 

### <a name="create-or-attach-existing-amlcompute"></a>Vytvořit nebo připojit existující AmlCompute
Budete muset vytvořit [výpočetní cíl](concept-azure-machine-learning-architecture.md#compute-targets) pro školení modelu. V tomto kurzu vytvoříte AmlCompute jako výpočetní prostředek pro školení.

Vytváření AmlCompute trvá přibližně 5 minut. Pokud AmlCompute s tímto názvem už ve vašem pracovním prostoru je, tento kód přeskočí proces vytváření.

Stejně jako u ostatních služeb Azure platí omezení určitých prostředků (např. AmlCompute) přidružených ke službě Azure Machine Learning. Přečtěte si prosím [Tento článek](how-to-manage-quotas.md) o výchozích omezeních a o tom, jak požádat o větší kvótu. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current AmlCompute
print(compute_target.get_status().serialize())
```

### <a name="create-a-scriptrunconfig"></a>Vytvoření ScriptRunConfig
Tento ScriptRunConfig nakonfiguruje vaši úlohu na provedení na požadovaném [cíli výpočtů](how-to-set-up-training-targets.md).

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

### <a name="submit-your-run"></a>Odeslat běh
Při odeslání školicího běhu pomocí objektu ScriptRunConfig vrátí metoda Submit objekt typu ScriptRun. Vrácený objekt ScriptRun poskytuje programový přístup k informacím o běhu školení. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning spouští školicí skripty zkopírováním celého zdrojového adresáře. Pokud máte citlivá data, která nechcete nahrávat, použijte [soubor. Ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) nebo ho nezahrnujte do zdrojového adresáře. Místo toho přístup k datům pomocí [úložiště](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)dat.

## <a name="next-steps"></a>Další kroky
V tomto článku jste proškolei model s použitím vlastní image Docker. Další informace o Azure Machine Learning najdete v těchto článcích.
* [Sledovat metriky spuštění](how-to-track-experiments.md) během školení
* [Nasazení modelu](how-to-deploy-custom-docker-image.md) pomocí vlastní image Docker.
