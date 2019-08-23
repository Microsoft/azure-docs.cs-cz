---
title: Nasazení modelů ml do Azure App Service (Preview)
titleSuffix: Azure Machine Learning service
description: Naučte se používat službu Azure Machine Learning k nasazení modelu do webové aplikace v Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: ada2a19de12c2f3f6b23fcc3d759afb0c747d37d
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897414"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Nasazení modelu Machine Learning do Azure App Service (Preview)

Naučte se, jak nasadit model z Azure Machine Learning služby jako webové aplikace v Azure App Service.

> [!IMPORTANT]
> I když jsou všeobecně k dispozici obě služby Azure Machine Learning i Azure App Service, možnost nasadit model ze služby Machine Learning na App Service je ve verzi Preview.

Pomocí Azure Machine Learning služby můžete vytvářet image Docker z školicích modelů strojového učení. Tato image obsahuje webovou službu, která přijímá data, odesílá je do modelu a pak vrátí odpověď. Azure App Service lze použít k nasazení bitové kopie a k dispozici jsou následující funkce:

* Rozšířené [ověřování](/azure/app-service/configure-authentication-provider-aad) pro rozšířené zabezpečení. Metody ověřování zahrnují Azure Active Directory i Multi-Factor auth.
* [Automatické škálování](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) bez nutnosti opětovného nasazení.
* [Podpora SSL](/azure/app-service/app-service-web-ssl-cert-load) pro zabezpečenou komunikaci mezi klienty a službou.

Další informace o funkcích poskytovaných nástrojem Azure App Service najdete v [přehledu App Service](/azure/app-service/overview).

