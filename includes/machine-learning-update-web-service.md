---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 694d7e6afbf7bd4b219d52254159dcf2e02f8769
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682085"
---
Chcete-li aktualizovat webovou službu, použijte metodu `update`. Webovou službu můžete aktualizovat tak, aby používala nový model, nový vstupní skript nebo nové závislosti, které lze zadat v konfiguraci odvození. Další informace najdete v dokumentaci ke službě [WebService. Update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> Když vytváříte novou verzi modelu, je nutné ručně aktualizovat každou službu, kterou chcete použít.

**Používání sady SDK**

Následující kód ukazuje, jak použít sadu SDK k aktualizaci modelu, prostředí a vstupního skriptu pro webovou službu:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**Použití rozhraní příkazového řádku**

Webovou službu můžete také aktualizovat pomocí rozhraní příkazového řádku ML. Následující příklad ukazuje registraci nového modelu a následné aktualizaci webové služby na použití nového modelu:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> V tomto příkladu se k předání informací o modelu z příkazu k registraci do příkazu Update používá dokument JSON.
>
> Chcete-li aktualizovat službu tak, aby používala nový skriptovací skript nebo prostředí, vytvořte [konfigurační soubor odvození](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) a zadejte jej pomocí parametru `ic`.

Další informace najdete v dokumentaci [AZ ml Service Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) .