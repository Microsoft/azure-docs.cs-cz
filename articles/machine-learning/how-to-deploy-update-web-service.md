---
title: Aktualizovat webové služby
titleSuffix: Azure Machine Learning
description: Naučte se aktualizovat webovou službu, která je už nasazená v Azure Machine Learning. Můžete aktualizovat nastavení, jako je model, prostředí a vstupní skript.
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.custom: deploy
ms.openlocfilehash: 703f5e360261c47c443320459d601d65a5503f5a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322425"
---
# <a name="update-a-deployed-web-service"></a>Aktualizace nasazené webové služby

V tomto článku se dozvíte, jak aktualizovat webovou službu nasazenou pomocí Azure Machine Learning.

## <a name="prerequisites"></a>Předpoklady

V tomto kurzu se předpokládá, že jste už nasadili webovou službu pomocí Azure Machine Learning. Pokud potřebujete zjistit, jak nasadit webovou službu, [postupujte podle těchto kroků](how-to-deploy-and-where.md).

## <a name="update-web-service"></a>Aktualizace webové služby

K aktualizaci webové služby použijte `update` metodu. Webovou službu můžete aktualizovat tak, aby používala nový model, nový vstupní skript nebo nové závislosti, které lze zadat v konfiguraci odvození. Další informace najdete v dokumentaci ke službě [WebService. Update](/python/api/azureml-core/azureml.core.webservice.webservice.webservice?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-).

Viz [Metoda aktualizace služby AKS.](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

Viz [Metoda aktualizace služby ACI.](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

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
service.wait_for_deployment(show_output=True)
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
> Chcete-li aktualizovat službu tak, aby používala nový skriptovací skript nebo prostředí, vytvořte [konfigurační soubor odvození](./reference-azure-machine-learning-cli.md#inference-configuration-schema) a zadejte jej s `ic` parametrem.

Další informace najdete v dokumentaci [AZ ml Service Update](/cli/azure/ext/azure-cli-ml/ml/service?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) .

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s neúspěšným nasazením](how-to-troubleshoot-deployment.md)
* [Nasazení do Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Vytváření klientských aplikací pro využívání webových služeb](how-to-consume-web-service.md)
* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Zabezpečení webové služby prostřednictvím služby Azure Machine Learning s využitím protokolu TLS](how-to-secure-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
* [Vytváření výstrah a triggerů událostí pro nasazení modelů](how-to-use-event-grid.md)