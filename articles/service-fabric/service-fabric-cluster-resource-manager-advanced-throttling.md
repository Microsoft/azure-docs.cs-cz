---
title: Omezení ve Správci prostředků clusteru Service Fabric
description: Naučte se konfigurovat omezení poskytovaná Správce prostředků clusteru Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b4d78b339bab02b5c44a31939e0da769dc21c3ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75452161"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Omezování Service Fabric clusteru Správce prostředků
I v případě, že cluster Správce prostředků správně nakonfigurovaný, může se cluster přerušit. Může se například jednat o současný počet selhání uzlů a domén selhání – co se stane, když během upgradu došlo k tomu? Cluster Správce prostředků se vždy snaží opravit vše a spotřebovávat prostředky clusteru, které se pokoušejí znovu uspořádat a opravit cluster. Omezení poskytují možnost zpětného zastavení, aby cluster mohl použít prostředky ke stabilizaci – uzly se vrátí. síťové oddíly zacelené a opravené bity se nasazují.

Aby bylo možné s těmito typy situací pomáhat, Cluster Service Fabric Správce prostředků zahrnuje několik omezení. Tato omezení jsou všechna poměrně velká kladiva. Obvykle by se neměly měnit bez pečlivého plánování a testování.

Pokud změníte omezení Správce prostředků clusteru, měli byste je ladit na očekávané skutečné zatížení. Můžete určit, že je nutné mít nějaká omezení, i když to znamená, že cluster trvá v některých situacích déle. K určení správných hodnot omezení se vyžaduje testování. Omezení musí být dostatečně vysoká, aby cluster mohl reagovat na změny v rozumné době a dostatečně malý, aby nedocházelo k příliš velkému množství spotřeby prostředků. 

Ve většině případů jsme zákazníkům viděli omezení, protože byly již v prostředí s omezeným využitím prostředků. V některých příkladech by byla omezená šířka pásma sítě pro jednotlivé uzly, nebo disky, které nemůžou sestavovat mnoho stavových replik paralelně v důsledku omezení propustnosti. Bez omezení můžou operace způsobit zahlcení těchto prostředků, což způsobí selhání operací nebo zpomalení. V těchto situacích zákazníci použili omezení a věděli, že prodlouží dobu, po kterou by cluster mohl dosáhnout stabilního stavu. Zákazníci také rozumí, že by mohli ukončit provoz při nižší celkové spolehlivosti, zatímco byly omezeny.


## <a name="configuring-the-throttles"></a>Konfigurace omezení

Service Fabric má dva mechanismy omezení počtu pohybů repliky. Výchozí mechanismus, který existoval před Service Fabric 5,7 představuje omezení jako absolutní počet povolených přesunutí. To nefunguje u clusterů všech velikostí. Zejména u velkých clusterů může být výchozí hodnota příliš malá, což výrazně zpomaluje vyvážení, i když je to nutné, a to bez vlivu na menší clustery. Tento předchozí mechanismus byl nahrazen omezením založeném na procentech, které se lépe škálují dynamickými clustery, ve kterých se počet služeb a uzlů pravidelně mění.

Omezení vycházejí z procenta počtu replik v clusterech. Omezení na základě procenta povolit vyjádření pravidla: "Nepřesouvat více než 10% replik v intervalu 10 minut", například.

Nastavení konfigurace pro omezení na základě procenta:

  - GlobalMovementThrottleThresholdPercentage – maximální počet pohybů povolených v clusteru kdykoli, vyjádřený jako procento celkového počtu replik v clusteru. 0 označuje bez omezení. Výchozí hodnota je 0. Pokud je zadáno jak toto nastavení, tak GlobalMovementThrottleThreshold, použije se větší konzervativní limit.
  - GlobalMovementThrottleThresholdPercentageForPlacement – maximální počet pohybů povolený během fáze umístění, vyjádřený jako procento celkového počtu replik v clusteru. 0 označuje bez omezení. Výchozí hodnota je 0. Pokud je zadáno jak toto nastavení, tak GlobalMovementThrottleThresholdForPlacement, použije se větší konzervativní limit.
  - GlobalMovementThrottleThresholdPercentageForBalancing – maximální počet pohybů povolený během fáze vyrovnávání, vyjádřené jako procento celkového počtu replik v clusteru. 0 označuje bez omezení. Výchozí hodnota je 0. Pokud je zadáno jak toto nastavení, tak GlobalMovementThrottleThresholdForBalancing, použije se větší konzervativní limit.

Když zadáte procento omezení, zadáte 5% jako 0,05. Interval, ve kterém se tato omezení řídí, je GlobalMovementThrottleCountingInterval, který je zadaný v sekundách.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

prostřednictvím ClusterConfig.jsv pro samostatná nasazení nebo Template.jsv případě hostovaných clusterů Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Výchozí omezení na základě počtu
Tyto informace jsou k dispozici v případě, že máte starší clustery nebo tyto konfigurace v clusterech, které byly upgradovány, zůstanou zachované. Obecně se doporučuje, aby se tyto míry nahradily výše uvedenými omezeními na základě procenta. Vzhledem k tomu, že omezování založené na procentech je ve výchozím nastavení zakázané, zůstanou tato omezení výchozím omezením pro cluster, dokud nejsou zakázaná a nahrazená omezeními založenými na procentech. 

  - GlobalMovementThrottleThreshold – toto nastavení řídí celkový počet přesunů v clusteru v určitou dobu. Doba je určena v sekundách jako GlobalMovementThrottleCountingInterval. Výchozí hodnota pro GlobalMovementThrottleThreshold je 1000 a výchozí hodnota pro GlobalMovementThrottleCountingInterval je 600.
  - MovementPerPartitionThrottleThreshold – toto nastavení řídí celkový počet přesunů pro libovolný oddíl služby za chvíli. Doba je určena v sekundách jako MovementPerPartitionThrottleCountingInterval. Výchozí hodnota pro MovementPerPartitionThrottleThreshold je 50 a výchozí hodnota pro MovementPerPartitionThrottleCountingInterval je 600.

Konfigurace pro tato omezení následuje po stejném vzoru jako omezování založené na procentech.

## <a name="next-steps"></a>Další kroky
- Informace o tom, jak cluster Správce prostředků spravuje a vyrovnává zatížení v clusteru, najdete v článku o [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md) .
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Pokud se o nich chcete dozvědět víc, přečtěte si článek [popisující Service Fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) .
