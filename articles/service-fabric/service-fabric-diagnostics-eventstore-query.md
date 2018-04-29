---
title: Dotaz pro události clusteru pomocí rozhraní API EventStore v clusterech Azure Service Fabric | Microsoft Docs
description: Naučte se používat rozhraní Azure Service Fabric EventStore API dotazu pro události platformy
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: af97385981c61f32c4136921d3cf14a526fc6ddb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Rozhraní API EventStore dotazu pro události clusteru

Tento článek popisuje postup dotazování EventStore rozhraní API, která jsou k dispozici v Service Fabric verze 6.2 a novější – Pokud chcete další informace o službě EventStore najdete [Přehled služby EventStore](service-fabric-diagnostics-eventstore.md). V současné době službu EventStore přístup jenom k data za posledních 7 dní (to je na základě zásad uchovávání dat diagnostiky vašeho clusteru).

>[!NOTE]
>Od verze Service Fabric verze 6.2. rozhraní API EventStore jsou aktuálně ve verzi preview pro clustery Windows jenom spuštěné v Azure. Pracujeme na portování tuto funkci do systému Linux a také naše samostatné clustery.

Rozhraní API EventStore lze přistupovat přímo přes koncový bod REST nebo prostřednictvím kódu programu. V závislosti na dotaz jsou několik parametrů, které jsou potřebné ke shromáždění správná data. Obvykle patří:
* `api-version`: verze rozhraní API EventStore používáte
* `StartTimeUtc`: definuje se začátkem období jste zajímavé v prohlížení
* `EndTimeUtc`: konci období

Kromě toho existuje volitelné parametry dostupné taky jako:
* `timeout`: přepsat výchozí časový limit 60 sekund pro provádění operací požadavku
* `eventstypesfilter`: To vám dává možnost filtrování pro určité typy událostí
* `ExcludeAnalysisEvents`: nevrátí, analýzy"události. Ve výchozím nastavení EventStore dotazy budou vracet s událostmi "analysis", kde je to možné – analýza události jsou bohatší provozní kanál události, které obsahují další kontext nebo informace o nad rámec pravidelné události Service Fabric a poskytují větší hloubky.
* `SkipCorrelationLookup`: není vyhledejte potenciální korelační události v clusteru. Ve výchozím nastavení EventStore pokusí vazbu mezi události v clusteru a propojit událostí společně, pokud je to možné. 

Každá entita v clusteru může být dotazy na události. Můžete také vyhledat události pro všechny entity typu. Například můžete dotazovat pro události pro konkrétní uzel nebo pro všechny uzly v clusteru. Aktuální sadu entit, pro které se můžete dotazovat pro události je (při tom, jak by být strukturovaná dotazu):
* Cluster: `/EventsStore/Cluster/Events`
* Uzly: `/EventsStore/Nodes/Events`
* Uzel: `/EventsStore/Nodes/<NodeName>/$/Events`
* Aplikace: `/EventsStore/Applications/Events`
* Aplikace: `/EventsStore/Applications/<AppName>/$/Events`
* Služba: `/EventsStore/Services/Events`
* Služba: `/EventsStore/Services/<ServiceName>/$/Events`
* Oddíly: `/EventsStore/Partitions/Events`
* Oddíl: `/EventsStore/Partitions/<PartitionID>/$/Events`
* Repliky: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Repliky: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Při odkazování na aplikace nebo název služby, nemusí zahrnovat dotaz "fabric: /" předponu. Navíc pokud svou aplikaci nebo názvy služby "/" v nich, přepínače, aby "~" zachovat pracovní dotazu. Například, pokud vaše aplikace se zobrazí jako "fabric: / App1/FrontendApp", vaše aplikace konkrétní dotazy by strukturovaná jako `/EventsStore/Applications/App1~FrontendApp/$/Events`.
>Navíc sestavy stavu pro služby dnes zobrazí na odpovídající aplikaci, aby mohl by dotazy pro `DeployedServiceHealthReportCreated` události pro entitu správné aplikace. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Dotaz EventStore prostřednictvím koncových bodů rozhraní REST API

Tím, že se můžete dotazovat EventStore přímo přes koncový bod REST, `GET` žádosti: `<your cluster address>/EventsStore/<entity>/Events/`.

