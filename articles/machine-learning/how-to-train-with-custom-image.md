---
title: Výuka modelu pomocí vlastní image Docker
titleSuffix: Azure Machine Learning
description: Naučte se, jak používat vlastní image Docker nebo jaké jsou od Microsoftu, k učení modelů v Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: cb8e71d9f94441f79dd7ce2fd5ee6458987563c3
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2020
ms.locfileid: "97795916"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Výuka modelu pomocí vlastní image Docker

V tomto článku se dozvíte, jak používat vlastní image Docker při výuce modelů pomocí Azure Machine Learning. Použijete ukázkové skripty v tomto článku ke klasifikaci PET imagí vytvořením sítě konvoluční neuronové. 

Azure Machine Learning poskytuje výchozí obrázek Docker Base. Pomocí Azure Machine Learning prostředí můžete také určit jinou základní image, jako je například jedna ze spravovaných [základních imagí Azure Machine Learning](https://github.com/Azure/AzureML-Containers) nebo vlastní [Image](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). Vlastní základní image umožňují pečlivě spravovat vaše závislosti a udržovat užší kontrolu nad verzemi komponent při spouštění školicích úloh.

## <a name="prerequisites"></a>Požadavky

Spusťte kód v některém z těchto prostředí:

* Azure Machine Learning výpočetní instance (nejsou nutné žádné soubory ke stažení nebo instalace):
  * Dokončete kurz [nastavení prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md) pro vytvoření vyhrazeného serveru poznámkového bloku, který je předem nazálohovaný pomocí sady SDK a ukázkového úložiště.
  * V úložišti s [Příklady](https://github.com/Azure/azureml-examples)Azure Machine Learning Najděte dokončený Poznámkový blok tak, že v adresáři **Poznámkový blok**  >  **fastai**  >  **výuka resnet34. ipynb** . 
* Váš vlastní server Jupyter Notebook:
  * Vytvořte [konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
  * Nainstalujte [sadu Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py). 
  * Vytvořte službu [Azure Container Registry](../container-registry/index.yml) nebo jiný registr Docker, který je k dispozici na internetu.

## <a name="set-up-a-training-experiment"></a>Nastavení experimentu školení

V této části nastavíte experiment pro školení inicializací pracovního prostoru, definováním vašeho prostředí a konfigurací cíle výpočetní služby.

### <a name="initialize-a-workspace"></a>Inicializovat pracovní prostor

[Azure Machine Learning pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte. V sadě Python SDK máte přístup k artefaktům pracovního prostoru vytvořením [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) objektu.

Vytvořte `Workspace` objekt z config.jsv souboru, který jste vytvořili jako [požadavek](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>Definice prostředí

Vytvořte `Environment` objekt a povolte Docker.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

Zadaný základní obrázek v následujícím kódu podporuje knihovnu fast.ai, která umožňuje distribuované možnosti hloubkového učení. Další informace najdete v [úložišti Fast.AI Docker Hub](https://hub.docker.com/u/fastdotai). 

Pokud používáte vlastní image Docker, možná jste už prostředí Python nastavili správně. V takovém případě nastavte `user_managed_dependencies` příznak na `True` použití integrovaného prostředí Python vlastní image. Ve výchozím nastavení Azure Machine Learning vytvoří prostředí conda se závislostmi, které jste určili. Služba spustí skript v tomto prostředí namísto použití jakýchkoli knihoven Pythonu, které jste nainstalovali na základní image.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>Použití privátního registru kontejnerů (volitelné)

Pokud chcete použít image z privátního registru kontejneru, který není ve vašem pracovním prostoru, použijte `docker.base_image_registry` k zadání adresy úložiště a uživatelského jména a hesla:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>Použití vlastního souboru dockerfileu (volitelné)

Je také možné použít vlastní souboru Dockerfile. Tento postup použijte v případě, že potřebujete instalovat balíčky mimo Python jako závislosti. Nezapomeňte nastavit základní bitovou kopii na `None` .

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

Další informace o vytváření a správě Azure Machine Learning prostředí najdete v tématu [vytváření a používání softwarových prostředí](how-to-use-environments.md). 

### <a name="create-or-attach-a-compute-target"></a>Vytvoření nebo připojení cíle výpočtů

Musíte vytvořit [výpočetní cíl](concept-azure-machine-learning-architecture.md#compute-targets) pro školení modelu. V tomto kurzu se vytváříte `AmlCompute` jako výpočetní prostředek pro školení.

Vytváření `AmlCompute` trvá několik minut. Pokud `AmlCompute` je prostředek již ve vašem pracovním prostoru, tento kód přeskočí proces vytváření.

Stejně jako u ostatních služeb Azure platí omezení určitých prostředků (například `AmlCompute` ) přidružených ke službě Azure Machine Learning. Další informace najdete v tématu [výchozí omezení a jak požádat o vyšší kvótu](how-to-manage-quotas.md).

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>Konfigurace školicí úlohy

Pro tento kurz použijte školicí skript *Train.py* na [GitHubu](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py). V praxi můžete použít libovolný vlastní školicí skript a spustit ho tak, jak je, pomocí Azure Machine Learning.

Vytvořte `ScriptRunConfig` prostředek pro konfiguraci úlohy pro spuštění na požadovaném cíli služby [COMPUTE](how-to-set-up-training-targets.md).

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>Odeslat školicí úlohu

Když odešlete školicí běh pomocí `ScriptRunConfig` objektu, `submit` Metoda vrátí objekt typu `ScriptRun` . Vrácený `ScriptRun` objekt poskytuje programový přístup k informacím o běhu školení. 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning spouští školicí skripty zkopírováním celého zdrojového adresáře. Pokud máte citlivá data, která nechcete nahrávat, použijte [soubor. Ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) nebo ho nezahrnujte do zdrojového adresáře. Místo toho přístup k datům pomocí [úložiště](/python/api/azureml-core/azureml.data?preserve-view=true&view=azure-ml-py)dat.

## <a name="next-steps"></a>Další kroky
V tomto článku jste proškolei model pomocí vlastní image Docker. Další informace o Azure Machine Learning najdete v těchto článcích:
* [Sledovat metriky spuštění](how-to-track-experiments.md) během školení.
* [Nasazení modelu](how-to-deploy-custom-docker-image.md) pomocí vlastní image Docker.
