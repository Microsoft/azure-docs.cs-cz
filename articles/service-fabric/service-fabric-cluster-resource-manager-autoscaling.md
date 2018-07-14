---
title: Azure Service Fabric automatické škálování služby a kontejnery | Dokumentace Microsoftu
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
ms.openlocfilehash: a742ac79f1152816621312e2ebc59598772ba127
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990617"
---
# <a name="introduction-to-auto-scaling"></a>Úvod k automatickému škálování
Automatické škálování je další schopností Service Fabric dynamické škálování služeb na základě zatížení, které služby se hlásí, nebo na základě jejich využití prostředků. Automatické škálování poskytuje skvělé pružnost a umožňuje zřízení dalších instancí nebo oddíly služby na vyžádání. Celý automatické škálování zpracování je automatické a transparentní, a po nastavení zásad pro službu není nutné pro ruční operace škálování na úrovni služby. Automatické škálování je možné zapnout na buď při vytváření služby, nebo kdykoli při aktualizaci.

Běžný scénář, kde automatické škálování je užitečné při zatížení na konkrétní služby se liší v čase. Například služby, jako je brána lze škálovat na základě množství prostředků, které jsou potřeba ke zpracování příchozích požadavků. Pojďme se podívat na příklad těchto škálovací pravidla může vypadat:
* Pokud všechny výskyty mám bránu jsou v průměru pomocí více než dvě jádra, škálování službě brány navýšení kapacity přidáním více instancí. Provést každou hodinu, ale nikdy mít více než sedm instancí celkem.
* Pokud všechny výskyty Moje brány používají v průměru méně než 0,5 jádra, pak škálujte službu v odebráním jedné instance. Provést každou hodinu, ale nikdy mají méně než tři instance celkem.

Automatické škálování se podporuje pro kontejnery a pravidelné služeb Service Fabric. Zbývající část tohoto článku popisuje zásad škálování způsoby, jak povolit nebo zakázat automatické škálování a uvádí příklady o tom, jak tuto funkci používat.

## <a name="describing-auto-scaling"></a>Popisující, automatické škálování
Automatické škálování zásad lze definovat pro každou službu v clusteru Service Fabric. Každé zásady škálování se skládá ze dvou částí:
* **Škálování aktivační událost** popisuje při škálování služby se provede. Podmínky, které jsou definovány v aktivační události jsou pravidelně zkontrolována k určení, pokud služba má být nastaveno měřítko nebo ne.

* **Škálování mechanismus** popisuje, jak škálování proběhne při aktivaci. Mechanismus platí jenom při splnění podmínek z triggeru.

Všechny aktivační události, které jsou aktuálně podporovány pracovat s [logické zatížení metriky](service-fabric-cluster-resource-manager-metrics.md), nebo o fyzické metriky, jako je využití procesoru nebo paměti. V obou případech Service Fabric bude monitorovat ohlášené zatížení metriky a vyhodnotí aktivační událost pravidelně k určení, pokud není nutné provádět škálování.

Existují dva mechanismy, které jsou aktuálně podporovány pro automatické škálování. První z nich je určena pro bezstavové služby nebo pro kontejnery, kde automatické škálování se provádí přidáním nebo odebráním [instance](service-fabric-concepts-replica-lifecycle.md). Stavové a bezstavové služby, automatické škálování můžete také provést přidáním nebo odebráním s názvem [oddíly](service-fabric-concepts-partitioning.md) služby.

> [!NOTE]
> Aktuálně je podpora jen jedny zásady škálování na službu.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Aktivační událost zatížení průměrné oddílu s instancí na základě škálování
První typ aktivační události je na základě zatížení instancí v oddílu bezstavové služby. Metrika zatížení jsou nejprve vyhlazené získat zatížení pro každou instanci oddílu, a pak jsou tyto hodnoty průměrovaný napříč všemi instancemi oddílu. Existují tři faktory, které určují, kdy se škálovat službu:

* _Nižší prahová hodnota načtení_ je hodnota, která určuje, kdy bude služba **horizontálně**. Pokud je průměrné zatížení všech instancí oddílů je nižší než tato hodnota, bude v škálovat službu.
* _Horní prahová hodnota zatížení_ je hodnota, která určuje, kdy bude služba **škálované**. Pokud je průměrné zatížení všech instancí oddílu je nižší než tato hodnota, bude služba horizontálním navýšení kapacity.
* _Interval škálování_ Určuje, jak často budou trigger kontrolovat. Po zaškrtnutí aktivační událost, pokud škálování je potřeba mechanismu, který se použije. Podle potřeby škálování není, bude provedena žádná akce. V obou případech se aktivační událost nebude znovu zkontrolovat vypršení platnosti interval škálování znovu.

Tato aktivační událost lze použít pouze s bezstavové služby (bezstavové kontejnery a služby Service Fabric). V případě, když služba obsahuje několik oddílů, trigger se vyhodnocuje pro každý oddíl samostatně a každý oddíl bude mít zadané mechanismus nezávisle na sobě použít. Proto v tomto případě je možné, že některé z oddílů služby se škálovat, některé bude možné horizontálně snížit a některé nebude možné škálovat na všech ve stejnou dobu, na základě jejich zatížení.

