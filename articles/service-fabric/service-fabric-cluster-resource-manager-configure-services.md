---
title: Zadat metriky a nastavení umístění
description: Přečtěte si, jak popsat Service Fabric službu zadáním metrik, omezení umístění a dalších zásad umístění.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75610093"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Konfigurace nastavení cluster Resource Manageru pro Service Fabric Services
Cluster Service Fabric Správce prostředků umožňuje jemně odstupňovanou kontrolu nad pravidly, která řídí každou jednotlivou pojmenovanou službu. Každá pojmenovaná služba může určovat pravidla, jak by měla být přidělena v clusteru. Každá pojmenovaná služba může také definovat sadu metrik, které chce vykázat, včetně důležitosti této služby. Konfigurace služeb se rozdělí na tři různé úlohy:

1. Konfigurace omezení umístění
2. Konfigurace metrik
3. Konfigurace pokročilých zásad umístění a dalších pravidel (méně časté)

## <a name="placement-constraints"></a>Omezení umístění
Omezení umístění slouží k řízení, na kterých uzlech v clusteru můžete službu skutečně spustit. Obvykle je konkrétní pojmenovaná instance služby nebo všechny služby daného typu omezené tak, aby běžely na konkrétním typu uzlu. Omezení umístění jsou rozšiřitelná. Můžete definovat libovolnou sadu vlastností na typ uzlu a pak pro ně vybrat omezení při vytváření služeb. Můžete také změnit omezení umístění služby, pokud je spuštěná. To vám umožní reagovat na změny v clusteru nebo požadavky služby. Vlastnosti daného uzlu lze také dynamicky aktualizovat v clusteru. Další informace o omezeních umístění a způsobu jejich konfigurace najdete v [tomto článku](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) .

## <a name="metrics"></a>Metriky
Metriky představují sadu prostředků, které pojmenovaná služba potřebuje. Konfigurace metriky služby zahrnuje, kolik prostředků má každá stavová replika nebo stavová instance této služby ve výchozím nastavení spotřebovat. Metriky obsahují také váhu, která indikuje, jak důležité je vyrovnávání metriky, která je pro danou službu, v případě potřeby kompromisů.

## <a name="advanced-placement-rules"></a>Pokročilá pravidla umístění
Existují i jiné typy pravidel umístění, které jsou užitečné v méně běžných scénářích. Tady je několik příkladů:
- Omezení, která vám pomůžou s geograficky distribuovanými clustery
- Určité architektury aplikace

Další pravidla umístění se konfigurují buď pomocí korelace, nebo zásad.

## <a name="next-steps"></a>Další kroky
- Metriky představují způsob, jakým Správce prostředků clusteru Service Fabric spravuje spotřebu a kapacitu v clusteru. Další informace o metrikách a jejich konfiguraci najdete v [tomto článku](service-fabric-cluster-resource-manager-metrics.md) .
- Spřažení je jeden režim, který můžete pro své služby nakonfigurovat. Nejedná se o běžné, ale pokud ho potřebujete, můžete [o něm získat](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) Další informace.
- Existuje mnoho různých pravidel pro umístění, které můžete ve vaší službě nakonfigurovat pro zpracování dalších scénářů. Informace o těchto různých zásadách umístění najdete [tady](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) .
- Začněte od začátku a [Získejte Úvod do clusteru Service Fabric správce prostředků](service-fabric-cluster-resource-manager-introduction.md)
- Informace o tom, jak cluster Správce prostředků spravuje a vyrovnává zatížení v clusteru, najdete v článku o [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md) .
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Pokud se o nich chcete dozvědět víc, přečtěte si článek [popisující Service Fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) .
