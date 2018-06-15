---
title: Azure Service Fabric automatické škálování služby a kontejnery | Microsoft Docs
description: Azure Service Fabric umožňuje nastavit automatické škálování zásady pro služby a kontejnery.
services: service-fabric
documentationcenter: .net
author: radicmilos
manager: ''
editor: nipuzovi
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: cd19c0e51ca1ac7863058d7c3944400719508f9b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213193"
---
# <a name="introduction-to-auto-scaling"></a>Úvod do automatické škálování
Automatické škálování je další schopností Service Fabric dynamicky škálování vašich služeb podle zatížení, které služby jsou vytváření sestav, nebo na základě jejich využití prostředků. Automatické škálování nabízí skvělý pružnost a umožňuje zřizování další instance nebo oddíly služby na vyžádání. Celý automatické škálování proces je automatické a transparentní, a po nastavení zásad na službě, není nutné pro ruční škálování operace na úrovni služby. Automatické škálování může být zapnut buď při vytváření služby, nebo kdykoli při aktualizaci.

Běžný scénář, kde automatického škálování je užitečné, je při zatížení na konkrétní službu liší v čase. Například služby, jako je možné škálovat brány na základě množství prostředků, které jsou nutné ke zpracování příchozích požadavků. Podívejme se na příklad, jak může vypadat tato škálování pravidla:
* Pokud všechny instance Moje brány v průměru používají více než dvě jádra, škálovat službu brány se přidáním jeden další instance. To každou hodinu, ale nikdy celkem mít víc než sedm instancí.
* Pokud všechny instance Moje brány používají v průměru méně než 0,5 jader, škálovat službu v odebráním jednu instanci. To každou hodinu, ale nikdy celkem mít méně než tři instance.

Automatické škálování je podporována pro kontejnery a pravidelné služby Service Fabric. Zbývající část tohoto článku popisuje škálování zásady, způsoby, jak povolit nebo zakázat automatické škálování a o tom, jak pomocí této funkce jsou uvedeny příklady.

## <a name="describing-auto-scaling"></a>Popisující automatické škálování
Automatické škálování zásady lze definovat pro každou službu Cluster Service Fabric. Každá zásada škálování se skládá ze dvou částí:
* **Škálování aktivační událost** popisuje při škálování služby bude provedena. Podmínky, které jsou definovány v aktivační události jsou pravidelně zkontrolována k určení, pokud služba má být nastaveno měřítko nebo ne.

* **Škálování mechanismus** popisuje, jak škálování se provede při své aktivaci. Mechanismus je použít jenom v případě splnění podmínek z aktivační událost.

Všechny aktivační události, které jsou aktuálně podporovány fungovat buď pomocí [logické zatížení metriky](service-fabric-cluster-resource-manager-metrics.md), nebo o fyzické metriky, jako je využití procesoru nebo paměti. V obou případech Service Fabric monitorovat hlášené zatížení pro metriku a vyhodnotí aktivační událost pravidelně k určení nutnosti škálování.

Existují dva mechanismy, které jsou aktuálně podporovány pro automatické škálování. První z nich je určená pro bezstavové služby nebo pro kontejnery, kde automatického škálování se provádí přidáním nebo odebráním [instance](service-fabric-concepts-replica-lifecycle.md). Stavová a Bezstavová služeb, automatické škálování lze také provést přidáním nebo odebráním s názvem [oddíly](service-fabric-concepts-partitioning.md) služby.

> [!NOTE]
> Aktuálně je podpora pouze jeden škálování zásad pro službu.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Aktivační událost průměrnou oddílu zatížení s instancí na základě škálování
První typ aktivační události je založena na zatížení instancí v oddílu bezstavové služby. Metriky zatížení jsou nejprve vyhlazené získat zatížení pro všechny instance oddílu, a potom jsou tyto hodnoty průměrem všechny instance oddílu. Existují tři faktory, které určují, kdy bude škálovat službu:

* _Nižší prahová hodnota načtení_ je hodnota, která určuje, kdy budou službu **škálovat v**. Pokud průměrná zatížení všech instancí oddíly je nižší než tato hodnota, bude v škálovat službu.
* _Prahová hodnota načtení horní_ je hodnota, která určuje, kdy budou službu **škálovat na více systémů**. Pokud průměrná zatížení všech instancí oddílu je nižší než tato hodnota, bude služba škálovat na více systémů.
* _Interval škálování_ Určuje, jak často bude ověřen aktivační událost. Jakmile aktivační události je zaškrtnuto, pokud je potřeba škálování mechanismus, který se použije. Pokud není nutné škálování, bude provedena žádná akce. V obou případech aktivační událost se znovu nekontroluje než interval škálování vyprší platnost znovu.

Této aktivační události lze použít pouze s bezstavové služby (bezstavové kontejnery nebo Service Fabric služby). V případě, když služba má více oddílů, aktivační události je vyhodnocena zvlášť pro každý oddíl a každý oddíl bude mít zadaný mechanismus nezávisle na něho použít. Proto v tomto případě je možné, že některé z oddílů služby bude škálovat na více systémů, některé bude škálovat v a některé nebude škálovat vůbec ve stejnou dobu, podle jejich zatížení.

