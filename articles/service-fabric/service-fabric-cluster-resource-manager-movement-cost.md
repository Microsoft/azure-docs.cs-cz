---
title: 'Správce prostředků clusteru Service Fabric: Náklady na přesun'
description: Seznamte se s náklady na přesun služeb Service Fabric a s tím, jak je lze zadat tak, aby vyhovovaly všem architektonickým potřebám, včetně dynamické konfigurace.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: af3e01d0d5a605c052be24eed8e14ee3449e2c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563339"
---
# <a name="service-movement-cost"></a>Náklady na přesun služeb
Faktor, který správce prostředků clusteru Service Fabric zvažuje při pokusu o určení, jaké změny provést v clusteru, jsou náklady na tyto změny. Pojem "náklady" se obchoduje off proti kolik clusteru lze zlepšit. Náklady se započítávají při přesunu služeb pro vyrovnávání, defragmentaci a další požadavky. Cílem je splnit požadavky co nejméně rušivým nebo drahým způsobem.

Přesouvání služeb stojí minimálně čas procesoru a šířku pásma sítě. Pro stavové služby vyžaduje kopírování stavu těchto služeb, spotřebovává další paměť a disk. Minimalizace nákladů na řešení, která Azure Service Fabric Cluster Resource Manager přichází s pomáhá zajistit, že prostředky clusteru nejsou vynaloženy zbytečně. Však také nechcete ignorovat řešení, která by výrazně zlepšit přidělení prostředků v clusteru.

Správce prostředků clusteru má dva způsoby výpočtu nákladů a jejich omezení při pokusu o správu clusteru. Prvním mechanismem je jednoduše počítání každého kroku, který by učinil. Pokud jsou generována dvě řešení s přibližně stejným zůstatkem (skóre), pak Správce prostředků clusteru upřednostňuje řešení s nejnižšími náklady (celkový počet tahů).

Tato strategie funguje dobře. Ale stejně jako u výchozího nebo statického zatížení je nepravděpodobné, že by v každém složitém systému byly všechny pohyby stejné. Některé z toho budou pravděpodobně mnohem dražší.

## <a name="setting-move-costs"></a>Nastavení nákladů na přesun 
Můžete určit výchozí náklady na přesunutí služby při jeho vytvoření:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#: 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Můžete také určit nebo aktualizovat MoveCost dynamicky pro službu po vytvoření služby: 

PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Dynamické určení nákladů na přesun na základě repliky

Předchozí úryvky jsou všechny pro určení MoveCost pro celou službu najednou mimo samotnou službu. Náklady na přesunutí jsou však nejužitečnější, když se náklady na přesunutí určitého objektu služby během jeho životnosti změní. Vzhledem k tomu, že samotné služby mají pravděpodobně nejlepší představu o tom, jak nákladné jsou přesunout daný čas, je rozhraní API pro služby, které hlásí své vlastní individuální náklady na přesun za běhu. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Dopad nákladů na přesun
MoveCost má pět úrovní: Zero, Low, Medium, High a VeryHigh. Platí následující pravidla:

* MoveCost jsou relativní k sobě navzájem, s výjimkou Zero a VeryHigh. 
* Nulové náklady na přesun znamenají, že pohyb je volný a neměl by se započítávat do skóre řešení.
* Nastavení nákladů na přesun na vysoké nebo velmi vysoké *neposkytuje* záruku, že replika nebude *nikdy* přesunuta.
* Repliky s velmi vysoké náklady přesunutí budou přesunuty pouze v případě, že je porušení omezení v clusteru, který nelze opravit jiným způsobem (i v případě, že vyžaduje přesunutí mnoho dalších replik opravit porušení)



<center>

![Přesunutí nákladů jako faktoru při výběru replik pro přesun][Image1]
</center>

MoveCost vám pomůže najít řešení, která způsobují nejmenší narušení celkově a je nejjednodušší dosáhnout při dosažení ekvivalentní rovnováhy. Služba je pojem nákladů může být ve vztahu k mnoha věcem. Nejběžnější faktory při výpočtu nákladů na přesun jsou:

- Množství stavu nebo data, která má služba přesunout.
- Náklady na odpojení klientů. Přesunutí primární repliky je obvykle nákladnější než náklady na přesunutí sekundární repliky.
- Náklady na přerušení provozu za letu. Některé operace na úrovni úložiště dat nebo operace prováděné v reakci na volání klienta jsou nákladné. Po určitém okamžiku, nechcete zastavit, pokud nechcete. Takže zatímco operace probíhá, můžete zvýšit náklady na přesunutí tohoto objektu služby snížit pravděpodobnost, že se přesune. Po dokončení operace nastavíte náklady zpět na normální.

> [!IMPORTANT]
> Pomocí velmi vysoké přesunout náklady by měly být pečlivě zváženy, protože výrazně omezuje schopnost Správce prostředků clusteru najít globálně optimální umístění řešení v clusteru. Repliky s velmi vysoké náklady přesunutí budou přesunuty pouze v případě, že je porušení omezení v clusteru, který nelze opravit jiným způsobem (i v případě, že vyžaduje přesunutí mnoho dalších replik opravit porušení)

## <a name="enabling-move-cost-in-your-cluster"></a>Povolení nákladů na přesun v clusteru
Aby bylo možné vzít v úvahu podrobnější movecost, movecost musí být povolena ve vašem clusteru. Bez tohoto nastavení se pro výpočet movecostu použije výchozí režim počítání přesunů a sestavy MoveCost jsou ignorovány.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

prostřednictvím souboru ClusterConfig.json pro samostatná nasazení nebo Template.json pro hostované clustery Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Další kroky
- Manger prostředků clusteru Service Fabric používá metriky ke správě spotřeby a kapacity v clusteru. Další informace o metrikách a jejich konfiguraci najdete v tématu [Správa spotřeby prostředků a načítání v service fabricu pomocí metrik](service-fabric-cluster-resource-manager-metrics.md).
- Informace o tom, jak Správce prostředků clusteru spravuje a vyrovnává zatížení clusteru, najdete v části [Vyvažování clusteru Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
