---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 03b40b1f334816f08d7e169f8ff78bdb7c06c4de
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348568"
---
Položky v `inferenceconfig.json` dokumentu jsou mapovány na parametry pro třídu [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry pro metodu:

| Entita JSON | Parametr metody | Popis |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Cesta k místnímu souboru, který obsahuje kód, který se má pro obrázek spustit. |
| `runtime` | `runtime` | Který modul runtime má být použit pro bitovou kopii. Aktuální podporované moduly runtime jsou Spark-py a Python. |
| `condaFile` | `conda_file` | Volitelné. Cesta k místnímu souboru obsahujícímu definici prostředí Conda, která se má použít pro bitovou kopii |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Volitelné. Cesta k místnímu souboru, který obsahuje další kroky Docker, které se mají spustit při nastavování image |
| `sourceDirectory` | `source_directory` | Volitelné. Cesta ke složkám, které obsahují všechny soubory pro vytvoření bitové kopie. |
| `enableGpu` | `enable_gpu` | Volitelné. Určuje, zda má být v imagi povolena podpora GPU. Image GPU se musí použít ve službě Microsoft Azure. Například Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines a Azure Kubernetes Service. Výchozí hodnota je false. |
| `baseImage` | `base_image` | Volitelné. Vlastní image, která se má použít jako základní image Pokud není zadána žádná základní image, použije se na základě zadaného parametru runtime základní image. |
| `baseImageRegistry` | `base_image_registry` | Volitelné. Registr imagí, který obsahuje základní image. |
| `cudaVersion` | `cuda_version` | Volitelné. Verze CUDA, která se má nainstalovat pro image, které potřebují podporu GPU Image GPU se musí použít pro Microsoft Azure služby. Například Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines a Azure Kubernetes Service. Podporované verze jsou 9,0, 9,1 a 10,0. Pokud je nastavená možnost enable_gpu, použije se výchozí hodnota 9,1. |
| `description` | `description` |  Popis tohoto obrázku |

Následující kód JSON je příkladem odvození konfigurace pro použití s rozhraním příkazového řádku:

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```