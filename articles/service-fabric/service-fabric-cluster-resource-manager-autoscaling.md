---
title: Služby a kontejnery automatického škálování pro Azure Service Fabric
description: Azure Service Fabric umožňuje nastavit zásady automatického škálování pro služby a kontejnery.
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.custom: devx-track-csharp
ms.openlocfilehash: 060bb9dcdd504846c76ab4c782b2857fdddfa394
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91354798"
---
# <a name="introduction-to-auto-scaling"></a>Úvod do automatického škálování
Automatické škálování je další možností Service Fabric dynamicky škálovat vaše služby na základě zatížení, které služby hlásí, nebo na základě využití prostředků. Automatické škálování dává velkou flexibilitu a umožňuje zřídit další instance nebo oddíly vaší služby na vyžádání. Celý proces automatického škálování je automatizovaný a transparentní a po nastavení zásad na službě není potřeba provádět operace ručního škálování na úrovni služby. Automatické škálování je možné zapnout buď při vytvoření služby, nebo kdykoli pomocí aktualizace služby.

Běžným scénářem, kdy je vhodné automatické škálování, je, když se zatížení konkrétní služby v průběhu času mění. Například služba, jako je brána, se může škálovat na základě množství prostředků potřebných pro zpracování příchozích požadavků. Pojďme se podívat na příklad toho, co pravidla škálování můžou vypadat takto:
* Pokud všechny instance mých bran v průměru používají více než dvě jádra, můžete službu brány škálovat tak, že přidáte ještě jednu instanci. Proveďte tuto akci každou hodinu, ale nikdy nepoužívejte celkem více než sedm instancí.
* Pokud všechny instance mých bran využívají méně než 0,5 jader v průměru, pak službu Škálujte tak, že odeberete jednu instanci. Proveďte tuto akci každou hodinu, ale nikdy nemají celkem méně než tři instance.

Automatické škálování je podporováno pro kontejnery i pro běžné Service Fabric služby. Aby bylo možné používat automatické škálování, je třeba spustit na verzi 6,2 nebo vyšší Service Fabric modulu runtime. 

Zbývající část tohoto článku popisuje zásady škálování, způsoby, jak povolit nebo zakázat automatické škálování, a obsahuje příklady použití této funkce.

## <a name="describing-auto-scaling"></a>Popisující automatické škálování
Zásady automatického škálování je možné definovat pro každou službu v clusteru Service Fabric. Každá zásada škálování se skládá ze dvou částí:
* **Aktivační událost škálování** popisuje, kdy se bude provádět škálování služby. Podmínky definované v triggeru se pravidelně kontrolují, aby se zjistilo, jestli se má služba škálovat nebo ne.

* **Mechanismus škálování** popisuje, jak se při aktivaci provede škálování. Mechanismus se aplikuje jenom v případě, že jsou splněné podmínky triggeru.

Všechny aktivační události, které aktuálně podporují práci s [logickými metrikami zatížení](service-fabric-cluster-resource-manager-metrics.md), nebo s fyzickými metrikami, jako je využití CPU nebo paměti. V obou případech Service Fabric monitoruje nahlášené zatížení pro danou metriku a vyhodnotí Trigger pravidelně, aby bylo možné určit, jestli je potřeba škálování potřebovat.

Existují dva mechanismy, které jsou aktuálně podporovány pro automatické škálování. První z nich je určena pro bezstavové služby nebo pro kontejnery, kde se automatické škálování provádí přidáním nebo odebráním [instancí](service-fabric-concepts-replica-lifecycle.md). U stavových i bezstavových služeb je možné automatické škálování také provést přidáním nebo odebráním pojmenovaných [oddílů](service-fabric-concepts-partitioning.md) služby.

> [!NOTE]
> V současné době podporuje jenom jednu zásadu škálování na službu a jenom jednu aktivační událost škálování podle zásad škálování.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Průměrná zátěžová aktivační událost oddílu s škálováním založeným na instancích
První typ triggeru vychází z zatížení instancí v oddílu bezstavové služby. Zatížení metriky jsou nejprve vyhlazené, aby získaly zatížení pro všechny instance oddílu, a tyto hodnoty jsou průměrně rozloženy mezi všechny instance oddílu. Existují tři faktory, které určují, kdy se má služba škálovat:

