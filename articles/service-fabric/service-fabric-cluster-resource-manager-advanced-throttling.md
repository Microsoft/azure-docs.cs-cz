---
title: Omezení ve Správci prostředků clusteru Service Fabric
description: Naučte se konfigurovat omezení poskytovaná Správcem prostředků clusteru Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b4d78b339bab02b5c44a31939e0da769dc21c3ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452161"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Omezení Správce prostředků clusteru prostředků infrastruktury prostředků služby
I v případě, že jste správně nakonfigurovali Správce prostředků clusteru, cluster může být narušen. Například může existovat souběžné selhání uzlu a domény selhání - co by se stalo, kdyby k tomu došlo během upgradu? Správce prostředků clusteru se vždy snaží vše opravit a spotřebovává prostředky clusteru, které se pokoušejí změnit uspořádání a opravu clusteru. Omezení pomáhají poskytnout backstop tak, aby cluster umoci prostředky ke stabilizaci - uzly vrátit, síťové oddíly léčit, opravené bity nasadit.

Chcete-li pomoci s těmito druhy situací, Service Fabric Cluster Resource Manager obsahuje několik omezení. Tyto škrticí klapky jsou poměrně velké kladiva. Obecně by neměly být změněny bez pečlivého plánování a testování.

Pokud změníte omezení Správce prostředků clusteru, měli byste je naladit na očekávané skutečné zatížení. Můžete určit, že potřebujete mít některé omezení na místě, i když to znamená, že clustertrvá déle stabilizovat v některých situacích. Testování je nutné určit správné hodnoty pro škrticí klapky. Omezení musí být dostatečně vysoká, aby clusteru reagovat na změny v přiměřené množství času a dostatečně nízká, aby skutečně zabránit příliš mnoho spotřeby prostředků. 

Většinu času jsme viděli zákazníci používají škrticí klapky to bylo proto, že již byly v prostředí s omezenými prostředky. Některé příklady by omezené šířky pásma sítě pro jednotlivé uzly nebo disky, které nejsou schopny vytvářet mnoho stavových replik paralelně z důvodu omezení propustnost. Bez omezení by operace mohly tyto prostředky zahltit, což by způsobilo selhání nebo zpomalení operací. V těchto situacích zákazníci používali omezení a věděli, že prodlužují dobu, po kterou bude clusteru trvat, než dosáhne stabilního stavu. Zákazníci také pochopili, že by mohli skončit na nižší celkové spolehlivosti, zatímco oni byli omezeni.


## <a name="configuring-the-throttles"></a>Konfigurace omezení

Service Fabric má dva mechanismy pro omezení počtu pohybů repliky. Výchozí mechanismus, který existoval před Service Fabric 5.7 představuje omezení jako absolutní počet tahů povoleno. To nefunguje pro clustery všech velikostí. Zejména pro velké clustery výchozí hodnota může být příliš malá, výrazně zpomaluje vyrovnávání i v případě, že je to nezbytné, přičemž nemá žádný vliv v menších clusterech. Tento předchozí mechanismus byl nahrazen omezením založeným na procentech, které se lépe škáluje s dynamickými clustery, ve kterých se počet služeb a uzlů pravidelně mění.

Omezení jsou založeny na procento z počtu replik v clusterech. Omezení založená na procentech umožňují vyjádřit pravidlo: "Nepřesunujte více než 10 % replik v intervalu 10 minut".

Nastavení konfigurace pro omezení na základě procenta jsou:

  - GlobalMovementThrottleThresholdPercentage - Maximální počet pohybů povolených v clusteru kdykoli, vyjádřený jako procento z celkového počtu replik v clusteru. 0 označuje žádné omezení. Výchozí hodnota je 0. Pokud jsou zadány obě toto nastavení a GlobalMovementThrottleThreshold, pak se použije konzervativnější limit.
  - GlobalMovementThrottleThresholdThresholdForPlacement - Maximální počet pohybů povolených během fáze umístění, vyjádřený jako procento z celkového počtu replik v clusteru. 0 označuje žádné omezení. Výchozí hodnota je 0. Pokud jsou zadány obě toto nastavení a GlobalMovementThrottleThresholdForPlacement, pak se použije konzervativnější limit.
  - GlobalMovementThrottleThresholdThresholdForBalancing - Maximální počet pohybů povolených během fáze vyrovnávání, vyjádřený jako procento z celkového počtu replik v clusteru. 0 označuje žádné omezení. Výchozí hodnota je 0. Pokud jsou zadány obě toto nastavení a GlobalMovementThrottleThresholdForBalancing, pak se použije konzervativnější limit.

Při zadávání procenta omezení byste zadali 5 % jako 0,05. Interval, na kterém jsou tyto škrticí klapky řídí globalMovementThrottleCountingInterval, který je určen v sekundách.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

prostřednictvím souboru ClusterConfig.json pro samostatná nasazení nebo Template.json pro hostované clustery Azure:

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

### <a name="default-count-based-throttles"></a>Výchozí omezení založená na počtu
Tyto informace jsou poskytovány v případě, že máte starší clustery nebo stále zachovat tyto konfigurace v clusterech, které byly od té doby inovovány. Obecně se doporučuje, aby byly nahrazeny výše uvedenými škrticími klapkami na základě procenta. Vzhledem k tomu, že omezení na základě procenta je ve výchozím nastavení zakázáno, tyto omezení zůstávají výchozí omezení pro cluster, dokud nejsou zakázány a nahrazeny omezeními založenými na procentu. 

  - GlobalMovementThrottleThreshold – toto nastavení řídí celkový počet pohybů v clusteru v průběhu času. Doba je určena v sekundách jako GlobalMovementThrottleCountingInterval. Výchozí hodnota pro GlobalMovementThrottleThreshold je 1000 a výchozí hodnota pro GlobalMovementThrottleCountingInterval je 600.
  - MovementPerPartitionThrottleThreshold – toto nastavení řídí celkový počet pohybů pro jakýkoli oddíl služby v průběhu času. Množství času je zadáno v sekundách jako MovementPerPartitionThrottleCountingInterval. Výchozí hodnota pro Hodnotu MovementPerPartitionThrottleThreshold je 50 a výchozí hodnota pro MovementPerPartitionThrottleCountingInterval je 600.

Konfigurace pro tyto omezení následuje stejný vzor jako omezení na základě procenta.

## <a name="next-steps"></a>Další kroky
- Informace o tom, jak Správce prostředků clusteru spravuje a vyrovnává zatížení clusteru, naleznete v článku o [vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Další informace o nich naleznete v tomto článku [popisujícím cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
