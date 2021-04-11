---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 0eeb82245a53c93af75fc3ce3f37cb588295e5b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508091"
---
Položky v dokumentu jsou `deploymentconfig.json` mapovány na parametry pro [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration). Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry pro metodu:

| Entita JSON | Parametr metody | Description |
| ----- | ----- | ----- |
| `computeType` | NA | Cílové výpočetní prostředí. Pro místní cíle musí být hodnota `local` . |
| `port` | `port` | Místní port, na kterém má být vystaven koncový bod HTTP služby. |

Tento kód JSON je příkladem konfigurace nasazení pro použití s rozhraním příkazového řádku:

```json
{
    "computeType": "local",
    "port": 32267
}
```