---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: a87773c8f6ddda11b62064758b46d666740a03c2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68556938"
---
Položky v `deploymentconfig.json` dokumentu jsou mapovány na parametry pro [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry pro metodu:

| Entita JSON | Parametr metody | Popis |
| ----- | ----- | ----- |
| `computeType` | Není k dispozici | Cílové výpočetní prostředí. Pro místní musí být `local`hodnota. |
| `port` | `port` | Místní port, na kterém má být vystaven koncový bod HTTP služby. |

Následující kód JSON je příkladem konfigurace nasazení pro použití s rozhraním příkazového řádku:

```json
{
    "computeType": "local",
    "port": 32267
}
```
