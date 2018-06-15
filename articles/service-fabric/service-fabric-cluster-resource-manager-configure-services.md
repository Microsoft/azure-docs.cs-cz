---
title: Zadejte nastavení metriky a umístění v Azure mikroslužeb | Microsoft Docs
description: Zadáním metriky, omezení umístění a další zásady umístění, která popisují služby Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 16e19a02bf12e30e81eb1743fc637bce435df914
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210575"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Konfigurace nastavení správce prostředků clusteru služby Service Fabric
Správce prostředků clusteru Service Fabric umožňuje jemně odstupňovanou kontrolu nad pravidla, která řídí každé jednotlivé s názvem služby. Každé pojmenované služby lze určit pravidla, jak by měla být přidělená v clusteru. Každé pojmenované služby můžete také definovat sadu metriky, které se chce k sestavě, včetně toho, jak důležité jsou k této službě. Konfigurace služeb, rozděleny do tří různých úloh:

1. Konfigurace omezení umístění
2. Konfigurace metriky
3. Konfigurace zásady rozšířené umístění a ostatní pravidla (méně běžné)

## <a name="placement-constraints"></a>Omezení umístění
Omezení umístění slouží k řízení, které uzly v clusteru služby ve skutečnosti můžete spustit na. Obvykle konkrétní s názvem instance služby nebo všechny služby daného typu omezené ke spuštění na konkrétní typ uzlu. Omezení umístění je rozšiřitelný. Můžete definovat libovolnou sadu vlastnosti na typ uzlu a potom vyberte pro ně s omezeními při vytváření služby. Můžete také změnit omezení umístění služby, když je spuštěná. To umožňuje reagovat na změny v clusteru nebo požadavky na služby. Vlastnosti daného uzlu můžete taky aktualizovat dynamicky v clusteru. Další informace o umístění omezení a způsob jejich konfigurace naleznete v [v tomto článku](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Metriky
Metriky jsou sadu prostředků, které potřebuje uvedená služba s názvem. Konfigurace metriky služby zahrnuje kolik prostředku každé stavová repliky nebo bezstavové instance této služby spotřebuje ve výchozím nastavení. Metriky taky zahrnovat váhy, která určuje, jak důležité vyrovnávání této metrika je do této služby, v případě kompromisy jsou nezbytné.

## <a name="advanced-placement-rules"></a>Pravidla pro pokročilé umístění
Existují jiné typy pravidla pro umístění, které jsou užitečné v méně běžné scénáře. Tady je několik příkladů:
- Omezení, které pomáhají s mnoha místech pracujícími clustery
- Některé architektury aplikace

Prostřednictvím korelací nebo zásady jsou nakonfigurované další pravidla pro umístění.

## <a name="next-steps"></a>Další postup
- Metriky se, jak správce prostředků služby Fabric clusteru spravuje využívání a kapacity v clusteru. Další informace o metriky a způsob jejich konfigurace, podívejte se na [v tomto článku](service-fabric-cluster-resource-manager-metrics.md)
- Spřažení je jeden režim, které můžete konfigurovat pro vaše služby. Není běžné, ale pokud to potřebujete informace o najdete ho [sem](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Existuje mnoho různých umístění pravidla, která se dá konfigurovat na služby pro zpracování další scénáře. Můžete zjistit o těchto zásadách různých umístění [sem](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Začít od začátku a [získejte Úvod do Service Fabric clusteru správce prostředků](service-fabric-cluster-resource-manager-introduction.md)
- Chcete-li zjistit, o tom, jak správce prostředků clusteru spravuje a vyrovnává zatížení v clusteru, podívejte se na článek na [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Další informace o nich, projděte si tento článek na [popisující cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
