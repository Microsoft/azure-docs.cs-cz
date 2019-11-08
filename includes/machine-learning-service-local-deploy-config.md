---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/04/2019
ms.author: larryfr
ms.openlocfilehash: f35245aea0d7ba20561d1504b111e747ccbe5e0d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799745"
---
Položky v dokumentu `deploymentconfig.json` namapovány na parametry pro [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry pro metodu:

| Entita JSON | Parametr metody | Popis |
| ----- | ----- | ----- |
| `computeType` | Není k dispozici | Cílové výpočetní prostředí. Pro místní cíle musí být hodnota `local`. |
| `port` | `port` | Místní port, na kterém má být vystaven koncový bod HTTP služby. |

Tento kód JSON je příkladem konfigurace nasazení pro použití s rozhraním příkazového řádku:

```json
{
    "computeType": "local",
    "port": 32267
}
```
