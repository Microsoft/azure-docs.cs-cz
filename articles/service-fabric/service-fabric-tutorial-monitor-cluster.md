---
title: Monitorování clusteru Service Fabric v Azure
description: V tomto kurzu se naučíte monitorovat cluster zobrazením Service Fabric událostí, dotazování rozhraní API pro Eventstoru, čítačů výkonu monitorování a zobrazování sestav o stavu.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 3e9c8011647567b6f07423e113f98493d80d8402
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628777"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Kurz: monitorování clusteru Service Fabric v Azure

Monitorování a diagnostika jsou zásadní pro vývoj, testování a nasazování úloh v jakémkoli cloudovém prostředí. Tento kurz je druhou částí série, kde se dozvíte, jak monitorovat a diagnostikovat clustery Service Fabric s využitím událostí, čítačů výkonu a zpráv o stavu.   Další informace najdete v tématu Přehled [monitorování clusteru](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) a [monitorování infrastruktury](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zobrazit Service Fabric události
> * Dotazování rozhraní API Eventstoru pro události clusteru
> * Monitorování infrastruktury/shromažďování čítačů výkonu
> * Zobrazení sestav stavu clusteru

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure pomocí šablony
> * Monitorování clusteru
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * [Odstranění clusteru](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) nebo rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli).
* Vytvoření zabezpečeného [clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 
* Nastavení [kolekce diagnostiky](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) pro cluster
* Povolení [služby eventstoru](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) v clusteru
* Konfigurace [protokolů Azure monitor a agenta Log Analytics](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) pro cluster

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Zobrazení Service Fabricch událostí pomocí protokolů Azure Monitor

Protokoly Azure Monitor shromažďuje a analyzuje telemetrii z aplikací a služeb hostovaných v cloudu a poskytuje analytické nástroje, které vám pomůžou maximalizovat jejich dostupnost a výkon. Dotazy můžete spouštět v protokolech Azure Monitor a získat tak přehledy a řešit potíže s tím, co se děje ve vašem clusteru.

Pokud chcete získat přístup k řešení Service Fabric Analytics, přejděte na [Azure Portal](https://portal.azure.com) a vyberte skupinu prostředků, ve které jste vytvořili řešení Service Fabric Analytics.

Vyberte prostředek **ServiceFabric (mysfomsworkspace)**.

V **přehledu** uvidíte dlaždice ve formě grafu pro každé povolené řešení, včetně jednoho pro Service Fabric. Kliknutím na graf **Service Fabric** pokračujte v řešení Service Fabric Analytics.

![Snímek obrazovky, který zobrazuje graf Service Fabric.](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

Následující obrázek ukazuje domovskou stránku Service Fabric Analytics řešení. Tato domovská stránka nabízí snímek toho, co se děje ve vašem clusteru.

![Řešení Service Fabric](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Pokud jste při vytváření clusteru povolili diagnostiku, můžete zobrazit události pro 

* [Service Fabric události clusteru](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors události programovacího modelu](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services události programovacího modelu](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Kromě Service Fabric událostí z pole mohou být shromážděny podrobnější systémové události prostřednictvím [aktualizace konfigurace diagnostického rozšíření](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Zobrazit Service Fabric události, včetně akcí na uzlech

Na stránce Service Fabric Analytics klikněte na graf pro **události clusteru**.  Zobrazí se protokoly pro všechny shromážděné systémové události. Azure Storage v případě, že se jedná o referenci z **WADServiceFabricSystemEventsTable** účtu, a podobně události Reliable Services a Actors, které vidíte, jsou z příslušných tabulek.
    
![Provozní kanál dotazů](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

Dotaz používá dotazovací jazyk Kusto, který můžete upravit, abyste mohli Upřesnit, co hledáte. Pokud například chcete najít všechny akce prováděné na uzlech v clusteru, můžete použít následující dotaz. ID událostí, která se používají níže, najdete v [referenčních událostech pro události provozních kanálů](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Dotazovací jazyk Kusto je výkonný. Tady jsou některé další užitečné dotazy.

Vytvořte vyhledávací tabulku *ServiceFabricEvent* jako uživatelsky definovanou funkci tak, že dotaz uložíte jako funkci s aliasem ServiceFabricEvent:

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

Vrátit provozní události zaznamenané za poslední hodinu:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Vrátí provozní události s ID události = = 18604 a EventName = = ' NodeDownOperational ':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Vrátí provozní události s ID události = = 18604 a EventName = = ' NodeUpOperational ':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 

Vrátí sestavy o stavu s podstavem \ = 3 (chyba) a extrahuje další vlastnosti z pole EventMessage:

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

Vrátí časový graf událostí s ID události! = 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Získat Service Fabric provozní události agregované se specifickou službou a uzlem:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Vykreslovat počet Service Fabricch událostí podle ID události/EventName pomocí dotazu na více prostředků:

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

### <a name="view-service-fabric-application-events"></a>Zobrazit Service Fabric události aplikace

Můžete zobrazit události pro aplikace Reliable Services a Reliable actor nasazené v clusteru.  Na stránce Service Fabric Analytics klikněte na graf pro **události aplikace**.

Spusťte následující dotaz pro zobrazení událostí z aplikací spolehlivé služby:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

V případě, že `runasync` je služba spuštěná a dokončená, což obvykle probíhá při nasazení a upgradech, se můžete podívat na různé události.

![Reliable Services řešení Service Fabric](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

Můžete také najít události pro Reliable Service pomocí ServiceName = = "Fabric:/sledovací/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Spolehlivé události objektu actor lze zobrazit podobným způsobem:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Chcete-li nakonfigurovat podrobnější události pro Reliable Actors, můžete změnit `scheduledTransferKeywordFilter` v konfiguraci pro diagnostické rozšíření v šabloně clusteru. Podrobnosti o hodnotách pro tyto položky jsou uvedeny v referenčních informacích o [událostech Reliable Actors](service-fabric-reliable-actors-diagnostics.md#keywords).

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

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Zobrazit čítače výkonu pomocí protokolů Azure Monitor
Chcete-li zobrazit čítače výkonu, otevřete [Azure Portal](https://portal.azure.com) a skupinu prostředků, ve které jste vytvořili řešení Service Fabric Analytics. 

Vyberte ServiceFabric prostředku **(mysfomsworkspace)**, pak **Log Analytics pracovní prostor** a pak **Rozšířené nastavení**.

Klikněte na **data** a pak na **čítače výkonu Windows**. K dispozici je seznam výchozích čítačů, které můžete povolit, a můžete nastavit interval pro shromažďování dat. Můžete také přidat [Další čítače výkonu](service-fabric-diagnostics-event-generation-perf.md) ke shromáždění. Správný formát je odkazován v tomto [článku](/windows/desktop/PerfCtrs/specifying-a-counter-path). Klikněte na **Uložit** a pak na **OK**.

Zavřete okno Upřesnit nastavení a v části **Obecné** vyberte možnost **Souhrn pracovního prostoru** . Pro každé z povolených řešení je k dispozici grafická dlaždice, včetně jednoho pro Service Fabric. Kliknutím na graf **Service Fabric** pokračujte v řešení Service Fabric Analytics.

Pro události provozních kanálů a spolehlivých služeb jsou k dispozici grafické dlaždice. Grafická reprezentace toku dat pro čítače, které jste vybrali, se zobrazí v části **metriky uzlů**. 

Výběrem grafu **metriky kontejnerů** zobrazíte další podrobnosti. Můžete také dotazovat se na data čítače výkonu podobně jako u událostí clusteru a filtrovat uzly, název čítače výkonu a hodnoty pomocí dotazovacího jazyka Kusto.

## <a name="query-the-eventstore-service"></a>Dotazování na službu Eventstoru
[Služba eventstoru](service-fabric-diagnostics-eventstore.md) poskytuje způsob, jak pochopit stav clusteru nebo úloh v daném časovém okamžiku. Eventstoru je stavová služba Service Fabric, která udržuje události z clusteru. Události jsou zpřístupněny prostřednictvím [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), Rest a rozhraní API. Eventstoru dotazuje cluster přímo, aby získal diagnostická data na jakékoli entitě v clusteru, aby se zobrazil úplný seznam událostí, které jsou k dispozici v Eventstoru, viz [Service Fabric události](service-fabric-diagnostics-event-generation-operational.md).

Rozhraní API pro Eventstoru se dají dotazovat programově pomocí [klientské knihovny Service Fabric](/dotnet/api/overview/azure/service-fabric#client-library).

Tady je příklad požadavku na všechny události clusteru mezi 2018-04-03T18:00:00Z a 2018-04-04T18:00:00Z, prostřednictvím funkce GetClusterEventListAsync.

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


## <a name="monitor-cluster-health"></a>Monitorování stavu clusteru
Service Fabric zavádí [model stavu](service-fabric-health-introduction.md) s entitami o stavu, na kterých mohou systémové komponenty a sledovací zařízení nahlásit místní podmínky, které monitorují. [Health Store](service-fabric-health-introduction.md#health-store) agreguje všechna data o stavu, abyste zjistili, jestli jsou entity v pořádku.

Cluster se vyplní automaticky zprávami o stavu, které odesílají součásti systému. Další informace najdete v tématu [použití sestav stavu systému k řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric zveřejňuje dotazy na stav pro každý z podporovaných [typů entit](service-fabric-health-introduction.md#health-entities-and-hierarchy). K nim lze přistupovat prostřednictvím rozhraní API pomocí metod na [FabricClient. HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager), rutin PowerShellu a REST. Tyto dotazy vrátí kompletní informace o stavu o entitě: agregovaný stav, události stavu entity, podřízené stavy (Pokud je k dispozici), nestavová hodnocení (Pokud entita není v pořádku) a údaje o podřízeném stavu (Pokud je k dispozici).

### <a name="get-cluster-health"></a>Získat stav clusteru
[Rutina Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) vrátí stav entity clusteru a obsahuje stav aplikací a uzlů (podřízené položky clusteru).  Nejdřív se připojte ke clusteru pomocí [rutiny Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster).

Stav clusteru je 11 uzlů, systémová aplikace a prostředky infrastruktury:/hlasování nakonfigurované podle popisu.

Následující příklad načte stav clusteru pomocí výchozích zásad stavu. 11 uzlů je v pořádku, ale stav agregovaného stavu clusteru je chyba, protože aplikace Fabric:/hlasovací aplikace je v chybovém stavu. Všimněte si, jak nestavová Hodnocení poskytují podrobnosti o podmínkách, které aktivovaly agregovaný stav.

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

Následující příklad načte stav clusteru pomocí vlastní zásady použití. Vyfiltruje výsledky, aby se v chybách nebo varování dostaly jenom aplikace a uzly. V tomto příkladu nejsou vráceny žádné uzly, protože jsou v pořádku. Filtr aplikací respektuje pouze aplikace Fabric:/hlasovací aplikace. Vzhledem k tomu, že vlastní zásady určují, že v případě chyb pro aplikaci Fabric:/hlasovacích se považují upozornění jako chyby, aplikace se vyhodnotí jako chyba a je to cluster.

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
[Rutina Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth) vrátí stav entity uzlu a obsahuje události stavu hlášené v uzlu. Nejdřív se připojte ke clusteru pomocí [rutiny Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster). Následující příklad načte stav konkrétního uzlu pomocí výchozích zásad stavu:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

Následující příklad načte stav všech uzlů v clusteru:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Získat stav systémové služby 

Získejte agregovaný stav systémových služeb:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zobrazit Service Fabric události
> * Dotazování rozhraní API Eventstoru pro události clusteru
> * Monitorování infrastruktury/shromažďování čítačů výkonu
> * Zobrazení sestav stavu clusteru

Potom přejděte k následujícímu kurzu, kde se dozvíte, jak škálovat cluster.
> [!div class="nextstepaction"]
> [Škálování clusteru](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