Pouze mechanismus, který lze použít s tímto triggerem je PartitionInstanceCountScaleMechanism. Existují tři faktory, které určují, jak tento mechanismus je použito:
* _Zvýšení škálování_ Určuje, kolik instancí budou přidány nebo odebrány při aktivaci mechanismus.
* _Maximální počet instancí_ definuje horní mez pro škálování. Pokud počet instancí oddílu dosáhne tohoto limitu, nebude možné službu škálovat, bez ohledu na to, zatížení. Je možné vynechat, nechte toto omezení zadáním hodnoty-1 a v tomto případě služba bude škálovat dolů, co nejvíc (limit je počet uzlů, které jsou k dispozici v clusteru).
* _Minimální počet instancí_ definuje dolní mez pro škálování. Pokud počet instancí oddílu dosáhne tohoto limitu, nebude možné službu škálovat ve bez ohledu na to, zatížení.

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
### <a name="using-powershell"></a>Pomocí Powershellu
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

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>Průměrná služby zatížení trigger s škálování oddílů na základě
Druhý aktivační událost podle zatížení všech oddílů z jedné služby. Metrika zatížení jsou nejprve vyhlazené získat zatížení pro každou repliku nebo instance oddílu. Pro stavové služby zatížení oddílu se považuje za zatížení primární replikou, zatímco pro bezstavové služby zatížení oddílu je průměrné zatížení všechny instance oddílu. Tyto hodnoty jsou průměrovaný napříč všechny oddíly služby a tato hodnota se používá k aktivaci automatického škálování. Stejné jako u předchozí mechanismus, existují tři faktory, které určují, když bude možné službu škálovat:

* _Nižší prahová hodnota načtení_ je hodnota, která určuje, kdy bude služba **horizontálně**. Pokud je průměrné zatížení všech oddílů služby je nižší než tato hodnota, bude v škálovat službu.
* _Horní prahová hodnota zatížení_ je hodnota, která určuje, kdy bude služba **škálované**. Pokud je průměrné zatížení všech oddílů služby je nižší než tato hodnota, bude služba horizontálním navýšení kapacity.
* _Interval škálování_ Určuje, jak často budou trigger kontrolovat. Po zaškrtnutí aktivační událost, pokud škálování je potřeba mechanismu, který se použije. Podle potřeby škálování není, bude provedena žádná akce. V obou případech se aktivační událost nebude znovu zkontrolovat vypršení platnosti interval škálování znovu.

Tato aktivační událost může být použit s stavové a bezstavové služby. Pouze mechanismus, který lze použít s tímto triggerem je AddRemoveIncrementalNamedParitionScalingMechanism. Služba horizontálně potom je přidán nový oddíl a data služby je škálování v jednom z existující oddíly se odebere. Platí omezení, které budou zkontrolovány, jakmile se vytvoří nebo aktualizuje služba a služba vytvoření/aktualizace se nezdaří, pokud nejsou splněny tyto podmínky:
* Použije schéma s názvem oddílu služby.
* Názvy oddílů musí být celé číslo po sobě jdoucí čísla, jako je "0", "1"...
* První název oddílu musí být "0".

Například pokud služba je původně vytvořeny pomocí tři oddíly, jediná platná možnost pro názvy oddílů je "0", "1" a "2".

Skutečné automatické škálování, které jsou prováděny respektuje Toto pojmenování schéma:
* Pokud aktuální oddílů služby se s názvem "0", "1" a "2", pak oddílu, který se přidá pro horizontální navýšení kapacity bude mít název "3".
* Pokud aktuální oddílů služby se s názvem "0", "1" a "2", je oddíl, který se odebere pro škálování v oddílu s názvem "2".

Stejně jako u mechanismus, který používá škálování přidáváním nebo odebíráním instancí stejné, existují tři parametry, které určují, jak tento mechanismus je použito:
* _Zvýšení škálování_ Určuje, kolik oddíly budou přidány nebo odebrány při aktivaci mechanismus.
* _Maximální počet oddílů_ definuje horní mez pro škálování. Pokud počet oddílů služby se dosáhne tohoto limitu, nebude možné službu škálovat, bez ohledu na to, zatížení. Je možné vynechat, nechte toto omezení zadáním hodnoty-1 a v tomto případě služba bude škálovat dolů, co nejvíc (limit je aktuální kapacita clusteru).
* _Minimální počet instancí_ definuje dolní mez pro škálování. Pokud počet oddílů služby se dosáhne tohoto limitu, nebude možné službu škálovat ve bez ohledu na to, zatížení.

> [!WARNING] 
> Při použití AddRemoveIncrementalNamedParitionScalingMechanism s stavové služby Service Fabric se přidat nebo odebrat oddíly **bez oznámení a upozornění**. Oddílů dat nebude provedena, když se aktivuje škálování mechanismus. V případě, že o operaci vertikálního navýšení, nových oddílů bude prázdný a v případě vertikálního snižování kapacity operace, **oddílu se odstraní spolu s všechna data, která obsahuje**.

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
### <a name="using-powershell"></a>Pomocí Powershellu
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

## <a name="auto-scaling-based-on-resources"></a>Automatické škálování na základě prostředků

Chcete-li povolit službu monitorování prostředků škálovat podle skutečných prostředků

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Existují dvě metriky, které představují skutečné fyzické prostředky. Jeden z nich je servicefabric: / _CpuCores, které představují skutečné využití procesoru (takže 0,5 představuje poloviční základní) a druhý se servicefabric: / _MemoryInMB, který představuje využití paměti v MB.
ResourceMonitorService zodpovídá za sledování využití procesoru a paměti služby uživatele. Tato služba bude účet pro možnými výkyvy na krátkodobé a jednorázové nevztahují Vážený klouzavý průměr. Sledování prostředků je podporována pro kontejnerizované i nekontejnerizované aplikace na Windows a pro kontejnerizované ty v Linuxu. Automatické škálování prostředků je povolená jenom pro aktivaci služby v [model procesu exkluzivní](service-fabric-hosting-model.md#exclusive-process-model).

## <a name="next-steps"></a>Další postup
Další informace o [aplikace škálovatelnost](service-fabric-concepts-scalability.md).
