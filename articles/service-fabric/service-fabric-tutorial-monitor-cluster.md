---
title: Monitorování clusteru Service Fabric v Azure
description: V tomto kurzu se dozvíte, jak sledovat cluster zobrazením událostí Service Fabric, dotazováním na pravidla api EventStore, sledováním čítačů perf a zobrazením sestav stavu.
author: srrengar
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: srrengar
ms.custom: mvc
ms.openlocfilehash: ab58d622511e0d5793eb6df312bc3fd6dd15bfd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75376626"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Kurz: Monitorování clusteru Service Fabric v Azure

Monitorování a diagnostika jsou důležité pro vývoj, testování a nasazování úloh v libovolném cloudovém prostředí. Tento kurz je druhá část řady a ukazuje, jak sledovat a diagnostikovat cluster Service Fabric pomocí událostí, čítačů výkonu a sestav stavu.   Další informace naleznete v přehledu monitorování [clusteru](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) a [monitorování infrastruktury](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zobrazit události Service Fabric
> * Dotaz na pravidla API úložiště událostí pro události clusteru
> * Monitorovat infrastrukturu/shromažďovat čítače perf
> * Zobrazit sestavy stavu clusteru

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure pomocí šablony
> * Sledování clusteru
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * [Odstranění clusteru](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Nainstalujte [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps) nebo [Azure CLI](/cli/azure/install-azure-cli).
* Vytvoření zabezpečeného [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 
* [Kolekce diagnostiky](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor) instalace pro cluster
* Povolení [služby EventStore](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) v clusteru
* Konfigurace [protokolů Azure Monitor a agenta Analýzy protokolů](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) pro cluster

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Zobrazení událostí Service Fabric pomocí protokolů Azure Monitor

Protokoly Azure Monitor shromažďuje a analyzuje telemetrie z aplikací a služeb hostovaných v cloudu a poskytuje analytické nástroje, které vám pomohou maximalizovat jejich dostupnost a výkon. Můžete spouštět dotazy v protokolech Azure Monitor získat přehledy a řešení potíží, co se děje ve vašem clusteru.

Chcete-li získat přístup k řešení Service Fabric Analytics, přejděte na [portál Azure](https://portal.azure.com) a vyberte skupinu prostředků, ve které jste vytvořili řešení Service Fabric Analytics.

Vyberte zdroj **ServiceFabric(mysfomsworkspace)**.

V **přehledu** se zobrazí dlaždice ve formě grafu pro každé z povolených řešení, včetně jednoho pro Service Fabric. Kliknutím na graf **Service Fabric** přenesete k řešení Service Fabric Analytics.

![Service Fabric řešení](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

Následující obrázek znázorňuje domovskou stránku řešení Service Fabric Analytics. Tato domovská stránka poskytuje snímek zobrazení toho, co se děje ve vašem clusteru.

![Service Fabric řešení](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Pokud jste při vytváření clusteru povolili diagnostiku, můžete zobrazit události pro 

* [Události clusteru Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Události programovacího modelu spolehlivých herců](service-fabric-reliable-actors-diagnostics.md)
* [Události programovacího modelu spolehlivých služeb](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Kromě události Service Fabric po vybalení, podrobnější systémové události lze shromažďovat [aktualizací konfigurace rozšíření diagnostiky](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Zobrazit události service fabric, včetně akcí na uzlech

Na stránce Service Fabric Analytics klikněte na graf **událostí clusteru**.  Zobrazí se protokoly pro všechny systémové události, které byly shromážděny. Pro referenci jsou z **WADServiceFabricSystemEventsTable** v účtu Azure Storage a podobně spolehlivé služby a objekty actor události, které vidíte další jsou z těchto příslušných tabulek.
    
![Dotaz na operační kanál](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

Dotaz používá dotazovací jazyk Kusto, který můžete upravit, abyste upřesnili, co hledáte. Chcete-li například vyhledat všechny akce provedené v uzlech v clusteru, můžete použít následující dotaz. ID událostí použitá níže se nacházejí v [odkazu na události operačního kanálu](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Dotazovací jazyk Kusto je silný. Zde jsou některé další užitečné dotazy.

Vytvořte vyhledávací tabulku *ServiceFabricEvent* jako uživatelem definovanou funkci uložením dotazu jako funkce s aliasem ServiceFabricEvent:

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

Zpětné provozní události zaznamenané za poslední hodinu:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Vrátit provozní události s EventId == 18604 a EventName == 'NodeDownOperational':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Vrátit provozní události s EventId == 18604 a EventName == 'NodeUpOperational':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
Vrátí zprávy o stavu se stavem Stav == 3 (Chyba) a extrahovat další vlastnosti z pole EventMessage:

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

Vrátit časový graf událostí s EventId != 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Získejte provozní události Service Fabric agregované s konkrétní službou a uzlem:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Vykreslení počtu událostí Service Fabric podle EventId / EventName pomocí dotazu křížového prostředku:

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

Můžete zobrazit události pro spolehlivé služby a spolehlivé aplikace aktérů nasazené v clusteru.  Na stránce Service Fabric Analytics klikněte na graf událostí **aplikace**.

Chcete-li zobrazit události ze spolehlivých aplikací služeb, spusťte následující dotaz:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

Můžete zobrazit různé události pro při spuštění a dokončení služby runasync, které se obvykle děje na nasazení a upgrady.

![Spolehlivé služby service fabric řešení](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

Můžete také najít události pro spolehlivé služby s ServiceName == "fabric:/Watchdog/WatchdogService":

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Spolehlivé události herce lze zobrazit podobným způsobem:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Chcete-li nakonfigurovat podrobnější události pro `scheduledTransferKeywordFilter` spolehlivé aktéry, můžete změnit v konfiguraci pro diagnostické rozšíření v šabloně clusteru. Podrobnosti o hodnotách pro tyto jsou v [odkazna události spolehlivé objekty](service-fabric-reliable-actors-diagnostics.md#keywords).

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

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Zobrazení čítačů výkonu pomocí protokolů Azure Monitoru
Pokud chcete zobrazit čítače výkonu, přejděte na [portál Azure](https://portal.azure.com) a skupinu prostředků, ve které jste vytvořili řešení Service Fabric Analytics. 

Vyberte prostředek **ServiceFabric (mysfomsworkspace)**, potom **Log Analytics Workspace**a potom **upřesnit nastavení**.

Klepněte na **položku Data**a potom klepněte na **položku Čítače výkonu systému Windows**. K dispozici je seznam výchozích čítačů, které můžete povolit, a můžete také nastavit interval pro shromažďování. Můžete také přidat [další čítače výkonu](service-fabric-diagnostics-event-generation-perf.md) shromažďovat. Správný formát je odkazován v tomto [článku](/windows/desktop/PerfCtrs/specifying-a-counter-path). Klepněte na tlačítko **Uložit**a potom klepněte na tlačítko **OK**.

Zavřete okno Upřesnit nastavení a pod nadpisem **Obecné** vyberte **souhrn pracovního prostoru.** Pro každé z povolených řešení je grafická dlaždice, včetně jedné pro Service Fabric. Kliknutím na graf **Service Fabric** přenesete k řešení Service Fabric Analytics.

K dispozici jsou grafické dlaždice pro provozní kanál a spolehlivé služby události. Grafické znázornění dat, která se pro počtočí, které jste vybrali, zobrazí v části **Metriky uzlů**. 

Kliknutím na graf **Metrika kontejneru** zobrazíte další podrobnosti. Můžete také dotaz na data čítače výkonu podobně jako události clusteru a filtrovat na uzly, název čítače perf a hodnoty pomocí dotazovacího jazyka Kusto.

## <a name="query-the-eventstore-service"></a>Dotaz na službu EventStore
[Služba EventStore](service-fabric-diagnostics-eventstore.md) poskytuje způsob, jak pochopit stav clusteru nebo úloh v daném okamžiku. EventStore je stavová služba Service Fabric, která udržuje události z clusteru. Události jsou vystaveny prostřednictvím [aplikace Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), REST a rozhraní API. EventStore se dotazuje clusteru přímo na získání diagnostických dat na libovolné entitě v clusteru Chcete-li zobrazit úplný seznam událostí dostupných v eventstore, viz [události Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Na api úložiště událostí lze programově dotazovat pomocí [klientské knihovny Service Fabric](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Zde je příklad požadavku na všechny události clusteru mezi 2018-04-03T18:00:00Z a 2018-04-04T18:00:00Z, prostřednictvím funkce GetClusterEventListAsync.

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


## <a name="monitor-cluster-health"></a>Monitorování stavu clusteru
Service Fabric zavádí [model stavu](service-fabric-health-introduction.md) s entity stavu, na kterých systémové součásti a watchdogs můžete hlásit místní podmínky, které jsou monitorování. [Úložiště stavu](service-fabric-health-introduction.md#health-store) agreguje všechna data o stavu k určení, zda entity jsou v pořádku.

Cluster je automaticky naplněn zprávami o stavu odeslanými systémovými součástmi. Další informace naleznete v [článek Použití zpráv o stavu systému k řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric zpřístupňuje dotazy na stav pro každý z [podporovaných typů entit](service-fabric-health-introduction.md#health-entities-and-hierarchy). K nim lze přistupovat prostřednictvím rozhraní API pomocí metod [fabricclient.healthmanager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), rutin prostředí PowerShell a REST. Tyto dotazy vrátí úplné informace o stavu entity: agregovaný stav, události stavu entity, stav dítěte stav (pokud je to možné), nefunkční hodnocení (pokud entita není v pořádku) a statistiky stavu dětí (když jsou k dispozici použitelné).

### <a name="get-cluster-health"></a>Získání stavu clusteru
[Rutina Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) vrátí stav entity clusteru a obsahuje stavy aplikací a uzlů (podřízených clusteru).  Nejprve se připojte ke clusteru pomocí [rutiny Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps).

Stav clusteru je 11 uzlů, systémová aplikace a prostředků infrastruktury:/Hlasování nakonfigurováno tak, jak je popsáno.

Následující příklad získá stav clusteru pomocí výchozích zásad stavu. 11 uzlů jsou v pořádku, ale clusteru agregované stav je chyba, protože fabric:/Hlasování aplikace je v chybě. Všimněte si, jak není v pořádku hodnocení poskytují podrobnosti o podmínky, které spustily agregované stavu.

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

Následující příklad získá stav clusteru pomocí vlastní zásady aplikace. Filtruje výsledky, aby se chybně nebo v upozornění dostaly pouze aplikace a uzly. V tomto příkladu jsou vráceny žádné uzly, protože jsou všechny v pořádku. Pouze fabric:/Hlasování aplikace respektuje aplikace filtr. Vzhledem k tomu, že vlastní zásady určuje považovat upozornění jako chyby pro fabric:/Hlasování aplikace, aplikace je vyhodnocena jako chybně a tak je cluster.

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
[Rutina Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth) vrátí stav entity uzlu a obsahuje události stavu hlášené v uzlu. Nejprve se připojte ke clusteru pomocí [rutiny Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps). Následující příklad získá stav konkrétního uzlu pomocí výchozích zásad stavu:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

Následující příklad získá stav všech uzlů v clusteru:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Získání stavu systémových služeb 

Získejte agregovaný stav systémových služeb:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zobrazit události Service Fabric
> * Dotaz na pravidla API úložiště událostí pro události clusteru
> * Monitorovat infrastrukturu/shromažďovat čítače perf
> * Zobrazit sestavy stavu clusteru

Dále přejdete k následujícímu kurzu, kde se dozvíte, jak škálovat cluster.
> [!div class="nextstepaction"]
> [Škálování clusteru](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
