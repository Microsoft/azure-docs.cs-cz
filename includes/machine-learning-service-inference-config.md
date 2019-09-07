---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 055909d51fcd1228e8eb26189ba682e09aee6a1a
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390601"
---
Položky v `inferenceconfig.json` dokumentu jsou mapovány na parametry pro třídu [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry pro metodu:

| Entita JSON | Parametr metody | Popis |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Cesta k místnímu souboru, který obsahuje kód, který se má pro obrázek spustit. |
| `runtime` | `runtime` | Který modul runtime má být použit pro bitovou kopii. Aktuální podporované moduly runtime jsou `spark-py` a `python`. |
| `condaFile` | `conda_file` | Volitelný parametr. Cesta k místnímu souboru, který obsahuje definici prostředí Conda, která se má použít pro bitovou kopii. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Volitelný parametr. Cesta k místnímu souboru, který obsahuje další kroky Docker, které se spustí při nastavování image. |
| `sourceDirectory` | `source_directory` | Volitelný parametr. Cesta ke složkám, které obsahují všechny soubory pro vytvoření bitové kopie. |
| `enableGpu` | `enable_gpu` | Volitelný parametr. Určuje, zda má být v imagi povolena podpora GPU. Image GPU se musí použít ve službě Azure, jako je Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines a Azure Kubernetes Service. Výchozí hodnota je false. |
| `baseImage` | `base_image` | Volitelný parametr. Vlastní image, která se má použít jako základní image Pokud není k dispozici žádná základní image, bude bitová kopie založena na zadaném parametru modulu runtime. |
| `baseImageRegistry` | `base_image_registry` | Volitelný parametr. Registr imagí, který obsahuje základní image. |
| `cudaVersion` | `cuda_version` | Volitelný parametr. Verze CUDA, která se má nainstalovat pro image, které potřebují podporu GPU Image GPU se musí použít ve službě Azure, jako je Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines a Azure Kubernetes Service. Podporované verze jsou 9,0, 9,1 a 10,0. Pokud `enable_gpu` je nastavená, výchozí hodnota je 9,1. |
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