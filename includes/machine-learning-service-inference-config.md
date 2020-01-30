---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 6970732f53ffa99849e20f279c8bdf4160c30a0a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845142"
---
Položky v dokumentu `inferenceconfig.json` namapovány na parametry pro třídu [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry pro metodu:

| Entita JSON | Parametr metody | Popis |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Cesta k místnímu souboru, který obsahuje kód, který se má pro obrázek spustit. |
| `runtime` | `runtime` | Nepovinný parametr. Který modul runtime má být použit pro bitovou kopii. Podporované moduly runtime jsou `spark-py` a `python`. Je-li nastavena hodnota `environment`, bude tato položka ignorována. |
| `condaFile` | `conda_file` | Nepovinný parametr. Cesta k místnímu souboru, který obsahuje definici prostředí Conda, která se má použít pro bitovou kopii.  Je-li nastavena hodnota `environment`, bude tato položka ignorována. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Nepovinný parametr. Cesta k místnímu souboru, který obsahuje další kroky Docker, které se spustí při nastavování image.  Je-li nastavena hodnota `environment`, bude tato položka ignorována.|
| `sourceDirectory` | `source_directory` | Nepovinný parametr. Cesta ke složkám, které obsahují všechny soubory pro vytvoření bitové kopie, což usnadňuje přístup k jakýmkoli souborům v této složce nebo podsložce. Můžete nahrát celou složku z místního počítače jako závislosti pro webovou službu. Poznámka: vaše entry_script, conda_file a extra_docker_file_steps cesty jsou relativní cesty k source_directory cestě. |
| `enableGpu` | `enable_gpu` | Nepovinný parametr. Určuje, zda má být v imagi povolena podpora GPU. Image GPU se musí použít ve službě Azure, například Azure Container Instances. Například Azure Machine Learning COMPUTE, Azure Virtual Machines a Azure Kubernetes Service. Výchozí hodnota je false. Je-li nastavena hodnota `environment`, bude tato položka ignorována.|
| `baseImage` | `base_image` | Nepovinný parametr. Vlastní image, která se má použít jako základní image Pokud není k dispozici žádná základní image, bude bitová kopie založena na zadaném parametru modulu runtime. Je-li nastavena hodnota `environment`, bude tato položka ignorována. |
| `baseImageRegistry` | `base_image_registry` | Nepovinný parametr. Registr imagí, který obsahuje základní image. Je-li nastavena hodnota `environment`, bude tato položka ignorována.|
| `cudaVersion` | `cuda_version` | Nepovinný parametr. Verze CUDA, která se má nainstalovat pro image, které potřebují podporu GPU Image GPU se musí použít ve službě Azure. Například Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines a Azure Kubernetes Service. Podporované verze jsou 9,0, 9,1 a 10,0. Pokud je nastavená `enable_gpu`, výchozí hodnota je 9,1. Je-li nastavena hodnota `environment`, bude tato položka ignorována. |
| `description` | `description` | Popis obrázku Je-li nastavena hodnota `environment`, bude tato položka ignorována.  |
| `environment` | `environment` | Nepovinný parametr.  Azure Machine Learning [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

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

Do konfiguračního souboru odvození můžete zahrnout úplné specifikace Azure Machine Learningho [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) . Pokud toto prostředí v pracovním prostoru neexistuje, Azure Machine Learning ho vytvoří. V opačném případě Azure Machine Learning v případě potřeby aktualizuje prostředí. Následující JSON je příklad:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

V oddělených parametrech rozhraní příkazového řádku můžete také použít existující [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning a odebrat klíč "prostředí" z konfiguračního souboru odvození. Pro název prostředí použijte-e a--EV pro verzi prostředí. Pokud nezadáte--EV, použije se nejnovější verze. Tady je příklad konfiguračního souboru odvození:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Následující příkaz ukazuje, jak nasadit model pomocí předchozího konfiguračního souboru pro odvození (s názvem myInferenceConfig. JSON). 

Používá také nejnovější verzi stávajícího Azure Machine Learning [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) (s názvem AzureML-Minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
