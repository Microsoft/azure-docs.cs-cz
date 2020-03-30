---
title: Jak nasadit modely pro výpočetní instance
titleSuffix: Azure Machine Learning
description: Zjistěte, jak nasadit modely Azure Machine Learning jako webovou službu pomocí výpočetních instancí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: afbd9950c31bc1c40b01ec0aaf3d2bfffb8a6b94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398239"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Nasazení modelu do výpočetních instancí Azure Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zjistěte, jak pomocí Azure Machine Learning nasadit model jako webovou službu na výpočetní instanci Azure Machine Learning. Výpočetní instance použijte, pokud platí jedna z následujících podmínek:

- Je třeba rychle nasadit a ověřit model.
- Testujete model, který je ve vývoji.

> [!TIP]
> Nasazení modelu z poznámkového bloku Jupyter na výpočetní instanci do webové služby na stejném virtuálním počítači je _místní nasazení_. V tomto případě je "místní" počítač výpočetní instance. Další informace o nasazeních najdete v [tématu Nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor Azure Machine Learning se spuštěnou výpočetní instanci. Další informace naleznete v [tématu Nastavení prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Nasazení do výpočetních instancí

Ukázkový poznámkový blok, který ukazuje místní nasazení, je součástí instance výpočetních prostředků. Pomocí následujících kroků načtěte poznámkový blok a nasaďte model jako webovou službu na virtuálním počítači:

1. Z [Azure Machine Learning studio](https://ml.azure.com), vyberte azure machine learning výpočetní instance.

1. Otevřete `samples-*` podadresář a `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`potom otevřete . Po otevření spusťte poznámkový blok.

    ![Snímek obrazovky spuštěné místní služby v poznámkovém bloku](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. Poznámkový blok zobrazuje adresu URL a port, na kterých je služba spuštěna. Například, `https://localhost:6789`. Můžete také spustit buňku `print('Local service port: {}'.format(local_service.port))` obsahující pro zobrazení portu.

    ![Snímek obrazovky spuštěného portu místní služby](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Chcete-li otestovat službu z `https://localhost:<local_service.port>` výpočetní instance, použijte adresu URL. Chcete-li otestovat ze vzdáleného klienta, získejte veřejnou adresu URL služby spuštěné na výpočetní instanci. Veřejnou adresu URL lze určit pomocí následujícího vzorce. 
    * Virtuální počítač `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`notebooku: . 
    * Instance Výpočtu: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 

    Například: 
    * Virtuální počítač notebooku:`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Instance Výpočtu:`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testování služby

Chcete-li odeslat ukázková data spuštěné službě, použijte následující kód. Nahraďte `service_url` hodnotu url z předchozího kroku:

> [!NOTE]
> Při ověřování nasazení na výpočetní instanci se ověřování provádí pomocí služby Azure Active Directory. Volání v `interactive_auth.get_authentication_header()` ukázkovém kódu vás ověří pomocí služby AAD a vrátí záhlaví, které lze pak použít k ověření služby v instanci výpočetních prostředků. Další informace najdete v tématu [Nastavení ověřování pro prostředky a pracovní postupy Azure Machine Learning](how-to-setup-authentication.md#interactive-authentication).
>
> Při ověřování nasazení ve službě Azure Kubernetes service nebo Azure Container Instances se používá jiná metoda ověřování. Další informace o tématu [Nastavení ověřování pro prostředky a pracovní postupy Azure Machine Learning](how-to-setup-authentication.md#web-service-authentication).

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Další kroky

* [Jak nasadit model pomocí vlastní image Dockeru](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Zabezpečené webové služby Azure Machine Learning s ssl](how-to-secure-web-service.md)
* [Využití modelu ML nasazeného jako webová služba](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning pomocí přehledů aplikací](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely ve výrobě](how-to-enable-data-collection.md)