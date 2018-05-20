---
title: Defragmentace metriky v Azure Service Fabric | Microsoft Docs
description: Přehled použití defragmentace nebo balení jako strategie pro metriky v Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: a35ae5933729615d634359e64e31d43536d81431
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentace metriky a zatížení v Service Fabric
Správce služby Fabric clusteru prostředků výchozí strategii správy metriky zatížení v clusteru je distribuce zatížení. Zajištění, že uzly jsou rovnoměrně využité zabraňuje úrovněmi horkého a studeného body, které vést k kolizí i nevyužité prostředky. Distribuce zatížení v clusteru je také nejbezpečnější z hlediska zbývajících selhání, protože zajišťuje selhání nebude vyjměte vysoké procento daného zatížení. 

Správce prostředků clusteru Service Fabric podporuje různé strategie pro správu zatížení, která je defragmentace. Defragmentace znamená, že místo došlo k pokusu o distribuci využití metriky napříč clusterem, ho jsou konsolidovány. Konsolidace je právě inverzi výchozí vyrovnávání strategie – místo minimalizovat průměrná směrodatnou odchylku metriky zatížení, správce prostředků clusteru pokusí o zvýšit ji.

## <a name="when-to-use-defragmentation"></a>Kdy použít Defragmentace
Distribuce zatížení v clusteru spotřebuje některé prostředky na každém uzlu. Některé úlohy vytvoření služby, které jsou velmi velké a využívat většinu nebo všechny uzlu. V těchto případech je možné, že po velkých úloh získávání vytvořen, který není dostatek místa v každém uzlu, abyste je mohli spustit. Rozsáhlejší úlohy nejsou potíže s Service Fabric; v těchto případech správce prostředků clusteru určí, že potřebuje reorganizace cluster tak, aby uvolnil prostor pro velké pracovního vytížení. Do té doby této úlohy má však má systém čekat před naplánována v clusteru.

Pokud máte mnoho služeb a stavu pohyb, může to trvat dlouhou dobu pro velké zatížení umístit do clusteru. Toto je pravděpodobnější, pokud dalších zatížení v clusteru jsou velké a proto trvat delší dobu reorganizovat. Tým Service Fabric měří času vytvoření v simulace tohoto scénáře. Zjistili jsme, že trvalo déle, mnohem vytváření velké služeb, při využití clusteru získali výše 30 až 50 %. Do tohoto scénáře zavedli jsme defragmentace jako strategie vyrovnávání. Zjistili jsme, že pro větší zatížení, zejména ty, kde je důležité, defragmentace skutečně pomohly tyto nové úlohy vytváření získat naplánován v clusteru.

Můžete nakonfigurovat defragmentace metriky tak, aby měl správce prostředků clusteru pokusí proaktivně zúžit zatížení služeb do menšího počtu uzlů. To pomáhá zajistit, že je téměř vždy prostor pro velké služeb bez reorganizace clusteru. Nemusí reorganizovat clusteru umožňuje vytváření rozsáhlých úloh s rychle.

Většina lidí nepotřebují defragmentace. Služby jsou obvykle být malé, takže není vyhledání místnosti pro ně v clusteru. Když reorganizaci je možné, přejde rychle znovu protože většina služeb jsou malé a přesunout rychle a paralelně. Pokud máte velké služby a je vytvořen rychle pak strategie defragmentace potřebovat, ale je pro vás. Probereme kompromisy defragmentace další použití. 

## <a name="defragmentation-tradeoffs"></a>Defragmentace kompromisy
Defragmentace může zvýšit impactfulness chyb, protože běží další služby na uzlech, které nesplní. Defragmentace můžete taky zvýšit náklady, protože zdrojů v clusteru, musí uchovávat v rezervy, čeká na vytvoření rozsáhlejší úlohy.

Následující obrázek poskytuje vizuální reprezentace dva clustery, ten, který je defragmentovat a ten, který není. 

<center>
![Porovnání vyrovnáváním a defragmentovat clustery][Image1]
</center>

V případě vyrovnáváním zvažte počet pohybů, které by bylo nutné umístit mezi největší objekty služby. V defragmentované clusteru může být umístěny na uzly čtyř nebo pěti velké zatížení bez čekání pro jiné služby pro přesun.

## <a name="defragmentation-pros-and-cons"></a>Defragmentace výhody a nevýhody
Jaké jsou proto tyto koncepční kompromisy? Tady je rychlý tabulku dbát:

| Defragmentace specialisté | Cons Defragmentace |
| --- | --- |
| Umožňuje rychlejší vytváření velké služeb |Koncentráty načíst do menšího počtu uzlů, zvýšení kolizí |
| Umožňuje snížit přesun dat během vytváření |Selhání může mít vliv na další služby a způsobit další změny |
| Umožňuje bohaté popis požadavků a recyklace místa |Složitější celkové konfiguraci správy prostředků |

Je možné kombinovat defragmentované a normální metriky ve stejném clusteru. Správce prostředků clusteru pokusí o konsolidovat co nejvíce při šíření na jiné defragmentace metriky. Výsledky kombinování defragmentace a vyrovnávání strategie závisí na několika různými faktory, včetně:
  - počet vyrovnávání metriky oproti počet defragmentace metriky
  - Tom, zda všechny služby používá oba typy metriky 
  - metriky vah
  - načte aktuální metrika
  
Experimentování, je potřeba určit přesnou konfiguraci nezbytné. Před povolením defragmentace metriky v produkčním prostředí doporučujeme důkladné měření vašich úloh. To platí hlavně při kombinování defragmentace a vyrovnáváním metriky v rámci stejné služby. 

## <a name="configuring-defragmentation-metrics"></a>Konfigurace defragmentace metriky
Konfigurace metriky defragmentace je globální rozhodnutí v clusteru a jednotlivé metriky můžete vybrat pro defragmentaci. Následující konfigurace fragmenty kódu ukazují, jak nakonfigurovat metriky pro defragmentaci. V tomto případě "Metric1" je nakonfigurovaná jako defragmentace metriku, zatímco "Metric2" bude dále vyváženy normálně. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

pomocí souboru ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

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
- Správce prostředků clusteru má man možnosti popisující clusteru. Další informace o nich, projděte si tento článek na [popisující cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Metriky se, jak správce prostředků služby Fabric clusteru spravuje využívání a kapacity v clusteru. Další informace o metriky a způsob jejich konfigurace, podívejte se na [v tomto článku](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
