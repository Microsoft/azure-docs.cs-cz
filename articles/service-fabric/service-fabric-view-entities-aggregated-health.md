---
title: Postup zobrazení Azure Service Fabric entity agregovat stavu | Microsoft Docs
description: Popisuje, jak pro dotazování, zobrazení a vyhodnotit agregovaný stav entity Azure Service Fabric, prostřednictvím dotazů na stav a obecné dotazy.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 2e5d1045edbbc3c71cb0ccff34d2ba327a98a409
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211853"
---
# <a name="view-service-fabric-health-reports"></a>Zobrazit sestavy stavu Service Fabric
Představuje Azure Service Fabric [stavu modelu](service-fabric-health-introduction.md) s entity stavu, ve které součásti systému a watchdogs můžou sestavy místní podmínky, které jsou monitorování. [Úložiště stavu](service-fabric-health-introduction.md#health-store) slučuje všechny data o stavu k určení, zda jsou v pořádku entity.

Cluster se automaticky zadá sestavy o stavu odeslaných součástech systému. Další informace v [sestav o stavu systému použít k řešení](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric nabízí více způsoby, jak získat agregovaný stav entity:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) nebo jiné vizualizace nástroje
* Dotazy na stav (pomocí prostředí PowerShell, rozhraní API nebo REST)
* Obecné dotazuje to návratový seznam entit, které mají stav jako jedna z vlastností (pomocí prostředí PowerShell, rozhraní API nebo REST)

K předvedení tyto možnosti, můžeme použít místní cluster s pěti uzly a [fabric: / WordCount aplikace](http://aka.ms/servicefabric-wordcountapp). **Fabric: / WordCount** aplikace obsahuje dvě výchozí služby, stavové služby typu `WordCountServiceType`a bezstavové služby typu `WordCountWebServiceType`. Po změně `ApplicationManifest.xml` tak, aby vyžadovala 7 cíle replik pro stavové služby a jeden oddíl. Protože jsou pouze pět uzlů v clusteru, součástech systému informovat upozornění v oddílu služby, protože je pod počtu cílových.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Stav v Service Fabric Exploreru
Service Fabric Explorer nabízí vizuální zobrazení clusteru. Na následujícím obrázku vidíte, který:

* Aplikace **fabric: / WordCount** je červený (v chyba), protože obsahuje událost chyby hlášené **MyWatchdog** pro vlastnost **dostupnosti**.
* Jeden z jejích služeb **fabric: / WordCount/WordCountService** žlutý (v upozornění). Služba je nakonfigurována s sedm repliky a cluster obsahuje pět uzlů, takže dvě repicas nemůže být umístěn. Je sice není vidět zde, oddílu služby žlutý kvůli sestavy systému z `System.FM` oznámením, že `Partition is below target replica or instance count`. Žlutý oddílu aktivuje žlutý služby.
* Cluster je red kvůli red aplikace.

Vyhodnocení používá výchozí zásady z manifestu clusteru a manifest aplikace. Jsou striktní zásady a není tolerovat žádné chyby.

Zobrazení clusteru Service Fabric Explorer:

![Zobrazení clusteru Service Fabric Exploreru.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Další informace o [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Dotazy na stav
Service Fabric vystaví dotazů na stav pro každý z podporovaném [typy entit](service-fabric-health-introduction.md#health-entities-and-hierarchy). Je přístupný prostřednictvím rozhraní API pomocí metody na [FabricClient.HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), rutiny prostředí PowerShell a REST. Tyto dotazy vrátí informace o stavu dokončení o entitě: agregovaný stav, události stavu entity, podřízené stavů (v případě potřeby), není v pořádku hodnocení (Pokud je entita není v pořádku) a podřízené objekty stavu statistiky (v případě potřeby).

> [!NOTE]
> Stav entity je vrácena, pokud je plně naplněna v health store. Entita musí být aktivní (nebyl odstraněn) a mít sestavy systému. Jeho nadřazený entit na řetězec hierarchie musí mít také sestav systému. Pokud nejsou splněné některé z těchto podmínek, stav vrácena dotazy [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) s [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` který ukazuje, proč se nevrátí entity.
>
>

Dotazy na stav musí předat identifikátor entity, které závisí na typu entity. Dotazy přijmout parametry zásad volitelné stavu. Pokud nejsou zadány žádné zásady stavu, [zásady stavu](service-fabric-health-introduction.md#health-policies) z manifestu clusteru nebo aplikace, které se používají pro vyhodnocení. Pokud manifesty nesmí obsahovat definice pro zásady stavu, výchozích zásad stavu se používají pro vyhodnocení. Výchozí zásady stavu není tolerovat případných selhání. Dotazy taky přijmout filtry pro vrácení pouze částečné podřízených objektů nebo událostí – ty, které respektují určenému filtru. Jiný filtr povoluje, s výjimkou statistik pro podřízené objekty.

> [!NOTE]
> Výstupní filtry se použijí na straně serveru, takže se snižuje velikost zprávy odpovědi. Doporučujeme vám použít výstupní filtry k omezení s daty vrácenými, že místo použití filtrů na straně klienta.
>
>

Stav entity obsahuje:

* Agregovaný stav entity. Vypočtená podle stavu úložiště, na základě sestav stavu entity, podřízené stavů (v případě potřeby) a zásad stavu. Další informace o [vyhodnocení stavu entity](service-fabric-health-introduction.md#health-evaluation).  
* Události stavu u entity.
* Kolekce stavů všechny podřízené objekty pro entity, které může mít podřízené objekty. Stav obsahovat identifikátory entity a agregovaný stav v pořádku. Pokud chcete získat úplný stavu pro podřízenou, volání stavu dotazu pro typ entity podřízené a předat identifikátor podřízené.
* Není v pořádku hodnocení, které odkazují na sestavu, která aktivuje stav entity, pokud entita není v pořádku. Hodnocení jsou rekurzivní, obsahující hodnocení stavu podřízené objekty, které aktivuje aktuálním stavu. Například sledovací zařízení oznámil chybu pro repliku. Zobrazuje stav aplikace není v pořádku zkušební verzi z důvodu služby není v pořádku; Služba je poškozen z důvodu oddíl v chybě; oddíl je poškozen z důvodu repliku v chybě; replika je poškozen z důvodu sestava stavu chyb sledovací zařízení.
* Statistika stavu pro všechny podřízené objekty typu entity, které mají podřízené objekty. Například stav clusteru zobrazuje celkový počet aplikací, služeb, oddíly, repliky a nasazení entit v clusteru. Stav služby se zobrazuje celkový počet oddílů a repliky v části zadaná služba.

## <a name="get-cluster-health"></a>Získání stavu clusteru
Vrátí stav entity clusteru a obsahuje stav aplikací a uzly (podřízené objekty daného clusteru). Vstup:

* [Nepovinné] Zásady stavu clusteru používá k vyhodnocení uzly a události clusteru.
* [Nepovinné] Aplikace stavu zásad mapy, pomocí zásad stavu používaná k přepsání zásady manifestu aplikace.
* [Nepovinné] Filtry pro události, uzly a aplikace, které určí položky, které jsou v zájmu a má být vrácen ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události, uzly a aplikace se používají k vyhodnocení stavu entity agregován, bez ohledu na to filtr.
* [Nepovinné] Filtr pro vyloučení statistiky stavu.
* [Nepovinné] Filtr, který patří fabric: / statistiky stavu systému do stavu statistiky. Platí jenom při statistiky stavu nejsou vyloučení. Ve výchozím stavu statistiky zahrnovat pouze statistiku pro uživatelské aplikace a ne aplikace systému.

### <a name="api"></a>Rozhraní API
Chcete-li získat stav clusteru, vytvořte `FabricClient` a volání [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) metoda na jeho **HealthManager**.

Toto volání získá stav clusteru:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Následující kód získá stav clusteru pomocí zásad stavu vlastní clusteru a filtry pro uzly a aplikace. Se určuje, že statistiky stavu obsahovat topologie fabric: / systému statistiky. Vytvoří [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), který obsahuje vstupní informace.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Rutina se získat stav clusteru je [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Nejprve se připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny.

Stav clusteru je pět uzlů, aplikaci systému a fabric: / WordCount nakonfigurována, jak je popsáno.

Následující rutiny získá stav clusteru pomocí výchozích zásad stavu. Agregovaný stav je upozornění, protože topologie fabric: / WordCount aplikace je v upozornění. Všimněte si, jak není v pořádku hodnocení poskytují podrobnosti o podmínky, které aktivuje agregovaný stav.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.
                          
                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.
                          
                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                          
                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.
                          
                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                          
                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                          
                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                          
                          
NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _Node_0
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

Následující rutiny prostředí PowerShell získá stav clusteru s použitím zásad vlastní aplikaci. Filtruje výsledky získat jenom aplikace a uzly ve chyby nebo upozornění. V důsledku toho jsou vráceny žádné uzly, jako jsou všechny v pořádku. Pouze fabric: / WordCount aplikace respektuje filtru aplikací. Protože vlastními zásadami určuje vzít v úvahu upozornění jako chyby pro topologie fabric: / WordCount aplikace, aplikace je vyhodnocena jako chyba, a stejně tak clusteru.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.
                          
                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.
                          
                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                          
                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                          
                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                          
                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                          
                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.
                          
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="rest"></a>REST
Můžete získat stav clusteru s [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) zásady stavu, které jsou popsané v textu, který obsahuje.

## <a name="get-node-health"></a>Získat stav uzlu
Vrátí stav uzlu entity a obsahuje události stavu na uzlu. Vstup:

* [Vyžaduje] Název uzlu, který identifikuje uzlu.
* [Nepovinné] Nastavení pro zásady stavu se cluster, používá k vyhodnocení stavu.
* [Nepovinné] Filtry pro události, které určují, položky, které jsou v zájmu a má být vrácen ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události se používají k vyhodnocení stavu entity agregován, bez ohledu na to filtr.

### <a name="api"></a>Rozhraní API
Získat stav uzlu prostřednictvím rozhraní API, vytvoření `FabricClient` a volání [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) metoda na jeho HealthManager.

Následující kód získá stav uzlu název určeného uzlu:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Následující kód získá stav uzlu pro název zadaný uzel a předá filtr událostí a vlastní zásady prostřednictvím [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Rutina se získat stav uzlu je [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Nejprve se připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny.
Následující rutiny získá stav uzlu pomocí výchozích zásad stavu:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Následující rutiny získá stav všech uzlů v clusteru:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
Můžete získat stav uzlu s [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) zásady stavu, které jsou popsané v textu, který obsahuje.

## <a name="get-application-health"></a>Získání stavu aplikací
Vrátí stav entity aplikací. Obsahuje stav nasazení aplikace a služby dětí. Vstup:

* [Vyžaduje] Název aplikace (URI) identifikující aplikace.
* [Nepovinné] Zásady stavu aplikace používaná k přepsání zásady manifestu aplikace.
* [Nepovinné] Filtry pro událostí, služeb a nasazené aplikace, které určují, položky, které jsou v zájmu a má být vrácen ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události, služby a nasazené aplikace se používají k vyhodnocení stavu entity agregován, bez ohledu na to filtr.
* [Nepovinné] Filtr pro vyloučení statistiky stavu. Pokud není zadaný, zahrnují statistiky stavu ok, upozornění a počet chyb pro všechny aplikace, děti: nasazené aplikace služby, oddíly, repliky a nasazené balíčky služeb.

### <a name="api"></a>Rozhraní API
Pokud chcete získat stav aplikací, vytvoření `FabricClient` a volání [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) metoda na jeho HealthManager.

Následující kód získá stav aplikace pro zadaný název aplikace (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Následující kód získá stav aplikace pro zadaný název aplikace (URI), s filtry a vlastní zásady zadaný prostřednictvím [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Rutina se získat stav aplikace je [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Nejprve se připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny.

Následující rutina vrátí stav **fabric: / WordCount** aplikace:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                                  
                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                                  
ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok
                                  
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning
                                  
DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok
                                  
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
                                  
HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

Následující rutiny prostředí PowerShell předá do vlastní zásady. Také filtry, děti a události.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                                  
                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.
                                  
ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error
                                  
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Můžete získat stav aplikací s [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) zásady stavu, které jsou popsané v textu, který obsahuje.

## <a name="get-service-health"></a>Získání stavu služby
Vrátí stav entity služby. Obsahuje stav oddílu. Vstup:

* [Vyžaduje] Název služby (URI), který určuje služba.
* [Nepovinné] Zásady stavu aplikace používaná k přepsání zásady manifestu aplikace.
* [Nepovinné] Filtry pro události a oddíly, které určují, položky, které jsou v zájmu a má být vrácen ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události a oddíly, které se používají k vyhodnocení stavu entity agregován, bez ohledu na to filtr.
* [Nepovinné] Filtr pro vyloučení statistiky stavu. Není-li zadána, zobrazují statistiky stavu ok, upozornění, a počet chyb pro všechny oddíly a repliky služby.

### <a name="api"></a>Rozhraní API
Získat stav služby prostřednictvím rozhraní API, vytvoření `FabricClient` a volání [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) metoda na jeho HealthManager.

Následující příklad načte stav služby pomocí zadaného názvu služby (URI):

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Následující kód získá stav služby pro zadaný název služby (URI), filtrů a vlastní zásady prostřednictvím [ServiceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Rutina se získat stav služby je [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Nejprve se připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny.

Následující rutiny získá stav služby pomocí výchozích zásad stavu:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                        
                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.
                        
                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                        
HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
Můžete získat stav služby s [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) zásady stavu, které jsou popsané v textu, který obsahuje.

## <a name="get-partition-health"></a>Získání stavu oddílu
Vrátí stav entity oddílu. Obsahuje stav repliky. Vstup:

* [Vyžaduje] Oddíl ID (GUID), který identifikuje oddílu.
* [Nepovinné] Zásady stavu aplikace používaná k přepsání zásady manifestu aplikace.
* [Nepovinné] Filtry pro události a repliky, které určují, položky, které jsou v zájmu a má být vrácen ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události a repliky se používají k vyhodnocení stavu entity agregován, bez ohledu na to filtr.
* [Nepovinné] Filtr pro vyloučení statistiky stavu. Pokud není zadaný, stavu statistiky zobrazit, kolik repliky byly ve ok, upozornění a chybové stavy.

### <a name="api"></a>Rozhraní API
Získat stav oddílu prostřednictvím rozhraní API, vytvoření `FabricClient` a volání [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) metoda na jeho HealthManager. Chcete-li zadat volitelné parametry, vytvořte [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
Rutina se získat stav oddílu je [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Nejprve se připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny.

Následující rutiny získá stav pro všechny oddíly **fabric: / WordCount/WordCountService** služby a filtry se stavy repliky:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
                        
HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Můžete získat stav oddílu s [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) zásady stavu, které jsou popsané v textu, který obsahuje.

## <a name="get-replica-health"></a>Získání stavu repliky
Vrátí stav repliky stavové služby nebo instance bezstavové služby. Vstup:

* [Vyžaduje] ID (GUID) a repliky ID oddílu identifikující repliky.
* [Nepovinné] Parametry pro zásady stavu se aplikace, používaná k přepsání zásady manifestu aplikace.
* [Nepovinné] Filtry pro události, které určují, položky, které jsou v zájmu a má být vrácen ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události se používají k vyhodnocení stavu entity agregován, bez ohledu na to filtr.

### <a name="api"></a>Rozhraní API
Získat stav repliky prostřednictvím rozhraní API, vytvoření `FabricClient` a volání [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) metoda na jeho HealthManager. Pokud chcete zadat upřesňující parametry, použijte [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
Rutina se získat stav repliky je [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Nejprve se připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny.

Následující rutiny získá stav primární repliky pro všechny oddíly služby:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Můžete získat stav repliky se [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) zásady stavu, které jsou popsané v textu, který obsahuje.

## <a name="get-deployed-application-health"></a>Získat stav nasazení aplikace
Vrátí stav aplikace nasazené na uzlu entity. Obsahuje stav balíčku nasazené služby. Vstup:

* [Vyžaduje] Název aplikace (URI) a název uzlu (string) identifikují nasazené aplikace.
* [Nepovinné] Zásady stavu aplikace používaná k přepsání zásady manifestu aplikace.
* [Nepovinné] Filtry pro události a nasazené služby balíčky, které určují, položky, které jsou v zájmu a má být vrácen ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události a nasazené balíčky služeb se používají k vyhodnocení stavu entity agregován, bez ohledu na to filtr.
* [Nepovinné] Filtr pro vyloučení statistiky stavu. Pokud není zadaný, stavu statistiky zobrazit počet nasazené balíčky služeb v ok, upozornění a chybové stavy.

### <a name="api"></a>Rozhraní API
Pokud chcete získat stav aplikace nasazené na uzlu prostřednictvím rozhraní API, vytvoření `FabricClient` a volání [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) metoda na jeho HealthManager. Pokud chcete zadat volitelné parametry, použijte [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
Rutina se získat stav nasazení aplikace je [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Nejprve se připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny. Chcete-li zjistit, kde je aplikace nasazena, spusťte [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) a prohlédněte si podřízené objekty nasazené aplikace.

Následující rutiny získá stav **fabric: / WordCount** aplikace nasazené na **to uzel _Node_2**.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok
                                     
                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM
                                     
HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Můžete získat stav nasazení aplikace s [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) zásady stavu, které jsou popsané v textu, který obsahuje.

## <a name="get-deployed-service-package-health"></a>Získat stav balíčku nasazené služby
Vrátí stav entity balíček nasazené služby. Vstup:

* [Vyžaduje] Název aplikace (URI), název uzlu (string) a service manifest název (string), který identifikovat balíček nasazené služby.
* [Nepovinné] Zásady stavu aplikace používaná k přepsání zásady manifestu aplikace.
* [Nepovinné] Filtry pro události, které určují, položky, které jsou v zájmu a má být vrácen ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události se používají k vyhodnocení stavu entity agregován, bez ohledu na to filtr.

### <a name="api"></a>Rozhraní API
Získat stav balíčku nasazené služby prostřednictvím rozhraní API, vytvoření `FabricClient` a volání [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) metoda na jeho HealthManager. Pokud chcete zadat volitelné parametry, použijte [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Rutina se získat stav balíčku nasazenou službu je [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Nejprve se připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny. Pokud chcete zjistit, kde je aplikace nasazena, spusťte [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) a prohlédněte si nasazené aplikace. Pokud chcete zjistit, které služby jsou balíčky v aplikaci, podívejte se na podřízené balíček nasazené služby v [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) výstup.

Následující rutiny získá stav **WordCountServicePkg** balíček služby **fabric: / WordCount** aplikace nasazené na **to uzel _Node_2**. Entita, která má **System.Hosting** sestavy pro úspěšné aktivaci balíček služby a vstupní bod a úspěšnou registraci typ služby.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Můžete získat stav balíčku nasazené služby s [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) zásady stavu, které jsou popsané v textu, který obsahuje.

## <a name="health-chunk-queries"></a>Dotazy na stav bloku
Dotazy bloku stavu může vrátit podřízené víceúrovňovou clusteru (rekurzivně), za vstupní filtry. Podporuje rozšířené filtry, které umožňují značnou flexibilitu při volbě podřízených má být vrácen. Jedinečný identifikátor nebo jiné skupiny nebo stavy, můžete zadat filtry podřízené objekty. Ve výchozím nastavení žádné podřízené objekty jsou zahrnuty, oproti stavu příkazy, které vždy zahrnovat první úrovně podřízené objekty.

[Dotazů na stav](service-fabric-view-entities-aggregated-health.md#health-queries) vrátit pouze první úroveň podřízených prvků zadanou entitu za požadované filtry. Získat podřízený element pro podřízené objekty, musí volat další stavu rozhraní API pro každé entity, které vás zajímají. Podobně se získat stav konkrétní entity, musí volat jednoho stavu rozhraní API pro každou požadovanou entitu. Dotaz bloku advanced filtrování umožňuje požadovat více položek, které vás zajímají v jednom dotazu, minimalizovat velikost zprávy a počet zpráv.

Hodnota dotazu bloku je, že můžete získat stav pro další clusteru entity (potenciálně všechny clusteru entity začínající na požadovaný kořenový) v jednom volání. Komplexní stavu dotazu lze vyjádřit jako:

* Návratový pouze aplikace, které jsou v chybě a pro tyto aplikace zahrnout všechny služby upozornění nebo chyby. Vrácených služeb zahrnují všechny oddíly.
* Vrátí pouze stav čtyři aplikací, určeného jejich názvy.
* Vrátí pouze stav aplikací typu požadované aplikace.
* Vrátí všechny nasazené entity na uzlu. Vrátí všechny aplikace, všechny nasazené aplikace v určeném uzlu a všech balíčků nasazené služby v tomto uzlu.
* Vrátí všechny repliky v chybě. Vrátí všechny aplikace, služby, oddíly a pouze repliky v chybě.
* Vrátí všechny aplikace. Zadaná služba zahrnují všechny oddíly.

V současné době dotazu bloku stavu je vystaven pouze pro entitu clusteru. Vrátí bloku stavu clusteru, který obsahuje:

* Stav clusteru agregovat.
* Stav stavu bloku seznam uzlů, které respektují vstupní filtry.
* Stav seznam bloků dat stavu aplikací, které respektují vstupní filtry. Každého bloku stavu stavu aplikace obsahuje seznam bloků dat u všech služeb, které respektují vstupní filtry a seznam bloků dat s všechny nasazené aplikace, které respektují filtry. Stejný pro děti služeb a nasazené aplikace. Tímto způsobem všechny entity v clusteru se může potenciálně vracet Pokud vyžaduje, je hierarchický.

### <a name="cluster-health-chunk-query"></a>Dotaz bloku stavu clusteru
Vrátí stav entity clusteru a obsahuje hierarchické stavu bloky dat stavu požadované podřízených prvků. Vstup:

* [Nepovinné] Zásady stavu clusteru používá k vyhodnocení uzly a události clusteru.
* [Nepovinné] Aplikace stavu zásad mapy, pomocí zásad stavu používaná k přepsání zásady manifestu aplikace.
* [Nepovinné] Filtry pro uzly a aplikace, které určí položky, které jsou v zájmu a má být vrácen ve výsledku. Filtry jsou specifické pro entity nebo skupinu entit nebo platí pro všechny entity na této úrovni. Seznam filtrů může obsahovat jeden obecné filtr a filtry pro konkrétní identifikátory na entity důkladnou vrácených dotazem. Pokud je prázdný, nebudou zobrazeny podřízené objekty ve výchozím nastavení.
  Další informace o filtrů při [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) a [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). Rekurzivní aplikace filtry můžete zadat rozšířené filtry pro děti.

Výsledek datových dávek obsahuje podřízené položky, které respektují filtry.

V současné době bloku dotaz nevrátí, není v pořádku hodnocení nebo události entity. Tyto doplňující informace lze získat pomocí existující dotaz stavu clusteru.

### <a name="api"></a>Rozhraní API
Chcete-li získat clusteru stavu bloku, vytvořit `FabricClient` a volání [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) metoda na jeho **HealthManager**. Abyste mohli předávat [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) k popisu zásady stavu a rozšířené filtry.

Následující kód získá bloku stavu clusteru se rozšířené filtry.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Rutina se získat stav clusteru je [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Nejprve se připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny.

Následující kód získá uzly pouze v případě, že se chyba s výjimkou konkrétním uzlu, který má být vždy vrácen.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1
                               
                               NodeName              : _Node_1
                               HealthState           : Ok
                               
ApplicationHealthStateChunks : None
```

Následující rutiny získá clusteru bloku s filtry aplikace.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1
                               
                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1
                               
                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1
                               
                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5
                               
                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok
                               
                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

Následující rutina vrátí všechny nasazené entity na uzlu.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2
                               
                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1
                               
                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1
                               
                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
                               
                               
                               
                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1
                               
                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1
                               
                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
Můžete získat blok stavu clusteru s [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) zásady stavu a rozšířené filtry, které jsou popsané v textu, který obsahuje.

## <a name="general-queries"></a>Obecné dotazy
Obecné dotazy vrátí seznam entity prostředků infrastruktury služby zadaného typu. Se zveřejňují přes rozhraní API (prostřednictvím metody na **FabricClient.QueryManager**), rutiny prostředí PowerShell a REST. Tyto dotazy agregovat poddotazy z několika součástí. Jeden z nich je [úložiště stavu](service-fabric-health-introduction.md#health-store), který naplní agregovaný stav pro každý výsledek dotazu.  

> [!NOTE]
> Obecné dotazy vraťte agregovaný stav entity a neobsahují data bohaté stavu. Pokud entity není v pořádku, můžete sledovat pomocí dotazů na stav zobrazíte všechny jeho stavu informace, včetně událostí, podřízené stavů a není v pořádku hodnocení.
>
>

Pokud obecné dotazy vrátit Neznámý stav pro entitu, je možné, že úložiště zdravotní nemá dokončení data o entitě. Je také možné, že poddotazu k úložišti stavu nebyl úspěšný (například došlo k chybě komunikace, nebo byla omezena úložiště zdravotní). Následnou akci s dotazem stavu pro entitu. Pokud je poddotaz došlo k přechodné chyby, například problémy se sítí, může být úspěšné následné dotaz. Ho může také získáte další informace z health store o proč nebude vystavena entity.

Dotazy, které obsahují **HealthState** pro entity jsou:

* Seznam uzlů: vrátí seznam uzlů v clusteru (stránkovaného).
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * Prostředí PowerShell: Get-ServiceFabricNode
* Seznam aplikací: vrátí seznam aplikací v clusteru (stránkovaného).
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * Prostředí PowerShell: Get-ServiceFabricApplication
* Seznam služeb: vrátí seznam služeb v aplikaci (stránkovaného).
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * Prostředí PowerShell: Get-ServiceFabricService
* Seznam oddílů: vrátí seznam oddílů ve službě (stránkovaného).
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * Prostředí PowerShell: Get-ServiceFabricPartition
* Seznam replik: vrátí seznam replik v oddílu (stránkovaného).
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * Prostředí PowerShell: Get-ServiceFabricReplica
* Nasazení seznam aplikací: vrátí seznam nasazené aplikace na uzlu.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * Prostředí PowerShell: Get-ServiceFabricDeployedApplication
* Nasazení služby seznam balíčků: vrátí seznam balíčků služby v nasazení aplikace.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * Prostředí PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Některé dotazy, vrátí stránkových výsledků. Návrat tyto dotazy je odvozen od seznamu [PagedList<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Pokud výsledky nebudou vyhovovat zprávu, je vrácena pouze na stránce a ContinuationToken, který sleduje kde výčtu zastavena. Pokračujte volání stejný dotaz a předejte token pro pokračování z předchozího dotazu a získat tak další výsledky.
>
>

### <a name="examples"></a>Příklady
Následující kód získá není v pořádku aplikace v clusteru:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Následující rutiny získá podrobností o aplikaci pro topologie fabric: / WordCount aplikace. Všimněte si, že stav je v upozornění.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

Následující rutiny získá služby s stav chyby:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Upgrady cluster a aplikace
Během monitorovaných upgrade clusteru a aplikace Service Fabric kontroluje stav zajistit, že všechno zůstane v pořádku. Pokud není v pořádku, jak se vyhodnocují se pomocí zásady nakonfigurované stavu entity se upgrade platí zásady specifické pro upgrade určit další akce. Upgrade může být pozastaveno umožňující interakci s uživatelem (například opravě chybové stavy nebo změna zásad) nebo ji může automaticky vrátit zpět na předchozí verzi funkční.

Během *clusteru* upgradu, můžete získat stav upgradu clusteru. Stav upgradu zahrnuje není v pořádku hodnocení, které odkazují na to, co je v clusteru není v pořádku. Pokud se upgrade je vrácena zpět kvůli problémům s stavu, stav upgradu pamatuje poslední důvodů není v pořádku. Tyto informace mohou pomoci správci prozkoumat, kde došlo k chybě po upgradu vrácena nebo zastavená.

Podobně při *aplikace* upgrade všech není v pořádku hodnocení jsou obsaženy v stav upgradu aplikace.

Následující příklad zobrazuje stav upgradu aplikace pro upravené fabric: / WordCount aplikace. Sledovací zařízení ohlásilo chybu na jednom z jejích replik. Upgrade je vrácení zpět, protože nejsou dodržovány kontroly stavu.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Další informace o [upgradu aplikace Service Fabric](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Hodnocení stavu použijte k řešení potíží
Vždy, když nastane problém s clusteru nebo aplikaci, podívejte se na stav clusteru nebo aplikace a určit, co je nesprávný. Není v pořádku hodnocení poskytují podrobnosti o zdroj, aktuální stav není v pořádku. Pokud potřebujete, můžete k podrobnostem na není v pořádku podřízených entit k identifikaci hlavní příčinu.

Představte si třeba aplikace není v pořádku, protože není zprávu o chybách na jednom z jejích replik. Následující rutiny prostředí Powershell ukazuje, není v pořádku hodnocení:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.
                                  
                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.
                                  
                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.
                                  
                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.
                                  
                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.
                                  
                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.
                                  
                                            Error event: SourceId='MyWatchdog', Property='Memory'.
                                  
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

Můžete se podívat na repliku, chcete-li získat další informace:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
                        
                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> Není v pořádku hodnocení ukazují, že je prvním důvodem entity vyhodnotit na aktuálním stavu. Může být více událostí, které spouštějí tento stav, ale neprojeví v hodnocení. Chcete-li získat další informace, přejděte do entity stavu a pokuste se zjistit všechny sestavy není v pořádku v clusteru.
>
>

## <a name="next-steps"></a>Další postup
[Použití sestav o stavu systému k řešení problémů](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Přidat vlastní sestavy stavu Service Fabric](service-fabric-report-health.md)

[Postup vytvoření sestavy a zkontrolujte stav služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorování a Diagnostika služby místně](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)
