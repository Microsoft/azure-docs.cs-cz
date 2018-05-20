---
title: 'Správce prostředků clusteru Service Fabric: pohyb náklady | Microsoft Docs'
description: Přehled náklady na přesunutí pro služby Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 74b61967a796fca22ab86918235f1def27a22f91
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="service-movement-cost"></a>Náklady na přesunutí služby
Faktor, který správce prostředků clusteru Service Fabric zvažuje při pokusu určit, jaké změny, aby do clusteru s podporou jsou náklady na tyto změny. Pojem "náklady" se prodávají vypnout proti kolik clusteru je možné zlepšit. Náklady se započítá při přesunu služby pro vyrovnání, Defragmentace a další požadavky. Cílem je pro splnění požadavků způsobem alespoň rušivý nebo nákladné. 

Přesunutí čas procesoru náklady na služby a šířka pásma sítě na minimální. Pro stavové služby vyžaduje kopírování stav těchto služeb, využívání další paměti a disku. Minimalizovat náklady na řešení, které správce prostředků Azure Service Fabric clusteru se dodává s pomáhá zajistit, že nejsou zbytečně stráví prostředky clusteru. Však také nechcete ignorovat řešení, které by výrazně zlepšit přidělení prostředků v clusteru.

Správce prostředků clusteru má dva způsoby, kterými computing náklady a omezení je při pokusu o správě clusteru. První mechanismus je jednoduše počítání každých přesunu, která by byla. Pokud dvě řešení se generují se o stejné vyvážit (skóre), pak upřednostní správce prostředků clusteru, jeden s nejnižší náklady na (celkový počet přesune).

Tato strategie funguje správně. Ale stejně jako u výchozí nebo statické zatížení, není pravděpodobné v jakékoli komplexní systému, zda jsou si rovny přesune všechny. Některé by mohly být mnohem nákladnější.

## <a name="setting-move-costs"></a>Náklady na přesunutí nastavení 
Můžete určit výchozí přesunout náklady pro službu, když je vytvořeno:

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

Můžete také zadat nebo aktualizovat MoveCost dynamicky pro službu po vytvoření služby: 

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

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Dynamicky zadání náklady na přesunutí na základě za repliky

Předchozí fragmenty kódu jsou všechny pro zadání MoveCost celou službu najednou mimo službu samotnou. Však přesunout náklady je velmi užitečné je při náklady na přesunutí objektu specifické služby změní přes jeho životnost. Vzhledem k tomu, že sami služeb pravděpodobně mají nejlepší představu o tom, jak nákladná jsou přesunout okamžiku, je rozhraní API služeb sestavu vlastní individuální přesunout náklady za běhu. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Dopad náklady na přesunutí
MoveCost má čtyři úrovně: nula, nízké, střední a vysokou. MoveCosts jsou relativní vzhledem k sobě navzájem, s výjimkou nula. Nulové náklady na přesunutí znamená, že pohyb je zdarma a neměli započítává skóre řešení. Nastavení vaší přesunutí náklady na horní nemá *není* záruka, že replika zůstane na jednom místě.

<center>
![Náklady na přesunutí jako faktor při výběru replik pro přesun][Image1]
</center>

MoveCost vám pomůže najít řešení, která způsobí celkové minimálně přerušení a jsou nejjednodušší dosáhnout při stále přicházejících na ekvivalentní vyrovnávání. Služby představu o náklady může být relativní vzhledem k celou řadu věcí. Nejběžnější faktory při výpočtu vaše náklady na přesunutí jsou:

- Množství dat, který má služba pro přesun nebo stav.
- Náklady na odpojení klientů. Přesunutí primární replice je obvykle dražší než náklady na přesunutí sekundární repliku.
- Náklady na přerušení operace během letu. Některé operace na data ukládat úroveň nebo jsou nákladná operace provedené v reakci na hovor klienta. Po určité míry nechcete nezastavíte, pokud není nutné. Proto při operaci se děje, můžete zvýšit náklady na přesunutí tohoto objektu služby snížit pravděpodobnost, že ji přesune. Pokud se provádí operaci, nastavíte náklady na zpět na normální.

## <a name="enabling-move-cost-in-your-cluster"></a>Povolení náklady na přesunutí v clusteru
Aby podrobnější MoveCosts vzít v úvahu musí být povolena MoveCost v clusteru. Bez tohoto nastavení výchozí režim počítání přesune se používá pro výpočet MoveCost a MoveCost sestavy jsou ignorovány.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

pomocí souboru ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

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

## <a name="next-steps"></a>Další postup
- Správce prostředků clusteru Service Fabric používá metriky ke správě využívání a kapacity v clusteru. Další informace o metriky a způsob jejich konfigurace, podívejte se na [Správa spotřeby prostředků a zatížení v Service Fabric s metriky](service-fabric-cluster-resource-manager-metrics.md).
- Další informace o tom, jak správce prostředků clusteru spravuje a vyrovnává zatížení v clusteru, podívejte se na [vyrovnávání cluster Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