Pouze mechanismus, který lze použít s této aktivační události je PartitionInstanceCountScaleMechanism. Existují tři faktory, které určují, jak se použije tento mechanismus:
* _Škálování přírůstek_ Určuje, kolik instancí budou přidány nebo odebrány při aktivaci mechanismus.
* _Maximální počet instancí_ definuje horní limit pro škálování. Pokud počet instancí oddílu dosáhne tento limit, nebude možné službu škálovat na více systémů, bez ohledu na zatížení. Je možné vynechat tento limit zadáním hodnoty-1 a v tom případě služba zvětšení nebo zmenšení out co nejvíce (limit je počet uzlů, které jsou k dispozici v clusteru).
* _Minimální počet instancí_ definuje dolní limit pro škálování. Pokud počet instancí oddílu dosáhne tento limit, nebude možné službu škálovat v bez ohledu na zatížení.

## <a name="setting-auto-scaling-policy"></a>Nastavení automatického škálování zásad

### <a name="using-application-manifest"></a>Pomocí manifest aplikace
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Pomocí rozhraní API jazyka C#
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>Pomocí prostředí Powershell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Aktivační událost průměrnou služby zatížení s oddílem na základě škálování
Druhý aktivační události je založena na zatížení všechny oddíly jedna služba. Metriky zatížení jsou nejprve vyhlazené získat zatížení pro každou repliku nebo instance oddílu. Pro stavové služby zatížení oddílu se považuje za zatížení primární replikou, zatímco pro bezstavové služby zatížení oddílu je průměrnou zátěž všech instancí oddílu. Tyto hodnoty jsou průměrem všech oddílů služby a tato hodnota se používá k aktivaci automatické škálování. Stejné jako v předchozích mechanismus, existují tři faktorů, které určení, kdy bude škálovat službu:

* _Nižší prahová hodnota načtení_ je hodnota, která určuje, kdy budou službu **škálovat v**. Pokud průměrná zatížení všech oddílů služby je nižší než tato hodnota, bude v škálovat službu.
* _Prahová hodnota načtení horní_ je hodnota, která určuje, kdy budou službu **škálovat na více systémů**. Pokud průměrná zatížení všech oddílů služby je nižší než tato hodnota, bude služba škálovat na více systémů.
* _Interval škálování_ Určuje, jak často bude ověřen aktivační událost. Jakmile aktivační události je zaškrtnuto, pokud je potřeba škálování mechanismus, který se použije. Pokud není nutné škálování, bude provedena žádná akce. V obou případech aktivační událost se znovu nekontroluje než interval škálování vyprší platnost znovu.

Může být této aktivační události používají službou stavová a Bezstavová. Pouze mechanismus, který lze použít s této aktivační události je AddRemoveIncrementalNamedParitionScalingMechanism. Pokud služba je škálovat na více systémů, pak se přidá nový oddíl, a při změně měřítka služby v jednom ze stávající oddíly se odebere. Existují určitá omezení, která se zkontrolují po vytvoření nebo aktualizaci služby a služby vytvoření nebo aktualizace se nezdaří, pokud nejsou splněné tyto podmínky:
* Schéma oddílu s názvem musí použít pro službu.
* Názvy oddílů musí být po sobě jdoucí celá čísla, třeba "0", "1",...
* První název oddílu musí být "0".

Například pokud služby se nejdřív vytvoří tři oddíly, jedinou platnou možností pro názvy oddílů je "0", "1" a "2".

Skutečné automatické škálování operace, které se provádí respektuje toto schéma pojmenování také:
* Pokud aktuální oddíly služby jsou s názvem "0", "1" a "2", pak oddílu, který bude přidán pro škálování bude mít název "3".
* Pokud aktuální oddíly služby jsou s názvem "0", "1" a "2", je oddílu, který se odeberou pro škálování v oddílu s názvem "2".

Stejné jako u mechanismus, který používá škálování přidáním nebo odebráním instancí, existují tři parametry, které určují, jak se použije tento mechanismus:
* _Škálování přírůstek_ Určuje, kolik oddíly budou přidány nebo odebrány při aktivaci mechanismus.
* _Maximální počet oddílů_ definuje horní limit pro škálování. Pokud počet oddílů služby dosáhne tento limit, nebude možné službu škálovat na více systémů, bez ohledu na zatížení. Je možné vynechat tento limit zadáním hodnoty-1 a v tom případě služba zvětšení nebo zmenšení out co nejvíce (limit je aktuální kapacita clusteru).
* _Minimální počet instancí_ definuje dolní limit pro škálování. Pokud počet oddílů služby dosáhne tento limit, nebude možné službu škálovat v bez ohledu na zatížení.

## <a name="setting-auto-scaling-policy"></a>Nastavení automatického škálování zásad

### <a name="using-application-manifest"></a>Pomocí manifest aplikace
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedParitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Pomocí rozhraní API jazyka C#
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedParitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>Pomocí prostředí Powershell
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedParitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>Automatické škálování podle prostředků

Chcete-li povolit službu sledování prostředků škálovat podle skutečné zdroje

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Existují dvě metriky, které představují skutečné fyzické prostředky. Jeden z nich je servicefabric: / _CpuCores, které představují skutečné využití procesoru (takže 0,5 představuje poloviční jádro) a dalších se servicefabric: / _MemoryInMB, která představuje využití paměti v MB.
ResourceMonitorService zodpovídá za sledování využití procesoru a paměti služby uživatele. Tato služba se uplatní váženého klouzavého průměru Chcete-li účet možných krátkodobou špiček. Sledování prostředků je podporována pro kontejnerizované i kontejnerizované aplikace v systému Windows a pro kontejnerizované ty, které jsou v systému Linux. Automatické škálování na prostředky je povolena pouze pro služby za aktivuje [model procesu výhradní](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Další postup
Další informace o [škálovatelnost aplikace](service-fabric-concepts-scalability.md).