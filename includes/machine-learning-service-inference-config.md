---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 8f7798e684a0a144cfe5285a0dd926a3b440934a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558421"
---
Položky v dokumentu jsou `inferenceconfig.json` mapovány na parametry pro třídu [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry pro metodu:

| Entita JSON | Parametr metody | Popis |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Cesta k místnímu souboru, který obsahuje kód, který se má pro obrázek spustit. |
| `sourceDirectory` | `source_directory` | Nepovinný parametr. Cesta ke složkám, které obsahují všechny soubory pro vytvoření bitové kopie, což usnadňuje přístup k jakýmkoli souborům v této složce nebo podsložce. Můžete nahrát celou složku z místního počítače jako závislosti pro webovou službu. Poznámka: vaše entry_script, conda_file a extra_docker_file_steps cesty jsou relativní cesty k source_directory cestě. |
| `environment` | `environment` | Nepovinný parametr.  Azure Machine Learning [prostředí](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

Do konfiguračního souboru odvození můžete zahrnout úplné specifikace Azure Machine Learningho [prostředí](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) . Pokud toto prostředí v pracovním prostoru neexistuje, Azure Machine Learning ho vytvoří. V opačném případě Azure Machine Learning v případě potřeby aktualizuje prostředí. Následující JSON je příklad:

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
                            "scikit-learn==0.22.1",
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

V oddělených parametrech rozhraní příkazového řádku můžete také použít existující [prostředí](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning a odebrat klíč "prostředí" z konfiguračního souboru odvození. Pro název prostředí použijte-e a--EV pro verzi prostředí. Pokud nezadáte--EV, použije se nejnovější verze. Tady je příklad konfiguračního souboru odvození:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Následující příkaz ukazuje, jak nasadit model pomocí předchozího konfiguračního souboru pro odvození (s názvem myInferenceConfig.json). 

Používá také nejnovější verzi stávajícího Azure Machine Learning [prostředí](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) (s názvem AzureML-Minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```