> [!IMPORTANT]
> Pokud potřebujete mít možnost protokolovat data bodování používaná s nasazeným modelem nebo výsledky bodování, měli byste je místo toho nasadit do služby Azure Kubernetes. Další informace najdete v tématu [shromažďování dat v produkčních modelech](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v článku o [Vytvoření pracovního prostoru](how-to-manage-workspace.md) .
* Vyškolený model strojového učení zaregistrovaný ve vašem pracovním prostoru. Pokud model nemáte, použijte [kurz k klasifikaci imagí: výukový model](tutorial-train-models-with-aml.md) pro výuku a registraci k jednomu.

    > [!IMPORTANT]
    > Fragmenty kódu v tomto článku předpokládají, že jste nastavili následující proměnné:
    >
    > * `ws`– Váš pracovní prostor Azure Machine Learning.
    > * `model`– Registrovaný model, který se nasadí.
    > * `inference_config`– Odvození konfigurace pro model.
    >
    > Další informace o nastavení těchto proměnných najdete v tématu [nasazení modelů pomocí služby Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Příprava nasazení

Před nasazením musíte definovat, co je potřeba ke spuštění modelu jako webové služby. Následující seznam popisuje základní položky, které jsou potřeba pro nasazení:

* __Vstupní skript__. Tento skript přijímá požadavky, vyhodnotí požadavek pomocí modelu a vrátí výsledky.

    > [!IMPORTANT]
    > Vstupní skript je specifický pro váš model; musí pochopit formát příchozích dat požadavků, formát dat očekávaných modelem a formát dat vrácených klientům.
    >
    > Pokud jsou data požadavku ve formátu, který model nepoužívá, skript ho může transformovat do přijatelného formátu. Může také transformovat odpověď předtím, než se vrátí do klienta.

    > [!IMPORTANT]
    > Sada Azure Machine Learning SDK neposkytuje způsob, jak webové službě přistupovat k úložišti dat nebo datovým sadám. Pokud potřebujete nasadit model pro přístup k datům uloženým mimo nasazení, jako je například v účtu Azure Storage, je nutné vyvinout vlastní řešení kódu pomocí příslušné sady SDK. Například [sada SDK Azure Storage pro Python](https://github.com/Azure/azure-storage-python).
    >
    > Další alternativou, která může fungovat pro váš scénář, je [Batch předpovědi](how-to-run-batch-predictions.md), která poskytuje přístup k úložišti dat při bodování.

    Další informace o vstupních skriptech najdete v tématu [nasazení modelů pomocí služby Azure Machine Learning](how-to-deploy-and-where.md).

* **Závislosti**, například pomocné skripty nebo balíčky python/conda potřebné ke spuštění skriptu vstupu nebo modelu

Tyto entity jsou zapouzdřeny do __Konfigurace odvození__. Konfigurace odvození odkazuje na skript vstupu a další závislosti.

> [!IMPORTANT]
> Při vytváření odvozených konfigurací pro použití s Azure App Service je nutné použít objekt [prostředí](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) . Následující příklad ukazuje vytvoření objektu prostředí a jeho použití s odvozenou konfigurací:
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

Další informace o prostředích najdete v tématu [vytváření a Správa prostředí pro školení a nasazení](how-to-use-environments.md).

Další informace o konfiguraci odvození najdete v tématu [nasazení modelů pomocí služby Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Při nasazování do Azure App Service nemusíte vytvářet __konfiguraci nasazení__.

## <a name="create-the-image"></a>Vytvoření image

Chcete-li vytvořit bitovou kopii Docker, která je nasazena do Azure App Service, použijte [model. Package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-). Následující fragment kódu ukazuje, jak vytvořit novou bitovou kopii z modelu a odvozené konfigurace:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

V `show_output=True`případě je zobrazen výstup procesu Docker Build. Po dokončení procesu se image vytvoří v Azure Container Registry pro váš pracovní prostor.

## <a name="deploy-image-as-a-web-app"></a>Nasazení image jako webové aplikace

1. V [Azure Portal](https://portal.azure.com)vyberte pracovní prostor Azure Machine Learning. V části __Přehled__ použijte odkaz __registru__ pro přístup k Azure Container registry pro daný pracovní prostor.

    [![Snímek obrazovky s přehledem pracovního prostoru](media/how-to-deploy-app-service/workspace-overview.png)](media/how-to-deploy-app-service/workspace-overview-expanded.png)

2. Z Azure Container Registry vyberte __úložiště__a pak vyberte __název bitové kopie__ , kterou chcete nasadit. Pro verzi, kterou chcete nasadit, vyberte položku __...__ a pak ji nasaďte __do webové aplikace__.

    [![Snímek obrazovky s nasazením z ACR do webové aplikace](media/how-to-deploy-app-service/deploy-to-web-app.png)](media/how-to-deploy-app-service/deploy-to-web-app-expanded.png)

3. Pokud chcete vytvořit webovou aplikaci, zadejte název lokality, předplatné, skupinu prostředků a vyberte plán/umístění služby App Service. Nakonec vyberte __vytvořit__.

    ![Snímek obrazovky s dialogem nové webové aplikace](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Použití webové aplikace

Z [Azure Portal](https://portal.azure.com)vyberte webovou aplikaci vytvořenou v předchozím kroku. V části __Přehled__ zkopírujte __adresu URL__. Tato hodnota je __základní adresa URL__ služby.

[![Snímek obrazovky s přehledem pro webovou aplikaci](media/how-to-deploy-app-service/web-app-overview.png)](media/how-to-deploy-app-service/web-app-overview-expanded.png)

Webová služba, která předává požadavky do modelu, je umístěna `{baseurl}/score`na adrese. Například, `https://mywebapp.azurewebsites.net/score`. Následující kód Pythonu ukazuje, jak odesílat data do adresy URL a zobrazovat odpověď:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Další postup

* Další informace o konfiguraci webové aplikace naleznete v dokumentaci [App Service v systému Linux](/azure/app-service/containers/) .
* Další informace o škálování najdete v tématu [Začínáme s automatickým škálováním v Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* Další informace o podpoře SSL najdete v tématu [použití certifikátu SSL v Azure App Service](/azure/app-service/app-service-web-ssl-cert-load).
* Další informace o ověřování najdete v tématu [Konfigurace aplikace App Service pro použití Azure Active Directory přihlášení](/azure/app-service/configure-authentication-provider-aad).
* [Používání modelu ML nasadit jako webovou službu](how-to-consume-web-service.md)