* _Nižší prahová hodnota zatížení_ je hodnota, která určuje, kdy se má služba **škálovat**. Pokud je průměrné zatížení všech instancí oddílů nižší než tato hodnota, služba se škáluje.
* _Horní prahová hodnota zatížení_ je hodnota, která určuje, kdy se má služba **škálovat**. Pokud je průměrné zatížení všech instancí oddílu vyšší než tato hodnota, služba se škáluje.
* _Interval škálování_ určuje, jak často se bude aktivační událost kontrolovat. V případě, že je aktivační událost zaškrtnuta, bude použit mechanismus škálování. Pokud se škálování nepotřebuje, neprovede se žádná akce. V obou případech se Trigger nevrátí znovu, než vyprší platnost intervalu škálování.

Tato aktivační událost se dá použít jenom u bezstavových služeb (buď nestavové kontejnery, nebo Service Fabric služby). V případě, že má služba více oddílů, vyhodnotí se Trigger pro každý oddíl samostatně a na každý oddíl se použije zadaný mechanismus nezávisle na sobě. V takovém případě je možné, že některé oddíly služby budou škálované, a některé z nich se škálují ve stejnou dobu, a to na základě jejich zatížení.

Jediným mechanismem, který lze použít s touto triggerem, je PartitionInstanceCountScaleMechanism. Existují tři faktory, které určují, jak se tento mechanismus používá:
* _Přírůstek měřítka_ určuje, kolik instancí bude přidáno nebo odebráno při aktivaci mechanismu.
* Hodnota _maximální počet instancí_ definuje horní limit pro škálování. Pokud počet instancí oddílu dosáhne tohoto limitu, nebude služba škálovat bez ohledu na zatížení. Tento limit je možné vynechat zadáním hodnoty-1. v takovém případě se služba bude škálovat co nejvíc (limit je počet uzlů, které jsou v clusteru k dispozici).
* _Minimální počet instancí_ definuje dolní limit pro škálování. Pokud počet instancí oddílu dosáhne tohoto limitu, služba nebude škálovat bez ohledu na zatížení.

## <a name="setting-auto-scaling-policy-for-instance-based-scaling"></a>Nastavení zásad automatického škálování pro škálování na základě instancí

### <a name="using-application-manifest"></a>Použití manifestu aplikace
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
### <a name="using-c-apis"></a>Použití rozhraní API jazyka C#
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
### <a name="using-powershell"></a>Použití PowerShellu
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

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Průměrná zátěžová aktivační událost služby s škálováním založeným na oddílech
Druhá aktivační událost vychází z zatížení všech oddílů jedné služby. Zatížení metriky se nejprve vyhlazuje, aby se získalo zatížení pro všechny repliky nebo instance oddílu. U stavových služeb se zatížení oddílu považuje za zatížení primární repliky, zatímco u bezstavových služeb je zatížení oddílu průměrnou zátěží všech instancí oddílu. Tyto hodnoty jsou v průměru na všech oddílech služby a tato hodnota se používá k aktivaci automatického škálování. Stejné jako v předchozím mechanismu, existují tři faktory, které určují, kdy se má služba škálovat:

* _Nižší prahová hodnota zatížení_ je hodnota, která určuje, kdy se má služba **škálovat**. Pokud je průměrné zatížení všech oddílů služby nižší než tato hodnota, služba se škáluje.
* _Horní prahová hodnota zatížení_ je hodnota, která určuje, kdy se má služba **škálovat**. Pokud je průměrné zatížení všech oddílů služby vyšší než tato hodnota, služba se škáluje.
* _Interval škálování_ určuje, jak často se bude aktivační událost kontrolovat. V případě, že je aktivační událost zaškrtnuta, bude použit mechanismus škálování. Pokud se škálování nepotřebuje, neprovede se žádná akce. V obou případech se Trigger nevrátí znovu, než vyprší platnost intervalu škálování.

