---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159403"
---
Položky v `inferenceconfig.json` dokumentu mapují na parametry třídy [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry metody:

| Entita JSON | Parametr metody | Popis |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Cesta k místnímu souboru, který obsahuje kód pro spuštění obrazu. |
| `sourceDirectory` | `source_directory` | Nepovinný parametr. Cesta ke složkám, které obsahují všechny soubory k vytvoření obrazu, což usnadňuje přístup k souborům v této složce nebo podsložce. Můžete nahrát celou složku z místního počítače jako závislosti pro webovou službu. Poznámka: cesty entry_script, conda_file a extra_docker_file_steps jsou relativní cesty k cestě source_directory. |
| `environment` | `environment` | Nepovinný parametr.  Prostředí Azure Machine [Learning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

Do konfiguračního souboru odvození můžete zahrnout úplné specifikace [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning. Pokud toto prostředí neexistuje ve vašem pracovním prostoru, Azure Machine Learning ho vytvoří. V opačném případě Azure Machine Learning bude aktualizovat prostředí v případě potřeby. Následující JSON je příkladem:

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

Můžete také použít existující [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning v oddělených parametrech rozhraní příkazového řádku a odebrat klíč "prostředí" z konfiguračního souboru odvození. Použijte -e pro název prostředí a --ev pro verzi prostředí. Pokud nezadáte --ev, bude použita nejnovější verze. Zde je příklad konfiguračního souboru odvození:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Následující příkaz ukazuje, jak nasadit model pomocí předchozího konfiguračního souboru odvození (s názvem myInferenceConfig.json). 

Používá také nejnovější verzi existujícího [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning (s názvem AzureML-Minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
