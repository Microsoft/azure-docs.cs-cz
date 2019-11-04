---
title: Postup nasazení modelů pro Azure Container Instances
titleSuffix: Azure Machine Learning
description: Naučte se, jak nasadit modely Azure Machine Learning jako webovou službu pomocí Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: 6590b6c3ecf1efc43822a4fefab79cdc7f624dcd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496981"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Nasazení modelu pro Azure Container Instances
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Naučte se používat Azure Machine Learning k nasazení modelu jako webové služby v Azure Container Instances (ACI). Použijte Azure Container Instances, pokud je splněna jedna z následujících podmínek:

- Potřebujete rychle nasadit a ověřit model. Nemusíte vytvářet kontejnery ACI předem. Jsou vytvořeny v rámci procesu nasazení.
- Testujete model, který je ve vývoji. 

Informace o dostupnosti kvót a oblastí pro ACI najdete v článku [kvóty a dostupnost oblastí pro Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) článek.

## <a name="prerequisites"></a>Předpoklady

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

- Model služby Machine Learning, který je zaregistrován ve vašem pracovním prostoru. Pokud nemáte registrovaný model, přečtěte si téma [jak a kde nasadit modely](how-to-deploy-and-where.md).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo [rozšíření Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

- Fragmenty kódu __Pythonu__ v tomto článku předpokládají, že jsou nastavené následující proměnné:

    * `ws` – nastaveno na váš pracovní prostor.
    * `model` – nastaveno na registrovaný model.
    * `inference_config` – nastaveno na odvození konfigurace modelu.

    Další informace o nastavení těchto proměnných najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

- Fragmenty rozhraní příkazového __řádku__ v tomto článku předpokládají, že jste vytvořili dokument `inferenceconfig.json`. Další informace o vytváření tohoto dokumentu najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="deploy-to-aci"></a>Nasazení do ACI

Pokud chcete nasadit model, který se Azure Container Instances, vytvořte __konfiguraci nasazení__ , která popisuje potřebné výpočetní prostředky. Například počet jader a paměti. Potřebujete také __konfiguraci odvození__, která popisuje prostředí potřebné pro hostování modelu a webové služby. Další informace o vytvoření konfigurace odvození najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Použití sady SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Další informace o třídách, metodách a parametrech použitých v tomto příkladu naleznete v následujících referenčních dokumentech:

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)
* [Model. deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Použití rozhraní příkazového řádku

Chcete-li nasadit pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `mymodel:1` názvem a verzí registrovaného modelu. Nahraďte `myservice` názvem, který tuto službu poskytne:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

Další informace najdete v referenčních informacích k [nasazení modelu AZ ml model](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) . 

## <a name="using-vs-code"></a>Použití VS Code

Viz [nasazení modelů pomocí vs Code](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT]
> Nemusíte vytvářet kontejner ACI k testování předem. Kontejnery ACI se vytvářejí podle potřeby.

## <a name="update-the-web-service"></a>Aktualizace webové služby

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Další kroky

* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Zabezpečené Azure Machine Learning webové služby pomocí protokolu SSL](how-to-secure-web-service.md)
* [Využití modelu ML nasazeného jako webové služby](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
