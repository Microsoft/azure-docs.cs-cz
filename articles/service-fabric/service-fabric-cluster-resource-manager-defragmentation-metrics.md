---
title: Defragmentaci metrik v Azure Service Fabric | Dokumentace Microsoftu
description: Přehled použití defragmentace a zabalení jako strategii pro metriky v Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6e041e41372c72c6792c1fb4a1fbdc3bbe475b21
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60844387"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentaci metrik a zatížení v Service Fabric
Service Fabric Cluster Resource Manager výchozí strategii pro správu metriky zatížení v clusteru je můžete distribuovat zatížení. Zajištění, že uzly jsou rovnoměrně využít zabraňuje horké a studené body, které vedou ke soupeření a nevyužité prostředky. Distribuce zatížení v clusteru je také nejbezpečnější z hlediska přežití selhání, protože zajišťuje, že selhání nepřijímá si vysoké procento konkrétní úlohu. 

Service Fabric Cluster Resource Manager podporuje různé strategie pro správu zatížení, což je defragmentaci. Defragmentace znamená, že namísto pokusu o využití metriku distribuovat napříč clusterem, to je konsolidovat. Konsolidace je právě inverzi výchozí strategie – místo minimalizovat průměrné směrodatné odchylky poměru metrika zatížení, Cluster Resource Manager se pokusí ji zvětšete.

## <a name="when-to-use-defragmentation"></a>Kdy použít Defragmentace
Distribuce zatížení v clusteru využívá některé z prostředků na každém uzlu. Některé úlohy vytvářet služby, které jsou mimořádně velkou a využívají většinu nebo všechny z uzlu. V těchto případech je možné, že při rozsáhlé úlohy vytvářené, který není dost místa na libovolném uzlu k jejich spuštění. Rozsáhlé úlohy nejsou k problému v Service Fabric; v těchto případech Cluster Resource Manager určuje, že je nutné změnit uspořádání cluster tak, aby uvolnila prostor pro danou úlohu velké. Do té doby úlohy má ale čekání na naplánované v clusteru.

Pokud mnoho služeb a stavu přesouvat, to může trvat dlouhou dobu pro velké zatížení budou umístěny v clusteru. Tím je pravděpodobnější, pokud dalších úloh v clusteru je velký a to trvat déle reorganizovat. Service Fabric týmu měří času vytvoření v simulace tohoto scénáře. Zjistili jsme, že vytváření velké služeb trvalo mnohem déle, jakmile využití clusteru je teď vyšší než 30 až 50 %. Tímto scénářem poradit, jsme představili defragmentace jako vyrovnávání strategie. Zjistili jsme, že pro rozsáhlé úlohy, zejména těch, ve kterém byla důležitá, defragmentace skutečně pomohla těchto nových úloh čas vytvoření získat naplánováno v clusteru.

Můžete nakonfigurovat defragmentaci metrik mít Cluster Resource Manageru se aktivně se pokouší zúžit zatížení služeb do menšího počtu uzlů. To pomáhá zajistit, že je téměř vždy volného místa pro velké služby bez reorganizace clusteru. Nebudete muset měnit uspořádání clusteru umožňuje rychlé vytváření rozsáhlých úlohách.

Defragmentaci nemusí většina lidí. Služby jsou obvykle neměl malé, takže není obtížné najít místnosti pro ně v clusteru. Po reorganizaci je možné, přejde rychle, znovu protože většina služeb jsou malé a je možné přesunout rychle a paralelně. Pokud máte velké služby a potřebujete je rychlé vytvoření pak defragmentace strategie, ale je za vás. Tomu se budeme podrobněji kompromisy defragmentace další použití. 

## <a name="defragmentation-tradeoffs"></a>Defragmentace kompromisy
Defragmentace zvýšit impactfulness selhání, protože další služby spuštěné na uzlech, které nesplní. Defragmentace může také zvýšit náklady, protože prostředky v clusteru musí uchovávat v datovém typu rezervy čekání na vytvoření rozsáhlé úlohy.

Následující diagram nabízí vizuální znázornění dva clustery, ten, který je defragmentovat a, který není. 

<center>

![Porovnání s vyrovnáváním a defragmentovat clusterů][Image1]
</center>

V tomto případě vyvážené vezměte v úvahu počet pohybů plb typu, které by bylo nutné umístit jeden z největších objektů služby. V defragmentovanou clusteru velkých úloh, můžete umístit na čtyři nebo pět uzlů bez nutnosti čekání na další služby pro přesun.

## <a name="defragmentation-pros-and-cons"></a>Defragmentace výhody a nevýhody
Co jsou tedy tyto koncepční kompromisy? Tady je rychlý tabulku co je potřeba zamyslet:

| Defragmentace profesionály | Nevýhody Defragmentace |
| --- | --- |
| Umožňuje rychlejší vytvoření velké služeb |Koncentráty načíst do menšího počtu uzlů, zvyšuje kolize |
| Umožňuje snížit přesun dat při vytváření |Selhání může mít vliv na další služby a způsobit, že více změn |
| Umožňuje bohaté popis požadavků a recyklaci místa |Složitější celkové konfiguraci správy prostředků |

Je možné kombinovat defragmentovanou a běžné metriky ve stejném clusteru. Cluster Resource Manager se pokusí o sloučení co nejvíc při šíření na ostatní defragmentaci metrik. Výsledky kombinování defragmentace a rozložení zátěže strategie závisí na několika různými faktory, včetně:
  - počet vyrovnávání metriky oproti počet defragmentaci metrik
  - Určuje, zda všechny služba používá oba typy metrik 
  - metriky váhy
  - načte aktuální metrika
  
Experimentování ve službě je potřeba určit přesnou konfiguraci nezbytné. Před povolením defragmentaci metrik v produkčním prostředí doporučujeme důkladně měření úloh. To platí zejména při kombinování defragmentace a vyvážený metriky v rámci stejné služby. 

## <a name="configuring-defragmentation-metrics"></a>Konfigurace defragmentaci metrik
Konfigurace defragmentaci metrik je globální rozhodnutí v clusteru, a jednotlivé metriky můžete vybrat pro defragmentace. Následující konfigurace fragmenty kódu ukazují, jak konfigurovat metriky pro defragmentaci. V tomto případě "Metric1" je nakonfigurovaný jako defragmentaci metrik, zatímco "Metric2" budou i nadále vyváženy normálně. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

prostřednictvím ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Další postup
- Cluster Resource Manager má man možnosti pro popis clusteru. Další informace o nich najdete v tomto článku na [popisující cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Metriky se, jak spravuje správce prostředků Service Fabric Cluster využití a kapacitu v clusteru. Další informace o metrikách a způsob jejich konfigurace, podívejte se na [v tomto článku](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
