---
title: Defragmentace metrik ve službě Azure Fabric
description: Další informace o použití defragmentace nebo balení jako strategie pro metriky v Service Fabric. Tato technika je užitečná pro velmi velké služby.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bba459be4408f4a4bc438bb33b0570a91e84f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563356"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentace metrik a zatížení v service fabric
Výchozí strategie Správce prostředků clusteru Service Fabric pro správu metrik zatížení v clusteru je distribuce zatížení. Zajištění, že uzly jsou rovnoměrně využívány vyhýbá horké a studené skvrny, které vedou k tvrzení a plýtvání prostředky. Distribuce úloh v clusteru je také nejbezpečnější z hlediska selhání, protože zajišťuje, že selhání nezabere velké procento daného pracovního vytížení. 

Správce prostředků clusteru Service Fabric podporuje jinou strategii pro správu zatížení, což je defragmentace. Defragmentace znamená, že místo pokusu o distribuci využití metriky v rámci clusteru je konsolidované. Konsolidace je pouze inverze výchozí strategie vyrovnávání – namísto minimalizace průměrné směrodatné odchylky zatížení metriky se správce prostředků clusteru pokusí zvýšit.

## <a name="when-to-use-defragmentation"></a>Kdy použít defragmentaci
Distribuce zatížení v clusteru spotřebovává některé prostředky na každém uzlu. Některé úlohy vytvářejí služby, které jsou mimořádně velké a spotřebovávají většinu nebo všechny uzly. V těchto případech je možné, že při vytváření velkých úloh není dostatek místa na žádném uzlu pro jejich spuštění. Velké úlohy nejsou problém v Service Fabric; V těchto případech Správce prostředků clusteru určí, že potřebuje reorganizovat cluster utvoří místo pro toto velké zatížení. Však do té doby, že zatížení musí čekat na plánované v clusteru.

Pokud existuje mnoho služeb a stavu se pohybovat, pak může trvat dlouhou dobu pro velké zatížení, které mají být umístěny v clusteru. To je pravděpodobnější, pokud ostatní úlohy v clusteru jsou také velké a tak trvat déle reorganizovat. Tým Service Fabric měří časy vytvoření v simulacích tohoto scénáře. Zjistili jsme, že vytváření velkých služeb trvalo mnohem déle, jakmile se využití clusteru dostalo nad 30% až 50%. Chcete-li zpracovat tento scénář, jsme zavedli defragmentace jako vyvažovací strategie. Zjistili jsme, že pro velké úlohy, zejména ty, kde byl důležitý čas vytvoření, defragmentace opravdu pomohla těmto novým úlohám naplánovat v clusteru.

Můžete nakonfigurovat defragmentace metriky mít Správce prostředků clusteru proaktivně pokusit zkondenzovat zatížení služeb do méně uzlů. To pomáhá zajistit, že je téměř vždy prostor pro velké služby bez reorganizace clusteru. Není třeba reorganizovat clusteru umožňuje vytvářet velké úlohy rychle.

Většina lidí nepotřebuje defragmentaci. Služby jsou obvykle malé, takže není těžké najít prostor pro ně v clusteru. Když je reorganizace možná, jde to rychle, opět proto, že většina služeb je malá a lze je přesunout rychle a paralelně. Nicméně, pokud máte velké služby a potřebujete je rychle vytvořit, pak defragmentace strategie je pro vás. Budeme diskutovat o kompromisy použití defragmentace další. 

## <a name="defragmentation-tradeoffs"></a>Kompromisy defragmentace
Defragmentace může zvýšit dopad selhání, protože další služby jsou spuštěny na uzlech, které se nezdaří. Defragmentace může také zvýšit náklady, protože prostředky v clusteru musí být drženy v rezervě a čeká se na vytvoření velkých úloh.

Následující diagram poskytuje vizuální reprezentaci dvou clusterů, jeden, který je defragmentován a jeden, který není. 

<center>

![Porovnání vyvážených a defragmentovaných clusterů][Image1]
</center>

Ve vyváženém případě zvažte počet pohybů, které by byly nezbytné k umístění jednoho z největších objektů služby. V defragmentovaném clusteru může být velké zatížení umístěno na uzlech čtyři nebo pět, aniž by bylo třeba čekat na další služby přesunout.

## <a name="defragmentation-pros-and-cons"></a>Defragmentace klady a zápory
Takže to, co jsou tyto další koncepční kompromisy? Zde je rychlý přehled věcí, o kterých je třeba přemýšlet:

| Defragmentace Klady | Defragmentace Nevýhody |
| --- | --- |
| Umožňuje rychlejší tvorbu velkých služeb |Soustřeďe zatížení na méně uzlů, což zvyšuje sváry |
| Umožňuje nižší pohyb dat při vytváření |Selhání může mít vliv na více služeb a způsobit více změn |
| Umožňuje bohatý popis požadavků a rekultivaci prostoru |Složitější celková konfigurace správy prostředků |

Defragmentované a normální metriky můžete kombinovat ve stejném clusteru. Správce prostředků clusteru se pokusí co nejvíce konsolidovat metriky defragmentace při rozprostření ostatních. Výsledky míchání defragmentace a vyvažování strategie závisí na několika faktorech, včetně:
  - počet vyrovnávacích metrik vs. počet metrik defragmentace
  - Zda nějaká služba používá oba typy metrik 
  - metrické váhy
  - aktuální metrický zatížení
  
K určení přesné potřebné konfigurace je nutné experimentovat. Před povolením metrik defragmentace v produkčním prostředí doporučujeme důkladné měření vašich úloh. To platí zejména při míchání defragmentace a vyvážené metriky v rámci stejné služby. 

## <a name="configuring-defragmentation-metrics"></a>Konfigurace metrik defragmentace
Konfigurace metrik defragmentace je globální rozhodnutí v clusteru a jednotlivé metriky lze vybrat pro defragmentaci. Následující fragmenty konfigurace ukazují, jak nakonfigurovat metriky pro defragmentaci. V tomto případě "Metric1" je nakonfigurován jako metrika defragmentace, zatímco "Metric2" bude i nadále vyváženy normálně. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

prostřednictvím souboru ClusterConfig.json pro samostatná nasazení nebo Template.json pro hostované clustery Azure:

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
- Správce prostředků clusteru má možnosti man pro popis clusteru. Další informace o nich naleznete v tomto článku [popisujícím cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Metriky jsou způsob, jakým je manger prostředků clusteru Service Fabric spravuje spotřebu a kapacitu v clusteru. Další informace o metrikách a jejich konfiguraci najdete v [tomto článku](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
