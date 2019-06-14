---
title: 'Service Fabric Cluster Resource Manager: Náklady na přesunutí | Dokumentace Microsoftu'
description: Přehled náklady na přesunutí pro služby Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 1bd049e6f929b6c3247ca1842412d5527605e643
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60516606"
---
# <a name="service-movement-cost"></a>Nákladech na přesun služeb
Faktor, který Service Fabric Cluster Resource Manager bere v úvahu při pokusu určit, co se změní na clusteru udělat, je náklady na tyto změny. Pojem "cost" vyměněno vypnout kolik clusteru může zlepšit. Náklady se dostaneme při přesunu služby Vyrovnávání, Defragmentace a další požadavky. Cílem je pro splnění požadavků způsobem nejméně rušivé nebo nákladné. 

Přesun služeb čas náklady na využití procesoru a šířka pásma minimálně sítě. Pro stavové služby vyžaduje kopírování stav těchto služeb, spotřebovává další paměti a disku. Minimalizace nákladů na řešení, které Azure Service Fabric Cluster Resource Manager nabízí zajišťuje, který se zbytečně stráví prostředky clusteru. Ale také nechcete ignorovat řešení, které by mohly výrazně zlepšit přidělení prostředků v clusteru.

Cluster Resource Manager má dva způsoby výpočetní náklady a omezení, kterým je při pokusu o správě clusteru. První mechanismus je jednoduše počítání každém přesunu, která by provedla. Pokud jsou obě tato řešení generuje s použitím stejné vyvážit (skóre), pak Cluster Resource Manager upřednostňuje jeden s nejnižšími náklady (celkový počet přesune).

Tato strategie funguje dobře. Ale stejně jako u výchozí nebo statické zatížení, je nepravděpodobné, že by v jakékoli komplexního systému, že všechny přesuny jsou stejné. Některé by mohly být mnohem víc.

## <a name="setting-move-costs"></a>Náklady na přesunutí nastavení 
Můžete určit výchozí přesunout náklady pro službu při jeho vytváření:

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

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Dynamicky zadání náklady na přesunutí na základě na repliku

Všechny předchozí fragmenty kódu jsou pro zadání MoveCost celou službu najednou mimo službu samotnou. Ale přesun náklady jsou nejužitečnější při náklady na přesunutí objektu konkrétní službu změny v průběhu jeho životnost. Protože vlastních služeb pravděpodobně nejlepší představu o jak nákladné se mají přesunout daném okamžiku, je rozhraní API pro služby na sestavu vlastní osoba přesunout náklady za běhu. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Dopad náklady na přesunutí
MoveCost má čtyři úrovně: Nula, nízká, střední a vysokou. MoveCosts jsou relativní vzhledem k sobě navzájem, s výjimkou nula. Nula náklady na přesunutí znamená, že pohyb je zdarma a by neměl započítávat skóre řešení. Nastavení vašeho přechodu náklady na nejvyšší nemá *není* záruka, že replika zůstane na jednom místě.

<center>

![Náklady na přesunutí jako faktor při výběru replik pro přesun][Image1]
</center>

MoveCost vám pomůže najít řešení, která způsobit, že celkový nejméně přerušení a jsou nejjednodušší při stále přicházejících u ekvivalentní zůstatek. Pojem náklady na služby může být relativní vzhledem k řadu věcí. Většina běžných faktorů při výpočtu vaše náklady na přesunutí jsou:

- Množství dat, který má služba pro přesun nebo stav.
- Náklady na odpojení klientů. Přesunutí primární repliky je obvykle dražší než náklady na přesunutí na sekundární repliku.
- Náklady na přerušení operace vydávaných za pochodu. Některé operace na data ukládat úroveň nebo operací provedených v reakci na hovor klienta je nákladné. Po určitém místě které nechcete toho není nutné. Proto při operaci se děje, zvýšit náklady na přesunutí tohoto objektu služby ke snížení pravděpodobnosti, že přesunu. Po dokončení operace nastavíte náklady zpět na normální.

## <a name="enabling-move-cost-in-your-cluster"></a>Povolení náklady na přesunutí ve vašem clusteru
Aby podrobnější MoveCosts vzít v úvahu musí být povolena MoveCost ve vašem clusteru. Bez tohoto nastavení výchozí režim počítání přesune se používá pro výpočet MoveCost a MoveCost sestavy jsou ignorovány.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

prostřednictvím ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

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
- Správce prostředků clusteru Service Fabric metriky používá ke správě využití a kapacitu v clusteru. Další informace o metrikách a způsob jejich konfigurace, podívejte se na [Správa spotřeby prostředků a zatížení v Service Fabric s metrikami](service-fabric-cluster-resource-manager-metrics.md).
- Další informace o tom, jak Cluster Resource Manager spravuje a vyrovnává zatížení v clusteru, podívejte se na [vyrovnávání vašeho clusteru Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
