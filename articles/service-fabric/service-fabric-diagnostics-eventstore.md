---
title: Úložiště událostí Azure Service Fabric
description: Zjistěte o EventStore Azure Service Fabric, způsob, jak pochopit a sledovat stav clusteru nebo úlohy kdykoli.
author: srrengar
ms.topic: conceptual
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: d23c8114bf10ef3225775accef6910c0ba539e15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645731"
---
# <a name="eventstore-overview"></a>Přehled úložiště událostí

>[!NOTE]
>Od service fabric verze 6.4. Rozhraní API úložiště událostí jsou dostupná jenom pro clustery Windows spuštěné jenom v Azure. Pracujeme na portování této funkce na Linux, stejně jako naše samostatné clustery.

## <a name="overview"></a>Přehled

Služba EventStore, která byla zavedena ve verzi 6.2, je v service fabricu možností monitorování. EventStore poskytuje způsob, jak pochopit stav clusteru nebo úlohy v daném okamžiku.
EventStore je stavová služba Service Fabric, která udržuje události z clusteru. Události jsou vystaveny prostřednictvím aplikace Service Fabric Explorer, REST a rozhraní API. EventStore se dotazuje clusteru přímo na získání diagnostických dat na libovolné entitě v clusteru a měl by být použit k pomoci:

* Diagnostikovat problémy ve vývoji nebo testování nebo kde pravděpodobně používáte kanál pro monitorování
* Zkontrolujte, zda jsou akce správy, které provádíte v clusteru, zpracovávány správně.
* Získejte "snímek" o tom, jak Service Fabric interaguje s konkrétní entitou

![Úložiště událostí](media/service-fabric-diagnostics-eventstore/eventstore.png)

Úplný seznam událostí dostupných v eventstore, najdete v tématu [Service Fabric události](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Od service fabric verze 6.4. Rozhraní API úložiště událostí a uživatelské rozhraní jsou obecně dostupné pro clustery Azure Windows. Pracujeme na portování této funkce na Linux, stejně jako naše samostatné clustery.

Služba EventStore může být dotazována na události, které jsou k dispozici pro každou entitu a typ entity v clusteru. To znamená, že můžete dotazovat na události na následujících úrovních:
* Cluster: události specifické pro samotný cluster (např. upgrade clusteru)
* Uzly: všechny události na úrovni uzlu
* Uzel: události specifické pro jeden uzel, identifikované`nodeName`
* Aplikace: všechny události na úrovni aplikace
* Použití: události specifické pro jednu aplikaci identifikované`applicationId`
* Služby: události ze všech služeb ve vašich klastrech
* Služba: události z konkrétní služby identifikované`serviceId`
* Oddíly: události ze všech oddílů
* Oddíl: události z určitého oddílu identifikovaného`partitionId`
* Repliky oddílů: události ze všech replik / instancí v rámci určitého oddílu identifikovaného`partitionId`
* Replika oddílu: události z určité `replicaId` repliky / instance identifikované a`partitionId`

Další informace o rozhraní API najdete v [odkazu rozhraní API úložiště událostí](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).

Služba EventStore má také možnost korelovat události ve vašem clusteru. Při pohledu na události, které byly napsány současně z různých entit, které mohou mít vliv na sebe, eventstore služba je schopna propojit tyto události pomoci s identifikací příčin aktivit v clusteru. Například pokud jeden z vašich aplikací stane není v pořádku bez jakékoli vyvolané změny, EventStore bude také podívat na jiné události vystavené platformou a může korelovat to nebo `Error` `Warning` událost. To pomáhá s rychlejší detekcí selhání a analýzou příčin kořenů.

## <a name="enable-eventstore-on-your-cluster"></a>Povolení úložiště událostí v clusteru

### <a name="local-cluster"></a>Místní cluster

V [souboru fabricSettings.json v clusteru](service-fabric-cluster-fabric-settings.md)přidejte službu EventStoreService jako funkci addOn a proveďte upgrade clusteru.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Azure cluster verze 6.5+
Pokud se váš cluster Azure upgraduje na verzi 6.5 nebo vyšší, eventstore se ve vašem clusteru automaticky aktivuje. Chcete-li se odhlásit, je třeba aktualizovat šablonu clusteru pomocí následujících možností:

* Použití verze rozhraní `2019-03-01` API nebo novější 
* Přidání následujícího kódu do oddílu vlastností v clusteru
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Cluster Azure verze 6.4

Pokud používáte verzi 6.4, můžete upravit šablonu Azure Resource Manager a zapnout službu EventStore. To se provádí provedením [upgradu konfigurace clusteru](service-fabric-cluster-config-upgrade-azure.md) a přidáním následujícího kódu, můžete použít PlacementConstraints umístit repliky služby EventStore na konkrétní NodeType např. V `upgradeDescription` části nakonfiguruje upgrade konfigurace tak, aby spustil restartování uzlů. Oddíl můžete odebrat v jiné aktualizaci.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>Další kroky
* Začínáme s rozhraním EventStore API – [použití rozhraní API eventstore v clusterech Azure Service Fabric](service-fabric-diagnostics-eventstore-query.md)
* Další informace o seznamu událostí nabízených eventstore - [Service Fabric události](service-fabric-diagnostics-event-generation-operational.md)
* Přehled monitorování a diagnostiky v service fabric - [monitoring a diagnostika pro service fabric](service-fabric-diagnostics-overview.md)
* Zobrazit úplný seznam volání rozhraní API – [odkaz na rozhraní REST API eventstore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* Další informace o monitorování clusteru – [Monitorování clusteru a platformy](service-fabric-diagnostics-event-generation-infra.md).
