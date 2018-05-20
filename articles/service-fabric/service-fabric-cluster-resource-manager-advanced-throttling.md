---
title: Omezení šířky pásma ve Správci prostředků clusteru Service Fabric | Microsoft Docs
description: Informace o konfiguraci omezení poskytuje služby infrastruktury clusteru správce prostředků.
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
ms.openlocfilehash: e9db1070066a2a02b72b5cc051e59d8b04dc9928
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Omezení správce prostředků clusteru Service Fabric
I v případě, že správce prostředků clusteru jste správně nakonfigurovaný, můžete získat dojde k narušení clusteru. Například může být současně uzel a selhání domény selhání - co by mohlo dojít, pokud, ke kterým došlo během upgradu? Správce prostředků clusteru se vždy pokusí opravit vše, využívání prostředků clusteru pokusu reorganizovat a opravte clusteru. Omezení zajišťuje backstop tak, aby cluster může používat prostředky na stabilizaci – uzly vraťte retušovat síťové oddíly, nasadí opravené bits.

K usnadnění pomocí těchto nastavení neovlivní situacích, správce prostředků clusteru Service Fabric obsahuje několik omezení. Tato omezení jsou všechny bourací kladiva poměrně velké. Obecně se nesmí změnit bez pečlivé plánování a testování.

Pokud změníte omezení správce prostředků clusteru, by měl naladění očekávané skutečné zatížení. Můžete určit, že je potřeba mít některé omezení na místě, i v případě, znamená to, že cluster trvá déle stabilizovat v některých situacích. Testování je potřeba určit správné hodnoty pro omezení. Omezení musí být dostatečně vysoká, aby umožnit, aby cluster reagovat na změny v přiměřené době a nízké, aby se zabránilo ve skutečnosti příliš mnoho spotřeby prostředků. 

Ve většině případů jsme viděli zákazníkům použít omezení, byla úspěšně, protože již byly v prostředí omezené prostředků. Příkladem může být omezena šířka pásma sítě pro jednotlivé uzly nebo disky, které nejsou možné sestavit mnoho stavových repliky paralelně z důvodu omezení propustnosti. Bez omezení může operace zahlcovat těchto prostředků, způsobuje operací selhat nebo být pomalé. V těchto situacích zákazníci používají omezení a věděl, že se měla rozšíření množství času, by byly třeba clusteru dosáhnout stabilního stavu. Zákazníci také za to, že se může stát, že běží na nižší celkovou spolehlivost, zatímco se byly omezeny.


## <a name="configuring-the-throttles"></a>Konfigurace omezení

Service Fabric má dva mechanismy pro omezení počtu pohybů typu repliky. Výchozí mechanismus, které existovaly před Service Fabric 5.7 představuje omezení jako absolutní počet přesune povoleny. Není to funguje pro clustery všech velikostí. Zejména u velkých clusterů výchozí hodnota může být příliš malá, výrazně zpomalení vyrovnávání i v případě, že je to nutné, přitom má neplatí v menší clustery. Tento mechanismus předchozí byla nahrazena na základě procenta omezení, která škáluje líp s dynamické clustery, ve kterých počet služeb a uzly pravidelně měnit.

Omezení jsou založené na procento počet replik v clusterech. Povolit omezení Percetage na základě vyjádření pravidlo: "nepřesouvejte více než 10 % repliky v intervalu 10 minut", například.

Nastavení konfigurace na základě procenta omezení jsou:

  - GlobalMovementThrottleThresholdPercentage – maximální počet pohybů v clusteru povolené kdykoli, vyjádřené jako procento celkového počtu replik v clusteru. 0 znamená bez omezení. Výchozí hodnota je 0. Pokud toto nastavení a GlobalMovementThrottleThreshold jsou nastaveny, se používá více konzervativní limit.
  - GlobalMovementThrottleThresholdPercentageForPlacement – maximální počet pohybů povolené během fáze umístění, vyjádřené jako procento celkového počtu replik v clusteru. 0 znamená bez omezení. Výchozí hodnota je 0. Pokud toto nastavení a GlobalMovementThrottleThresholdForPlacement jsou nastaveny, se používá více konzervativní limit.
  - GlobalMovementThrottleThresholdPercentageForBalancing – maximální počet pohybů povolené vyrovnávání fázi, vyjádřené jako procento celkového počtu replik v clusteru. 0 znamená bez omezení. Výchozí hodnota je 0. Pokud toto nastavení a GlobalMovementThrottleThresholdForBalancing jsou nastaveny, se používá více konzervativní limit.

Při zadávání procento omezení, zadáte jako hodnotu 0,05 5 %. Interval, ve kterém se řídí těchto omezení je GlobalMovementThrottleCountingInterval, které se určuje v sekundách.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

pomocí souboru ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

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

### <a name="default-count-based-throttles"></a>Výchozí počet na základě omezení
Tyto informace jsou poskytovány v případě, že máte starší clustery nebo zachovat přitom tyto konfigurace clusterů, které od té doby byly upgradovány. Obecně se doporučuje, ty jsou nahrazeny výše na základě procenta omezení. Vzhledem k tomu, že ve výchozím nastavení vypnutá omezování na základě procenta, zůstanou tato omezení výchozí omezení pro cluster s podporou, dokud jsou zakázána a nahradí omezení na základě procenta. 

  - GlobalMovementThrottleThreshold – toto nastavení určuje celkový počet pohybů v clusteru delší dobu. Množství času se zadávají v sekundách, jako GlobalMovementThrottleCountingInterval. Výchozí hodnota GlobalMovementThrottleThreshold je 1000 a výchozí hodnota GlobalMovementThrottleCountingInterval je 600.
  - MovementPerPartitionThrottleThreshold – toto nastavení určuje celkový počet pohybů pro všechny služby oddíl delší dobu. Množství času se zadávají v sekundách, jako MovementPerPartitionThrottleCountingInterval. Výchozí hodnota MovementPerPartitionThrottleThreshold je 50 a výchozí hodnota MovementPerPartitionThrottleCountingInterval je 600.

Konfiguraci těchto omezení následující stejné jako omezení na základě procenta.

## <a name="next-steps"></a>Další postup
- Chcete-li zjistit, o tom, jak správce prostředků clusteru spravuje a vyrovnává zatížení v clusteru, podívejte se na článek na [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Další informace o nich, projděte si tento článek na [popisující cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
