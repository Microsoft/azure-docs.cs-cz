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
ms.openlocfilehash: 84de9d53b19f5aa9b73570aa0d115d204e8b6596
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848222"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Nasazení modelu Machine Learning do Azure App Service (Preview)

Naučte se, jak nasadit model z Azure Machine Learning služby jako webové aplikace v Azure App Service.

> [!IMPORTANT]
> I když jsou všeobecně k dispozici obě služby Azure Machine Learning i Azure App Service, možnost nasadit model ze služby Machine Learning na App Service je ve verzi Preview.

Pomocí Azure Machine Learning služby můžete vytvářet image Docker z školicích modelů strojového učení. Tato image obsahuje webovou službu, která přijímá data, odesílá je do modelu a pak vrátí odpověď. Azure App Service lze použít k nasazení bitové kopie a k dispozici jsou následující funkce:

* [Podpora SSL](/azure/app-service/app-service-web-ssl-cert-load) pro zabezpečenou komunikaci mezi klienty a službou.
* [Horizontální](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) navýšení kapacity na více instancí bez nutnosti opětovného nasazení.
* [Rozšířené ověřování](/azure/app-service/configure-authentication-provider-aad) pro rozšířené zabezpečení.

Další informace o funkcích poskytovaných nástrojem Azure App Service najdete v [přehledu App Service](/azure/app-service/overview).

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v článku o [Vytvoření pracovního prostoru](how-to-manage-workspace.md) .
* Vyškolený model strojového učení zaregistrovaný ve vašem pracovním prostoru. Pokud model nemáte, použijte [kurz k klasifikaci imagí: výukový model](tutorial-train-models-with-aml.md) pro výuku a registraci k jednomu.
* Obrázek Docker vytvořený z modelu Pokud nemáte image, použijte k vytvoření [model klasifikace Image: nasazení](tutorial-deploy-models-with-aml.md) .

## <a name="deploy-image-as-a-web-app"></a>Nasazení image jako webové aplikace

1. V [Azure Portal](https://portal.azure.com)vyberte pracovní prostor Azure Machine Learning. V části __Přehled__ použijte odkaz __registru__ pro přístup k Azure Container registry pro daný pracovní prostor.

    ![Snímek obrazovky s přehledem pracovního prostoru](media/how-to-deploy-app-service/workspace-overview.png)

2. Z Azure Container Registry vyberte __úložiště__a pak vyberte __název bitové kopie__ , kterou chcete nasadit. Pro verzi, kterou chcete nasadit, vyberte položku __...__ a pak ji nasaďte __do webové aplikace__.

    ![Snímek obrazovky s nasazením z ACR do webové aplikace](media/how-to-deploy-app-service/deploy-to-web-app.png)

3. Pokud chcete vytvořit webovou aplikaci, zadejte název lokality, předplatné, skupinu prostředků a vyberte plán/umístění služby App Service. Nakonec vyberte __vytvořit__.

    ![Snímek obrazovky s dialogem nové webové aplikace](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Použití webové aplikace

Z [Azure Portal](https://portal.azure.com)vyberte webovou aplikaci vytvořenou v předchozím kroku. V části __Přehled__ zkopírujte __adresu URL__. Tato hodnota je __základní adresa URL__ služby.

![Snímek obrazovky s přehledem pro webovou aplikaci](media/how-to-deploy-app-service/web-app-overview.png)

Webová služba, která předává požadavky do modelu, je umístěna `{baseurl}/score`na adrese. Například, `https://mywebapp.azurewebsites.net/score`. Následující kód Pythonu ukazuje, jak odesílat data do adresy URL a zobrazovat odpověď:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

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

## <a name="next-steps"></a>Další kroky

* Další informace o konfiguraci webové aplikace naleznete v dokumentaci [App Service v systému Linux](/azure/app-service/containers/) .
* Další informace o škálování najdete v tématu [Začínáme s automatickým škálováním v Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* Další informace o podpoře SSL najdete v tématu [použití certifikátu SSL v Azure App Service](/azure/app-service/app-service-web-ssl-cert-load).
* Další informace o ověřování najdete v tématu [Konfigurace aplikace App Service pro použití Azure Active Directory přihlášení](/azure/app-service/configure-authentication-provider-aad).