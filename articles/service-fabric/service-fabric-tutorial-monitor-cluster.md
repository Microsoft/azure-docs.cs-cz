---
title: Monitorování clusteru Service Fabric v Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak pro monitorování clusteru tak, že zobrazení událostí Service Fabric, dotazování rozhraní API Eventstoru, monitorování čítače výkonu a zobrazení sestav o stavu.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/13/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: b8234f286f4304b83969a01704735e1f3a7da2c6
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227293"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Kurz: Monitorování clusteru Service Fabric v Azure

Monitorování a Diagnostika jsou zásadní pro vývoj, testování a nasazování úloh v jakémkoli prostředí cloud. Tento kurz je součástí série a ukazuje, jak monitorovat a diagnostikovat cluster Service Fabric pomocí události, čítače výkonu a sestav o stavu.   Další informace najdete v přehledu o [monitorování clusterů](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) a [monitorování infrastruktury](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zobrazení událostí modulu Service Fabric
> * Dotazování rozhraní API Eventstoru pro události clusteru
> * Monitorování infrastruktury a shromáždit čítače výkonu
> * Zobrazení sestav o stavu clusteru

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure pomocí šablony
> * Monitorování clusteru
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * [Odstranění clusteru](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte si [modul Azure PowerShell verze 4.1 nebo novější ](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) nebo [Azure CLI](/cli/azure/install-azure-cli).
* Vytvoření zabezpečeného [Windows cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 
* Instalační program [shromažďování diagnostických dat](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) pro cluster
* Povolit [Eventstoru služby](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) v clusteru
* Konfigurace [agenta Log Analytics a Azure Monitor protokolování](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) pro cluster

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Zobrazení událostí Service Fabric pomocí protokoly Azure monitoru

Protokoly Azure monitoru shromažďuje a analyzuje telemetrii z aplikace a služby hostované v cloudu a poskytuje analytické nástroje, které vám pomůžou maximálně využít jejich dostupnost a výkon. Spouštění dotazů v protokolech Azure Monitor k získání přehledu a řešení potíží s co se děje ve vašem clusteru.

Chcete-li získat přístup k řešení analýza služby Service Fabric, přejděte [webu Azure portal](https://portal.azure.com) a vyberte skupinu prostředků, ve které jste vytvořili řešení analýza služby Service Fabric.

Vyberte prostředek **ServiceFabric(mysfomsworkspace)**.

V **přehled** uvidíte dlaždice ve formě grafu pro každou z řešení povolené, včetně pro Service Fabric. Klikněte na tlačítko **Service Fabric** grafu a pokračujte v Service Fabric analytického řešení.

![Řešení Service Fabric](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

Následující obrázek ukazuje domovské stránce řešení analýza služby Service Fabric. Tato stránka Domovská stránka obsahuje zobrazení snímku, co se děje ve vašem clusteru.

![Řešení Service Fabric](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Pokud jste povolili Diagnostika při vytváření clusteru, zobrazí se události pro 

* [Události clusteru Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Programovacího modelu Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Programovacího modelu Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Kromě události Service Fabric připravené, můžete shromážděná podrobnější systémové události [aktualizací konfigurace diagnostického rozšíření](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Zobrazit události Service Fabric, včetně akce na uzlech

Na stránce Analýza služby Service Fabric, klikněte na graf pro **události clusteru**.  Zobrazit protokoly pro všechny systémové události, které byly shromážděny. Pro srovnání jde z **WADServiceFabricSystemEventsTable** ve službě Azure Storage jsou z těchto tabulek příslušných účtu a podobně spolehlivé služby a objekty actor události se zobrazí vedle.
    
![Provozní kanál dotazu](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

Dotaz využívá dotazovací jazyk Kusto, kterou můžete upravit, pro upřesnění, co hledáte. Například pokud chcete najít všechny akce prováděné na uzlech v clusteru, můžete použít následující dotaz. ID událostí používá pod se nacházejí v [provozní kanál události – referenční informace](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Je výkonný dotazovací jazyk Kusto. Tady jsou některé užitečné dotazy.

Vytvoření *ServiceFabricEvent* vyhledávací tabulky jako uživatelem definované funkce Uložit dotaz jako funkce s aliasem ServiceFabricEvent:

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

Návratový provozních událostí zaznamenaných za poslední hodinu:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Vrátí provozní události s ID události == 18604 a EventName == "NodeDownOperational":
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Vrátí provozní události s ID události == 18604 a EventName == "NodeUpOperational":
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
Vrátí sestav o stavu k elementu HealthState == 3 (chyba) a další vlastnosti extrahovat z pole zpráva události:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

Vrátí čas graf událostí s ID události! = 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Získáte provozní události agregovat pomocí konkrétní službu a uzel Service Fabric:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Počet událostí Service Fabric podle EventId vykreslení / EventName pomocí dotazu napříč prostředky:

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>Zobrazit události aplikace Service Fabric

Můžete zobrazit události modelu reliable services a reliable actors aplikacím nasazeným v clusteru.  Na stránce Analýza služby Service Fabric, klikněte na graf pro **události aplikace**.

Spusťte následující dotaz, chcete-li zobrazit události z aplikací modelu reliable services:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

Můžete zobrazit jednotlivé události pro při runasync služby je spuštěno a dokončeno, obvykle probíhá na nasazení a upgrade.

![Řešení Service Fabric Reliable Services](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

Můžete také vyhledat události pro spolehlivé služby pomocí ServiceName == "fabric: / sledovacího zařízení/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Události Reliable actors můžou zobrazit podobným způsobem:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Pokud chcete nakonfigurovat podrobnější události reliable actors, můžete změnit `scheduledTransferKeywordFilter` v konfiguraci pro diagnostické rozšíření v šabloně clusteru. Podrobnosti o hodnoty pro tyto [události reliable actors – referenční informace](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Zobrazit čítače výkonu s protokoly Azure monitoru
Chcete-li zobrazit čítače výkonu, přejděte [webu Azure portal](https://portal.azure.com) a skupinu prostředků, ve které jste vytvořili řešení analýza služby Service Fabric. 

Vyberte prostředek **ServiceFabric(mysfomsworkspace)**, pak **pracovní prostor Log Analytics**a potom **Upřesnit nastavení**.

Klikněte na tlačítko **Data**, pak klikněte na tlačítko **čítače výkonu Windows**. Zde je seznam Výchozí čítače, které můžete povolit a interval pro shromažďování lze nastavit příliš. Můžete také přidat [další čítače výkonu](service-fabric-diagnostics-event-generation-perf.md) ke shromažďování. Správný formát je odkazováno v tomto [článku](/windows/desktop/PerfCtrs/specifying-a-counter-path). Klikněte na tlačítko **Uložit**, pak klikněte na tlačítko **OK**.

Zavřete okno Upřesnit nastavení a vyberte **shrnutí pracovního prostoru** pod **Obecné** záhlaví. Pro každé řešení není povoleno je grafický dlaždice, včetně pro Service Fabric. Klikněte na tlačítko **Service Fabric** grafu a pokračujte v Service Fabric analytického řešení.

Existují grafické dlaždic pro provozní kanál a události reliable services. Grafické znázornění dat v toku pro čítače, které jste vybrali, se zobrazí v části **metriky uzlů**. 

Vyberte **kontejneru metrika** grafu zobrazíte další podrobnosti. Můžete také zadávat dotazy na data čítače výkonu podobně k události clusteru a filtrováním podle uzlů, název čítače výkonu a hodnoty v jazyce dotaz Kusto.

## <a name="query-the-eventstore-service"></a>Dotazování na službu Eventstoru
[Eventstoru služby](service-fabric-diagnostics-eventstore.md) poskytuje způsob, jak porozumět stavu clusteru nebo úloh v daném bodě v čase. Eventstoru je stavovou službu Service Fabric, který udržuje události z clusteru. Události jsou vystaveny prostřednictvím [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), REST a rozhraní API. Eventstoru dotazů clusteru přímo diagnostická data libovolné entitě ve vašem clusteru, pokud chcete zobrazit úplný seznam událostí, které jsou k dispozici v Eventstoru, získáte [události Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Rozhraní API Eventstoru může být dotázán programově pomocí [klientské knihovny pro Service Fabric](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Tady je příklad žádosti pro všechny události clusteru mezi 2018-04-03T18:00:00Z a 2018-04-04T18:00:00Z prostřednictvím funkce GetClusterEventListAsync.

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


## <a name="monitor-cluster-health"></a>Monitorování stavu clusteru
Service Fabric představuje [modelu stavu](service-fabric-health-introduction.md) u entity stavu, na které systémové součásti a watchdogs můžete sestavu místní podmínky, které jsou monitorovat. [Health store](service-fabric-health-introduction.md#health-store) agreguje všechna data stavu k určení, zda jsou v dobrém stavu entity.

Cluster se automaticky vyplní sestav o stavu odeslané součásti systému. Další informace najdete v [použití sestav stavu systému k řešení potíží s](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric poskytuje pro každou z podporovaných dotazů na stav [typy entit](service-fabric-health-introduction.md#health-entities-and-hierarchy). Jsou dostupné prostřednictvím rozhraní API pomocí metod na [FabricClient.HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), rutin Powershellu a REST. Tyto dotazy vrátit kompletní zdravotních informací o entitě: agregovaný stav, události týkající se stavu entity, podřízené stavů (Pokud se používá), špatná vyhodnocení (když entita není v pořádku) a podřízené položky stavu statistiky (při použít).

### <a name="get-cluster-health"></a>Získání stavu clusteru
[Rutiny Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) vrátí stav clusteru entita a obsahuje stavy aplikací a uzlů (podřízené objekty daného clusteru).  Nejprve připojte ke clusteru pomocí [rutina Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps).

Stav clusteru je 11 uzlů, systémová aplikace a fabric: / Voting nakonfigurovali podle popisu.

Následující příklad získá stav clusteru s použitím výchozí zásady stavu. 11 uzly jsou v pořádku, ale clusteru agregovaný stav je chyba, protože topologie fabric: / Voting aplikace došlo k chybě. Všimněte si, jak špatná vyhodnocení poskytují podrobné informace o podmínky, které aktivuje agregovaný stav.

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

Následující příklad získá stav clusteru pomocí zásad vlastní aplikace. Filtruje výsledky zobrazíte pouze aplikací a uzlů v chybě nebo upozornění. V tomto příkladu jsou vráceny žádné uzly, protože jde o všechno v pořádku. Pouze fabric: / hlasovací aplikace respektuje filtru aplikací. Protože vlastní zásady určuje, které byste měli zvážit upozornění jako chyby pro prostředky infrastruktury: / hlasovací aplikace, aplikace se vyhodnocuje jako chyba a proto je clusteru.

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>Získat stav uzlu
[Rutiny Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth) vrátí stav uzlu entity a obsahuje událostí stavu hlášené pro uzel. Nejprve připojte ke clusteru pomocí [rutina Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps). Následující příklad získá stav konkrétní uzel s použitím výchozí zásady stavu:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

Následující příklad získá stav všech uzlů v clusteru:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Získat stav služby systému 

Získejte agregovaný stav služeb systému:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zobrazení událostí modulu Service Fabric
> * Dotazování rozhraní API Eventstoru pro události clusteru
> * Monitorování infrastruktury a shromáždit čítače výkonu
> * Zobrazení sestav o stavu clusteru

Teď přejděte k následujícímu kurzu se naučíte škálovat cluster.
> [!div class="nextstepaction"]
> [Škálování clusteru](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json