Tato aktivační událost se dá použít se stavovou a bezstavovou službou. Jediným mechanismem, který lze použít s touto triggerem, je AddRemoveIncrementalNamedPartitionScalingMechanism. Při horizontálním navýšení kapacity služby se přidá nový oddíl a když se služba škáluje v jednom z existujících oddílů, odeberou se. Existují omezení, která budou kontrolována při vytvoření nebo aktualizaci služby a vytvoření nebo aktualizace služby selže, pokud nejsou splněny tyto podmínky:
* Pro službu se musí použít schéma pojmenovaného oddílu.
* Názvy oddílů musí být po sobě jdoucí celočíselná čísla, například 0, 1,...
* Název prvního oddílu musí být "0".

Pokud je například služba zpočátku vytvořená se třemi oddíly, jedinou platnou možností pro názvy oddílů je "0", "1" a "2".

Skutečná prováděná operace automatického škálování bude brát ohled i na toto schéma pojmenování:
* Pokud jsou aktuální oddíly služby pojmenované "0", "1" a "2", pak oddíl, který bude přidán pro horizontální navýšení kapacity, bude nazván "3".
* Pokud jsou aktuální oddíly služby pojmenované "0", "1" a "2", pak oddíl, který bude odstraněn pro škálování v, je oddíl s názvem "2".

Stejné jako u mechanismu, který používá škálování přidáváním nebo odebíráním instancí, existují tři parametry, které určují, jak se tento mechanismus používá:
* _Přírůstek měřítka_ určuje, kolik oddílů bude přidáno nebo odebráno při aktivaci mechanismu.
* _Maximální počet oddílů_ definuje horní limit pro škálování. Pokud počet oddílů služby dosáhne tohoto limitu, nebude služba škálovat bez ohledu na zatížení. Tento limit je možné vynechat zadáním hodnoty-1. v takovém případě se služba bude škálovat co nejvíc (limit je skutečná kapacita clusteru).
* _Minimální počet instancí_ definuje dolní limit pro škálování. Pokud počet oddílů služby dosáhne tohoto limitu, služba nebude škálovat bez ohledu na zatížení.

> [!WARNING] 
> Pokud se pro stavové služby používá AddRemoveIncrementalNamedPartitionScalingMechanism, Service Fabric bude přidávat nebo odebírat oddíly **bez oznámení nebo upozornění**. Při aktivaci mechanismu škálování se neprovede změna rozdělení dat do oddílů. V případě operace horizontálního navýšení kapacity budou nové oddíly prázdné a v případě škálování v provozu **bude oddíl odstraněn společně se všemi daty, která obsahuje**.

## <a name="setting-auto-scaling-policy-for-partition-based-scaling"></a>Nastavení zásad automatického škálování pro škálování na základě oddílů

### <a name="using-application-manifest"></a>Použití manifestu aplikace
``` xml
<NamedPartition>
    <Partition Name="0" />
</NamedPartition>
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>Použití rozhraní API jazyka C#
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
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
### <a name="using-powershell"></a>Použití PowerShellu
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
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

## <a name="auto-scaling-based-on-resources"></a>Automatické škálování na základě prostředků

Aby bylo možné povolit škálování služby monitorování prostředků na základě skutečných prostředků

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
K dispozici jsou dvě metriky, které představují skutečné fyzické prostředky. Jedním z nich je servicefabric:/_CpuCores, který představuje skutečné využití procesoru (takže 0,5 představuje polovinu jádra) a druhý servicefabric:/_MemoryInMB, který představuje využití paměti v MB.
ResourceMonitorService zodpovídá za sledování využití procesoru a paměti u uživatelských služeb. Tato služba bude uplatňovat vážený klouzavý průměr, aby se zohlednily možné krátkodobé špičky. Monitorování prostředků je podporováno pro kontejnerové i nekontejnerové aplikace ve Windows a pro kontejnery na platformě Linux. Automatické škálování u prostředků je povolené jenom pro služby aktivované ve [výhradním modelu procesu](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [škálovatelnosti aplikace](service-fabric-concepts-scalability.md).
