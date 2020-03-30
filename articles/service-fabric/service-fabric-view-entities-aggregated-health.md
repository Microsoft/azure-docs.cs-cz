---
title: Jak zobrazit agregovaný stav entit Azure Service Fabric
description: Popisuje, jak dotazovat, zobrazit a vyhodnotit agregované stav y Azure Service Fabric prostřednictvím dotazů na stav a obecných dotazů.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d02d8f717801bf51e43c9dafa5eb9379d0737674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464125"
---
# <a name="view-service-fabric-health-reports"></a>Zobrazit sestavy stavu service fabric
Azure Service Fabric zavádí [model stavu](service-fabric-health-introduction.md) s entity stavu, na kterých systémové komponenty a watchdogs můžete hlásit místní podmínky, které jsou monitorování. [Úložiště stavu](service-fabric-health-introduction.md#health-store) agreguje všechna data o stavu k určení, zda entity jsou v pořádku.

Cluster je automaticky naplněn zprávami o stavu odeslanými systémovými součástmi. Další informace naleznete v [článek Použití zpráv o stavu systému k řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric poskytuje několik způsobů, jak získat agregované stav entit:

* [Průzkumník service fabric nebo](service-fabric-visualizing-your-cluster.md) jiné vizualizační nástroje
* Dotazy na stav (prostřednictvím Prostředí PowerShell, ROZHRANÍ API nebo REST)
* Obecné dotazy, které vracejí seznam entit, které mají stav jako jednu z vlastností (prostřednictvím prostředí PowerShell, ROZHRANÍ API nebo REST)

Chcete-li tyto možnosti předvést, použijte místní cluster s pěti uzly a [aplikaci fabric:/WordCount](https://github.com/Azure-Samples/service-fabric-wordcount/raw/master/WordCountV1.sfpkg). **Fabric:/WordCount** aplikace obsahuje dvě výchozí služby, `WordCountServiceType`stavové služby typu `WordCountWebServiceType`a bezstavové služby typu . Změnil jsem `ApplicationManifest.xml` vyžadovat sedm cílových replik pro stavové služby a jeden oddíl. Vzhledem k tomu, že v clusteru je pouze pět uzlů, systémové součásti hlásí upozornění na oddíl služby, protože je pod počtem cílů.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Health in Service Fabric Explorer
Aplikace Service Fabric Explorer poskytuje vizuální zobrazení clusteru. Na obrázku níže, můžete vidět, že:

* Fabric **aplikace:/WordCount** je červená (v chybě), protože má chybovou událost hlášenou **MyWatchdog** pro **dostupnost vlastnosti**.
* Jedna z jeho služeb, **fabric:/WordCount/WordCountService** je žlutá (v upozornění). Služba je nakonfigurována se sedmi replikami a cluster má pět uzlů, takže dvě repliky nelze umístit. I když to není zobrazeno zde, oddíl služby `System.FM` je `Partition is below target replica or instance count`žlutá z důvodu systémové sestavy z říká, že . Žlutý oddíl spustí žlutou službu.
* Cluster je červený z důvodu červené aplikace.

Vyhodnocení používá výchozí zásady z manifestu clusteru a manifestu aplikace. Jsou to přísné politiky a netolerují žádné selhání.

Zobrazení clusteru pomocí průzkumníka service fabric:

![Zobrazení clusteru pomocí aplikace Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Přečtěte si další informace o [aplikaci Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Dotazy na stav
Service Fabric zpřístupňuje dotazy na stav pro každý z [podporovaných typů entit](service-fabric-health-introduction.md#health-entities-and-hierarchy). K nim lze přistupovat prostřednictvím rozhraní API pomocí metod [fabricclient.healthmanager](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), rutin prostředí PowerShell a REST. Tyto dotazy vrátí úplné informace o stavu entity: agregovaný stav, události stavu entity, stav dítěte stav (pokud je to možné), nefunkční hodnocení (pokud entita není v pořádku) a statistiky stavu dětí (když jsou k dispozici použitelné).

> [!NOTE]
> Entita stavu je vrácena, když je plně naplněna v úložišti stavu. Entita musí být aktivní (neodstraněná) a musí mít systémovou sestavu. Jeho nadřazené entity v řetězci hierarchie musí mít také systémové sestavy. Pokud některá z těchto podmínek nejsou splněny, dotazy na stav vrátí [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) s [FabricErrorCode,](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` který ukazuje, proč není vrácena entita.
>
>

Dotazy na stav musí předat v identifikátoru entity, který závisí na typu entity. Dotazy přijímají volitelné parametry zásad stavu. Pokud nejsou zadány žádné zásady stavu, [zásady stavu](service-fabric-health-introduction.md#health-policies) z clusteru nebo manifestu aplikace se používají pro hodnocení. Pokud manifesty neobsahují definici zásad stavu, výchozí zásady stavu se používají pro hodnocení. Výchozí zásady stavu netolerují žádné chyby. Dotazy také přijímají filtry pro vrácení pouze částečné podřízené nebo události – ty, které respektují zadané filtry. Jiný filtr umožňuje vyloučit statistiky dětí.

> [!NOTE]
> Výstupní filtry jsou použity na straně serveru, takže velikost odpovědi na zprávu je snížena. Doporučujeme použít výstupní filtry k omezení vrácených dat, spíše než použít filtry na straně klienta.
>
>

Stav entity obsahuje:

* Agregovaný stav entity. Vypočítáno úložištěm stavu na základě sestav stavu entity, stavu dítěte (pokud je to možné) a zásad stavu. Přečtěte si další informace o [hodnocení stavu entity](service-fabric-health-introduction.md#health-evaluation).  
* Události stavu entity.
* Kolekce stavů stavu všech dětí pro entity, které mohou mít podřízené osoby. Stavy obsahují identifikátory entit a agregovaný stav. Chcete-li získat úplný stav pro dítě, zavolejte stav dotazu pro typ podřízené entity a předat v podřízeném identifikátoru.
* Nefunkční hodnocení, které odkazují na sestavu, která spustila stav entity, pokud entita není v pořádku. Hodnocení jsou rekurzivní, obsahující hodnocení zdraví dětí, která vyvolala aktuální zdravotní stav. Například watchdog ohlásil chybu proti replice. Stav aplikace ukazuje hodnocení není v pořádku z důvodu služby není v pořádku; služba není v pořádku z důvodu chyby oddílu; oddíl není v pořádku z důvodu repliky v chybě; replika není v pořádku z důvodu sestavy stavu chyb y sledovacího zařízení.
* Statistiky stavu pro všechny podřízené typy entit, které mají podřízené. Stav clusteru například zobrazuje celkový počet aplikací, služeb, oddílů, replik a nasazených entit v clusteru. Stav služby zobrazuje celkový počet oddílů a replik v rámci zadané služby.

## <a name="get-cluster-health"></a>Získání stavu clusteru
Vrátí stav entity clusteru a obsahuje stavy aplikací a uzlů (podřízené položky clusteru). Vstup:

* [Nepovinné] Zásady stavu clusteru používané k vyhodnocení uzlů a událostí clusteru.
* [Nepovinné] Mapa zásad stavu aplikace s zásadami stavu, které se používají k přepsání zásad manifestu aplikace.
* [Nepovinné] Filtry pro události, uzly a aplikace, které určují, které položky jsou zajímavé a měly by být vráceny ve výsledku (například pouze chyby nebo upozornění a chyby). Všechny události, uzly a aplikace se používají k vyhodnocení agregovaného stavu entity bez ohledu na filtr.
* [Nepovinné] Filtrem vyloučíte statistiky stavu.
* [Nepovinné] Filtr zahrnout fabric:/Statistiky stavu systému ve statistikách stavu. Použitelné pouze v případě, že nejsou vyloučeny zdravotní statistiky. Ve výchozím nastavení obsahují statistiky stavu pouze statistiky pro uživatelské aplikace a nikoli systémové aplikace.

### <a name="api"></a>rozhraní API
Chcete-li získat stav `FabricClient` clusteru, vytvořte a zavolejte metodu [GetClusterHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) na svém **HealthManageru**.

Následující volání získá stav clusteru:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Následující kód získá stav clusteru pomocí vlastní zásady stavu clusteru a filtry pro uzly a aplikace. Určuje, že statistiky stavu zahrnují statistiky prostředků fabric:/System. Vytvoří [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthquerydescription), který obsahuje vstupní informace.

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
Rutina získat stav clusteru je [Get-ServiceFabricClusterHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealth). Nejprve se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Stav clusteru je pět uzlů, systémová aplikace a fabric:/WordCount nakonfigurován tak, jak je popsáno.

Následující rutina získá stav clusteru pomocí výchozích zásad stavu. Agregovaný stav je upozornění, protože fabric:/WordCount aplikace je v upozornění. Všimněte si, jak není v pořádku hodnocení poskytují podrobnosti o podmínky, které spustily agregované stavu.

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

Následující rutina prostředí PowerShell získá stav clusteru pomocí vlastní chrupu aplikací. Filtruje výsledky, aby se chybně nebo v upozornění dostaly pouze aplikace a uzly. V důsledku toho jsou vráceny žádné uzly, protože jsou všechny v pořádku. Pouze fabric:/WordCount aplikace respektuje filtr aplikace. Vzhledem k tomu, že vlastní zásady určuje považovat upozornění jako chyby pro aplikaci fabric:/WordCount, aplikace je vyhodnocena jako chybná a tak je cluster.

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
Můžete získat stav clusteru s [požadavkem GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster) nebo [požadavek POST,](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) který obsahuje zásady stavu popsané v těle.

## <a name="get-node-health"></a>Získat stav uzlu
Vrátí stav entity uzlu a obsahuje události stavu hlášené v uzlu. Vstup:

* [Povinné] Název uzlu, který identifikuje uzel.
* [Nepovinné] Nastavení zásad stavu clusteru slouží k vyhodnocení stavu.
* [Nepovinné] Filtry pro události, které určují, které položky jsou zajímavé a měly by být vráceny ve výsledku (například pouze chyby nebo upozornění a chyby). Všechny události se používají k vyhodnocení agregovaného stavu entity bez ohledu na filtr.

### <a name="api"></a>rozhraní API
Chcete-li získat stav uzlu prostřednictvím rozhraní API, vytvořte `FabricClient` a zavolejte metodu [GetNodeHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) na jeho HealthManager.

Následující kód získá stav uzlu pro název zadaného uzlu:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Následující kód získá stav uzlu pro název zadaného uzlu a předá ve filtru událostí a vlastní zásady prostřednictvím [NodeHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Rutina získat stav uzlu je [Get-ServiceFabricNodeHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnodehealth). Nejprve se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)
Následující rutina získá stav uzlu pomocí výchozích zásad stavu:

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

Následující rutina získá stav všech uzlů v clusteru:

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
Můžete získat stav uzlu s [požadavkem GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node) nebo [požadavek POST,](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) který obsahuje zásady stavu popsané v těle.

## <a name="get-application-health"></a>Získání stavu aplikace
Vrátí stav entity aplikace. Obsahuje stavy nasazených aplikací a podřízených služeb. Vstup:

* [Povinné] Název aplikace (URI), který identifikuje aplikaci.
* [Nepovinné] Zásady stavu aplikace používané k přepsání zásad manifestu aplikace.
* [Nepovinné] Filtry pro události, služby a nasazené aplikace, které určují, které položky jsou zajímavé a měly by být vráceny ve výsledku (například pouze chyby nebo upozornění a chyby). Všechny události, služby a nasazené aplikace se používají k vyhodnocení agregovaného stavu entity bez ohledu na filtr.
* [Nepovinné] Filtrem vyloučíte statistiky stavu. Pokud není zadán, statistiky stavu zahrnují ok, upozornění a počet chyb pro všechny podřízené aplikace: služby, oddíly, repliky, nasazené aplikace a nasazené balíčky služeb.

### <a name="api"></a>rozhraní API
Chcete-li získat stav `FabricClient` aplikace, vytvořte a zavolejte metodu [GetApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) na svém HealthManageru.

Následující kód získá stav aplikace pro zadaný název aplikace (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Následující kód získá stav aplikace pro zadaný název aplikace (URI), s filtry a vlastní zásady zadané prostřednictvím [ApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.applicationhealthquerydescription).

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
Rutina získat stav aplikace je [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps). Nejprve se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Následující rutina vrátí stav **aplikace fabric:/WordCount:**

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

Následující rutina prostředí PowerShell prochází ve vlastních zásadách. Filtruje také děti a události.

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
Můžete získat stav aplikace s [požadavkem GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application) nebo [požadavek POST,](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) který obsahuje zásady stavu popsané v těle.

## <a name="get-service-health"></a>Získání stavu služby
Vrátí stav entity služby. Obsahuje stavy oddílu. Vstup:

* [Povinné] Název služby (URI), který identifikuje službu.
* [Nepovinné] Zásady stavu aplikace používané k přepsání zásad manifestu aplikace.
* [Nepovinné] Filtry pro události a oddíly, které určují, které položky jsou zajímavé a měly by být vráceny ve výsledku (například pouze chyby nebo upozornění a chyby). Všechny události a oddíly se používají k vyhodnocení agregovaného stavu entity bez ohledu na filtr.
* [Nepovinné] Filtrem vyloučíte statistiky stavu. Pokud není zadán, statistiky stavu zobrazit ok, upozornění a počet chyb pro všechny oddíly a repliky služby.

### <a name="api"></a>rozhraní API
Chcete-li získat stav služby `FabricClient` prostřednictvím rozhraní API, vytvořte a zavolejte metodu [GetServiceHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) na svém HealthManageru.

Následující příklad získá stav služby se zadaným názvem služby (URI):

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Následující kód získá stav služby pro zadaný název služby (URI) a určuje filtry a vlastní zásady prostřednictvím [serviceHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Rutina získat stav služby je [Get-ServiceFabricServiceHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicehealth). Nejprve se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Následující rutina získá stav služby pomocí výchozích zásad stavu:

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
Můžete získat stav služby s [požadavkem GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service) nebo [požadavek POST,](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) který obsahuje zásady stavu popsané v těle.

## <a name="get-partition-health"></a>Získání stavu oddílu
Vrátí stav entity oddílu. Obsahuje stavy repliky. Vstup:

* [Povinné] ID oddílu (GUID), který identifikuje oddíl.
* [Nepovinné] Zásady stavu aplikace používané k přepsání zásad manifestu aplikace.
* [Nepovinné] Filtry pro události a repliky, které určují, které položky jsou zajímavé a měly by být vráceny ve výsledku (například pouze chyby nebo upozornění a chyby). Všechny události a repliky se používají k vyhodnocení agregovaného stavu entity bez ohledu na filtr.
* [Nepovinné] Filtrem vyloučíte statistiky stavu. Pokud není zadán, statistiky stavu ukazují, kolik replik y jsou v ok, upozornění a chybové stavy.

### <a name="api"></a>rozhraní API
Chcete-li získat stav oddílu `FabricClient` prostřednictvím rozhraní API, vytvořte a zavolejte metodu [GetPartitionHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) na svém HealthManageru. Chcete-li zadat volitelné parametry, vytvořte [partitionHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
Rutina získat stav oddílu je [Get-ServiceFabricPartitionHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricpartitionhealth). Nejprve se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Následující rutina získá stav pro všechny oddíly **služby fabric:/WordCount/WordCountService** a filtruje stav repliky:

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
Můžete získat stav oddílu s [požadavkem GET](/rest/api/servicefabric/sfclient-api-getpartitionhealth) nebo [požadavek POST,](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) který obsahuje zásady stavu popsané v těle.

## <a name="get-replica-health"></a>Získat stav repliky
Vrátí stav repliky stavové služby nebo instance bezstavové služby. Vstup:

* [Povinné] ID oddílu (GUID) a ID repliky, které identifikuje repliku.
* [Nepovinné] Parametry zásad stavu aplikace používané k přepsání zásad manifestu aplikace.
* [Nepovinné] Filtry pro události, které určují, které položky jsou zajímavé a měly by být vráceny ve výsledku (například pouze chyby nebo upozornění a chyby). Všechny události se používají k vyhodnocení agregovaného stavu entity bez ohledu na filtr.

### <a name="api"></a>rozhraní API
Chcete-li získat stav repliky `FabricClient` prostřednictvím rozhraní API, vytvořte a zavolejte metodu [GetReplicaHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) na jeho HealthManager. Chcete-li zadat rozšířené parametry, použijte [replicaHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
Rutina získat stav repliky je [Get-ServiceFabricReplicaHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricreplicahealth). Nejprve se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Následující rutina získá stav primární repliky pro všechny oddíly služby:

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
Můžete získat stav repliky s [požadavkem GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica) nebo [požadavek POST,](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) který obsahuje zásady stavu popsané v těle.

## <a name="get-deployed-application-health"></a>Získání stavu nasazených aplikací
Vrátí stav aplikace nasazené na entitu uzlu. Obsahuje stavy nasazeného balíčku služeb. Vstup:

* [Povinné] Název aplikace (URI) a název uzlu (řetězec), které identifikují nasazenou aplikaci.
* [Nepovinné] Zásady stavu aplikace používané k přepsání zásad manifestu aplikace.
* [Nepovinné] Filtry pro události a nasazené balíčky služeb, které určují, které položky jsou zajímavé a měly by být vráceny ve výsledku (například pouze chyby nebo upozornění a chyby). Všechny události a nasazené balíčky služeb se používají k vyhodnocení agregovaného stavu entity bez ohledu na filtr.
* [Nepovinné] Filtrem vyloučíte statistiky stavu. Pokud není zadán, statistiky stavu zobrazit počet nasazených balíčků služeb v ok, upozornění a chybové stavy.

### <a name="api"></a>rozhraní API
Chcete-li získat stav aplikace nasazené na uzlu prostřednictvím `FabricClient` rozhraní API, vytvořte a zavolejte [metodu GetDeployedApplicationHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) na jeho HealthManager. Chcete-li zadat volitelné parametry, použijte [deployedApplicationHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
Rutina získat nasazené stavu aplikace je [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps). Nejprve se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) Chcete-li zjistit, kde je aplikace nasazena, spusťte [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) a podívejte se na nasazené aplikace podřízené.

Následující rutina získá stav **prostředků fabric:/WordCount** aplikace nasazená na **_Node_2**.

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
Můžete získat nasazené stav aplikace s [požadavkem GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application) nebo [post požadavek,](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) který obsahuje zásady stavu popsané v těle.

## <a name="get-deployed-service-package-health"></a>Získání stavu nasazených balíčků služeb
Vrátí stav entity balíčku nasazených služeb. Vstup:

* [Povinné] Název aplikace (URI), název uzlu (řetězec) a název manifestu služby (řetězec), které identifikují balíček nasazených služeb.
* [Nepovinné] Zásady stavu aplikace používané k přepsání zásad manifestu aplikace.
* [Nepovinné] Filtry pro události, které určují, které položky jsou zajímavé a měly by být vráceny ve výsledku (například pouze chyby nebo upozornění a chyby). Všechny události se používají k vyhodnocení agregovaného stavu entity bez ohledu na filtr.

### <a name="api"></a>rozhraní API
Chcete-li získat stav balíčku nasazených služeb prostřednictvím rozhraní API, vytvořte `FabricClient` a zavolejte metodu [GetDeployedServicePackageHealthAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) na svém HealthManageru. Chcete-li zadat volitelné parametry, použijte [deployedServicePackageHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Rutina pro získání stavu balíčku nasazených služeb je [Get-ServiceFabricDeployedServicePackageHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Nejprve se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) Chcete-li zjistit, kde se aplikace nasadí, spusťte [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth?view=azureservicefabricps) a podívejte se na nasazené aplikace. Chcete-li zjistit, které balíčky služeb jsou v aplikaci, podívejte se na podřízené balíčky nasazených služeb ve výstupu [Get-ServiceFabricDeployedApplicationHealth.](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth?view=azureservicefabricps)

Následující rutina získá stav balíčku služby **WordCountServicePkg** **aplikace fabric:/WordCount** nasazené v **_Node_2**. Entita má **System.Hosting** sestavy pro úspěšnou aktivaci service-package a entry-point a úspěšnou registraci typu služby.

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
Můžete získat nasazené služby balíček stavu s [požadavkem GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package) nebo [post požadavek,](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) který obsahuje zásady stavu popsané v těle.

## <a name="health-chunk-queries"></a>Dotazy na bloky dat stavu
Dotazy bloku stavu můžete vrátit víceúrovňové clusteru podřízené (rekurzivně) na vstupní filtry. Podporuje pokročilé filtry, které umožňují velkou flexibilitu při výběru dětí, které mají být vráceny. Filtry mohou určit podřízené objekty podle jedinečného identifikátoru nebo podle jiných identifikátorů skupiny nebo stavů. Ve výchozím nastavení nejsou zahrnuty žádné podřízené děti, na rozdíl od příkazů stavu, které vždy zahrnují podřízené děti první úrovně.

Dotazy na stav vrátí pouze podřízené [položky](service-fabric-view-entities-aggregated-health.md#health-queries) první úrovně zadané entity na požadované filtry. Chcete-li získat děti dětí, musíte volat další zdravotní api pro každou entitu zájmu. Podobně chcete-li získat stav konkrétních entit, musíte volat jedno rozhraní API stavu pro každou požadovanou entitu. Rozšířené filtrování dotazu bloku umožňuje požadovat více položek, které vás zajímají v jednom dotazu, a minimalizovat tak velikost zprávy a počet zpráv.

Hodnota dotazu bloku je, že můžete získat stav pro více entit clusteru (potenciálně všechny entity clusteru začínající na požadované matné kořenové) v jednom volání. Můžete vyjádřit komplexní dotaz na stav, například:

* Vrátit pouze aplikace omylem a pro tyto aplikace zahrnout všechny služby v upozornění nebo chyby. Pro vrácené služby zahrnout všechny oddíly.
* Vrátí pouze stav čtyř aplikací určených jejich názvy.
* Vrátí pouze stav aplikací požadovaného typu aplikace.
* Vrátí všechny nasazené entity v uzlu. Vrátí všechny aplikace, všechny nasazené aplikace v zadaném uzlu a všechny nasazené balíčky služeb v tomto uzlu.
* Vrátit všechny repliky omylem. Vrátí všechny aplikace, služby, oddíly a pouze repliky omylem.
* Vraťte všechny aplikace. Pro zadanou službu zahrňte všechny oddíly.

V současné době je dotaz bloku stavu vystaven pouze pro entitu clusteru. Vrátí blok stavu clusteru, který obsahuje:

* Cluster agregované stav.
* Seznam dat stavu uzlů, které respektují vstupní filtry.
* Seznam bloků dat stavu aplikací, které respektují vstupní filtry. Každý blok stavu aplikace obsahuje seznam bloků dat se všemi službami, které respektují vstupní filtry a seznam bloků dat se všemi nasazenými aplikacemi, které respektují filtry. Stejné pro děti služeb a nasazených aplikací. Tímto způsobem všechny entity v clusteru může být potenciálně vrácena na požádání, hierarchickým způsobem.

### <a name="cluster-health-chunk-query"></a>Dotaz na blok ový blok clusteru
Vrátí stav entity clusteru a obsahuje hierarchické bloky stavu požadovaných podřízených položek. Vstup:

* [Nepovinné] Zásady stavu clusteru používané k vyhodnocení uzlů a událostí clusteru.
* [Nepovinné] Mapa zásad stavu aplikace s zásadami stavu, které se používají k přepsání zásad manifestu aplikace.
* [Nepovinné] Filtry pro uzly a aplikace, které určují, které položky jsou zajímavé a měly by být vráceny ve výsledku. Filtry jsou specifické pro entitu/skupinu entit nebo jsou použitelné pro všechny entity na této úrovni. Seznam filtrů může obsahovat jeden obecný filtr nebo filtry pro konkrétní identifikátory pro jemně zrnité entity vrácené dotazem. Pokud prázdné, podřízené nejsou vráceny ve výchozím nastavení.
  Další informace o filtrech naleznete na [nodeHealthStatefilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.nodehealthstatefilter) a [ApplicationHealthStateFilter](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthstatefilter). Filtry aplikace mohou rekurzivně určit rozšířené filtry pro podřízené objekty.

Výsledek bloku zahrnuje podřízené položky, které respektují filtry.

V současné době dotaz bloku nevrátí nevyhovené hodnocení nebo události entity. Tyto další informace lze získat pomocí existujícího dotazu na stav clusteru.

### <a name="api"></a>rozhraní API
Chcete-li získat blok `FabricClient` stavu clusteru, vytvořte a zavolejte metodu [GetClusterHealthChunkAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) na svém **HealthManageru**. Můžete předat v [ClusterHealthQueryDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) k popisu zásad stavu a rozšířené filtry.

Následující kód získá blok stavu clusteru s pokročilými filtry.

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
Rutina pro získání stavu clusteru je [Get-ServiceFabricClusterChunkHealth](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Nejprve se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster.](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)

Následující kód získá uzly pouze v případě, že jsou v Error s výjimkou konkrétní uzel, který by měl být vždy vrácena.

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

Následující rutina získá blok clusteru s filtry aplikací.

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

Následující rutina vrátí všechny nasazené entity v uzlu.

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
Můžete získat blok stavu clusteru s [požadavkem GET](https://docs.microsoft.com/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) nebo [požadavek POST,](https://docs.microsoft.com/rest/api/servicefabric/health-of-cluster) který obsahuje zásady stavu a rozšířené filtry popsané v těle.

## <a name="general-queries"></a>Obecné dotazy
Obecné dotazy vrátí seznam entit Service Fabric zadaného typu. Jsou vystaveny prostřednictvím rozhraní API (prostřednictvím metod na **FabricClient.QueryManager**), Rutiny prostředí PowerShell a REST. Tyto dotazy agregovat poddotazy z více součástí. Jedním z nich je [úložiště stavu](service-fabric-health-introduction.md#health-store), které naplní agregovaný stav pro každý výsledek dotazu.  

> [!NOTE]
> Obecné dotazy vrátí agregovaný stav entity a neobsahují bohatá data o stavu. Pokud entita není v pořádku, můžete zřadit dotazy na stav získat všechny informace o stavu, včetně událostí, stavdítěte stavů a nefunkční hodnocení.
>
>

Pokud obecné dotazy vrátí neznámý stav pro entitu, je možné, že úložiště stavu nemá úplná data o entitě. Je také možné, že poddotaz do úložiště stavu nebyl úspěšný (například došlo k chybě komunikace nebo byl omezen úložiště stavu). Zpracování dotazu na stav entity. Pokud poddotaz zjistil přechodné chyby, například problémy se sítí, může být tento následný dotaz úspěšný. Může také poskytnout další podrobnosti z úložiště stavu o tom, proč entita není vystavena.

Dotazy, které obsahují **HealthState** pro entity jsou:

* Seznam uzlů: Vrátí uzly seznamu v clusteru (stránkované).
  * Rozhraní API: [FabricClient.QueryClient.GetNodeListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Seznam aplikací: Vrátí seznam aplikací v clusteru (stránkované).
  * Rozhraní API: [FabricClient.QueryClient.GetApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * Prostředí PowerShell: Aplikace Get-ServiceFabric
* Seznam služeb: Vrátí seznam služeb v aplikaci (stránkované).
  * Rozhraní API: [FabricClient.QueryClient.GetServiceListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Seznam oddílů: Vrátí seznam oddílů ve službě (stránkované).
  * Rozhraní API: [FabricClient.QueryClient.GetPartitionListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * Prostředí PowerShell: Oddíl Get-ServiceFabric
* Seznam replik: Vrátí seznam replik v oddílu (stránkovaném).
  * Rozhraní API: [FabricClient.QueryClient.GetReplicaListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * Prostředí PowerShell: Replika struktury prostředků Get-ServiceFabric
* Seznam nasazených aplikací: Vrátí seznam nasazených aplikací v uzlu.
  * ROZHRANÍ API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Seznam balíčků nasazených služeb: Vrátí seznam balíčků služeb v nasazené aplikaci.
  * ROZHRANÍ API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Některé dotazy vrátí stránkované výsledky. Vrácení těchto dotazů je seznam odvozený od [PagedList\<T>](https://docs.microsoft.com/dotnet/api/system.fabric.query.pagedlist-1). Pokud výsledky nevejdou zprávu, je vrácena pouze stránka a ContinuationToken, který sleduje, kde výčet zastaven. Pokračujte v volání stejného dotazu a předejte token pokračování z předchozího dotazu, abyste získali další výsledky.

### <a name="examples"></a>Příklady
Následující kód získá aplikace není v pořádku v clusteru:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Následující rutina získá podrobnosti aplikace pro aplikaci fabric:/WordCount. Všimněte si, že stav je na upozornění.

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

Následující rutina získá služby se stavem chyby:

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

## <a name="cluster-and-application-upgrades"></a>Upgrady clusteru a aplikací
Během sledované inovace clusteru a aplikace service fabric zkontroluje stav, aby zajistily, že vše zůstane v pořádku. Pokud entita není v pořádku, jak je vyhodnocena pomocí nakonfigurovaných zásad stavu, upgrade použije zásady specifické pro upgrade k určení další akce. Upgrade může být pozastaven, aby bylo možné interakci s uživatelem (například oprava chybových stavů nebo změna zásad), nebo se může automaticky vrátit k předchozí dobré verzi.

Během upgradu *clusteru* můžete získat stav upgradu clusteru. Stav upgradu zahrnuje hodnocení není v pořádku, které odkazují na co není v pořádku v clusteru. Pokud upgrade je vrácena zpět z důvodu zdravotních problémů, stav upgradu si pamatuje poslední důvody není v pořádku. Tyto informace mohou správcům pomoci zjistit, co se po vrácení nebo zastavení upgradu stalo.

Podobně během upgradu *aplikace* jsou všechna hodnocení není v pořádku obsažena ve stavu upgradu aplikace.

Následující ukazuje stav upgradu aplikace pro upravenou aplikaci fabric:/WordCount. Hlídací pes ohlásil chybu v jedné z jeho replik. Upgrade se vrací zpět, protože nejsou respektovány kontroly stavu.

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

## <a name="use-health-evaluations-to-troubleshoot"></a>Řešení potíží pomocí hodnocení stavu
Kdykoli dojde k problému s clusterem nebo aplikací, podívejte se na stav clusteru nebo aplikace a určete, co je špatně. Nefunkční hodnocení poskytují podrobnosti o tom, co vyvolalo aktuální stav není v pořádku. Pokud potřebujete, můžete přejít k podrobnostem do nefunkční podřízené entity k identifikaci hlavní příčinu.

Zvažte například aplikace není v pořádku, protože je zpráva o chybě na jedné z jeho replik. Následující rutina prostředí Powershell zobrazuje hodnocení není v pořádku:

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

Můžete se podívat na repliku získat další informace:

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
> Hodnocení není v pořádku zobrazit první důvod, proč je entita vyhodnocena na aktuální stav. Může existovat více dalších událostí, které aktivují tento stav, ale nejsou zohledněny v hodnocení. Chcete-li získat další informace, přejděte k podrobnostem o entitách stavu a zjistěte všechny sestavy není v pořádku v clusteru.
>
>

## <a name="next-steps"></a>Další kroky
[Použití sestav o stavu systému k řešení problémů](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Přidání vlastních sestav stavu service fabric](service-fabric-report-health.md)

[Jak nahlásit a zkontrolovat stav služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Místní monitorování a diagnostika služeb](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)
