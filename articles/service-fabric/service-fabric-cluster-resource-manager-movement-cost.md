---
title: 'Správce prostředků Service Fabric clusteru: náklady na přesun'
description: Přečtěte si informace o nákladech na přesun pro Service Fabric Services a o tom, jak je můžete zadat, aby vyhovovaly libovolné potřebě architektury, včetně dynamické konfigurace.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 0fdcfb02851d56ed996ae4bf32671ab545782733
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89005339"
---
# <a name="service-movement-cost"></a>Náklady na přesun služeb
Faktor, který Správce prostředků Cluster Service Fabric zvažuje při pokusu o určení toho, jaké změny provedete v clusteru, jsou náklady na tyto změny. Pojem "náklady" se nepodílí na tom, kolik clusteru je možné zlepšit. Náklady se připravují při přesunu služeb pro vyrovnávání, defragmentaci a další požadavky. Cílem je splnit požadavky alespoň na rušivý nebo nákladný způsob.

Přesun služeb má za nejnižší náklady na čas procesoru a šířku pásma sítě. Pro stavové služby je nutné zkopírovat stav těchto služeb a spotřebovat další paměť a disk. Minimalizace nákladů na řešení, která Správce prostředků cluster Azure Service Fabric, vám pomůže zajistit, že prostředky clusteru nestráví zbytečně. Nechcete ale také ignorovat řešení, která by významně vylepšila přidělení prostředků v clusteru.

Cluster Správce prostředků má dva způsoby výpočtu nákladů a jejich omezení při pokusu o správu clusteru. První mechanismus se počítá jenom při každém přesunu, který by provedl. Pokud jsou vygenerována dvě řešení se stejnou bilancí (skóre), pak cluster Správce prostředků upřednostňuje ten s nejnižšími náklady (celkový počet přesunů).

Tato strategie funguje dobře. Ale stejně jako u výchozích nebo statických zátěží není pravděpodobné v žádném komplexním systému, který je stejný jako u všech přesunů. Některé jsou pravděpodobně mnohem dražší.

## <a name="setting-move-costs"></a>Nastavení nákladů na přesun 
Můžete určit výchozí náklady na přesunutí pro službu při jejím vytvoření:

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

Po vytvoření služby můžete také zadat nebo aktualizovat Cenazapřesun dynamicky pro službu: 

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

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Dynamické určení nákladů na přesun u jednotlivých replik

Předchozí fragmenty kódu jsou všechny pro zadání Cenazapřesun pro celou službu najednou mimo samotnou službu. Náklady na přesunutí jsou však nejužitečnější, pokud se náklady na přesunutí určitého objektu služby mění po jeho životnosti. Vzhledem k tomu, že samotné služby pravděpodobně mají nejlepší představu o tom, jak nákladná mají přesunout určitou dobu, existuje rozhraní API pro služby, které během běhu oznamují své vlastní náklady na přesun. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Dopad nákladů na přesun
Cenazapřesun má pět úrovní: 0, nízká, střední, vysoká a VeryHigh. Platí následující pravidla:

* MoveCosts jsou vzájemně relativní, s výjimkou nul a VeryHigh. 
* Nulové náklady na přesun znamená, že pohyb je bezplatný a neměl by se počítat s skóre řešení.
* Nastavení nákladů na přesun na vysoká nebo VeryHigh *neposkytuje jistotu* , že replika nebude *nikdy* přesunuta.
* Repliky s VeryHigh náklady na přesun budou přesunuty pouze v případě, že dojde k porušení omezení v clusteru, které nelze opravit jiným způsobem (ani když vyžaduje přesunutí mnoha dalších replik za účelem opravy porušení).



<center>

![Přesunutí nákladů jako faktoru při výběru replik pro pohyb][Image1]
</center>

Cenazapřesun vám pomůže najít řešení, která způsobují nejmenší přerušení a jsou nejjednodušší, abyste dosáhli stejného zůstatku. Pojem nákladů na službu může být relativní vzhledem k mnoha účelům. Mezi nejběžnější faktory při výpočtu nákladů na stěhování patří:

- Množství stavu nebo dat, které musí služba přesunout.
- Náklady na odpojení klientů. Přesunutí primární repliky je obvykle nákladnější než náklady na přesun sekundární repliky.
- Náklady na přerušení provozu v letadle. Některé operace na úrovni úložiště dat nebo operací provedených v reakci na klientské volání jsou nákladné. Po určitém okamžiku je nechcete zastavit, pokud ho nepotřebujete. Takže když operace probíhá, zvýšíte tím náklady na přesun tohoto objektu služby, aby se snížila pravděpodobnost, že se přesune. Po dokončení operace nastavte náklady zpět na normální.

> [!IMPORTANT]
> Cena za stěhování VeryHigh by se měla pečlivě zvážit, protože významně omezuje schopnost clusteru Správce prostředků najít globálně optimální řešení umístění v clusteru. Repliky s VeryHigh náklady na přesun budou přesunuty pouze v případě, že dojde k porušení omezení v clusteru, které nelze opravit jiným způsobem (ani když vyžaduje přesunutí mnoha dalších replik za účelem opravy porušení).

## <a name="enabling-move-cost-in-your-cluster"></a>Povolení přesunutí nákladů v clusteru
Aby bylo možné podrobnější MoveCosts vzít v úvahu, musí být ve vašem clusteru povolený Cenazapřesun. Bez tohoto nastavení se pro výpočet Cenazapřesun používá výchozí režim počítání přesunů a sestavy Cenazapřesun se ignorují.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

prostřednictvím ClusterConfig.jsv pro samostatná nasazení nebo Template.jsv případě hostovaných clusterů Azure:

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
- Správce prostředků clusteru Service Fabric využívá metriky ke správě využití a kapacity v clusteru. Další informace o metrikách a o tom, jak je nakonfigurovat, najdete [v tématu Správa spotřeby prostředků a načítání v Service Fabric se metrikami](service-fabric-cluster-resource-manager-metrics.md).
- Pokud se chcete dozvědět, jak cluster Správce prostředků spravuje a vyrovnává zatížení v clusteru, podívejte se na [vyvážení Service Fabric clusteru](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
