---
title: Dotaz pro události clusteru v clusteru Azure Service Fabric pomocí rozhraní API Eventstoru | Dokumentace Microsoftu
description: Zjistěte, jak použít rozhraní Azure Service Fabric Eventstoru API k dotazování pro události platformy
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/29/2018
ms.author: dekapur
ms.openlocfilehash: 556b3375a0f5d138255ba4c46b034894b1037da0
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722322"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Dotazování rozhraní API Eventstoru pro události clusteru

Tento článek popisuje, jak zadávat dotazy na rozhraní API Eventstoru, která jsou k dispozici v Service Fabric verze 6.2 a novější – Pokud chcete získat další informace o službě Eventstoru najdete v článku [přehled Eventstoru služby](service-fabric-diagnostics-eventstore.md). V současné době služba Eventstoru lze pouze přístup k datům za posledních 7 dní (to je na základě zásad uchovávání dat diagnostiky váš cluster).

>[!NOTE]
>Rozhraní API Eventstoru jsou obecně dostupné od verze 6.4 pouze clustery Windows běží na Azure Service Fabric.

Rozhraní API Eventstoru lze přistupovat přímo přes koncový bod REST, nebo prostřednictvím kódu programu. V závislosti na dotazu existuje několik parametrů, které je potřeba získat správná data. Tyto parametry obvykle zahrnují:
* `api-version`: verze rozhraní API Eventstoru používáte
* `StartTimeUtc`: Určuje začátek doby zájem o prohlížení
* `EndTimeUtc`: konec časového období

Kromě těchto parametrů, volitelné parametry k dispozici jsou také jako:
* `timeout`: přepsat výchozí 60 druhý časový limit pro provedení operace žádosti
* `eventstypesfilter`: získáte tak možnost filtrování pro určité typy událostí
* `ExcludeAnalysisEvents`: nevrátí události "Analýzy". Ve výchozím nastavení Eventstoru dotazy budou vracet s událostmi "analýza" kde je to možné. Události analýzy jsou bohatší provozní kanál událostí, které obsahují další kontext a informace o normální událost Service Fabric a poskytují větší hloubky.
* `SkipCorrelationLookup`: není hledejte potenciální korelačních událostí v clusteru. Ve výchozím nastavení pokusí Eventstoru korelovat události napříč clusterem a propojit události Pokud je to možné. 

Každá entita v clusteru může být dotazy na události. Můžete také zadávat dotazy pro události pro všechny entity typu. Například můžete zadat dotaz na události pro konkrétní uzel nebo pro všechny uzly v clusteru. Je aktuální sadu entit, pro které můžete zadat dotaz na události (s jakým způsobem bude strukturovaná dotazu):
* Cluster: `/EventsStore/Cluster/Events`
* Uzly: `/EventsStore/Nodes/Events`
* Uzel: `/EventsStore/Nodes/<NodeName>/$/Events`
* Aplikace: `/EventsStore/Applications/Events`
* Aplikace: `/EventsStore/Applications/<AppName>/$/Events`
* Služby: `/EventsStore/Services/Events`
* Služba: `/EventsStore/Services/<ServiceName>/$/Events`
* Oddíly: `/EventsStore/Partitions/Events`
* Oddíl: `/EventsStore/Partitions/<PartitionID>/$/Events`
* Repliky: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Repliky: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Při odkazování na aplikace nebo název služby, není nutné zahrnovat dotaz "fabric: /" předponu. Kromě toho umožňuje přepnout Pokud vaše aplikace nebo názvy služeb znak "/" v nich, "~" zachovat funkční dotaz. Například, pokud vaše aplikace se zobrazí jako "fabric: / App1/FrontendApp", dotazech určité aplikaci by strukturované jako `/EventsStore/Applications/App1~FrontendApp/$/Events`.
>Kromě toho sestav o stavu služby ještě dnes zobrazovala pod příslušné aplikace, takže by dotázat `DeployedServiceHealthReportCreated` události entity správné aplikace. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Dotaz Eventstoru prostřednictvím koncových bodů rozhraní REST API

Můžete dát dotaz na Eventstoru přímo přes koncový bod REST, tím, že `GET` žádosti: `<your cluster address>/EventsStore/<entity>/Events/`.

Například, aby bylo možné dotazovat pro všechny události clusteru mezi `2018-04-03T18:00:00Z` a `2018-04-04T18:00:00Z`, vaši žádost vypadat nějak takto:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

To může vrátit žádné události nebo seznam událostí, které jsou vráceny ve formátu json:

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

Tady vidíme, že mezi `2018-04-03T18:00:00Z` a `2018-04-04T18:00:00Z`, tento cluster úspěšně dokončil jeho nejdříve upgradovat, pokud byl nejdříve jednoduchému, z `"CurrentClusterVersion": "0.0.0.0:"` k `"TargetClusterVersion": "6.2:1.0"`v `"OverallUpgradeElapsedTimeInMs": "120196.5212"`.

