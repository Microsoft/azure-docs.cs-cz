---
title: Postup nasazení modelů pro Azure Container Instances
titleSuffix: Azure Machine Learning
description: Naučte se, jak nasadit modely Azure Machine Learning jako webovou službu pomocí Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: 5bf3c92f07cc33b35a070a3479e0063a63c9e43a
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522015"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Nasazení modelu do služby Azure Container Instances

Naučte se používat Azure Machine Learning k nasazení modelu jako webové služby v Azure Container Instances (ACI). Použijte Azure Container Instances, pokud je splněna jedna z následujících podmínek:

- Potřebujete rychle nasadit a ověřit model. Nemusíte vytvářet kontejnery ACI předem. Jsou vytvořeny v rámci procesu nasazení.
- Testujete model, který je ve vývoji. 

Informace o dostupnosti kvót a oblastí pro ACI najdete v článku [kvóty a dostupnost oblastí pro Azure Container Instances](../container-instances/container-instances-quotas.md) článek.

> [!IMPORTANT]
> Důrazně doporučujeme ladit místně před nasazením do webové služby, další informace najdete v tématu [ladění místně](./how-to-troubleshoot-deployment-local.md) .
>
> Můžete využít také [poznámkový blok služby Azure Machine Learning pro místní nasazení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local).

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

- Model služby Machine Learning, který je zaregistrován ve vašem pracovním prostoru. Pokud nemáte registrovaný model, přečtěte si téma [jak a kde nasadit modely](how-to-deploy-and-where.md).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)nebo [rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Fragmenty kódu __Pythonu__ v tomto článku předpokládají, že jsou nastavené následující proměnné:

    * `ws` – Nastavte na svůj pracovní prostor.
    * `model` – Nastavte na registrovaný model.
    * `inference_config` – Nastavte na odvození konfigurace pro model.

    Další informace o nastavení těchto proměnných najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

- Fragmenty rozhraní příkazového __řádku__ v tomto článku předpokládají, že jste vytvořili `inferenceconfig.json` dokument. Další informace o vytváření tohoto dokumentu najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="limitations"></a>Omezení

* Při použití Azure Container Instances ve virtuální síti musí být virtuální síť ve stejné skupině prostředků jako pracovní prostor Azure Machine Learning.
* Při použití Azure Container Instances uvnitř virtuální sítě se Azure Container Registry (ACR) pro váš pracovní prostor nemůže nacházet i ve virtuální síti.

Další informace najdete v tématu [Jak zabezpečit Inferencing pomocí virtuálních sítí](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci).

## <a name="deploy-to-aci"></a>Nasazení do ACI

Pokud chcete nasadit model, který se Azure Container Instances, vytvořte __konfiguraci nasazení__ , která popisuje potřebné výpočetní prostředky. Například počet jader a paměti. Potřebujete také __konfiguraci odvození__, která popisuje prostředí potřebné pro hostování modelu a webové služby. Další informace o vytvoření konfigurace odvození najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

> [!NOTE]
> * ACI je vhodný jenom pro malé modely, které jsou menší než 1 GB. 
> * Pro vývoj a testování větších modelů doporučujeme použít AKS s jedním uzlem.
> * Počet modelů, které se mají nasadit, je omezený na 1 000 modelů na jedno nasazení (na kontejner). 

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

* [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model. deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Použití rozhraní příkazového řádku

Chcete-li nasadit pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `mymodel:1` názvem a verzí registrovaného modelu. Nahraďte `myservice` názvem, který tuto službu poskytne:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Další informace najdete v referenčních informacích k [nasazení modelu AZ ml model](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) . 

## <a name="using-vs-code"></a>Použití VS Code

Viz [nasazení modelů pomocí vs Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Nemusíte vytvářet kontejner ACI k testování předem. Kontejnery ACI se vytvářejí podle potřeby.

> [!IMPORTANT]
> K vytvoření všech základních prostředků ACI přidáváme ID pracovního prostoru s algoritmem hash, takže všechny ACI názvy ze stejného pracovního prostoru budou mít stejnou příponu. Název služby Azure Machine Learning by byl stejný zákazník, který poskytl "service_name" a všechna Azure Machine Learning rozhraní API sady SDK pro uživatele nepotřebují žádnou změnu. Neposkytujeme žádné záruky na názvy podkladových prostředků, které se vytvářejí.

## <a name="next-steps"></a>Další kroky

* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Aktualizace webové služby](how-to-deploy-update-web-service.md)
* [Zabezpečení webové služby prostřednictvím služby Azure Machine Learning s využitím protokolu TLS](how-to-secure-web-service.md)
* [Využití modelu ML nasazeného jako webové služby](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)