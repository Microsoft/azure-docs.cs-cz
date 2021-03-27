---
title: Dotaz na události clusteru pomocí rozhraní API Eventstoru
description: Naučte se používat rozhraní API Azure Service Fabric Eventstoru k dotazování na události platformy.
ms.topic: conceptual
ms.date: 02/25/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: bc8909a6f25c3adcba60de639d822769baff703f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628795"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Dotazování rozhraní API Eventstoru pro události clusteru

Tento článek popisuje, jak zadat dotaz na rozhraní API Eventstoru, která jsou k dispozici ve verzi Service Fabric 6,2 a novější – Pokud se chcete dozvědět víc o službě Eventstoru, přečtěte si téma [Přehled služby eventstoru](service-fabric-diagnostics-eventstore.md). V současné době může služba Eventstoru přistupovat jenom k datům za posledních 7 dnů (vychází ze zásad pro uchovávání dat v diagnostice vašeho clusteru).

>[!NOTE]
>Rozhraní Eventstoru API jsou GA od Service Fabric verze 6,4 pouze pro clustery se systémem Windows běžící v Azure.

K rozhraním API Eventstoru lze přistupovat přímo prostřednictvím koncového bodu REST nebo prostřednictvím kódu programu. V závislosti na dotazu je k dispozici několik parametrů potřebných ke shromáždění správných dat. Mezi tyto parametry obvykle patří:
* `api-version`: verze Eventstoru rozhraní API, která používáte
* `StartTimeUtc`: definuje začátek období, které vás zajímá.
* `EndTimeUtc`: konec časového období

Kromě těchto parametrů jsou k dispozici také volitelné parametry, jako například:
* `timeout`: přepsat výchozí časový limit 60 sekund pro provedení operace Request
* `eventstypesfilter`: Tato možnost umožňuje filtrovat konkrétní typy událostí.
* `ExcludeAnalysisEvents`: nevracet události Analysis. Ve výchozím nastavení budou dotazy Eventstoru vracet s událostmi "analýza", pokud je to možné. Události analýzy jsou bohatší provozní kanály s událostmi, které obsahují další informace o kontextu nebo informacích, mimo běžné Service Fabric události a poskytují větší hloubku.
* `SkipCorrelationLookup`: nehledat možné korelační události v clusteru. Ve výchozím nastavení se Eventstoru pokusí korelovat události napříč clusterem a současně propojit události, pokud je to možné. 

Každá entita v clusteru může být dotazem na události. Můžete také zadat dotaz na události pro všechny entity typu. Můžete například zadat dotaz na události pro určitý uzel nebo pro všechny uzly v clusteru. Aktuální sada entit, pro které se můžete dotazovat na události, je (s jakým způsobem je strukturovaný dotaz):
* Služby `/EventsStore/Cluster/Events`
* Sortiment `/EventsStore/Nodes/Events`
* Uzlu `/EventsStore/Nodes/<NodeName>/$/Events`
* Vyrovnání `/EventsStore/Applications/Events`
* Použití `/EventsStore/Applications/<AppName>/$/Events`
* Orgány `/EventsStore/Services/Events`
* Službám `/EventsStore/Services/<ServiceName>/$/Events`
* Disk `/EventsStore/Partitions/Events`
* Rozdělován `/EventsStore/Partitions/<PartitionID>/$/Events`
* Repliky `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Změněno `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Při odkazování na název aplikace nebo služby nemusí dotaz zahrnovat "Fabric:/". směr. Kromě toho, pokud jsou v názvech aplikace nebo služby v nich znak "/", přepněte jej na "~", aby dotaz zůstal fungovat. Pokud se například vaše aplikace zobrazí jako "Fabric:/app1/FrontendApp", dotazy na konkrétní aplikace by měly být strukturované `/EventsStore/Applications/App1~FrontendApp/$/Events` .
>Sestavy o stavu pro služby, které se dnes zobrazují, se zobrazí v příslušné aplikaci, takže byste měli dotaz na `DeployedServiceHealthReportCreated` události pro správnou entitu aplikace. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Dotazování na Eventstoru prostřednictvím koncových bodů REST API

Eventstoru se můžete dotazovat přímo přes koncový bod REST tím, že vydáte `GET` požadavky na: `<your cluster address>/EventsStore/<entity>/Events/` .

