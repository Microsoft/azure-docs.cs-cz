---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: ab31d45808a8c77c53b895643eec63952201d9e4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799744"
---
Položky v dokumentu `inferenceconfig.json` namapovány na parametry pro třídu [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry pro metodu:

| Entita JSON | Parametr metody | Popis |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Cesta k místnímu souboru, který obsahuje kód, který se má pro obrázek spustit. |
| `runtime` | `runtime` | Který modul runtime má být použit pro bitovou kopii. Aktuální podporované moduly runtime jsou `spark-py` a `python`. |
| `condaFile` | `conda_file` | Volitelné. Cesta k místnímu souboru, který obsahuje definici prostředí Conda, která se má použít pro bitovou kopii. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Volitelné. Cesta k místnímu souboru, který obsahuje další kroky Docker, které se spustí při nastavování image. |
| `sourceDirectory` | `source_directory` | Volitelné. Cesta ke složkám, které obsahují všechny soubory pro vytvoření bitové kopie. |
| `enableGpu` | `enable_gpu` | Volitelné. Určuje, zda má být v imagi povolena podpora GPU. Image GPU se musí použít ve službě Azure, jako je Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines a Azure Kubernetes Service. Výchozí hodnota je false. |
| `baseImage` | `base_image` | Volitelné. Vlastní image, která se má použít jako základní image Pokud není k dispozici žádná základní image, bude bitová kopie založena na zadaném parametru modulu runtime. |
| `baseImageRegistry` | `base_image_registry` | Volitelné. Registr imagí, který obsahuje základní image. |
| `cudaVersion` | `cuda_version` | Volitelné. Verze CUDA, která se má nainstalovat pro image, které potřebují podporu GPU Image GPU se musí použít ve službě Azure, jako je Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines a Azure Kubernetes Service. Podporované verze jsou 9,0, 9,1 a 10,0. Pokud je nastavená `enable_gpu`, výchozí hodnota je 9,1. |
| `description` | `description` | Popis obrázku |

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