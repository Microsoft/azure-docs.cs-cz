---
title: Nasazení modelů do výpočetních instancí
titleSuffix: Azure Machine Learning
description: Naučte se, jak nasadit modely Azure Machine Learning jako webovou službu pomocí výpočetních instancí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: 1767f149d9935c86281c7b7be3e684aa82a603f9
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999047"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Nasazení modelu pro Azure Machine Learning výpočetních instancí



Naučte se používat Azure Machine Learning k nasazení modelu jako webové služby ve vaší instanci služby Azure Machine Learning Compute. Pokud je splněna jedna z následujících podmínek, použijte výpočetní instance:

- Potřebujete rychle nasadit a ověřit model.
- Testujete model, který je ve vývoji.

> [!TIP]
> Nasazení modelu z Jupyter Notebook na výpočetní instanci, do webové služby na stejném virtuálním počítači, je _místní nasazení_. V tomto případě je místní počítač výpočetní instancí. Další informace o nasazeních najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Požadavky

- Azure Machine Learning pracovní prostor se spuštěnou výpočetní instancí. Další informace najdete v tématu [nastavení prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Nasazení do výpočetních instancí

Příklad poznámkového bloku, který ukazuje místní nasazení, je součástí výpočetní instance. Pomocí následujících kroků načtete Poznámkový blok a nasadíte model jako webovou službu na virtuálním počítači:

1. V [Azure Machine Learning Studiu](https://ml.azure.com)vyberte své Azure Machine Learning výpočetní instance.

1. Otevřete `samples-*` podadresář a pak otevřete `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb` . Po otevření spusťte Poznámkový blok.

    ![Snímek obrazovky běžící místní služby v poznámkovém bloku](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. Poznámkový blok zobrazuje adresu URL a port, na kterém je služba spuštěná. Například, `https://localhost:6789`. Můžete také spustit buňku obsahující `print('Local service port: {}'.format(local_service.port))` k zobrazení portu.

    ![Snímek obrazovky běžícího portu místní služby](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. K otestování služby z výpočetní instance použijte `https://localhost:<local_service.port>` adresu URL. Chcete-li provést test ze vzdáleného klienta, Získejte veřejnou adresu URL služby běžící na instanci Compute. Veřejnou adresu URL lze určit pomocí následujícího vzorce: 
    * Virtuální počítač poznámkového bloku: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score` . 
    * Instance Compute: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score` . 

    Příklad: 
    * Virtuální počítač poznámkového bloku: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Instance Compute: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testování služby

Chcete-li odeslat ukázková data do běžící služby, použijte následující kód. Nahraďte hodnotu `service_url` adresou URL z předchozího kroku:

> [!NOTE]
> Při ověřování k nasazení na výpočetní instanci se ověřování provádí pomocí Azure Active Directory. Volání `interactive_auth.get_authentication_header()` v příkladu kódu ověřujete pomocí AAD a vrátí hlavičku, která se pak může použít k ověření služby na výpočetní instanci. Další informace najdete v tématu [nastavení ověřování pro Azure Machine Learning prostředky a pracovní postupy](how-to-setup-authentication.md#interactive-authentication).
>
> Při ověřování pro nasazení ve službě Azure Kubernetes nebo Azure Container Instances se používá jiná metoda ověřování. Další informace o najdete v tématu [nastavení ověřování pro Azure Machine Learning prostředky a pracovní postupy](how-to-setup-authentication.md#web-service-authentication).

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

* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Zabezpečení webové služby prostřednictvím služby Azure Machine Learning s využitím protokolu TLS](how-to-secure-web-service.md)
* [Využití modelu ML nasazeného jako webové služby](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
