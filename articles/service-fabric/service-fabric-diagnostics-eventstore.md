---
title: Úložiště událostí v Azure Service Fabric
description: Přečtěte si o Eventstoru Azure Service Fabric, jak můžete kdykoli pochopit a monitorovat stav clusteru nebo úloh.
ms.topic: conceptual
ms.date: 6/6/2019
ms.openlocfilehash: ef5049fd934a29fa1d96514c334b13358e6600cf
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626550"
---
# <a name="eventstore-overview"></a>Eventstoru – přehled

>[!NOTE]
>Od verze Service Fabric 6,4. Rozhraní API Eventstoru jsou dostupná jenom pro clustery s Windows, které běží jenom na Azure. Pracujeme na přenosu této funkce na Linux i na naší samostatné clustery.

## <a name="overview"></a>Přehled

Služba Eventstoru byla představena ve verzi 6,2, ale možnost monitorování v Service Fabric. Eventstoru poskytuje způsob, jak pochopit stav clusteru nebo úloh v daném časovém okamžiku.
Eventstoru je stavová služba Service Fabric, která udržuje události z clusteru. Událost se zveřejňuje prostřednictvím Service Fabric Explorer, REST a rozhraní API. Eventstoru dotazuje cluster přímo, aby získal diagnostická data na jakékoli entitě v clusteru a měla by se používat k usnadnění:

* Diagnostikujte problémy při vývoji nebo testování nebo na místě, kde je možné používat sledovací kanál.
* Ověřte, že se správně zpracovávají akce správy, které provedete v clusteru.
* Získání "snímku" způsobu, jakým Service Fabric interakci s konkrétní entitou

![Snímek obrazovky se zobrazí na kartě události v podokně uzly několik událostí, včetně události NodeDown.](media/service-fabric-diagnostics-eventstore/eventstore.png)

Úplný seznam událostí, které jsou k dispozici v Eventstoru, najdete v tématu [události Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Od verze Service Fabric 6,4. Rozhraní Eventstoru API a uživatelské prostředí jsou všeobecně k dispozici pro clustery Azure Windows. Pracujeme na přenosu této funkce na Linux i na naší samostatné clustery.

Služba Eventstoru se dá dotazovat na události, které jsou k dispozici pro každou entitu a typ entity v clusteru. To znamená, že se můžete dotazovat na události na následujících úrovních:
* Cluster: události specifické pro samotný cluster (například upgrade clusteru)
* Uzly: události na úrovni všech uzlů
* Uzel: události specifické pro jeden uzel identifikovaný `nodeName`
* Aplikace: všechny události na úrovni aplikace
* Aplikace: události specifické pro jednu aplikaci identifikovanou `applicationId`
* Služby: události ze všech služeb ve vašich clusterech
* Služba: události z konkrétní služby identifikované `serviceId`
* Oddíly: události ze všech oddílů
* Oddíl: události z konkrétního oddílu identifikovaného `partitionId`
* Repliky oddílů: události ze všech replik/instancí v rámci určitého oddílu identifikovaného `partitionId`
* Replika oddílu: události z konkrétní repliky/instance identifikované `replicaId` a `partitionId`

Další informace o rozhraní API najdete v referenčních informacích k [rozhraní eventstoru API](/rest/api/servicefabric/sfclient-index-eventsstore).

Služba Eventstoru má taky možnost korelovat události v clusteru. Když prohlížíte události, které byly zapsané současně z různých entit, které by mohly mít vliv na sebe navzájem, služba Eventstoru je schopná propojit tyto události s cílem identifikovat příčiny aktivit ve vašem clusteru. Například pokud dojde k tomu, že jedna z vašich aplikací přestane být v pořádku bez jakýchkoli nevolaných změn, Eventstoru se také podívat na další události vystavené platformou a může je korelovat `Error` `Warning` událostmi nebo. To pomáhá s rychlejší detekcí selhání a hlavními příčinami při analýze.

## <a name="enable-eventstore-on-your-cluster"></a>Povolení Eventstoru v clusteru

### <a name="local-cluster"></a>Místní cluster

V [fabricSettings.jsv clusteru](service-fabric-cluster-fabric-settings.md)přidejte EventStoreService jako funkci addOn a proveďte upgrade clusteru.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Cluster Azure verze 6.5 +
Pokud se cluster Azure upgraduje na verzi 6,5 nebo vyšší, Eventstoru se ve vašem clusteru automaticky povolí. Chcete-li se odhlásit, musíte aktualizovat šablonu clusteru pomocí následujících kroků:

* Použijte verzi rozhraní API `2019-03-01` nebo novější. 
* Přidejte následující kód do oddílu Properties (vlastnosti) v clusteru.
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Cluster Azure verze 6,4

Pokud používáte verzi 6,4, můžete upravit šablonu Azure Resource Manager a zapnout službu Eventstoru. K tomu je potřeba provést [Upgrade konfigurace clusteru](service-fabric-cluster-config-upgrade-azure.md) a přidat následující kód. pomocí PlacementConstraints můžete umístit repliky služby eventstoru na konkrétní typ NodeType, např. na uzel NodeType vyhrazený pro systémové služby. V `upgradeDescription` části se nakonfiguruje upgrade konfigurace, aby se aktivoval restart na uzlech. Oddíl můžete odebrat v jiné aktualizaci.

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
* Začínáme s rozhraním API pro Eventstoru – [používání rozhraní API eventstoru v clusterech Azure Service Fabric](service-fabric-diagnostics-eventstore-query.md)
* Další informace o seznamu událostí nabízených [událostmi](service-fabric-diagnostics-event-generation-operational.md) eventstoru-Service Fabric
* Přehled monitorování a diagnostiky v Service Fabric – [monitorování a Diagnostika pro Service Fabric](service-fabric-diagnostics-overview.md)
* Zobrazit úplný seznam volání rozhraní API – [eventstoru REST API Reference](/rest/api/servicefabric/sfclient-index-eventsstore)
* Přečtěte si další informace o monitorování clusteru – [monitorování clusteru a platformy](service-fabric-diagnostics-event-generation-infra.md).