## <a name="query-the-eventstore-programmatically"></a>Dotazování Eventstoru prostřednictvím kódu programu

Můžete také zadávat dotazy Eventstoru prostřednictvím kódu programu, přes [klientské knihovny pro Service Fabric](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Jakmile budete mít nastavení klienta služby prostředků infrastruktury, můžete zadat dotaz na události, díky přístupu do Eventstoru takto: ` sfhttpClient.EventStore.<request>`

Tady je příklad žádosti pro všechny clusteru událostí mezi `2018-04-03T18:00:00Z` a `2018-04-04T18:00:00Z`, prostřednictvím `GetClusterEventListAsync` funkce.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Tady je další příklad, který dotazuje na stav clusteru a všechny události uzlu v září 2018 a vytiskne navýšení kapacity.

```csharp
  const int timeoutSecs = 60;
  var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
  var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

  var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
  Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());

  
  Console.WriteLine("Querying for node events...");
  var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
      "2018-09-01T00:00:00Z",
      "2018-09-30T23:59:59Z",
      timeoutSecs,
      "NodeDown,NodeUp")
      .GetAwaiter()
      .GetResult()
      .ToList();
  Console.WriteLine("Result Count: {0}", nodesEvents.Count());

  foreach (var nodeEvent in nodesEvents)
  {
      Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
      if (nodeEvent is NodeDownEvent)
      {
          var nodeDownEvent = nodeEvent as NodeDownEvent;
          Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
      }
      else if (nodeEvent is NodeUpEvent)
      {
          var nodeUpEvent = nodeEvent as NodeUpEvent;
          Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
      }
  }
```

## <a name="sample-scenarios-and-queries"></a>Vzorové scénáře a dotazy

Tady je několik příkladů v jak může volat rozhraní REST API pro zjištění stavu vašeho clusteru události Store.

*Inovace clusteru:*

Pokud chcete zobrazit čas poslední clusteru se úspěšně nebo došlo k pokusu o upgradovat minulý týden, můžete dát dotaz na rozhraní API pro nedávno dokončených upgradů ke svému clusteru, pomocí dotazu pro události "ClusterUpgradeCompleted" Eventstoru: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problémy upgradu clusteru:*

Podobně pokud byly nějaké problémy s nedávným upgradem clusteru, můžete dotaz na všechny události pro entitu clusteru. Zobrazí se vám různé události, včetně upgradů a každý UD, pro kterou inovace prostřednictvím je úspěšně vrácený zahájení. Události pro bod, ve kterém se zobrazí také vrácení práce a odpovídající události týkající se stavu. Tady je dotaz, který použijete pro toto: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Změny stavu uzlu:*

Pokud chcete zobrazit že uzel stav se změní za posledních několik dnů – při uzlů nahoru nebo dolů, se nepovedlo nebo se aktivuje nebo deaktivuje (podle platformy, službu chaos nebo ze vstupu uživatele) – použijte tento dotaz: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Události aplikace:*

Můžete také sledovat, poslední nasazení aplikací a inovací. Pomocí následujícího dotazu zobrazíte všechny události aplikace ve vašem clusteru: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Historický stav aplikace:*

Kromě toho jenom události životního cyklu aplikace, můžete také zobrazit historická data na stav této konkrétní aplikace. Můžete to provést tak, že zadáte název aplikace, pro které chcete shromažďovat data. Pomocí tohoto dotazu zobrazíte všechny události stavu aplikace: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`. Pokud budete chtít zahrnout stavu události, které možná vypršela platnost (pryč uvádění předán live (TTL)), přidejte `,ApplicationHealthReportExpired` na konec dotazu k filtrování dva typy událostí.

*Historie stavu pro všechny služby v "aplikace":*

V současné době se sestava události stavu služby zobrazují jako `DeployedServicePackageNewHealthReport` události v rámci odpovídající entity aplikací. Chcete-li zobrazit, jak mají byla vaše služby to jde pro "Počítač App1", použijte následující dotaz: `https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Rekonfigurace oddílu:*

Aby se zobrazily všechny přesuny oddílů, ke kterým došlo ve vašem clusteru dotázat `PartitionReconfigured` událostí. To vám může pomoct zjistit, co úlohy byly spuštěny v který uzel v určitých časech, kdy Diagnostika problémů ve vašem clusteru. Tady je ukázkový dotaz, který činí: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Chaos služby:*

Je událost pro při tento Chaos se dá služba spuštěna nebo zastavena, který je vystaven na úrovni clusteru. Poslední použití službu Chaos zobrazíte pomocí následujícího dotazu: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

