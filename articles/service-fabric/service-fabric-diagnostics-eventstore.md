---
title: Azure Service Fabric události Store | Dokumentace Microsoftu
description: Další informace o Azure Service Fabric Eventstoru
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: b66373b6847b96a4fcbc1a0c9da42d285d089a9d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727881"
---
# <a name="eventstore-service-overview"></a>Přehled služby Eventstoru

>[!NOTE]
>Od verze 6.4 Service Fabric. rozhraní API Eventstoru jsou dostupné jenom pro clustery Windows pouze běžící v Azure. Pracujeme na přenos tuto funkci pro Linux, stejně jako naše samostatné clustery.

## <a name="overview"></a>Přehled

Počínaje verze 6.2, je služba Eventstoru možnosti monitorování v Service Fabric. Eventstoru poskytuje způsob, jak porozumět stavu clusteru nebo úloh v daném bodě v čase. Eventstoru je stavovou službu Service Fabric, který udržuje události z clusteru. Události jsou vystaveny prostřednictvím Service Fabric Explorer, REST a rozhraní API. Eventstoru dotazů clusteru přímo k získání diagnostických dat na entitu ve vašem clusteru a by měla sloužit ke:

* Diagnostikujte problémy ve vývoj a testování, nebo kde může pomocí monitorování kanálu
* Potvrďte, že správně zpracovává akce správy, které je možné ve vašem clusteru
* Získat "snímek" jak dixons carphone Service Fabric s konkrétní entity


Pokud chcete zobrazit úplný seznam událostí, které jsou k dispozici v Eventstoru, naleznete v tématu [události Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Od verze Service Fabric verze 6.2. rozhraní API Eventstoru jsou aktuálně ve verzi preview pro clustery Windows pouze běžící v Azure. Pracujeme na přenos tuto funkci pro Linux, stejně jako naše samostatné clustery.

Pro události, které jsou k dispozici pro každou entitu a typ entity ve vašem clusteru může být dotazována Eventstoru služby. To znamená, že můžete zadat dotaz na události na následujících úrovních:
* Cluster: události specifické pro cluster sám (např. upgrade clusteru)
* Uzly: všechny uzel úroveň události
* Uzel: události specifické pro jeden uzel, identifikovaný `nodeName`
* Aplikace: všech událostí na úrovni aplikace
* Aplikace: specifické pro jednu aplikaci identifikovaný události `applicationId`
* Služby: události ze všech služeb ve vašich clusterů
* Služby: události z konkrétní služby identifikovaný `serviceId`
* Oddíly: události ze všech oddílů
* Oddíl: události z konkrétního oddílu identifikovaný `partitionId`
* Repliky oddílů: události ze všech replik nebo instancí v rámci konkrétního oddílu identifikovaný `partitionId`
* Repliky oddílu: události z konkrétní repliky / instance identifikovaný `replicaId` a `partitionId`

Další informace o rozhraní API, projděte si [referenční dokumentace rozhraní API Eventstoru] ((https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).

Služba Eventstoru má také možnost ke korelaci událostí ve vašem clusteru. Zobrazením událostí, které byly vytvořeny ve stejnou dobu od různé entity, které může mít vliv na sobě navzájem, služba Eventstoru je moci připojit k těmto událostem a pomůže identifikovat příčiny pro aktivity ve vašem clusteru. Například pokud nastane jednou z vašich aplikací bez nutnosti jakkoli měnit vyvolané špatného, Eventstoru se také podívat na další události vystavené platformy a to s může korelovat `Error` nebo `Warning` událostí. To pomáhá při rychlejší detekce chyb a hlavní příčiny potíží analýzy.

## <a name="enable-eventstore-on-your-cluster"></a>Povolit ve vašem clusteru Eventstoru

### <a name="local-cluster"></a>Místní Cluster

V [fabricSettings.json ve vašem clusteru](service-fabric-cluster-fabric-settings.md), přidejte EventStoreService jako doplněk funkce a provedení upgradu clusteru.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster"></a>Azure cluster

Ve vašem clusteru šablony Azure Resource Manageru, můžete zapnout službu Eventstoru pomocí provádí [config upgradovat cluster](service-fabric-cluster-config-upgrade-azure.md) a přidáním následujícího kódu. `upgradeDescription` Části nakonfiguruje upgrade config aktivovat restartování na uzlech. Odebrat oddíl v jiné aktualizace.

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


## <a name="next-steps"></a>Další postup
* Začínáme s rozhraním API Eventstoru - [v clusterech Azure Service Fabric pomocí rozhraní API Eventstoru](service-fabric-diagnostics-eventstore-query.md)
* Další informace o seznamu událostí, které nabízí Eventstoru – [události Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* Přehled monitorování a diagnostiky v Service Fabric – [monitorování a Diagnostika pro Service Fabric](service-fabric-diagnostics-overview.md)
* Zobrazit úplný seznam volání rozhraní API – [Reference k REST API Eventstoru](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* Další informace o monitorování clusteru- [monitorování clusteru a platforma](service-fabric-diagnostics-event-generation-infra.md).