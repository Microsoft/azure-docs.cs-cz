---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486014"
---
Položky v `deploymentconfig.json` dokumentu mapují na parametry pro [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry metody:

| Entita JSON | Parametr metody | Popis |
| ----- | ----- | ----- |
| `computeType` | Není k dispozici | Cílové výpočetní prostředí. Pro ACI hodnota musí `ACI`být . |
| `containerResourceRequirements` | Není k dispozici | Kontejner pro entity procesoru a paměti. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Počet jader procesoru, které mají být přiděleny. Výchozí`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Velikost paměti (v GB) přidělit pro tuto webovou službu. Výchozí`0.5` |
| `location` | `location` | Oblast Azure pro nasazení této webové služby. Pokud není zadáno umístění pracovního prostoru, bude použito. Více informací o dostupných regionech naleznete zde: [Regiony ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Určuje, zda má být pro tuto webovou službu povolena auth. Výchozí hodnota je nepravda |
| `sslEnabled` | `ssl_enabled` | Určuje, zda má být pro tuto webovou službu povolen a povoluje ssl. Výchozí hodnota je Nepravda. |
| `appInsightsEnabled` | `enable_app_insights` | Určuje, zda má být pro tuto webovou službu povolena možnost ApplicationInsights. Výchozí hodnota je nepravda |
| `sslCertificate` | `ssl_cert_pem_file` | Soubor certifikátu potřebný, pokud je povolen protokol SSL |
| `sslKey` | `ssl_key_pem_file` | Soubor klíče potřebný, pokud je povolen protokol SSL |
| `cname` | `ssl_cname` | Název c, pokud je povoleno SSL je povolena |
| `dnsNameLabel` | `dns_name_label` | Popisek názvu DNS pro koncový bod hodnocení. Pokud není zadán jedinečný popisek názvu DNS budou generovány pro koncový bod bodování. |

Následující JSON je příklad konfigurace nasazení pro použití s cli:

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```