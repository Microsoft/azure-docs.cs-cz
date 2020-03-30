---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486012"
---
Položky v `deploymentconfig.json` dokumentu mapují na parametry pro [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry metody:

| Entita JSON | Parametr metody | Popis |
| ----- | ----- | ----- |
| `computeType` | Není k dispozici | Cílové výpočetní prostředí. Pro AKS hodnota musí `aks`být . |
| `autoScaler` | Není k dispozici | Obsahuje konfigurační prvky pro automatické škálování. Podívejte se na tabulku autoscaler. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Určuje, zda má být pro webovou službu povoleno automatické škálování. `numReplicas`  = Pokud `0` `True`, ; v `False`opačném případě . |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Minimální počet kontejnerů, které mají být používány při automatickém škálování této webové služby. Výchozí `1`nastavení . |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Maximální počet kontejnerů, které mají být používány při automatickém škálování této webové služby. Výchozí `10`nastavení . |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Jak často se autoscaler pokouší škálovat tuto webovou službu. Výchozí `1`nastavení . |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Využití cíle (v procentech ze 100), které by se autoškálovací zařízení mělo pokusit udržovat pro tuto webovou službu. Výchozí `70`nastavení . |
| `dataCollection` | Není k dispozici | Obsahuje konfigurační prvky pro sběr dat. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Určuje, zda má být povolensběr dat modelu pro webovou službu. Výchozí `False`nastavení . |
| `authEnabled` | `auth_enabled` | Určuje, zda má být pro webovou službu povolení ověřování pomocí klíče. Oba `tokenAuthEnabled` `authEnabled` a `True`nemůže být . Výchozí `True`nastavení . |
| `tokenAuthEnabled` | `token_auth_enabled` | Určuje, zda má být pro webovou službu povolení ověřování tokenů. Oba `tokenAuthEnabled` `authEnabled` a `True`nemůže být . Výchozí `False`nastavení . |
| `containerResourceRequirements` | Není k dispozici | Kontejner pro entity procesoru a paměti. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Počet jader procesoru, které mají být přiděleny pro tuto webovou službu. Výchozí`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Velikost paměti (v GB) přidělit pro tuto webovou službu. Výchozí`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Určuje, zda povolit protokolování Application Insights pro webovou službu. Výchozí `False`nastavení . |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Časový čas pro vynucení pro vyhodnocování volání webové služby. Výchozí `60000`nastavení . |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Maximální souběžné požadavky na uzel pro tuto webovou službu. Výchozí `1`nastavení . |
| `maxQueueWaitMs` | `max_request_wait_time` | Maximální doba, po kterou požadavek zůstane ve frontě (v milisekundách), než bude vrácena chyba 503. Výchozí `500`nastavení . |
| `numReplicas` | `num_replicas` | Počet kontejnerů, které mají být přiděleny pro tuto webovou službu. Žádná výchozí hodnota. Pokud tento parametr není nastaven, autoscaler je povolenve výchozím nastavení. |
| `keys` | Není k dispozici | Obsahuje konfigurační prvky pro klíče. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Primární auth klíč pro tuto webovou službu |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Sekundární auth klíč pro tuto webovou službu |
| `gpuCores` | `gpu_cores` | Počet jader GPU (replika kontejneru), která mají být přidělena pro tuto webovou službu. Výchozí hodnota je 1. Podporuje pouze celé číselné hodnoty. |
| `livenessProbeRequirements` | Není k dispozici | Obsahuje konfigurační prvky pro požadavky na sondu živosti. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Jak často (v sekundách) provést sondu živosti. Výchozí hodnota je 10 sekund. Minimální hodnota je 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Počet sekund po spuštění kontejneru před spuštěním sond živosti. Výchozí hodnota je 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Počet sekund, po kterém vypadne sonda živosti. Výchozí hodnota je 2 sekundy. Minimální hodnota je 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Minimální po sobě jdoucí úspěchy pro sondu živosti, které mají být považovány za úspěšné poté, co selhal. Výchozí hodnota je 1. Minimální hodnota je 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Když pod spustí a liveness sonda selže, Kubernetes se pokusí failureThreshold časy před vzdání. Výchozí hodnota je 3. Minimální hodnota je 1. |
| `namespace` | `namespace` | Obor názvů Kubernetes, do kterého je webová služba nasazena. Až 63 alfanumerických znaků s nižšími písmeny ('a'-'z', '0'-'9') a pomlčkou ('-') znaků. První a poslední znak nemůže být pomlčky. |

Následující JSON je příklad konfigurace nasazení pro použití s cli:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
