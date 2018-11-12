---
title: Stav agregovaných entit Azure Service Fabric zobrazení | Dokumentace Microsoftu
description: Popisuje, jak dotazovat, zobrazení a vyhodnotit stav agregovaných entit Azure Service Fabric, prostřednictvím dotazů na stav a obecné dotazy.
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
ms.openlocfilehash: b6f6653381b5fcf80b9647c64334dfed1a2230bb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230848"
---
# <a name="view-service-fabric-health-reports"></a>Zobrazení sestav health Service Fabric
Azure Service Fabric představuje [modelu stavu](service-fabric-health-introduction.md) u entity stavu, na které systémové součásti a watchdogs můžete sestavu místní podmínky, které jsou monitorovat. [Health store](service-fabric-health-introduction.md#health-store) agreguje všechna data stavu k určení, zda jsou v dobrém stavu entity.

Cluster se automaticky vyplní sestav o stavu odeslané součásti systému. Další informace najdete v [použití sestav stavu systému k řešení potíží s](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric poskytuje několik způsobů, jak získat stav agregovaných entit:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) nebo jiné nástroje pro vizualizace
* Dotazy na stav (prostřednictvím Powershellu, rozhraní API nebo REST)
* Obecné dotazy to návratový seznam entit, které mají stav jako jedna z vlastností (prostřednictvím Powershellu, rozhraní API nebo REST)

Abychom si předvedli tyto možnosti, můžeme použít místní cluster s pěti uzly a [fabric: / WordCount aplikace](https://aka.ms/servicefabric-wordcountapp). **Fabric: / WordCount** aplikace obsahuje dvě výchozí služby, stavové služby typu `WordCountServiceType`a bezstavové služby typu `WordCountWebServiceType`. Po změně `ApplicationManifest.xml` tak, aby vyžadovala sedm cílit replik pro stavové služby a jeden oddíl. Protože jsou pouze pět uzlů v clusteru, součásti systému sestavy upozornění oddílu služby vzhledem k tomu, že ho nedosahuje počtu cílových.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Stav v Service Fabric Exploreru
Service Fabric Explorer nabízí vizuální zobrazení clusteru. Na následujícím obrázku vidíte, který:

* Aplikace **fabric: / WordCount** je red (v chybě), protože má událost chyby hlášených **MyWatchdog** pro vlastnost **dostupnosti**.
* Jeden z jejích služeb **fabric: / WordCount/WordCountService** žlutý (v upozornění). Služba je nakonfigurována s sedm repliky a cluster s pěti uzly, tak dva repicas nemůže být umístěn. I když tady není zobrazený, je oddíl služby žlutý kvůli sestavy systému z `System.FM` chci říct, že `Partition is below target replica or instance count`. Žlutý oddílu aktivuje žlutý služby.
* Cluster je red kvůli red aplikace.

Vyhodnocení využívá výchozí zásady z manifestu clusteru a manifest aplikace. Striktní zásady, které jsou a nejsou tolerovat jakékoli neúspěchy.

Přehled clusteru pomocí Service Fabric Exploreru:

![Přehled clusteru pomocí Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Další informace o [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Dotazy na stav
Service Fabric poskytuje pro každou z podporovaných dotazů na stav [typy entit](service-fabric-health-introduction.md#health-entities-and-hierarchy). Jsou dostupné prostřednictvím rozhraní API pomocí metod na [FabricClient.HealthManager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), rutin Powershellu a REST. Tyto dotazy vrátit kompletní zdravotních informací o entitě: agregovaný stav, události týkající se stavu entity, podřízené stavů (Pokud se používá), špatná vyhodnocení (když entita není v pořádku) a podřízené položky stavu statistiky (při použít).

> [!NOTE]
> Stavu entity je vrácena, když je plně naplní v health store. Entita musí být aktivní (nebyl odstraněn) a mít sestavu systému. Jeho nadřazené entity na řetězec hierarchie musí mít také sestavy systému. Pokud nejsou splněné některé z těchto podmínek, stav dotáže se vrátit [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) s [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` , který ukazuje, proč se vrátí entity.
>
>

Identifikátor entity, která závisí na typu entity musí předat dotazů na stav. Dotazy přijímají parametry zásad volitelné stavu. Pokud nejsou zadány žádné zásady stavu, [zásady stavu](service-fabric-health-introduction.md#health-policies) z manifestu clusteru nebo aplikace, které se používají k vyhodnocení. Pokud manifesty neobsahují definici zásady stavu, výchozí zásady stavu slouží k vyhodnocení. Výchozí zásady stavu není tolerovat žádné chyby. Dotazy také přijímat filtry pro vrácení pouze částečné podřízené položky ani události – ty, které respektovat zadaných filtrů. Jiný filtr umožňuje vyloučit statistiky podřízené položky.

> [!NOTE]
> Výstup jsou použity na straně serveru, takže se snižuje velikost zprávy odpovědi. Je vhodné, můžete použít k omezení dat vrácených výstupní filtry, nikoli použít filtry na straně klienta.
>
>

Stav entity obsahuje:

* Agregovaný stav entity. Počítají tak, že úložiště stavů na základě sestav o stavu entity, podřízené stavů (v případě potřeby) a zásady stavu. Další informace o [vyhodnocování stavu entity](service-fabric-health-introduction.md#health-evaluation).  
* Události stavu v entitě.
* Kolekce stavů všechny podřízené objekty pro entity, které můžou mít podřízené objekty. Stav obsahovat identifikátory entity a agregovaný stav v pořádku. Zobrazíte kompletní stavu pro dítě volání stavu dotazu pro typ podřízené entity a předat identifikátor podřízené.
* Špatná vyhodnocení, které odkazují na sestavu, která aktivuje stav entity, pokud entita není v pořádku. Hodnocení je rekurzivní, obsahující hodnocení stavu podřízené položky, které aktivuje aktuálního stavu. Například sledovacích ohlásil chybu pro repliku. Stav aplikace zobrazí z důvodu služby není v pořádku; není v pořádku zkušební verzi Služba je z důvodu oddílu v chybě; není v pořádku oddíl je v chybném stavu z důvodu repliky v chybě; replika je z důvodu stavu zprávy o chybách sledovacího zařízení není v pořádku.
* Statistika stavu pro všechny podřízené typy entit, které mají podřízené položky. Například stav clusteru zobrazuje celkový počet aplikací, služeb, oddíly, repliky a nasazení entit v clusteru. Stav služby ukazuje celkový počet oddílů a replik v rámci zadaná služba.

## <a name="get-cluster-health"></a>Získání stavu clusteru
Vrátí stav clusteru entita a obsahuje stavy aplikací a uzlů (podřízené objekty daného clusteru). Vstup:

* [Volitelné] Zásady stavu clusteru k vyhodnocení, uzly a události clusteru.
* [Volitelné] Stav zásad mapy aplikace pomocí zásad stavu, použít k přepsání zásad manifestu aplikace.
* [Volitelné] Filtry pro události, uzly a aplikace, které určují položky, které jsou zajímavé a by měl být vrácené ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události, uzly a aplikace se používají k vyhodnocení stavu entity agregovat, bez ohledu na to, filtr.
* [Volitelné] Filtr k vyloučení statistik stavu.
* [Volitelné] Filtr zahrnout fabric: / statistické údaje stavu systému ve stavu statistiky. Platí pouze pokud nejsou vyloučeny statistik stavu. Ve výchozím nastavení statistiky stavu obsahovat pouze statistiky pro uživatele aplikace a ne systémová aplikace.

### <a name="api"></a>Rozhraní API
Pokud chcete získat stav clusteru, vytvořit `FabricClient` a volat [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) metodu na jeho **HealthManager**.

Následující volání získá stav clusteru:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Následující kód načte stav clusteru s použitím zásad stavu vlastního clusteru a filtry pro uzly a aplikace. Určuje, že zahrnují statistiky stavu prostředků infrastruktury: / System statistiky. Vytvoří [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), který obsahuje vstupní informace.

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
Rutiny pro získání stavu clusteru je [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Nejprve připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny.

Stav clusteru je pět uzlů, systémová aplikace a prostředků infrastruktury: / WordCount nakonfigurovali podle popisu.

Následující rutina získá stav clusteru pomocí výchozí zásady stavu. Agregovaný stav je upozornění, protože topologie fabric: / WordCount aplikace je v upozornění. Všimněte si, jak špatná vyhodnocení poskytují podrobné informace o podmínky, které aktivuje agregovaný stav.

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

Následující rutina Powershellu načte stav clusteru pomocí zásad vlastní aplikace. Filtruje výsledky zobrazíte pouze aplikací a uzlů v chybě nebo upozornění. V důsledku toho jsou vráceny žádné uzly, protože jde o všechno v pořádku. Pouze fabric: / aplikace WordCount respektuje filtru aplikací. Protože vlastní zásady určuje, které byste měli zvážit upozornění jako chyby pro prostředky infrastruktury: / aplikace WordCount je vyhodnocen jako chyby, a to je cluster.

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
Můžete získat stav clusteru pomocí [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) , který obsahuje zásady stavu, které jsou popsané v textu.

## <a name="get-node-health"></a>Získat stav uzlu
Vrátí stav uzlu entity a obsahuje událostí stavu hlášené pro uzel. Vstup:

* [Povinné] Název uzlu, který identifikuje uzel.
* [Volitelné] Nastavení pro zásady stavu se clusteru, používá k vyhodnocení stavu.
* [Volitelné] Filtry pro události, které určují, které položky jsou zajímavé a by měl být vrácené ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události se používají k vyhodnocení stavu entity agregovat, bez ohledu na to, filtr.

### <a name="api"></a>Rozhraní API
Pokud chcete získat stav uzlu prostřednictvím rozhraní API, vytvořit `FabricClient` a volat [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) metodu na jeho HealthManager.

Následující kód načte stav uzlu pro zadaný uzel název:

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
Rutiny pro získání stavu uzlu je [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Nejprve připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny.
Následující rutina získá stav uzlu pomocí výchozí zásady stavu:

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

Následující rutina načte stav všech uzlů v clusteru:

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
Můžete získat stav uzlu pomocí [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) , který obsahuje zásady stavu, které jsou popsané v textu.

## <a name="get-application-health"></a>Získat stav aplikace
Vrátí stav entity aplikací. Obsahuje stav nasazení aplikace a služby potomků. Vstup:

* [Povinné] Název aplikace (URI), který identifikuje aplikaci.
* [Volitelné] Zásady stavu aplikace používaná k přepsání zásad manifestu aplikace.
* [Volitelné] Filtry pro události, služeb a nasazené aplikace, které určují, které položky jsou zajímavé a by měl být vrácené ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události, služeb a nasazené aplikace se používají k vyhodnocení stavu entity agregovat, bez ohledu na to, filtr.
* [Volitelné] Filtr k vyloučení statistik stavu. Pokud není zadán, zahrnují statistiky stav ok, upozornění a počet chyb pro všechny podřízené objekty aplikace: služby, oddíly, repliky, nasazené aplikace a nasazené balíčky služeb.

### <a name="api"></a>Rozhraní API
Pokud chcete získat stav aplikace, vytvořte `FabricClient` a volat [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) metodu na jeho HealthManager.

Následující kód načte stav aplikace pro zadaný název aplikace (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Následující kód načte stav aplikace pro zadaný název aplikace (URI), s filtry a vlastní zásady určené pomocí [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

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
Rutiny pro získání stavu aplikace je [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Nejprve připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny.

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

Následující rutiny Powershellu se předá do vlastní zásady. Také vyfiltruje události a podřízené položky.

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
Můžete získat stav aplikace se [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) , který obsahuje zásady stavu, které jsou popsané v textu.

## <a name="get-service-health"></a>Získání stavu služby
Vrátí stav do entity služby service. Obsahuje stav oddílu. Vstup:

* [Povinné] Název služby (URI), která identifikuje službu.
* [Volitelné] Zásady stavu aplikace používaná k přepsání zásad manifestu aplikace.
* [Volitelné] Filtry pro události a oddíly, které určují položky, které jsou zajímavé a by měl být vrácené ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události a oddíly se používají k vyhodnocení stavu entity agregovat, bez ohledu na to, filtr.
* [Volitelné] Filtr k vyloučení statistik stavu. Pokud není zadán, stavu statistiky ukazují ok, upozornění, a počet chyb pro všechny oddíly a repliky služby.

### <a name="api"></a>Rozhraní API
Získat stav služby prostřednictvím rozhraní API, vytvořit `FabricClient` a volat [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) metodu na jeho HealthManager.

Následující příklad získá stav služby pomocí zadaného názvu služby (URI):

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Následující kód načte stav služby pro zadaný název služby (URI), filtry a vlastních zásad prostřednictvím [ServiceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Rutiny pro získání stavu služby je [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Nejprve připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny.

Následující rutina získá stav služby pomocí výchozí zásady stavu:

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
Můžete získat service health se službou [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) , který obsahuje zásady stavu, které jsou popsané v textu.

## <a name="get-partition-health"></a>Získat stav oddílu
Vrátí stav oddílu entity. Obsahuje stav repliky. Vstup:

* [Povinné] Oddíl ID (GUID), který identifikuje oddíl.
* [Volitelné] Zásady stavu aplikace používaná k přepsání zásad manifestu aplikace.
* [Volitelné] Filtry pro události a repliky, které určují položky, které jsou zajímavé a by měl být vrácené ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události a repliky se používají k vyhodnocení stavu entity agregovat, bez ohledu na to, filtr.
* [Volitelné] Filtr k vyloučení statistik stavu. Pokud není zadán, stavu statistiky ukazují, kolik repliky byly ve ok, upozornění a chybové stavy.

### <a name="api"></a>Rozhraní API
Pokud chcete získat stav oddílu prostřednictvím rozhraní API, vytvořit `FabricClient` a volat [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) metodu na jeho HealthManager. Chcete-li určit volitelné parametry, vytvořte [PartitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
Rutiny pro získání stavu oddílu je [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Nejprve připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny.

Následující rutina načte stav pro všechny oddíly **fabric: / WordCount/WordCountService** služby a filtry navýšení kapacity replik stavů:

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
Můžete získat partition health se službou [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) , který obsahuje zásady stavu, které jsou popsané v textu.

## <a name="get-replica-health"></a>Získání stavu repliky
Vrátí stav repliky stavové služby nebo instance bezstavové služby. Vstup:

* [Povinné] ID (GUID) a replikou, ID oddílu, který identifikuje repliky.
* [Volitelné] Stav zásad parametry aplikace používaná k přepsání zásad manifestu aplikace.
* [Volitelné] Filtry pro události, které určují, které položky jsou zajímavé a by měl být vrácené ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události se používají k vyhodnocení stavu entity agregovat, bez ohledu na to, filtr.

### <a name="api"></a>Rozhraní API
Pokud chcete získat stav repliky prostřednictvím rozhraní API, vytvořte `FabricClient` a volat [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) metodu na jeho HealthManager. Pokud chcete zadat upřesňující parametry, použijte [ReplicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
Rutiny pro získání stavu repliky je [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Nejprve připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny.

Následující rutina načte stav primární repliky pro všechny oddíly služby:

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
Můžete získat stav repliky se [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) , který obsahuje zásady stavu, které jsou popsané v textu.

## <a name="get-deployed-application-health"></a>Získání stavu nasazení aplikace
Vrátí stav aplikace nasazené na uzlu entity. Obsahuje stav balíčku nasazené služby. Vstup:

* [Povinné] Název aplikace (URI) a název uzlu (řetězec), které určují nasazené aplikace.
* [Volitelné] Zásady stavu aplikace používaná k přepsání zásad manifestu aplikace.
* [Volitelné] Filtry pro události a nasazené balíčky služeb, které určují položky, které jsou zajímavé a by měl být vrácené ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události a nasazené balíčky služeb se používají k vyhodnocení stavu entity agregovat, bez ohledu na to, filtr.
* [Volitelné] Filtr k vyloučení statistik stavu. Pokud není zadán, stavu statistiky ukazují počet nasazené balíčky služeb v ok, upozornění a chybové stavy.

### <a name="api"></a>Rozhraní API
Získat stav aplikace nasazené na uzlu prostřednictvím rozhraní API, vytvořit `FabricClient` a volat [GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) metodu na jeho HealthManager. Chcete-li určit volitelné parametry, použijte [DeployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
Rutiny pro získání stavu nasazení aplikace je [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Nejprve připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny. Chcete-li zjistit, kde je aplikace nasazená, spusťte [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) a podívejte se na podřízené položky nasazené aplikace.

Následující rutina načte stav **fabric: / WordCount** aplikace nasazená na **to uzel _Node_2**.

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
Můžete získat stav nasazení aplikace pomocí [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) , který obsahuje zásady stavu, které jsou popsané v textu.

## <a name="get-deployed-service-package-health"></a>Získat stav balíčku nasazené službě
Vrátí stav balíčku entity nasazené služby. Vstup:

* [Povinné] Název aplikace (URI), název uzlu (řetězec) a název souboru manifestu služby (řetězec), které identifikují balíček nasazené služby.
* [Volitelné] Zásady stavu aplikace používaná k přepsání zásad manifestu aplikace.
* [Volitelné] Filtry pro události, které určují, které položky jsou zajímavé a by měl být vrácené ve výsledku (například pouze chyby nebo upozornění i chyby). Všechny události se používají k vyhodnocení stavu entity agregovat, bez ohledu na to, filtr.

### <a name="api"></a>Rozhraní API
Pokud chcete získat stav balíčku nasazené služby přes rozhraní API, vytvořit `FabricClient` a volat [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) metodu na jeho HealthManager. Chcete-li určit volitelné parametry, použijte [DeployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Rutiny pro získání stavu balíčku nasazenou službu je [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Nejprve připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny. Chcete-li zjistit, kde je aplikace nasazená, spusťte [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) a podívejte se na nasazené aplikace. Pokud chcete zjistit, jaké služby jsou balíčky v aplikaci, podívejte se na podřízené položky balíčku službu nasazenou v [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps) výstup.

Následující rutina načte stav **WordCountServicePkg** balíček služby **fabric: / WordCount** nasazenou aplikaci v **to uzel _Node_2**. Entita má **System.Hosting** sestavy pro úspěšné aktivaci balíček služby a vstupního bodu a úspěšnou registraci. typ služby.

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
Můžete získat stav balíčku nasazené služby pomocí [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) , který obsahuje zásady stavu, které jsou popsané v textu.

## <a name="health-chunk-queries"></a>Dotazy na stav bloku
Dotazy na datových dávek stav může vrátit víceúrovňových clusteru děti (rekurzivně), za vstupní filtry. Podporuje rozšířené filtry, které umožní značnou flexibilitu při výběru podřízené položky, který se má vrátit. Jedinečný identifikátor nebo jiné skupiny a/nebo stavy, můžete zadat filtry podřízené položky. Ve výchozím nastavení jsou zahrnuty, na rozdíl od stavu příkazy, které vždy zahrnují podřízené objekty první úrovně žádné podřízené položky.

[Dotazů na stav](service-fabric-view-entities-aggregated-health.md#health-queries) vrátit pouze první úroveň podřízených prvků zadaná entita na požadované filtry. Získat podřízený element podřízené objekty, musí volat další stav rozhraní API pro každou entitu, které vás zajímají. Podobně pokud chcete získat stav konkrétních entit, musí volat jeden stav rozhraní API pro každou požadovanou entitu. Dotaz bloků dat, rozšířené filtrování umožňuje žádosti více položek zájmu v jednom dotazu, minimalizovat velikosti zpráv a počet zpráv.

Hodnota dotazu blok dat je, že můžete získat stav pro další entity clusteru (potenciálně všechny clusteru entity začínající na požadovaný kořenový) v jednom volání. Komplexní stavu dotazu můžete vyjádřit jako například:

* Vrácení pouze aplikace v chybě a pro tyto aplikace zahrnout všechny služby upozornění nebo chyby. Vrácené služby zahrnují všechny oddíly.
* Vrátíte pouze stav čtyři aplikací podle jejich názvy.
* Vrátíte pouze stav aplikací typu požadované aplikace.
* Vrátí všechny nasazené entity na uzlu. Vrátí všechny aplikace, všechny nasazené aplikace na zadaný uzel a všechny balíčky nasazené služby v tomto uzlu.
* Vrátí všechny repliky v chybě. Vrátí všechny aplikace, služby, oddíly a repliky pouze v chybě.
* Vrátí všechny aplikace. V případě zadanou službu obsahuje všechny oddíly.

V současné době dotazu bloku stavu je přístupný pouze pro entitu clusteru. Vrátí blok stav clusteru, který obsahuje:

* Stav clusteru agregovat.
* Stav seznam bloků dat Stav uzlů, které respektovat vstupní filtry.
* Stavu seznam bloků dat stavu aplikací, které respektovat vstupní filtry. Každý blok zdravotní stav aplikace obsahuje seznam bloků dat se všemi službami, které respektovat vstupní filtry a seznam bloků dat se všechny nasazené aplikace, které respektovat filtry. Stejný pro podřízené objekty služeb a nasazené aplikace. Tímto způsobem všechny entity v clusteru může být potenciálně vrácena, pokud požadovaný skupinami je hierarchický.

### <a name="cluster-health-chunk-query"></a>Dotaz bloku stavu clusteru
Vrátí stav clusteru entita a obsahuje bloky stavu hierarchické stavu vyžaduje podřízených prvků. Vstup:

* [Volitelné] Zásady stavu clusteru k vyhodnocení, uzly a události clusteru.
* [Volitelné] Stav zásad mapy aplikace pomocí zásad stavu, použít k přepsání zásad manifestu aplikace.
* [Volitelné] Filtry pro uzly a aplikace, které určují položky, které jsou zajímavé a má být vrácen ve výsledku. Filtry specifické pro entitu nebo skupinu entit nebo se vztahují na všechny entity na této úrovni. Seznam filtrů může obsahovat jeden obecné filtru nebo filtry pro konkrétní identifikátory podrobnější entit vrácených dotazem. Pokud je prázdný, podřízené objekty nebudou zobrazeny ve výchozím nastavení.
  Další informace o filtry v [NodeHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) a [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). Rekurzivně může filtry aplikace zadat rozšířené filtry pro děti.

Výsledek bloku dat obsahuje podřízené položky, které respektovat filtry.

V současné době bloků dotaz nevrací špatná vyhodnocení nebo entit událostí. Další informace lze zjistit pomocí existujícího dotazu stav clusteru.

### <a name="api"></a>Rozhraní API
Pokud chcete získat stav clusteru bloků dat, vytvořte `FabricClient` a volat [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) metodu na jeho **HealthManager**. Můžete předat [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) k popisu stavu zásad a rozšířené filtry.

Následující kód načte stav clusteru bloků dat s rozšířené filtry.

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
Rutiny pro získání stavu clusteru je [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Nejprve připojte ke clusteru pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) rutiny.

Následující kód načte uzly pouze v případě, že se chyba s výjimkou konkrétní uzel, který by vždycky měla být vrácena.

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

Následující rutiny získá clusteru bloků dat s filtry aplikace.

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
Můžete získat blok stavu clusteru s [požadavek GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) nebo [požadavek POST](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) , který obsahuje zásady stavu a rozšířené filtry, které jsou popsané v textu.

## <a name="general-queries"></a>Obecné dotazy
Obecné dotazy vrací seznam entit Service Fabric zadaného typu. Jsou přístupné prostřednictvím rozhraní API (prostřednictvím metod na **FabricClient.QueryManager**), rutin Powershellu a REST. Tyto dotazy agregovat poddotazy z několika součástí. Jeden z nich je [health store](service-fabric-health-introduction.md#health-store), která naplní agregovaný stav každého výsledku dotazu.  

> [!NOTE]
> Obecné dotazy vracet agregovaný stav entity a neobsahují data bohaté stavu. Pokud entita není v pořádku, můžete sledovat pomocí dotazů na stav zobrazíte všechny její stav informace, včetně událostí, podřízené stavů a špatná vyhodnocení.
>
>

Pokud obecné dotazy Neznámý stav pro entitu, je možné, že stav úložiště nemá úplná data o entitě. Je také možné, že nebyla úspěšná poddotaz k úložišti stavů (například došlo k chybě komunikace, nebo byla omezena health store). Proveďte s dotazem stavu entity. Pokud poddotazu došlo k přechodné chyby, jako je například problémy se sítí, může být úspěšné následné dotaz. To může také přineseme další informace z health store o proč entity není dostupná.

Dotazy, které obsahují **stavu HealthState** pro entity jsou:

* Seznam uzlů: vrátí seznam uzlů v clusteru (stránkování).
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * Powershellu: Get-ServiceFabricNode
* Seznam aplikací: vrátí seznam aplikací v clusteru (stránkování).
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * Powershellu: Get-ServiceFabricApplication
* Seznam služeb: vrátí seznam služeb v aplikaci (stránkování).
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * Powershellu: Get-ServiceFabricService
* Seznam oddílů: vrátí seznam oddílů služby (stránkování).
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * Powershellu: Get-ServiceFabricPartition
* Seznam replik: vrátí seznam replik v oddílu (stránkování).
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * Powershellu: Get-ServiceFabricReplica
* Nasadit seznam aplikací: vrátí seznam nasazených aplikací na uzlu.
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * Powershellu: Get-ServiceFabricDeployedApplication
* Nasadit seznam balíčků služby: vrátí seznam balíčků služby v nasazené aplikaci.
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * Powershellu: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Některé dotazy vrátit stránkových výsledků. Návrat tyto dotazy je odvozen ze seznamu [PagedList<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Pokud výsledky nebudou vyhovovat zprávu, vrátí se pouze na stránce a ContinuationToken, který sleduje kde výčet zastavena. I nadále volání stejný dotaz a předejte token pro pokračování z předchozího dotazu zobrazíte další výsledky.
>
>

### <a name="examples"></a>Příklady
Následující kód načte poškozené aplikace v clusteru:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Následující rutiny získá podrobnosti o aplikaci pro topologie fabric: / WordCount aplikace. Všimněte si, že stav je v upozornění.

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

Následující rutina načte služby se stav chyby:

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

## <a name="cluster-and-application-upgrades"></a>Upgrady aplikací a clusteru
Během monitorovaných inovace aplikací a clusteru Service Fabric kontroluje zajištění, že vše zůstane v dobrém stavu. Pokud není v pořádku, jak se vyhodnocují se pomocí zásady nakonfigurované stavu entity se bude upgrade vztahovat zásady specifické pro upgrade k určení další akce. Upgrade může pozastaví. Pokud chcete povolit interakci uživatele (například opravujete chybové stavy nebo změna zásad) nebo ji může automaticky vrátit k předchozí funkční verzi.

Během *clusteru* upgradu, můžete získat stav upgradu clusteru. Stav upgradu zahrnuje špatná vyhodnocení, které odkazují na to, co je v clusteru není v pořádku. Pokud upgradu se vrátí zpět z důvodu problémů se stavem, stav upgradu si pamatuje poslední důvodů není v pořádku. Tyto informace můžou pomoct správci zjistit, k jakému po upgradu přestal nebo vrácena zpět.

Podobně při *aplikace* upgrade všech není v pořádku, hodnocení jsou obsaženy v stav upgradu aplikace.

Následující příklad zobrazuje stav upgradu aplikace pro upravené fabric: / WordCount aplikace. Sledovacích ohlásil chybu na jednom z jejích replik. Upgrade se zajištěním provozu zpět, protože není respektována kontroly stavu.

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

Další informace najdete [upgrade aplikace Service Fabric](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Řešení potíží s pomocí vyhodnocení stavu
Pokaždé, když se vyskytl problém s clusteru nebo aplikace, podívejte se na stav clusteru nebo aplikaci pro identifikaci, co je špatně. Špatná vyhodnocení obsahují podrobné informace o co vyvolalo aktuální stav není v pořádku. Pokud je potřeba, můžete procházet hierarchii na není v pořádku podřízené entity a identifikovat hlavní příčinu.

Zvažte například aplikaci není v pořádku, protože zprávu o chybách na jednom z jejích replik. Následující rutiny prostředí Powershell ukazuje špatná vyhodnocení:

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
> Špatná vyhodnocení zobrazí že první důvod entity se vyhodnocuje do aktuálního stavu. Může existovat více událostí, které aktivují tento stav, ale neprojeví v hodnocení. Pokud chcete získat další informace, k podrobnostem stavu entity, které chcete zjistit všechny sestavy není v pořádku v clusteru.
>
>

## <a name="next-steps"></a>Další postup
[Použití sestav o stavu systému k řešení problémů](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Přidání vlastních stavových sestav Service Fabric](service-fabric-report-health.md)

[Způsob hlášení a kontrola stavu služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorování a Diagnostika služeb místně](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)