Např. Chcete-li vyhledat všechny události clusteru mezi `2018-04-03T18:00:00Z` a `2018-04-04T18:00:00Z`, bude vypadat vaší žádosti:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.2-preview&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Buď to může vrátí chybu, pokud nejsou k dispozici žádné události nebo pokud byl úspěšný, zobrazí se událostí vrácených ve formátu json:

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

Zde jsme uvidíte, že mezi `2018-04-03T18:00:00Z` a `2018-04-04T18:00:00Z`, tento cluster úspěšně dokončil svůj první upgrade, pokud byl nejdříve platnosti, z `"CurrentClusterVersion": "0.0.0.0:"` k `"TargetClusterVersion": "6.2:1.0"`v `"OverallUpgradeElapsedTimeInMs": "120196.5212"`.

## <a name="query-the-eventstore-programmatically"></a>Dotaz EventStore prostřednictvím kódu programu

Můžete taky zadat dotaz EventStore prostřednictvím kódu programu, prostřednictvím [Klientská knihovna pro Service Fabric](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Jakmile máte vašeho klienta Fabric Service nastavení, můžete dát dotaz pro události přístupu k EventStore takto: ` sfhttpClient.EventStore.<request>`

Tady je příklad požadavek pro všechny události mezi clusteru `2018-04-03T18:00:00Z` a `2018-04-04T18:00:00Z`, prostřednictvím `GetClusterEventListAsync` funkce.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

## <a name="sample-scenarios-and-queries"></a>Vzorové scénáře a dotazy

Tady je několik příkladů na tom, jak můžete volat rozhraní API REST úložiště událostí zjistit stav clusteru.

1. Upgrade clusteru:

Pokud chcete zobrazit čas poslední clusteru byla úspěšně nebo pokus o upgrade poslední týden, můžete dát dotaz na rozhraní API pro nedávno provedených upgrady do clusteru, pomocí dotazu pro "ClusterUpgradeComplete" události EventStore: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeComplete`

1. Problémy s upgradem clusteru:

Podobně pokud byly nějaké problémy s poslední upgrade clusteru, můžete dotazovat pro všechny události pro entitu clusteru. Zobrazí se různé události, včetně zahájení upgrady a každý UD, pro kterou upgrade prostřednictvím úspěšně vrátit. Události pro bod, ve kterém se zobrazí také vrácení spuštěn a odpovídajících události stavu. Zde je dotaz, které se používají pro toto: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

1. Změny stavu uzlu:

Pokud chcete zobrazit, že se stav uzlu změní přes posledních několik dní – když uzly se nahoru nebo dolů, nebo byly aktivace nebo deaktivace (podle platformy, službu chaos, nebo z uživatelský vstup) – použijte následující dotaz: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

1. Události aplikace:

Můžete také sledovat poslední nasazení aplikací a upgrady. Použijte tento dotaz zobrazíte všechny aplikace související události v clusteru: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

1. Historie stavu pro aplikaci:

Kromě pouze na zobrazení události životního cyklu aplikací, můžete také zobrazit historická data na stav této konkrétní aplikace. To provedete tak, že zadáte název aplikace, pro které chcete shromažďovat data. Pomocí tohoto dotazu zobrazíte všechny události stavu aplikace: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.2-preview&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ProcessApplicationReport`. Pokud chcete zahrnout události stavu, které platnost vypršela (zmizel předaný za provozu (TTL) vypršela doba pro jejich), přidejte `,ExpiredDeployedApplicationEvent` na konec dotaz pro filtrování dva typy událostí.

1. Historie stavu pro všechny služby v "Moje aplikace":

V současné době události sestavy stavu pro služby zobrazují jako `DeployedServiceHealthReportCreated` události v rámci odpovídající entity aplikací. Chcete-li zobrazit úspěšnost vašich služeb pro "App1", použijte následující dotaz: `https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServiceHealthReportCreated`

1. Rekonfigurace oddílu:

Aby se zobrazily všechny přesuny oddílu, které bylo provedeno v clusteru, vyhledat `ReconfigurationCompleted` událostí. To vám může pomoci zjistit, co úlohy byla spuštěna na uzel v určitých časech, při diagnostikování vystaví v clusteru. Tady je ukázkový dotaz, který nemá který: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.2-preview&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigurationCompleted`

1. Služba Chaos:

Existuje událost pro při Chaos spuštěna nebo který je zastavena služba umístěné na úrovni clusteru. Pokud chcete zobrazit poslední používání Chaos služby, použijte následující dotaz: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

