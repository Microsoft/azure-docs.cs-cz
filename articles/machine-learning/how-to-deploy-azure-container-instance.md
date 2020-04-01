---
title: Jak nasadit modely do instancí kontejnerů Azure
titleSuffix: Azure Machine Learning
description: Zjistěte, jak nasadit modely Azure Machine Learning jako webovou službu pomocí azure container instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.openlocfilehash: d460112394d7c7b7d2da4e8af41c0085b67226ec
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475473"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Nasazení modelu do instancí kontejnerů Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zjistěte, jak pomocí Azure Machine Learning nasadit model jako webovou službu v instanci kontejnerů Azure (ACI). Instance kontejneru Azure použijte, pokud platí jedna z následujících podmínek:

- Je třeba rychle nasadit a ověřit model. Není nutné vytvářet kontejnery ACI předem. Jsou vytvořeny jako součást procesu nasazení.
- Testujete model, který je ve vývoji. 

Informace o dostupnosti kvót a oblasti pro ACI najdete v článku [Kvóty a dostupnost oblasti pro instance kontejnerů Azure.](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Další informace najdete [v tématu Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

- Model strojového učení registrovaný ve vašem pracovním prostoru. Pokud nemáte registrovaný model, přečtěte si, [jak a kde nasadit modely](how-to-deploy-and-where.md).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), Azure Machine Learning Python [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo [rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Fragmenty kódu __Pythonu__ v tomto článku předpokládají, že jsou nastaveny následující proměnné:

    * `ws`- Nastavte do pracovního prostoru.
    * `model`- Nastavte na váš registrovaný model.
    * `inference_config`- Nastavte na odvození konfigurace pro model.

    Další informace o nastavení těchto proměnných naleznete v tématu [Jak a kde nasadit modely](how-to-deploy-and-where.md).

- Fragmenty __zapínání na vod v__ ykrese v tomto článku předpokládají, že jste vytvořili `inferenceconfig.json` dokument. Další informace o vytvoření tohoto dokumentu naleznete v tématu [Jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="deploy-to-aci"></a>Nasazení do ACI

Chcete-li nasadit model do azure container instances, vytvořte __konfiguraci nasazení,__ která popisuje potřebné výpočetní prostředky. Například počet jader a paměti. Potřebujete také __konfiguraci odvození__, která popisuje prostředí potřebné k hostování modelu a webové služby. Další informace o vytvoření konfigurace odvození naleznete v tématu [Jak a kde nasadit modely](how-to-deploy-and-where.md).

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

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Použití cli

Chcete-li nasadit pomocí příkazového příkazu k příkazu příkazu, použijte následující příkaz. Nahraďte `mymodel:1` název a verzi registrovaného modelu. Chcete-li tuto službu poskytnout, nahraďte `myservice` názvem:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Další informace naleznete v odkazu [na nasazení modelu az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) 

## <a name="using-vs-code"></a>Použití VS Code

Viz [nasazení modelů pomocí VS Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Není nutné vytvořit kontejner ACI k testování předem. Kontejnery ACI jsou vytvořeny podle potřeby.

## <a name="update-the-web-service"></a>Aktualizace webové služby

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Další kroky

* [Jak nasadit model pomocí vlastní image Dockeru](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Použití TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](how-to-secure-web-service.md)
* [Využití modelu ML nasazeného jako webová služba](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning pomocí přehledů aplikací](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely ve výrobě](how-to-enable-data-collection.md)
