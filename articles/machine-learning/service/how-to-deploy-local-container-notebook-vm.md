---
title: Postup nasazení modelů do výpočetních instancí
titleSuffix: Azure Machine Learning
description: Naučte se, jak nasadit modely Azure Machine Learning jako webovou službu pomocí výpočetních instancí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: bb187826250b3edc9ac3d9e36a243d75819a45b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496871"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Nasazení modelu pro Azure Machine Learning výpočetních instancí

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

> [!NOTE]
> Výpočetní instance jsou k dispozici pouze pro pracovní prostory s oblastí **střed USA – sever** nebo **Velká Británie – jih**.
>Pokud je váš pracovní prostor v jakékoli jiné oblasti, můžete místo toho vytvořit a použít [virtuální počítač s poznámkovým blokem](concept-compute-instance.md#notebookvm) .  Model můžete nasadit buď do výpočetní instance, nebo do virtuálního počítače poznámkového bloku pomocí kroků v tomto článku.

Naučte se používat Azure Machine Learning k nasazení modelu jako webové služby ve vaší instanci služby Azure Machine Learning Compute. Pokud je splněna jedna z následujících podmínek, použijte výpočetní instance:

- Potřebujete rychle nasadit a ověřit model.
- Testujete model, který je ve vývoji.

> [!TIP]
> Nasazení modelu z Jupyter Notebook na výpočetní instanci, do webové služby na stejném virtuálním počítači, je _místní nasazení_. V tomto případě je místní počítač výpočetní instancí. Další informace o nasazeních najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Předpoklady

- Azure Machine Learning pracovní prostor se spuštěnou výpočetní instancí. Další informace najdete v tématu [nastavení prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Nasazení do výpočetních instancí

Příklad poznámkového bloku, který ukazuje místní nasazení, je součástí výpočetní instance. Pomocí následujících kroků načtete Poznámkový blok a nasadíte model jako webovou službu na virtuálním počítači:

1. V [Azure Machine Learning Studiu](https://ml.azure.com)vyberte své Azure Machine Learning výpočetní instance.

1. Otevřete podadresář `samples-*` a pak otevřete `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. Po otevření spusťte Poznámkový blok.

    ![Snímek obrazovky běžící místní služby v poznámkovém bloku](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. Poznámkový blok zobrazuje adresu URL a port, na kterém je služba spuštěná. Například, `https://localhost:6789`. Můžete také spustit buňku obsahující `print('Local service port: {}'.format(local_service.port))` pro zobrazení portu.

    ![Snímek obrazovky běžícího portu místní služby](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. K otestování služby z výpočetní instance použijte `https://localhost:<local_service.port>` URL. Chcete-li provést test ze vzdáleného klienta, Získejte veřejnou adresu URL služby běžící na instanci Compute. Veřejnou adresu URL lze určit pomocí následujícího vzorce: 
    * Virtuální počítač poznámkového bloku: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * Instance Compute: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 
    
    Například: 
    * Virtuální počítač poznámkového bloku: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Instance Compute: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testování služby

Chcete-li odeslat ukázková data do běžící služby, použijte následující kód. Hodnotu `service_url` nahraďte adresou URL z předchozího kroku:

```python
import requests
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')
access_token = "your bearer token"
headers = {'Content-Type':'application/json', 'Authorization': 'Bearer ' + access_token}
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Další kroky

* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Zabezpečené Azure Machine Learning webové služby pomocí protokolu SSL](how-to-secure-web-service.md)
* [Využití modelu ML nasazeného jako webové služby](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)