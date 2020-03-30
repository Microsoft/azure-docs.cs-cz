---
title: Určení metrik a nastavení umístění
description: Zjistěte, jak popsat službu Service Fabric zadáním metrik, omezení umístění a dalších zásad umístění.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610093"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Konfigurace nastavení správce prostředků clusteru pro služby Service Fabric
Správce prostředků clusteru Service Fabric umožňuje jemně odstupňovanou kontrolu nad pravidly, kterými se řídí každá jednotlivá pojmenovaná služba. Každá pojmenovaná služba může určit pravidla pro to, jak by měla být přidělena v clusteru. Každá pojmenovaná služba může také definovat sadu metrik, které chce nahlásit, včetně toho, jak jsou pro tuto službu důležité. Konfigurace služeb se rozdělí na tři různé úkoly:

1. Konfigurace omezení umístění
2. Konfigurace metrik
3. Konfigurace pokročilých zásad umístění a dalších pravidel (méně běžných)

## <a name="placement-constraints"></a>Omezení umístění
Omezení umístění se používají k řízení uzlů v clusteru, na kterých může služba skutečně spouštět. Obvykle konkrétní pojmenovaná instance služby nebo všechny služby daného typu omezeny na spuštění na určitém typu uzlu. Omezení umístění jsou rozšiřitelná. Můžete definovat libovolnou sadu vlastností na typ uzlu a při vytváření služeb je vybrat s omezeními. Můžete také změnit omezení umístění služby, když je spuštěna. To umožňuje reagovat na změny v clusteru nebo požadavky služby. Vlastnosti daného uzlu lze také dynamicky aktualizovat v clusteru. Další informace o omezeníumístění a jejich konfiguraci naleznete v [tomto článku](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Metriky
Metriky jsou sada prostředků, které daná pojmenovaná služba potřebuje. Konfigurace metriky služby zahrnuje, kolik z tohoto prostředku každá stavová replika nebo bezstavová instance této služby spotřebovává ve výchozím nastavení. Metriky také obsahují váhu, která označuje, jak důležité je vyvážení této metriky pro tuto službu v případě, že jsou nezbytné kompromisy.

## <a name="advanced-placement-rules"></a>Pokročilá pravidla pro umístění
Existují i jiné typy pravidel umístění, které jsou užitečné v méně běžných scénářích. Tady je několik příkladů:
- Omezení, která pomáhají s geograficky distribuovanými clustery
- Některé architektury aplikací

Ostatní pravidla umístění jsou konfigurována prostřednictvím korelace nebo zásady.

## <a name="next-steps"></a>Další kroky
- Metriky jsou způsob, jakým je manger prostředků clusteru Service Fabric spravuje spotřebu a kapacitu v clusteru. Další informace o metrikách a jejich konfiguraci najdete v [tomto článku](service-fabric-cluster-resource-manager-metrics.md)
- Spřažení je jeden režim, který můžete nakonfigurovat pro své služby. To není běžné, ale pokud ji potřebujete, můžete se o tom dozvědět [zde](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Existuje mnoho různých pravidel umístění, které lze nakonfigurovat ve službě pro zpracování dalších scénářů. O těchto různých zásadách pro umístění se dozvíte [zde](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Začněte od začátku a [získejte úvod do Správce prostředků clusteru Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Informace o tom, jak Správce prostředků clusteru spravuje a vyrovnává zatížení clusteru, naleznete v článku o [vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Další informace o nich naleznete v tomto článku [popisujícím cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
