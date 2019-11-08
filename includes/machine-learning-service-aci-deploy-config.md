---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 10/06/2019
ms.author: larryfr
ms.openlocfilehash: b3c1ad48da0ef28c73e3af5d1e30414534f4e94d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799746"
---
Položky v dokumentu `deploymentconfig.json` namapovány na parametry pro [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry pro metodu:

| Entita JSON | Parametr metody | Popis |
| ----- | ----- | ----- |
| `computeType` | Není k dispozici | Cílové výpočetní prostředí. Pro ACI musí být hodnota `ACI`. |
| `containerResourceRequirements` | Není k dispozici | Kontejner pro entity CPU a paměti |
| &emsp;&emsp;`cpu` | `cpu_cores` | Počet jader procesoru, které se mají přidělit. Výchozí `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Velikost paměti (v GB), která má být přidělena této webové službě. Výchozí `0.5` |
| `location` | `location` | Oblast Azure, do které se má tato webová služba nasadit Pokud není zadáno, bude použito umístění pracovního prostoru. Další podrobnosti o dostupných oblastech najdete tady: [ACI oblasti](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Určuje, jestli se má pro tuto webovou službu povolit ověřování. Výchozí hodnota je false. |
| `sslEnabled` | `ssl_enabled` | Určuje, jestli se má pro tuto webovou službu povolit protokol SSL. Výchozí hodnota je false. |
| `appInsightsEnabled` | `enable_app_insights` | Určuje, jestli se má pro tuto webovou službu povolit AppInsights. Výchozí hodnota je false. |
| `sslCertificate` | `ssl_cert_pem_file` | Soubor certifikátu potřebný, pokud je povolený protokol SSL |
| `sslKey` | `ssl_key_pem_file` | Soubor klíče potřebný v případě, že je povolený protokol SSL |
| `cname` | `ssl_cname` | Záznam CNAME pro, pokud je povolený protokol SSL |
| `dnsNameLabel` | `dns_name_label` | Popisek názvu DNS pro koncový bod bodování. Pokud není zadán, bude pro koncový bod bodování vygenerován jedinečný popisek názvu DNS. |

Následující kód JSON je příkladem konfigurace nasazení pro použití s rozhraním příkazového řádku:

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