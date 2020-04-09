---
title: Služby automatického škálování azure service fabric a kontejnery
description: Azure Service Fabric umožňuje nastavit zásady automatickéškálování pro služby a kontejnery.
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: edcf2774873cc23a74a47cc1c9a12e2daa2ed419
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984533"
---
# <a name="introduction-to-auto-scaling"></a>Úvod do automatického škálování
Automatické škálování je další možnost Service Fabric dynamicky škálovat služby na základě zatížení, které služby hlásí, nebo na základě jejich využití prostředků. Automatické škálování poskytuje velkou pružnost a umožňuje zřizování dalších instancí nebo oddílů služby na vyžádání. Celý proces automatického škálování je automatizovaný a transparentní a po nastavení zásad ve službě není nutné provádět ruční škálování na úrovni služby. Automatické škálování lze zapnout buď v době vytvoření služby, nebo kdykoli aktualizací služby.

Běžný scénář, kde automatické škálování je užitečné je, když zatížení na konkrétní služby se liší v průběhu času. Například služba, jako je brána můžete škálovat na základě množství prostředků potřebných ke zpracování příchozích požadavků. Podívejme se na příklad toho, jak by tato pravidla škálování mohla vypadat:
* Pokud všechny instance mé brány používají v průměru více než dvě jádra, pak škálujte službu brány přidáním další instance. Udělejte to každou hodinu, ale nikdy mít více než sedm instancí celkem.
* Pokud všechny instance mé brány používají v průměru méně než 0,5 jader, pak škálujte službu odebráním jedné instance. Proveďte to každou hodinu, ale nikdy mít méně než tři instance celkem.

Automatické škálování je podporováno pro kontejnery i pravidelné služby Service Fabric. Chcete-li použít automatické škálování, musíte být spuštěna na verzi 6.2 nebo vyšší service fabric runtime. 

Zbývající část tohoto článku popisuje zásady škálování, způsoby povolení nebo zakázání automatickéškálování a uvádí příklady použití této funkce.

## <a name="describing-auto-scaling"></a>Popis automatického škálování
Zásady automatického škálování lze definovat pro každou službu v clusteru Service Fabric. Každá zásada škálování se skládá ze dvou částí:
* **Aktivační událost škálování** popisuje, kdy bude provedeno škálování služby. Podmínky, které jsou definovány v aktivační události jsou pravidelně kontrolovány k určení, zda služba by měla být škálována nebo ne.

* **Mechanismus škálování** popisuje, jak bude škálování provedeno při jeho aktivaci. Mechanismus se použije pouze v případě, že jsou splněny podmínky z aktivační události.

Všechny aktivační události, které jsou aktuálně podporovány pracovat buď s [metriky logické zatížení](service-fabric-cluster-resource-manager-metrics.md), nebo s fyzické metriky, jako je využití procesoru nebo paměti. V obou tak či onak service fabric bude sledovat hlášené zatížení pro metriku a bude pravidelně vyhodnocovat aktivační událost k určení, zda je potřeba škálování.

Existují dva mechanismy, které jsou aktuálně podporovány pro automatické škálování. První z nich je určena pro bezstavové služby nebo pro kontejnery, kde se provádí automatické škálování přidáním nebo odebráním [instancí](service-fabric-concepts-replica-lifecycle.md). Pro stavové i bezstavové služby lze automatické škálování provádět také přidáním nebo odebráním pojmenovaných [oddílů služby.](service-fabric-concepts-partitioning.md)

> [!NOTE]
> V současné době je podpora pouze pro jednu zásadu škálování na službu a pouze jednu aktivační událost škálování na zásadu škálování.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Průměrná aktivační událost načtení oddílu s měřítkem založeným na instancích
První typ aktivační události je založen na zatížení instancí v oddílu služby bez stavů. Metrika zatížení jsou nejprve vyhlazeny získat zatížení pro každou instanci oddílu a pak tyto hodnoty jsou zprůměrovány ve všech instancích oddílu. Existují tři faktory, které určují, kdy bude služba škálovat:

* _Nižší prahová hodnota zatížení_ je hodnota, která určuje, kdy bude služba **škálovat v**. Pokud je průměrné zatížení všech instancí oddílů nižší než tato hodnota, bude služba škálována.
* _Horní prahová hodnota zatížení_ je hodnota, která určuje, kdy bude služba **škálovat**. Pokud je průměrné zatížení všech instancí oddílu vyšší než tato hodnota, bude služba škálována.
* _Interval změny velikosti_ určuje, jak často bude aktivační událost kontrolována. Jakmile je zaškrtnuto aktivační události, pokud je potřeba škálování mechanismus bude použit. Pokud není potřeba škálování, nebude provedena žádná akce. V obou případech aktivační událost nebude znovu zkontrolována před vypršením intervalu škálování znovu vyprší.

Tuto aktivační událost lze použít pouze se bezstavovými službami (bezstavové kontejnery nebo služby Service Fabric). V případě, že služba má více oddílů, aktivační událost je vyhodnocena pro každý oddíl zvlášť a každý oddíl bude mít zadaný mechanismus použít nezávisle. Proto v tomto případě je možné, že některé oddíly služby budou škálovány, některé budou škálovány a některé nebudou škálovány vůbec současně na základě jejich zatížení.

Jediný mechanismus, který lze použít s touto aktivační událostí je PartitionInstanceCountScaleMechanism. Existují tři faktory, které určují, jak je tento mechanismus použit:
* _Zvýšení měřítka určuje,_ kolik instancí bude přidáno nebo odebráno při aktivaci mechanismu.
* _Maximální počet instancí_ definuje horní limit pro změnu měřítka. Pokud počet instancí oddílu dosáhne tohoto limitu, nebude služba škálovat, bez ohledu na zatížení. Je možné vynechat tento limit zadáním hodnoty -1 a v takovém případě bude služba škálovat co nejvíce (limit je počet uzlů, které jsou k dispozici v clusteru).
* _Minimální počet instancí_ definuje dolní limit pro změnu měřítka. Pokud počet instancí oddílu dosáhne tohoto limitu, pak služba nebude škálovat bez ohledu na zatížení.

## <a name="setting-auto-scaling-policy"></a>Nastavení zásad automatického škálování

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
### <a name="using-c-apis"></a>Použití přístupů api jazyka C#
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
### <a name="using-powershell"></a>Použití prostředí Powershell
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

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Průměrná aktivační událost zatížení služby s škálováním založeným na oddílech
Druhá aktivační událost je založena na zatížení všech oddílů jedné služby. Metrika zatížení jsou nejprve vyhlazeny získat zatížení pro každou repliku nebo instanci oddílu. Pro stavové služby je zatížení oddílu považováno za zatížení primární repliky, zatímco pro bezstavové služby je zatížení oddílu průměrné zatížení všech instancí oddílu. Tyto hodnoty jsou zprůměrovány ve všech oddílech služby a tato hodnota se používá k aktivaci automatickéškálování. Stejné jako v předchozím mechanismu, existují tři faktory, které určují, kdy bude služba škálovat:

* _Nižší prahová hodnota zatížení_ je hodnota, která určuje, kdy bude služba **škálovat v**. Pokud je průměrné zatížení všech oddílů služby nižší než tato hodnota, bude služba škálována.
* _Horní prahová hodnota zatížení_ je hodnota, která určuje, kdy bude služba **škálovat**. Pokud je průměrné zatížení všech oddílů služby vyšší než tato hodnota, bude služba škálována.
* _Interval změny velikosti_ určuje, jak často bude aktivační událost kontrolována. Jakmile je zaškrtnuto aktivační události, pokud je potřeba škálování mechanismus bude použit. Pokud není potřeba škálování, nebude provedena žádná akce. V obou případech aktivační událost nebude znovu zkontrolována před vypršením intervalu škálování znovu vyprší.