Například pro dotazování na všechny události clusteru mezi `2018-04-03T18:00:00Z` a `2018-04-04T18:00:00Z` by vaše žádost vypadala takto:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

To by buď nevrátilo žádné události, nebo seznam událostí vrácených ve formátu JSON:

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

Tady vidíte, že mezi `2018-04-03T18:00:00Z` a byl `2018-04-04T18:00:00Z` tento cluster úspěšně dokončen první upgrade při prvním stood, od `"CurrentClusterVersion": "0.0.0.0:"` do `"TargetClusterVersion": "6.2:1.0"` v `"OverallUpgradeElapsedTimeInMs": "120196.5212"` .

## <a name="query-the-eventstore-programmatically"></a>Programové dotazování na Eventstoru

Pomocí [klientské knihovny Service Fabric také můžete prostřednictvím klientské knihovny](/dotnet/api/overview/azure/service-fabric#client-library)dotazovat na eventstoru programově.

Po nastavení klienta Service Fabric můžete zadávat dotazy na události tím, že získáte přístup k Eventstoru, jako je tato: `sfhttpClient.EventStore.<request>`

Tady je příklad požadavku na všechny události clusteru mezi `2018-04-03T18:00:00Z` a `2018-04-04T18:00:00Z` , prostřednictvím `GetClusterEventListAsync` funkce.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Tady je další příklad, který se dotazuje na stav clusteru a události všech uzlů v září 2018 a tiskne je.

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

Tady je několik příkladů, jak můžete volat rozhraní REST API úložiště událostí, abyste pochopili stav clusteru.

*Upgrady clusteru:*

Pokud chcete zjistit, kdy byl cluster naposledy úspěšně nebo jste se pokusili upgradovat minulý týden, můžete zadat dotaz na rozhraní API pro nedávno dokončené upgrady na svůj cluster pomocí dotazování na události ClusterUpgradeCompleted v Eventstoru: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problémy s upgradem clusteru:*

Podobně, pokud došlo k potížím s nedávným upgradem clusteru, mohli byste zadat dotaz na všechny události pro entitu clusteru. Zobrazí se různé události, včetně zahájení upgradů a všech UD, pro které byl upgrade úspěšně zahrnut. Zobrazí se také události pro bod, ve kterém bylo vráceno zpět a odpovídající události stavu. Tady je dotaz, který byste měli použít: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Změny stavu uzlu:*

Chcete-li zobrazit změny stavu uzlu za posledních několik dní – když se uzly nacházely nahoru nebo dolů nebo byly aktivovány nebo dezaktivovány (buď platformou, službou chaos nebo uživatelským vstupem), použijte následující dotaz: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Události aplikace:*

Můžete také sledovat vaše nedávná nasazení a upgrady aplikací. K zobrazení všech událostí aplikace v clusteru použijte následující dotaz: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Historický stav aplikace:*

Kromě toho, že se jenom zobrazují události životního cyklu aplikace, můžete taky chtít zobrazit historická data o stavu konkrétní aplikace. Můžete to provést tak, že zadáte název aplikace, pro kterou chcete data shromažďovat. Pomocí tohoto dotazu můžete získat všechny události stavu aplikace: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport` . Pokud chcete zahrnout události stavu, u kterých vypršela platnost (neprošla doba TTL (Time to Live)), přidejte `,ApplicationHealthReportExpired` na konec dotazu, aby bylo možné filtrovat dva typy událostí.

*Historická služba pro všechny služby v "myApp":*

V současné době se události sestav stavu pro služby zobrazují jako `DeployedServicePackageNewHealthReport` události v příslušné entitě aplikace. Pokud chcete zjistit, jak vaše služby prováděly "app1", použijte následující dotaz: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Konfigurace oddílu:*

Pokud chcete zobrazit všechny přesuny oddílů, ke kterým došlo v clusteru, dotaz na `PartitionReconfigured` událost. To vám může při diagnostikování problémů v clusteru zjistit, jaké úlohy běžely v konkrétní době. Zde je ukázkový dotaz, který provede následující akce: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Služba chaos:*

Existuje událost pro případ, že je služba chaos spuštěná nebo zastavená, která je vystavená na úrovni clusteru. Pokud chcete zobrazit poslední použití služby chaos, použijte následující dotaz: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`
