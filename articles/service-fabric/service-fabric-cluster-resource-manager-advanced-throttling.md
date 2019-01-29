---
title: Omezování v cluster resource manager Service Fabric | Dokumentace Microsoftu
description: Naučte se konfigurovat omezení pomocí Service Fabric Cluster Resource Manager k dispozici.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 4c86655b650464f1debadab35fdd82611d17ad81
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55092335"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Omezení šířky pásma Service Fabric Cluster Resource Manageru
I v případě, že jste správně nakonfigurovali Cluster Resource Manager, můžete získat přerušit clusteru. Například může být současně uzlu a odolnost domény selhání – co by mohlo dojít, pokud, ke kterým došlo během upgradu? Cluster Resource Manageru se vždy pokusí opravit vše, využívání prostředků clusteru změnit uspořádání a opravy clusteru. Omezení pomáhají backstop tak, aby cluster můžete používat prostředky na stabilizaci - uzly se vraťte, rozdělení sítě opravy, nasadí opravený bitů.

Smyslem těchto řadu situací, Service Fabric Cluster Resource Manager obsahuje několik omezení. Tato omezení jsou všechny hammers docela velké. Obecně se nesmí změnit bez pečlivé plánování a testování.

Pokud změníte omezení Cluster Resource Manageru, by měl naladit skutečné očekávané zatížení. Můžete určit, že je potřeba mít několik omezení na místě, i v případě, znamená to, že cluster trvá déle stabilizovat v některých situacích. Testování je potřeba určit správné hodnoty pro omezení. Omezení musí být dostatečně vysoká, aby umožnit, aby cluster reakce na změny v přiměřené době a dostatečně nízko, aby se zabránilo ve skutečnosti příliš velkému využití prostředků. 

Ve většině případů zaznamenali jsme zákazníkům použít omezení, které bylo, protože byl již v prostředí omezené prostředků. Několik příkladů by byla omezena šířka pásma sítě pro jednotlivé uzly nebo disky, které nejsou možné sestavit mnoho stavových replik paralelně vzhledem k omezením propustnosti. Bez omezení může operace zahlcovat tyto prostředky, způsobí operace selhat nebo být pomalé. V těchto situacích zákazníci používají omezení a věděl, že bylo rozšíření množství času by to obnášelo clusteru dosáhnout stabilního stavu. Zákazníci také rozumí, že můžou být nakonec spuštěnou na nižší celkovou spolehlivost byly omezené.


## <a name="configuring-the-throttles"></a>Konfigurace omezení

Service Fabric má dva mechanismy pro omezení počtu pohybů plb typu repliky. Výchozí mechanismus, který existoval Service Fabric 5.7 představuje omezení jako absolutní počet přesune povolené. To nebude fungovat pro clustery všech velikostí. Zejména u velkých clusterech výchozí hodnota může být příliš malá, výrazně se zpomalení vyrovnávání i v případě, že je nezbytné, při nemají žádný vliv ve menší clustery. Tento mechanismus předchozí bylo nahrazeno založenou na procentech omezení šířky pásma, která škáluje líp s dynamické clustery, ve kterých počet služeb a uzlů pravidelně měnit.

Omezení jsou založeny na procento počet replik v clusterech. Povolit omezení procento na základě vyjádření pravidlo: "nelze přesunout více než 10 % repliky v intervalu 10 minut", např.

Nastavení konfigurace pro založenou na procentech omezení jsou:

  - GlobalMovementThrottleThresholdPercentage – maximální počet pohybů plb typu v clusteru povolené v okamžiku, vyjádřené jako procentní podíl celkového počtu replik v clusteru. Hodnota 0 znamená bez omezení. Výchozí hodnota je 0. Pokud toto nastavení a GlobalMovementThrottleThreshold nejsou zadány, použije se konzervativnější limit.
  - GlobalMovementThrottleThresholdPercentageForPlacement – maximální počet pohybů plb typu povolené během fáze umístění vyjádřené jako procentní podíl celkového počtu replik v clusteru. Hodnota 0 znamená bez omezení. Výchozí hodnota je 0. Pokud toto nastavení a GlobalMovementThrottleThresholdForPlacement nejsou zadány, použije se konzervativnější limit.
  - GlobalMovementThrottleThresholdPercentageForBalancing – maximální počet pohybů plb typu povolené vyrovnávání fázi, vyjádřené jako procentní podíl celkového počtu replik v clusteru. Hodnota 0 znamená bez omezení. Výchozí hodnota je 0. Pokud toto nastavení a GlobalMovementThrottleThresholdForBalancing nejsou zadány, použije se konzervativnější limit.

Při zadávání procento omezení, zadejte jako 0,05 5 %. Interval, ve kterém se řídí těchto omezení je GlobalMovementThrottleCountingInterval, které se zadávají v sekundách.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

prostřednictvím ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

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

### <a name="default-count-based-throttles"></a>Výchozí omezení podle počtu
Tyto informace jsou poskytnuté pro případ máte starší clustery nebo stále zachovat tyto konfigurace v clusterech, které od té doby byly upgradovány. Obecně se doporučuje, ty jsou nahrazeny výše uvedené omezení založenou na procentech. Protože založenou na procentech omezení je ve výchozím nastavení zakázané, zůstanou tato omezení výchozí omezení pro cluster, dokud jsou zakázány a nahradí omezení založenou na procentech. 

  - GlobalMovementThrottleThreshold – toto nastavení určuje celkový počet pohybů plb typu v clusteru nějakou dobu. Množství času se zadávají v sekundách, jako GlobalMovementThrottleCountingInterval. Výchozí hodnota GlobalMovementThrottleThreshold je 1000 a výchozí hodnota GlobalMovementThrottleCountingInterval je 600.
  - MovementPerPartitionThrottleThreshold – toto nastavení určuje celkový počet pohybů plb typu pro libovolný oddíl služby nějakou dobu. Množství času se zadávají v sekundách, jako MovementPerPartitionThrottleCountingInterval. Výchozí hodnota MovementPerPartitionThrottleThreshold je 50 a výchozí hodnota MovementPerPartitionThrottleCountingInterval je 600.

Konfiguraci těchto omezení používá stejný vzor jako založenou na procentech omezování.

## <a name="next-steps"></a>Další postup
- Přečtěte si o tom, jak Cluster Resource Manager spravuje a vyrovnává zatížení v clusteru, přečtěte si článek na [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)
- Cluster Resource Manager má mnoho možností pro popis clusteru. Další informace o nich najdete v tomto článku na [popisující cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
