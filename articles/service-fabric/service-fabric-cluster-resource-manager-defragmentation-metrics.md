---
title: Defragmentace metrik v Azure Service Fabric
description: Naučte se používat defragmentaci nebo balení jako strategii pro metriky v Service Fabric. Tato technika je užitečná pro velmi velké služby.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bba459be4408f4a4bc438bb33b0570a91e84f2cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75563356"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentace metrik a načítání v Service Fabric
Služba Service Fabric clusteru Správce prostředků výchozí strategii pro správu metrik zatížení v clusteru slouží k distribuci zátěže. Zajištění toho, aby se uzly rovnoměrně využily, nemusíte používat horké a studené body, které vedou k kolizí i k nevyužitým prostředkům. Distribuce úloh v clusteru je také nejbezpečnější z důvodu nedodržení selhání, protože zajišťuje, že selhání netrvá vysoké procento dané úlohy. 

Správce prostředků Service Fabric clusteru podporuje jinou strategii pro správu zátěže, což je defragmentace. Defragmentace znamená, že se místo pokusu o distribuci využití metriky v clusteru konsoliduje. Konsolidace je jenom inverze výchozí strategie vyrovnávání – místo minimalizace průměrné směrodatné odchylky zatížení se cluster Správce prostředků pokusí ho zvýšit.

## <a name="when-to-use-defragmentation"></a>Kdy použít defragmentaci
Distribuce zatížení v clusteru využívá některé prostředky na jednotlivých uzlech. Některé úlohy vytvářejí služby, které jsou mimořádně velké a využívají většinu nebo všechny uzly. V těchto případech je možné, že když dojde k vytvoření velkých úloh, že na žádném uzlu není dost místa pro jejich spuštění. U velkých úloh se nejedná o problém v Service Fabric. v těchto případech cluster Správce prostředků zjistí, že je potřeba reorganizovat cluster, aby uvolnil prostor pro tuto velkou úlohu. Mezitím ale musí zatížení čekat na naplánování v clusteru.

Pokud existuje mnoho služeb a stavů, které je třeba přesunout, může trvat dlouhou dobu, než se velké zatížení umístí do clusteru. Pravděpodobnou příčinou je, že další úlohy v clusteru jsou také velké a jejich uspořádání trvá déle. Service Fabric tým měřené časy vytváření v simulacích tohoto scénáře. Zjistili jsme, že vytváření velkých služeb trvalo mnohem déle, protože využití clusteru je výše v rozsahu 30 až 50%. Pro zpracování tohoto scénáře jsme zavedli defragmentaci jako vyrovnávání strategie. Zjistili jsme, že pro velké úlohy, zejména v případě, kdy čas vytvoření je důležité, Defragmentace skutečně pomohlo, aby tyto nové úlohy naplánovaly v clusteru.

Metriky defragmentace můžete nakonfigurovat tak, aby se cluster Správce prostředků proaktivně pokoušet o zúžení zatížení služeb do menšího počtu uzlů. To pomáhá zajistit, aby pro velké služby bylo skoro vždy místo, aniž by bylo nutné znovu organizovat cluster. Nemusíte reorganizovat cluster, což umožňuje rychlé vytváření velkých úloh.

Většina lidí nepotřebuje defragmentaci. Služby jsou obvykle malé, takže není obtížné najít pro ně místo v clusteru. V případě, že je reorganizace možná, bude rychle a znovu fungovat, protože většina služeb je malá a je možné ji rychle a paralelně přesunout. Pokud ale máte velké služby a potřebujete je rychle vytvořit, vytvoří se vám strategie defragmentace. Budeme se zabývat kompromisy používání defragmentace v další části. 

## <a name="defragmentation-tradeoffs"></a>Nevýhody defragmentace
Defragmentace může zvýšit impactfulness selhání, protože na uzlech, které selžou, jsou spuštěné další služby. Defragmentace může také zvýšit náklady, protože prostředky v clusteru musí být uchovávány v rezervě a čekají na vytvoření velkých úloh.

Následující diagram poskytuje vizuální znázornění dvou clusterů, z nichž jeden je defragmentované a druhý. 

<center>

![Porovnání vyrovnaných a defragmentovaných clusterů][Image1]
</center>

V případě vyrovnaného případu zvažte počet přesunů potřebných k umístění jednoho z největších objektů služby. V defragmentovaných clusterech může být velké zatížení umístěno na uzlech čtyř nebo 5 bez nutnosti čekat na přesun žádné jiné služby.

## <a name="defragmentation-pros-and-cons"></a>Defragmentace – specialisté a nevýhody
Co jsou to ostatní koncepční kompromisy? Tady je Stručná tabulka věcí, které je třeba zvážit:

| Specialisté na defragmentaci | Nevýhody defragmentace |
| --- | --- |
| Umožňuje rychlejší vytváření velkých služeb. |Soustřeďuje zatížení na méně uzlů a zvyšuje kolize obsahu. |
| Umožňuje snížení přesunu dat během vytváření. |Selhání může mít dopad na další služby a způsobit více změn. |
| Umožňuje bohatě popis požadavků a recyklaci místa. |Složitější konfigurace celkového řízení prostředků |

Ve stejném clusteru můžete kombinovat defragmentované a normální metriky. Cluster Správce prostředků se snaží sloučit metriky defragmentace co nejvíc, a to při rozšiřování dalších. Výsledky kombinování defragmentace a vyrovnávání strategií závisí na několika faktorech, včetně těchto:
  - počet metrik vyrovnávání vs. počet metrik defragmentace
  - Zda kterákoli služba používá oba typy metrik 
  - váhy metriky
  - aktuální zatížení metriky
  
K určení přesné konfigurace je nutné experimentovat. Než povolíte metriky defragmentace v produkčním prostředí, doporučujeme důkladné měření vašich úloh. To platí hlavně při kombinování defragmentace a vyvážených metrik v rámci stejné služby. 

## <a name="configuring-defragmentation-metrics"></a>Konfigurace metrik defragmentace
Konfigurace metrik defragmentace je globální rozhodnutí v clusteru a jednotlivé metriky je možné vybrat k defragmentaci. Následující fragmenty kódu konfigurace ukazují, jak nakonfigurovat metriky pro defragmentaci. V tomto případě je "Metric1" nakonfigurovaný jako metrika defragmentace, zatímco "Metric2" bude i nadále vyvážené normálně. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

prostřednictvím ClusterConfig.jsv pro samostatná nasazení nebo Template.jsv případě hostovaných clusterů Azure:

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


## <a name="next-steps"></a>Další kroky
- Správce prostředků clusteru obsahuje možnosti člověka pro popis clusteru. Pokud se o nich chcete dozvědět víc, přečtěte si článek [popisující Service Fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) .
- Metriky představují způsob, jakým Správce prostředků clusteru Service Fabric spravuje spotřebu a kapacitu v clusteru. Další informace o metrikách a jejich konfiguraci najdete v [tomto článku](service-fabric-cluster-resource-manager-metrics.md) .

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
