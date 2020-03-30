---
title: Dotaz na události clusteru pomocí úložišť API úložiště událostí
description: Zjistěte, jak používat azure service fabric eventstore eventstore pro dotazování na události platformy
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 48350caef6bdaafda9aff7ac776d67b314aeaf8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614396"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Dotaz na pravidla API úložiště událostí pro události clusteru

Tento článek popisuje, jak dotaz na rozhraní API EventStore, které jsou k dispozici v Service Fabric verze 6.2 a novější – pokud chcete získat další informace o službě EventStore, naleznete v [přehledu služby EventStore](service-fabric-diagnostics-eventstore.md). V současné době může služba EventStore přistupovat k datům pouze za posledních 7 dní (to je založeno na zásadách uchovávání dat diagnostiky vašeho clusteru).

>[!NOTE]
>Pravidla api úložiště událostí jsou ga jako Service Fabric verze 6.4 pro pouze clustery Windows spuštěné v Azure.

K úložišti API úložiště událostí lze přistupovat přímo prostřednictvím koncového bodu REST nebo programově. V závislosti na dotazu existuje několik parametrů, které jsou nutné ke shromažďování správných dat. Tyto parametry obvykle zahrnují:
* `api-version`: verze api úložiště událostí, které používáte
* `StartTimeUtc`: definuje začátek období, na které máte zájem
* `EndTimeUtc`: konec časového období

Kromě těchto parametrů jsou k dispozici také volitelné parametry, jako například:
* `timeout`: přepsat výchozí časový limit 60 sekund pro provedení operace požadavku
* `eventstypesfilter`: To vám dává možnost filtrovat pro konkrétní typy událostí
* `ExcludeAnalysisEvents`: nevracejte události analýzy. Ve výchozím nastavení eventstore dotazy vrátí s "analýzy" události, kde je to možné. Události analýzy jsou bohatší události operačního kanálu, které obsahují další kontext nebo informace nad rámec pravidelné události Service Fabric a poskytují větší hloubku.
* `SkipCorrelationLookup`: nehledejte potenciální korelované události v clusteru. Ve výchozím nastavení eventstore se pokusí korelovat události v rámci clusteru a propojit události dohromady, pokud je to možné. 

Každá entita v clusteru může být dotazy na události. Můžete také dotaz na události pro všechny entity typu. Můžete například dotazovat na události pro konkrétní uzel nebo pro všechny uzly v clusteru. Aktuální sada entit, pro které můžete dotaz ovat události (s tím, jak by byl dotaz strukturován):
* Clusteru:`/EventsStore/Cluster/Events`
* Uzly:`/EventsStore/Nodes/Events`
* Uzel:`/EventsStore/Nodes/<NodeName>/$/Events`
* Aplikace:`/EventsStore/Applications/Events`
* Aplikace:`/EventsStore/Applications/<AppName>/$/Events`
* Služby:`/EventsStore/Services/Events`
* Služby:`/EventsStore/Services/<ServiceName>/$/Events`
* Oddíly:`/EventsStore/Partitions/Events`
* Oddíl:`/EventsStore/Partitions/<PartitionID>/$/Events`
* Repliky:`/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Replika:`/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Při odkazování na název aplikace nebo služby dotaz nemusí obsahovat "fabric:/" Předponu. Navíc pokud vaše aplikace nebo názvy služeb mají "/" v nich, přepněte na "~", aby dotaz fungoval. Například pokud vaše aplikace se zobrazí jako "fabric:/App1/FrontendApp", vaše konkrétní `/EventsStore/Applications/App1~FrontendApp/$/Events`dotazy aplikace by být strukturovány jako .
>Kromě toho sestavy stavu pro služby dnes zobrazí v rámci `DeployedServiceHealthReportCreated` odpovídající aplikace, takže byste dotaz na události pro správnou entitu aplikace. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Dotaz na úložiště událostí prostřednictvím koncových bodů rozhraní REST API

EventStore můžete dotazovat přímo prostřednictvím koncového `GET` bodu REST, a to tak, že požádáte o: `<your cluster address>/EventsStore/<entity>/Events/`.

