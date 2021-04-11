---
title: Jak zobrazit agregovaný stav entit služby Azure Service Fabric
description: Popisuje postup dotazování, zobrazení a hodnocení agregovaného stavu entit Azure Service Fabric, a to prostřednictvím dotazů na stav a obecných dotazů.
ms.topic: conceptual
ms.date: 2/28/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 261343537b6d18c98c27c9cc9453d2a64f064216
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628063"
---
# <a name="view-service-fabric-health-reports"></a>Zobrazit Service Fabric sestavy o stavu
Azure Service Fabric zavádí [model stavu](service-fabric-health-introduction.md) s entitami o stavu, na kterých mohou systémové komponenty a sledovací zařízení nahlásit místní podmínky, které monitorují. [Health Store](service-fabric-health-introduction.md#health-store) agreguje všechna data o stavu, abyste zjistili, jestli jsou entity v pořádku.

Cluster se vyplní automaticky zprávami o stavu, které odesílají součásti systému. Další informace najdete v tématu [použití sestav stavu systému k řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric poskytuje několik způsobů, jak získat agregovaný stav entit:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) nebo jiné nástroje pro vizualizaci
* Dotazy na stav (prostřednictvím PowerShellu, rozhraní API nebo REST)
* Obecné dotazy, které vracejí seznam entit, které mají stav jako jednu z vlastností (prostřednictvím PowerShellu, rozhraní API nebo REST)

K předvedení těchto možností použijte místní cluster s pěti uzly a [aplikací Fabric:/WORDCOUNT](https://github.com/Azure-Samples/service-fabric-wordcount/raw/master/WordCountV1.sfpkg). Aplikace **Fabric:/WORDCOUNT** obsahuje dvě výchozí služby, stavovou službu typu `WordCountServiceType` a bezstavovou službu typu `WordCountWebServiceType` . Změnil (a) `ApplicationManifest.xml` tak, aby vyžadoval sedm cílových replik pro stavovou službu a jeden oddíl. Vzhledem k tomu, že cluster obsahuje jenom pět uzlů, systémové komponenty nahlásí upozornění na oddíl služby, protože je pod počtem cílů.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Stav v Service Fabric Explorer
Service Fabric Explorer poskytuje vizuální zobrazení clusteru. Na obrázku níže vidíte, že:

* **Prostředky infrastruktury aplikace:/WORDCOUNT** jsou červené (v případě chyby), protože má událost Error hlášenou **MyWatchdog** pro **dostupnost** vlastnosti.
* Jedna ze služeb **Fabric:/WORDCOUNT/WordCountService** je žlutá (v upozornění). Služba je nakonfigurovaná se sedmi replikami a cluster má pět uzlů, takže nejde umístit dvě repliky. I když tady není zobrazený, oddíl služby je žlutý z důvodu systémové sestavy `System.FM` , kterou říká `Partition is below target replica or instance count` . Žlutý oddíl spustí žlutou službu.
* Cluster je červený z důvodu červené aplikace.

Vyhodnocení používá výchozí zásady z manifestu clusteru a manifestu aplikace. Jsou to přísné zásady a Netolerovat žádné chyby.

Zobrazení clusteru s Service Fabric Explorer:

![Zobrazení clusteru s Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Přečtěte si další informace o [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Dotazy na stav
Service Fabric zveřejňuje dotazy na stav pro každý z podporovaných [typů entit](service-fabric-health-introduction.md#health-entities-and-hierarchy). K nim lze přistupovat prostřednictvím rozhraní API pomocí metod na [FabricClient. HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager), rutin PowerShellu a REST. Tyto dotazy vrátí kompletní informace o stavu o entitě: agregovaný stav, události stavu entity, podřízené stavy (Pokud je k dispozici), nestavová hodnocení (Pokud entita není v pořádku) a údaje o podřízeném stavu (Pokud je k dispozici).

> [!NOTE]
> Entita Health se vrátí, když je plně naplněna v Health Store. Entita musí být aktivní (neodstraněno) a mít systémovou sestavu. Jeho nadřazené entity v řetězci hierarchie musí mít také systémové sestavy. Pokud některá z těchto podmínek není splněná, dotazy na stav vrátí [FabricException](/dotnet/api/system.fabric.fabricexception) s [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) `FabricHealthEntityNotFound` , který ukazuje, proč se entita nevrátí.
>
>

Dotazy na stav musí předat identifikátor entity, který závisí na typu entity. Dotazy přijímají parametry volitelné zásady stavu. Nejsou-li zadány žádné zásady stavu, jsou pro vyhodnocení použity [zásady stavu](service-fabric-health-introduction.md#health-policies) z manifestu clusteru nebo aplikace. Pokud manifesty neobsahují definici pro zásady stavu, jsou pro vyhodnocení použity výchozí zásady stavu. Výchozí zásady stavu netolerovány žádné chyby. Dotazy také přijímají filtry pro vracení pouze částečně podřízených objektů nebo událostí – těch, které respektují zadané filtry. Jiný filtr povoluje vyloučení statistik dětí.

> [!NOTE]
> Výstupní filtry se aplikují na straně serveru, takže se zmenší velikost odpovědi zprávy. Doporučujeme použít výstupní filtry k omezení vrácených dat místo použití filtrů na straně klienta.
>
>

Stav entity obsahuje:

* Agregovaný stav entity Vypočítáno Health Store na základě sestav stavu entity, podřízených stavů (Pokud je k dispozici) a zásad stavu. Přečtěte si další informace o [vyhodnocení stavu entity](service-fabric-health-introduction.md#health-evaluation).  
* Události stavu v entitě.
* Kolekce stavů všech podřízených objektů pro entity, které mohou mít podřízené objekty. Stavové stavy obsahují identifikátory entit a agregovaný stav. Chcete-li získat úplný stav pro podřízenou položku, zavolejte stav dotazu pro typ podřízené entity a předejte podřízený identifikátor.
* Bezstavová vyhodnocení, která odkazují na sestavu, která aktivovala stav entity, pokud entita není v pořádku. Vyhodnocení jsou rekurzivní, obsahuje vyhodnocení stavu dětí, které aktivované aktuálním stavem. Sledovací zařízení například oznámilo chybu proti replice. Stav aplikace zobrazuje chybné vyhodnocení z důvodu chybné služby; Služba není v pořádku kvůli chybě v oddílu. oddíl není v pořádku kvůli chybě repliky. replika není v pořádku z důvodu chybové zprávy o stavu sledovacích zařízení.
* Statistika stavu pro všechny podřízené typy entit, které mají podřízené objekty. Například stav clusteru zobrazuje celkový počet aplikací, služeb, oddílů, replik a nasazených entit v clusteru. Stav služby zobrazuje celkový počet oddílů a replik v rámci zadané služby.

## <a name="get-cluster-health"></a>Získat stav clusteru
Vrátí stav entity clusteru a obsahuje stav aplikací a uzlů (podřízené položky clusteru). Vstup:

* Volitelné Zásady stavu clusteru používané k vyhodnocení uzlů a událostí clusteru.
* Volitelné Mapa zásad stavu aplikace s použitím zásad stavu, které slouží k přepsání zásad manifestu aplikace.
* Volitelné Filtry pro události, uzly a aplikace, které určují, které položky jsou zajímavé a které by měly být vráceny ve výsledku (například jenom chyby nebo upozornění a chyby). Všechny události, uzly a aplikace se používají k vyhodnocení agregovaného stavu entit bez ohledu na filtr.
* Volitelné Filtr pro vyloučení statistik stavu
* Volitelné Filtr, který zahrnuje prostředky infrastruktury:/Statistika stavu systému v statistikách stavu. Platí pouze v případě, že nejsou vyloučeny statistiky stavu. Ve výchozím nastavení zahrnují Statistika stavu pouze statistiku pro uživatelské aplikace, nikoli systémovou aplikaci.

### <a name="api"></a>Rozhraní API
Chcete-li získat stav clusteru, vytvořte `FabricClient` a zavolejte metodu [GetClusterHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) na své **HealthManager**.

Stav clusteru získá následující volání:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Následující kód získá stav clusteru pomocí vlastní zásady stavu clusteru a filtry pro uzly a aplikace. Určuje, že statistiky stavu zahrnují statistiku prostředků infrastruktury:/systém. Vytvoří [ClusterHealthQueryDescription](/dotnet/api/system.fabric.description.clusterhealthquerydescription), který obsahuje vstupní informace.

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
Rutina pro získání stavu clusteru je [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth). Nejdřív se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Stav clusteru je pět uzlů, systémová aplikace a Fabric:/WordCount nakonfigurovaný tak, jak je popsáno.

Následující rutina Získá stav clusteru pomocí výchozích zásad stavu. Agregovaný stav je upozornění, protože aplikace Fabric:/WordCount je v upozornění. Všimněte si, jak nestavová Hodnocení poskytují podrobnosti o podmínkách, které aktivovaly agregovaný stav.

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

Následující rutina PowerShellu načte stav clusteru pomocí vlastní zásady použití. Vyfiltruje výsledky, aby se v chybách nebo varování dostaly jenom aplikace a uzly. V důsledku toho nejsou vráceny žádné uzly, protože všechny jsou v pořádku. Filtr aplikací respektuje pouze aplikace Fabric:/WordCount. Vzhledem k tomu, že vlastní zásady určují, že se pro aplikaci Fabric:/WordCount považují upozornění jako chyby, aplikace se vyhodnotí jako chyba, a proto je cluster.

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
Stav clusteru můžete získat pomocí žádosti o [získání](/rest/api/servicefabric/get-the-health-of-a-cluster) nebo [požadavku POST](/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) , který obsahuje zásady stavu popsané v těle.

## <a name="get-node-health"></a>Získat stav uzlu
Vrátí stav entity uzlu a obsahuje události stavu hlášené v uzlu. Vstup:

* Požadovanou Název uzlu, který identifikuje uzel.
* Volitelné Nastavení zásad stavu clusteru používané k vyhodnocení stavu.
* Volitelné Filtry pro události, které určují, které položky jsou zajímavé a měly by být vráceny ve výsledku (například jenom chyby nebo upozornění a chyby). Všechny události se používají k vyhodnocení agregovaného stavu entit bez ohledu na filtr.

### <a name="api"></a>Rozhraní API
Chcete-li získat stav uzlu prostřednictvím rozhraní API, vytvořte `FabricClient` a zavolejte metodu [GetNodeHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) na své HealthManager.

Následující kód získá stav uzlu pro zadaný název uzlu:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Následující kód získá stav uzlu pro zadaný název uzlu a projde do filtru události a vlastní zásady prostřednictvím [NodeHealthQueryDescription](/dotnet/api/system.fabric.description.nodehealthquerydescription):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Rutina pro získání stavu uzlu je [Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth). Nejdřív se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .
Následující rutina Získá stav uzlu pomocí výchozích zásad stavu:

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
Můžete získat stav uzlu pomocí [žádosti Get](/rest/api/servicefabric/get-the-health-of-a-node) nebo [požadavku POST](/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) , který obsahuje zásady stavu popsané v těle.

## <a name="get-application-health"></a>Získat stav aplikace
Vrátí stav entity aplikace. Obsahuje stav nasazené aplikace a podřízených služeb. Vstup:

* Požadovanou Název aplikace (URI), který identifikuje aplikaci.
* Volitelné Zásady stavu aplikace použité k přepsání zásad manifestu aplikace.
* Volitelné Filtry pro události, služby a nasazené aplikace, které určují, které položky jsou zajímavé a které by měly být vráceny ve výsledku (například jenom chyby nebo upozornění a chyby). Všechny události, služby a nasazené aplikace se používají k vyhodnocení agregovaného stavu entit bez ohledu na filtr.
* Volitelné Filtr pro vyloučení statistik stavu. Pokud tento parametr nezadáte, zahrnují statistiky stavu OK, upozornění a počet chyb pro všechny podřízené položky aplikace: služby, oddíly, repliky, nasazené aplikace a nasazené balíčky služeb.

### <a name="api"></a>Rozhraní API
Chcete-li získat stav aplikace, vytvořte `FabricClient` a zavolejte metodu [GetApplicationHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) na své HealthManager.

Následující kód získá stav aplikace pro zadaný název aplikace (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Následující kód získá stav aplikace pro zadaný název aplikace (URI) s filtry a vlastními zásadami určenými prostřednictvím [ApplicationHealthQueryDescription](/dotnet/api/system.fabric.description.applicationhealthquerydescription).

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
Rutina pro získání stavu aplikace je [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth). Nejdřív se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Následující rutina vrátí stav aplikace **Fabric:/WORDCOUNT** :

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

Následující rutina prostředí PowerShell projde vlastními zásadami. Také filtruje podřízené položky a události.

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
Můžete získat stav aplikace pomocí žádosti o [získání](/rest/api/servicefabric/get-the-health-of-an-application) nebo [požadavku POST](/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) , který obsahuje zásady stavu popsané v těle.

## <a name="get-service-health"></a>Získat stav služby
Vrátí stav entity služby. Obsahuje stav oddílu. Vstup:

* Požadovanou Název služby (URI) identifikující službu.
* Volitelné Zásady stavu aplikace použité k přepsání zásad manifestu aplikace.
* Volitelné Filtry pro události a oddíly, které určují, které položky jsou zajímavé a které by měly být vráceny ve výsledku (například jenom chyby nebo upozornění a chyby). Všechny události a oddíly slouží k vyhodnocení agregovaného stavu entit bez ohledu na filtr.
* Volitelné Filtr pro vyloučení statistik stavu Pokud tento parametr nezadáte, zobrazí se v statistikách o stavu hodnota OK, upozornění a počet chyb pro všechny oddíly a repliky služby.

### <a name="api"></a>Rozhraní API
Chcete-li získat stav služby prostřednictvím rozhraní API, vytvořte `FabricClient` a zavolejte metodu [GetServiceHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) na své HealthManager.

Následující příklad načte stav služby se zadaným názvem služby (URI):

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Následující kód získá stav služby pro zadaný název služby (URI), zadáním filtrů a vlastních zásad prostřednictvím [ServiceHealthQueryDescription](/dotnet/api/system.fabric.description.servicehealthquerydescription):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Rutina pro získání stavu služby je [Get-ServiceFabricServiceHealth](/powershell/module/servicefabric/get-servicefabricservicehealth). Nejdřív se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Následující rutina Získá stav služby pomocí výchozích zásad stavu:

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
Můžete získat stav služby pomocí žádosti o [získání](/rest/api/servicefabric/get-the-health-of-a-service) nebo [požadavku POST](/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) , který obsahuje zásady stavu popsané v těle.

## <a name="get-partition-health"></a>Získat stav oddílu
Vrátí stav entity oddílu. Obsahuje stav repliky. Vstup:

* Požadovanou IDENTIFIKÁTOR oddílu (GUID), který identifikuje oddíl.
* Volitelné Zásady stavu aplikace použité k přepsání zásad manifestu aplikace.
* Volitelné Filtry pro události a repliky, které určují, které položky jsou zajímavé a které by měly být vráceny ve výsledku (například jenom chyby nebo upozornění a chyby). Všechny události a repliky se používají k vyhodnocení agregovaného stavu entit bez ohledu na filtr.
* Volitelné Filtr pro vyloučení statistik stavu Pokud tento parametr nezadáte, zobrazí se v statistikách o stavu počet replik, které jsou v pořádku, upozornění a chybové stavy.

### <a name="api"></a>Rozhraní API
Chcete-li získat stav oddílu přes rozhraní API, vytvořte `FabricClient` a zavolejte metodu [GetPartitionHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) na své HealthManager. Chcete-li zadat volitelné parametry, vytvořte [PartitionHealthQueryDescription](/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
Rutina pro získání stavu oddílu je [Get-ServiceFabricPartitionHealth](/powershell/module/servicefabric/get-servicefabricpartitionhealth). Nejdřív se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Následující rutina Získá stav pro všechny oddíly služby **Fabric:/WORDCOUNT/WordCountService** a vyfiltruje stav repliky:

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
Můžete získat stav oddílů pomocí [žádosti Get](/rest/api/servicefabric/sfclient-api-getpartitionhealth) nebo [požadavku POST](/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) , který obsahuje zásady stavu popsané v těle.

## <a name="get-replica-health"></a>Získat stav repliky
Vrátí stav repliky stavové služby nebo instance bezstavové služby. Vstup:

* Požadovanou ID oddílu (GUID) a ID repliky, které identifikují repliku.
* Volitelné Parametry zásad stavu aplikace použité k přepsání zásad manifestu aplikace.
* Volitelné Filtry pro události, které určují, které položky jsou zajímavé a měly by být vráceny ve výsledku (například jenom chyby nebo upozornění a chyby). Všechny události se používají k vyhodnocení agregovaného stavu entit bez ohledu na filtr.

### <a name="api"></a>Rozhraní API
Chcete-li získat stav repliky prostřednictvím rozhraní API, vytvořte `FabricClient` a zavolejte metodu [GetReplicaHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) na své HealthManager. Chcete-li zadat upřesňující parametry, použijte [ReplicaHealthQueryDescription](/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
Rutina pro získání stavu repliky je [Get-ServiceFabricReplicaHealth](/powershell/module/servicefabric/get-servicefabricreplicahealth). Nejdřív se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

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
Stav repliky můžete získat pomocí [žádosti o získání](/rest/api/servicefabric/get-the-health-of-a-replica) nebo [požadavku POST](/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) , který obsahuje zásady stavu popsané v těle.

## <a name="get-deployed-application-health"></a>Získat stav nasazené aplikace
Vrátí stav aplikace nasazené v entitě uzlu. Obsahuje stav nasazených balíčků služeb. Vstup:

* Požadovanou Název aplikace (URI) a název uzlu (řetězec) identifikující nasazenou aplikaci.
* Volitelné Zásady stavu aplikace použité k přepsání zásad manifestu aplikace.
* Volitelné Filtry pro události a nasazené balíčky služeb, které určují, které položky jsou zajímavé a které by měly být vráceny ve výsledku (například jenom chyby nebo upozornění a chyby). Všechny události a nasazené balíčky služby se používají k vyhodnocení agregovaného stavu entit bez ohledu na filtr.
* Volitelné Filtr pro vyloučení statistik stavu Pokud není zadaný, Statistika stavu zobrazuje počet nasazených balíčků služeb v stavech OK, varování a chyba.

### <a name="api"></a>Rozhraní API
Chcete-li získat stav aplikace nasazené na uzlu prostřednictvím rozhraní API, vytvořte `FabricClient` a zavolejte metodu [GetDeployedApplicationHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) na své HealthManager. Chcete-li zadat volitelné parametry, použijte [DeployedApplicationHealthQueryDescription](/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
Rutina pro získání stavu nasazené aplikace je [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth). Nejdřív se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) . Chcete-li zjistit, kde je aplikace nasazena, spusťte příkaz [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth) a podívejte se na podřízené objekty nasazené aplikace.

Následující rutina Získá stav aplikace **Fabric:/WORDCOUNT** nasazené na **_Node_2**.

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
Můžete získat stav nasazené aplikace pomocí [žádosti o získání](/rest/api/servicefabric/get-the-health-of-a-deployed-application) nebo [požadavku POST](/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) , který obsahuje zásady stavu popsané v těle.

## <a name="get-deployed-service-package-health"></a>Získat stav nasazeného balíčku služby
Vrátí stav nasazené entity balíčku služby. Vstup:

* Požadovanou Název aplikace (URI), název uzlu (řetězec) a název manifestu služby (řetězec) identifikující nasazený balíček služby.
* Volitelné Zásady stavu aplikace použité k přepsání zásad manifestu aplikace.
* Volitelné Filtry pro události, které určují, které položky jsou zajímavé a měly by být vráceny ve výsledku (například jenom chyby nebo upozornění a chyby). Všechny události se používají k vyhodnocení agregovaného stavu entit bez ohledu na filtr.

### <a name="api"></a>Rozhraní API
Chcete-li získat stav nasazeného balíčku služby prostřednictvím rozhraní API, vytvořte `FabricClient` a zavolejte metodu [GetDeployedServicePackageHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) na své HealthManager. Chcete-li zadat volitelné parametry, použijte [DeployedServicePackageHealthQueryDescription](/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Rutina pro získání stavu balíčku nasazené služby je [Get-ServiceFabricDeployedServicePackageHealth](/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Nejdřív se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) . Chcete-li zjistit, kde je aplikace nasazena, spusťte příkaz [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth) a podívejte se na nasazené aplikace. Pokud chcete zjistit, které balíčky služeb jsou v aplikaci, podívejte se na nasazené podřízené balíčky služby ve výstupu [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth) .

Následující rutina načte stav balíčku služby **WordCountServicePkg** v aplikaci **Fabric:/WORDCOUNT** nasazené na **_Node_2**. Entita má **systém. hostování** sestav pro úspěšnou aktivaci Service-Package a vstupní bod a úspěšnou registraci typu služby.

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
Nasazený stav balíčku služby můžete získat pomocí [žádosti Get](/rest/api/servicefabric/get-the-health-of-a-service-package) nebo [požadavku POST](/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) , který obsahuje zásady stavu popsané v těle.

## <a name="health-chunk-queries"></a>Dotazy na blok dat stavu
Dotazy na blok dat stavu můžou vracet podřízené objekty clusteru na více úrovních (rekurzivně) na vstupní filtry. Podporuje pokročilé filtry, které umožňují značnou flexibilitu při volbě podřízených objektů, které se mají vrátit. Filtry mohou určovat podřízené položky pomocí jedinečného identifikátoru nebo jiných identifikátorů skupin nebo stavů. Ve výchozím nastavení nejsou k dispozici žádné podřízené položky, nikoli příkazy stavu, které vždy obsahují podřízené položky první úrovně.

[Dotazy na stav](service-fabric-view-entities-aggregated-health.md#health-queries) vrátí pouze podřízené objekty první úrovně zadané entity na požadované filtry. Chcete-li získat podřízené položky podřízených objektů, je nutné volat další rozhraní API pro každou entitu zájmu. Podobně pokud chcete získat stav konkrétních entit, je nutné pro každou požadovanou entitu volat jedno rozhraní API pro zjištění stavu. Rozšířené filtrování dotazů na blok dat umožňuje vyžádat si více položek zájmu v jednom dotazu a minimalizovat velikost zprávy a počet zpráv.

Hodnota dotazu bloku dat znamená, že můžete získat stav pro více entit clusteru (potenciálně všechny entity clusteru začínající v požadovaném kořenu) v jednom volání. Můžete vyjádřit složitý dotaz na stav, jako je:

* Vrátí jenom aplikace v chybách a pro tyto aplikace zahrnuje všechny služby v upozornění nebo chyba. V případě vrácených služeb Zahrňte všechny oddíly.
* Vrátí pouze stav čtyř aplikací určených podle jejich názvů.
* Vrátí pouze stav aplikací požadovaného typu aplikace.
* Vrátí všechny nasazené entity na uzlu. Vrátí všechny aplikace, všechny nasazené aplikace v zadaném uzlu a všechny nasazené balíčky služeb v tomto uzlu.
* Vrátí všechny repliky s chybou. Vrátí všechny aplikace, služby, oddíly a jenom repliky s chybou.
* Vrátí všechny aplikace. Pro zadanou službu uveďte všechny oddíly.

V současné době se dotaz na blok dat stavu zveřejňuje jenom pro entitu clusteru. Vrátí blok stavu clusteru, který obsahuje:

* Stav agregovaného stavu clusteru.
* Seznam bloků stavu uzlů, které se týkají vstupních filtrů.
* Seznam bloků dat o stavu aplikací, které dotýkají vstupní filtry. Každý blok stavu aplikace obsahuje seznam bloků dat se všemi službami, které respektují vstupní filtry a seznam bloků dat se všemi nasazenými aplikacemi, které respektují filtry. Stejné pro podřízené položky služeb a nasazených aplikací. Tímto způsobem mohou být všechny entity v clusteru potenciálně vráceny, pokud jsou požadovány, hierarchicky způsobem.

### <a name="cluster-health-chunk-query"></a>Dotaz na stavový blok clusteru
Vrátí stav entity clusteru a obsahuje hierarchické bloky stavu požadovaných podřízených objektů. Vstup:

* Volitelné Zásady stavu clusteru používané k vyhodnocení uzlů a událostí clusteru.
* Volitelné Mapa zásad stavu aplikace s použitím zásad stavu, které slouží k přepsání zásad manifestu aplikace.
* Volitelné Filtry pro uzly a aplikace, které určují, které položky jsou zajímavé a které by měly být vráceny ve výsledku. Filtry jsou specifické pro entitu nebo skupinu entit nebo platí pro všechny entity na této úrovni. Seznam filtrů může obsahovat jeden obecný filtr nebo filtry pro konkrétní identifikátory pro jemně odstupňované entity vrácené dotazem. Pokud je toto pole prázdné, podřízené položky se ve výchozím nastavení nevrátí.
  Přečtěte si další informace o filtrech na [NodeHealthStateFilter](/dotnet/api/system.fabric.health.nodehealthstatefilter) a [ApplicationHealthStateFilter](/dotnet/api/system.fabric.health.applicationhealthstatefilter). Filtry aplikace můžou rekurzivně zadat rozšířené filtry pro podřízené objekty.

Výsledek bloku dat zahrnuje podřízené objekty, které jsou v souladu s filtry.

V současné době dotaz na blok dat nevrací hodnocení ve špatném stavu ani události entit. Tyto další informace lze získat pomocí stávajícího dotazu na stav clusteru.

### <a name="api"></a>Rozhraní API
Chcete-li získat blok stavu clusteru, vytvořte `FabricClient` a zavolejte metodu [GetClusterHealthChunkAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) na své **HealthManager**. Můžete předat [ClusterHealthQueryDescription](/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) a popsat zásady stavu a rozšířené filtry.

Následující kód Získá blok stavu clusteru s rozšířenými filtry.

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
Rutina pro získání stavu clusteru je [Get-ServiceFabricClusterChunkHealth](/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Nejdřív se připojte ke clusteru pomocí rutiny [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .

Následující kód získá uzly pouze v případě chyby s výjimkou konkrétního uzlu, který by měl být vždy vrácen.

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

Následující rutina Získá blok dat clusteru s filtry aplikace.

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
Blok stavu clusteru můžete získat pomocí žádosti o [získání](/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) nebo [požadavku POST](/rest/api/servicefabric/health-of-cluster) , který obsahuje zásady stavu a rozšířené filtry popsané v těle.

## <a name="general-queries"></a>Obecné dotazy
Obecné dotazy vrátí seznam Service Fabric entit zadaného typu. Jsou přístupné prostřednictvím rozhraní API (prostřednictvím metod na **FabricClient. QueryManager**), rutin PowerShellu a REST. Tyto dotazy agregují poddotazy z více komponent. Jedním z nich je [Health Store](service-fabric-health-introduction.md#health-store), který naplňuje agregovaný stav pro každý výsledek dotazu.  

> [!NOTE]
> Obecné dotazy vrátí agregovaný stav entity a neobsahují rozsáhlá data o stavu. Pokud entita není v pořádku, můžete s dotazy na stav získat informace o všech svých informacích o stavu, včetně událostí, podřízených stavů a hodnocení, která nejsou v pořádku.
>
>

Pokud obecné dotazy vrátí Neznámý stav pro entitu, je možné, že Health Store nemá úplná data o entitě. Je také možné, že poddotaz na Health Store nebyl úspěšný (například došlo k chybě komunikace nebo Health Store omezil). Sledujte dotaz na stav pro entitu. Pokud v poddotazu došlo k přechodným chybám, například k problémům se sítí, může tento následný dotaz úspěšně probíhat. Může vám taky poskytnout více podrobností z Health Store o tom, proč se entita nezveřejňuje.

Dotazy, které obsahují stav **elementu** pro entity, jsou:

* Seznam uzlů: vrátí seznam uzlů v clusteru (stránkovaného).
  * Rozhraní API: [FabricClient. QueryClient. GetNodeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Seznam aplikací: vrátí seznam aplikací v clusteru (stránkovaného).
  * Rozhraní API: [FabricClient. QueryClient. GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Seznam služeb: vrátí seznam služeb v aplikaci (stránkované).
  * Rozhraní API: [FabricClient. QueryClient. GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Seznam oddílů: vrátí seznam oddílů ve službě (stránkované).
  * Rozhraní API: [FabricClient. QueryClient. GetPartitionListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Seznam replik: vrátí seznam replik v oddílu (stránkovaného).
  * Rozhraní API: [FabricClient. QueryClient. GetReplicaListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Seznam nasazených aplikací: vrátí seznam nasazených aplikací na uzlu.
  * Rozhraní API: [FabricClient. QueryClient. GetDeployedApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Seznam nasazených balíčků služeb: vrátí seznam balíčků služeb v nasazené aplikaci.
  * Rozhraní API: [FabricClient. QueryClient. GetDeployedServicePackageListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Některé dotazy vracejí stránkované výsledky. Vrácení těchto dotazů je seznam odvozený od [PagedList \<T> ](/dotnet/api/system.fabric.query.pagedlist-1). Pokud se výsledky nevejdou do zprávy, vrátí se jenom stránka a token continuationtoken, který sleduje, kde se výčet zastavil. Pokračujte v volání stejného dotazu a předejte token pokračování z předchozího dotazu, aby se zobrazily další výsledky.

### <a name="examples"></a>Příklady
Následující kód získá v clusteru poškozené aplikace:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Následující rutina Získá podrobnosti o aplikaci pro aplikaci Fabric:/WordCount. Všimněte si, že stav je upozornění.

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

Následující rutina vrátí služby se stavem Chyba:

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

## <a name="cluster-and-application-upgrades"></a>Upgrady clusterů a aplikací
Během monitorovaného upgradu clusteru a aplikace Service Fabric kontroluje stav, aby se zajistilo, že vše zůstane v dobrém stavu. Pokud je entita ve špatném stavu jako vyhodnocená pomocí nakonfigurovaných zásad stavu, upgrade použije zásady pro konkrétní upgrade k určení další akce. Upgrade může být pozastaven, aby umožňoval interakci s uživatelem (například opravit chybové stavy nebo měnit zásady), nebo se může automaticky vrátit k předchozí správné verzi.

Během upgradu *clusteru* můžete získat stav upgradu clusteru. Stav upgradu zahrnuje hodnocení ve špatném stavu, které odkazuje na to, co v clusteru není v pořádku. Pokud se upgrade vrátí zpátky z důvodu problémů se stavem, stav upgradu přepamatuje poslední příčiny, které nejsou v pořádku. Tyto informace můžou správcům pomáhat prozkoumat, co se nepovedlo po odvolání nebo zastavení upgradu.

Podobně během upgradu *aplikace* jsou ve stavu upgradu aplikace obsaženy všechna Bezstavová hodnocení.

Následující příklad ukazuje stav upgradu aplikace pro upravenou aplikaci Fabric:/WordCount. Sledovací zařízení oznámilo chybu v jedné z jeho replik. Upgrade se vrátí zpátky, protože se nerespektují kontroly stavu.

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

Přečtěte si další informace o [upgradu aplikace Service Fabric](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Řešení potíží pomocí hodnocení stavu
Pokaždé, když dojde k potížím s clusterem nebo aplikací, podívejte se na stav clusteru nebo aplikace a popište, co je chybné. Bezstavová Hodnocení poskytují podrobné informace o tom, co aktivovalo aktuální stav není v pořádku. Pokud potřebujete, můžete přejít k podrobnostem o nefunkčních podřízených entitách a identifikovat tak původní příčinu.

Představte si například, že aplikace není v pořádku, protože na jedné z jejích replik se nachází zpráva o chybě. Následující rutina PowerShellu zobrazuje hodnocení, která nejsou v pořádku:

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

Pokud chcete získat další informace, můžete se podívat na repliku:

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
> Hodnocení, která nejsou v pořádku, ukazují první důvod, proč je entita vyhodnocena na aktuální stav. Tento stav může aktivovat několik dalších událostí, ale v hodnocení se neprojeví. Chcete-li získat další informace, přejděte k podrobnostem o entitách stavu a zjistěte všechny sestavy, které nejsou v pořádku v clusteru.
>
>

## <a name="next-steps"></a>Další kroky
[Použití sestav o stavu systému k řešení problémů](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Přidat vlastní sestavy o stavu Service Fabric](service-fabric-report-health.md)

[Postup hlášení a kontroly stavu služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Místní monitorování a diagnostika služeb](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)
