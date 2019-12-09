---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 52689c585e148c18d16ad627570414a8de444fea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927001"
---
Položky v dokumentu `inferenceconfig.json` namapovány na parametry pro třídu [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry pro metodu:

| Entita JSON | Parametr metody | Popis |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Cesta k místnímu souboru, který obsahuje kód, který se má pro obrázek spustit. |
| `runtime` | `runtime` | Volitelné. Který modul runtime má být použit pro bitovou kopii. Aktuální podporované moduly runtime jsou `spark-py` a `python`. Je-li nastavena `environment`, bude ignorována. |
| `condaFile` | `conda_file` | Volitelné. Cesta k místnímu souboru, který obsahuje definici prostředí Conda, která se má použít pro bitovou kopii.  Je-li nastavena `environment`, bude ignorována. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Volitelné. Cesta k místnímu souboru, který obsahuje další kroky Docker, které se spustí při nastavování image.  Je-li nastavena `environment`, bude ignorována.|
| `sourceDirectory` | `source_directory` | Volitelné. Cesta ke složkám, které obsahují všechny soubory pro vytvoření bitové kopie. |
| `enableGpu` | `enable_gpu` | Volitelné. Určuje, zda má být v imagi povolena podpora GPU. Image GPU se musí použít ve službě Azure, jako je Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines a Azure Kubernetes Service. Výchozí hodnota je False. Je-li nastavena `environment`, bude ignorována.|
| `baseImage` | `base_image` | Volitelné. Vlastní image, která se má použít jako základní image Pokud není k dispozici žádná základní image, bude bitová kopie založena na zadaném parametru modulu runtime. Je-li nastavena `environment`, bude ignorována. |
| `baseImageRegistry` | `base_image_registry` | Volitelné. Registr imagí, který obsahuje základní image. Je-li nastavena `environment`, bude ignorována.|
| `cudaVersion` | `cuda_version` | Volitelné. Verze CUDA, která se má nainstalovat pro image, které potřebují podporu GPU Image GPU se musí použít ve službě Azure, jako je Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines a Azure Kubernetes Service. Podporované verze jsou 9,0, 9,1 a 10,0. Pokud je nastavená `enable_gpu`, výchozí hodnota je 9,1. Je-li nastavena `environment`, bude ignorována. |
| `description` | `description` | Popis obrázku Je-li nastavena `environment`, bude ignorována.  |
| `environment` | `environment` | Volitelné.  Azure Machine Learning [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

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

Následující JSON je příkladem odvození konfigurace, která používá existující Azure Machine Learning [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) s konkrétní verzí pro použití s rozhraním příkazového řádku:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": "1"
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```

Následující JSON je příkladem odvození konfigurace, která používá existující Azure Machine Learning [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) s nejnovější verzí pro použití s rozhraním příkazového řádku:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": null
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```