Tuto aktivační událost lze použít se stavovými i bezstavovými službami. Jediný mechanismus, který lze použít s touto aktivační událostí je AddRemoveIncrementalNamedPartitionScaleingMechanism. Při škálování služby je přidán nový oddíl a při škálování služby v jednom z existujících oddílů je odebrána. Existují omezení, která budou kontrolována při vytvoření nebo aktualizaci služby a vytvoření/aktualizace služby se nezdaří, pokud tyto podmínky nejsou splněny:
* Pro službu je nutné použít pojmenované schéma oddílů.
* Názvy oddílů musí být po sobě jdoucí celá čísla, jako "0", "1", ...
* První název oddílu musí být "0".

Například pokud je služba původně vytvořena se třemi oddíly, jedinou platnou možností pro názvy oddílů je "0", "1" a "2".

Skutečná operace automatického škálování, která se provádí, bude respektovat také toto schéma pojmenování:
* Pokud aktuální oddíly služby jsou pojmenovány "0", "1" a "2", pak oddíl, který bude přidán pro horizontální navýšení kapacity bude mít název "3".
* Pokud aktuální oddíly služby jsou pojmenovány "0", "1" a "2", pak oddíl, který bude odebrán pro změnu měřítka v je oddíl s názvem "2".

Stejné jako u mechanismu, který používá škálování přidáním nebo odebráním instancí, existují tři parametry, které určují, jak se tento mechanismus použije:
* _Zvýšení měřítka určuje,_ kolik oddílů bude přidáno nebo odebráno při aktivaci mechanismu.
* _Maximální počet oddílů_ definuje horní limit pro změnu měřítka. Pokud počet oddílů služby dosáhne tohoto limitu, nebude služba škálovat, bez ohledu na zatížení. Je možné vynechat tento limit zadáním hodnoty -1 a v takovém případě bude služba co nejvíce škálovat (limit je skutečná kapacita clusteru).
* _Minimální počet instancí_ definuje dolní limit pro změnu měřítka. Pokud počet oddílů služby dosáhne tohoto limitu, pak služba nebude škálovat bez ohledu na zatížení.

> [!WARNING] 
> Při AddRemoveIncrementalNamedPartitionScale se používá se stavovými službami, Service Fabric přidá nebo odebere oddíly **bez oznámení nebo upozornění**. Při aktivaci mechanismu škálování se nebude provedeno rozdělení dat. V případě operace škálování nahoru budou nové oddíly prázdné a v případě operace škálování **dolů bude oddíl odstraněn spolu se všemi daty, která obsahuje**.

## <a name="setting-auto-scaling-policy"></a>Nastavení zásad automatického škálování

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
### <a name="using-c-apis"></a>Použití přístupů api jazyka C#
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
### <a name="using-powershell"></a>Použití prostředí Powershell
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

Aby bylo možné službu sledování zdrojů škálovat na základě skutečných zdrojů

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Existují dvě metriky, které představují skutečné fyzické prostředky. Jedním z nich je servicefabric:/_CpuCores, které představují skutečné využití procesoru (takže 0,5 představuje polovinu jádra) a druhý je servicefabric:/_MemoryInMB, který představuje využití paměti v MBs.
ResourceMonitorService je zodpovědný za sledování využití procesoru a paměti uživatelských služeb. Tato služba bude používat vážený klouzavý průměr, aby se zohlednily potenciální krátkodobé špičky. Monitorování prostředků je podporováno pro kontejnerizované i nekontejnerizované aplikace v systému Windows a pro kontejnerizované aplikace v systému Linux. Automatické škálování prostředků je povoleno pouze pro služby aktivované ve [výhradním modelu procesu](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Další kroky
Další informace o [škálovatelnosti aplikací](service-fabric-concepts-scalability.md).
