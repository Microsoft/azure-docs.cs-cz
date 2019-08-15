---
title: Postup nasazení modelů na virtuální počítače poznámkových bloků
titleSuffix: Azure Machine Learning service
description: Naučte se, jak nasadit modely služby Azure Machine Learning jako webovou službu pomocí virtuálních počítačů poznámkových bloků.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 08/08/2019
ms.openlocfilehash: d6b26dfe1eb8ea65dd7c751a148c599123b0f6db
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947757"
---
# <a name="deploy-a-model-to-notebook-vms"></a>Nasazení modelu do virtuálních počítačů poznámkových bloků

Naučte se používat službu Azure Machine Learning k nasazení modelu jako webové služby na VIRTUÁLNÍm počítači poznámkového bloku. Virtuální počítače poznámkového bloku použijte, pokud je splněná jedna z následujících podmínek:

- Potřebujete k rychlému nasazení a ověření modelu.
- Testování modelu, který je ve vývoji.

> [!TIP]
> Nasazení modelu z Jupyter Notebook na virtuálním počítači poznámkového bloku do webové služby na stejném virtuálním počítači je _místní nasazení_. V tomto případě je místní počítač VIRTUÁLNÍm počítačem poznámkového bloku. Další informace o nasazeních najdete v tématu [nasazení modelů pomocí služby Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning se spuštěným virtuálním počítačem s poznámkovým blokem. Další informace najdete v tématu [nastavení prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-notebook-vms"></a>Nasazení na virtuální počítače poznámkových bloků

Ukázkový Poznámkový blok, který ukazuje místní nasazení, se nachází na vašem VIRTUÁLNÍm počítači poznámkového bloku. Pomocí následujících kroků načtete Poznámkový blok a nasadíte model jako webovou službu na virtuálním počítači:

1. Z [Azure Portal](https://portal.azure.com)vyberte své virtuální počítače Azure Machine Learning poznámkového bloku.

1. Otevřete podadresář a pak otevřete `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. `samples-*` Po otevření spusťte Poznámkový blok.

    ![Snímek obrazovky běžící místní služby v poznámkovém bloku](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. Poznámkový blok zobrazuje adresu URL a port, na kterém je služba spuštěná. Například, `https://localhost:6789`. Můžete také spustit buňku obsahující `print('Local service port: {}'.format(local_service.port))` k zobrazení portu.

    ![Snímek obrazovky běžícího portu místní služby](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. K otestování služby z virtuálního počítače poznámkového bloku `https://localhost:<local_service.port>` použijte adresu URL. Chcete-li provést test ze vzdáleného klienta, Získejte veřejnou adresu URL služby spuštěné na virtuálním počítači poznámkového bloku. veřejnou adresu URL lze určit pomocí následujícího vzorce: `https://<notebookvm_name>-<local_service_port>.<azure_region_of_notebook>.notebooks.azureml.net/score`. Například, `https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score`.

## <a name="test-the-service"></a>Testování služby

Chcete-li odeslat ukázková data do běžící služby, použijte následující kód. Nahraďte hodnotu `service_url` adresou URL z předchozího kroku:

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
service_url = "https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score"
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Další kroky

* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Zabezpečení webových služeb Azure Machine Learning s protokolem SSL](how-to-secure-web-service.md)
* [Používání modelu ML nasadit jako webovou službu](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)