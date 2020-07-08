---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: e6a7ee8ed497c87c08f13e09a0022b0835c952cc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82975070"
---
K aktualizaci webové služby použijte `update` metodu. Webovou službu můžete aktualizovat tak, aby používala nový model, nový vstupní skript nebo nové závislosti, které lze zadat v konfiguraci odvození. Další informace najdete v dokumentaci ke službě [WebService. Update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> Když vytváříte novou verzi modelu, je nutné ručně aktualizovat každou službu, kterou chcete použít.
>
> Sadu SDK nemůžete použít k aktualizaci webové služby publikované z návrháře Azure Machine Learning.

**Použití sady SDK**

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
> Chcete-li aktualizovat službu tak, aby používala nový skriptovací skript nebo prostředí, vytvořte [konfigurační soubor odvození](/azure/machine-learning/reference-azure-machine-learning-cli#inference-configuration-schema) a zadejte jej s `ic` parametrem.

Další informace najdete v dokumentaci [AZ ml Service Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) .