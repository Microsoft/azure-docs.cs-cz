---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: d36bf2db05113656a77e76ff900d95910f313c73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477251"
---
Chcete-li aktualizovat webovou `update` službu, použijte metodu. Můžete aktualizovat webovou službu tak, aby používala nový model, nový vstupní skript nebo nové závislosti, které lze zadat v konfiguraci odvození. Další informace naleznete v dokumentaci k [webu Webservice.update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> Při vytváření nové verze modelu je nutné ručně aktualizovat každou službu, kterou chcete použít.
>
> Sadu SDK nelze použít k aktualizaci webové služby publikované z návrháře Azure Machine Learning.

**Použití sady SDK**

Následující kód ukazuje, jak pomocí sady SDK aktualizovat model, prostředí a vstupní skript pro webovou službu:

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

**Použití cli**

Můžete také aktualizovat webovou službu pomocí cli ML. Následující příklad ukazuje registraci nového modelu a aktualizaci webové služby tak, aby používala nový model:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> V tomto příkladu se dokument JSON používá k předání informací o modelu z příkazu registrace do příkazu update.
>
> Chcete-li službu aktualizovat tak, aby používala nový vstupní skript nebo `ic` prostředí, [vytvořte konfigurační soubor odvození](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) a zadejte jej pomocí parametru.

Další informace naleznete v dokumentaci k [aktualizaci služby az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update)