Například pro dotazování na všechny `2018-04-03T18:00:00Z` události `2018-04-04T18:00:00Z`clusteru mezi a , váš požadavek bude vypadat takto:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

To může vrátit žádné události nebo seznam událostí vrácených v json:

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

Zde můžeme vidět, `2018-04-03T18:00:00Z` `2018-04-04T18:00:00Z`že mezi a , tento cluster úspěšně dokončil svůj `"CurrentClusterVersion": "0.0.0.0:"` `"TargetClusterVersion": "6.2:1.0"`první `"OverallUpgradeElapsedTimeInMs": "120196.5212"`upgrade, když byl poprvé vstal, z do , v .

## <a name="query-the-eventstore-programmatically"></a>Programový dotaz na eventstore

Můžete také dotaz EventStore programově, prostřednictvím [knihovny klienta Service Fabric](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Jakmile máte klienta Service Fabric nastavit, můžete dotaz na události pomocí přístupu EventStore takto:`sfhttpClient.EventStore.<request>`

Zde je příklad požadavku pro `2018-04-03T18:00:00Z` všechny `2018-04-04T18:00:00Z`události `GetClusterEventListAsync` clusteru mezi a , prostřednictvím funkce.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Zde je další příklad, který se dotazuje na stav clusteru a všechny události uzlu v září 2018 a vytiskne je.

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

## <a name="sample-scenarios-and-queries"></a>Ukázkové scénáře a dotazy

Zde je několik příkladů, jak můžete volat rest API úložiště událostí pochopit stav clusteru.

*Upgrady clusteru:*

Chcete-li zobrazit poslední čas, kdy byl cluster úspěšně nebo se pokusil o upgrade minulý týden, můžete dotaz ovat api pro nedávno dokončené upgrady do clusteru dotazem na události "ClusterUpgradeCompleted" v EventStore:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problémy s upgradem clusteru:*

Podobně pokud došlo k problémům s nedávnou inovaci clusteru, můžete dotaz na všechny události pro entitu clusteru. Uvidíte různé události, včetně zahájení upgradů a každé ud, pro které upgrade úspěšně prošel. Zobrazí se také události pro bod, ve kterém bylo zahájeno vrácení zpět a odpovídající události stavu. Zde je dotaz, který byste použili pro toto:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Změny stavu uzlu:*

Chcete-li zobrazit změny stavu uzlu v posledních několika dnech - když uzly šly nahoru nebo dolů, nebo byly aktivovány nebo deaktivovány (buď platformou, službou chaos, nebo z uživatelského vstupu) - použijte následující dotaz:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Události aplikace:*

Můžete také sledovat vaše poslední nasazení a upgrady aplikací. Všechny události aplikace v clusteru zobrazíte pomocí následujícího dotazu:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Historické zdraví pro aplikaci:*

Kromě pouze zobrazení událostí životního cyklu aplikace můžete také chtít zobrazit historická data o stavu konkrétní aplikace. To lze provést zadáním názvu aplikace, pro který chcete shromažďovat data. Tento dotaz slouží k získání všech `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`událostí stavu aplikace: . Pokud chcete zahrnout události stavu, které mohou mít vypršela (pryč uplynulý `,ApplicationHealthReportExpired` jejich čas žít (TTL)), přidejte na konec dotazu, filtrovat na dva typy událostí.

*Historické zdraví pro všechny služby v "myApp":*

Události sestavy stavu pro služby `DeployedServicePackageNewHealthReport` se v současné době zobrazují jako události v rámci příslušné entity aplikace. Chcete-li zjistit, jak si vaše služby vedou pro "App1", použijte následující dotaz:`https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Změna konfigurace oddílu:*

Chcete-li zobrazit všechny přesuny oddílu, ke `PartitionReconfigured` kterým došlo v clusteru, dotaz na událost. To vám může pomoci zjistit, jaké úlohy běžel na kterém uzlu v určitých časech, při diagnostice problémů v clusteru. Zde je ukázkový dotaz, který to dělá:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Služba Chaos:*

Je událost pro při spuštění nebo zastavení služby Chaos, která je vystavena na úrovni clusteru. Chcete-li zobrazit nedávné použití služby Chaos, použijte následující dotaz:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

