---
title: Zadejte nastavení metriky a umístění v Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak k popisu služby Service Fabric zadáním jiné zásady umístění, metriky a omezení umístění.
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
ms.openlocfilehash: ea781b5dbbf09c0a21fbcf781ae129295d02dbad
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054787"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Konfigurace nastavení správce prostředků clusteru služby Service Fabric
Service Fabric Cluster Resource Manager umožňuje velice přesně kontrolovat, pravidla, kterými se řídí každé jednotlivé s názvem služby. Každá služba s názvem můžete určit pravidla pro jak by měly být přiděleny v clusteru. Každá služba s názvem, můžete také definovat sadu metriky, které chce do sestavy, včetně jak důležité jsou na tuto službu. Konfigurace služeb rozdělí do tří různých úloh:

1. Konfigurace omezení umístění
2. Konfigurace metrik
3. Konfigurace zásady rozšířené umístění a dalších pravidel (méně běžné)

## <a name="placement-constraints"></a>Omezení umístění
Omezení umístění umožňují řídit, které uzly v clusteru služby můžete spouštět ve skutečnosti. Konkrétní se obvykle označuje jako instance služby nebo všech služeb daného typu s omezením spouštět na konkrétním typu uzlu. Omezení umístění je rozšiřitelné. Můžete definovat libovolnou sadu vlastností pro jednotlivé typ uzlu a pak vyberte pro ně omezení při vytváření služby. Můžete také změnit omezení umístění služby během jejího běhu. To umožňuje reagovat na změny v clusteru nebo požadavky na služby. Vlastnosti daný uzel můžete taky aktualizovat dynamicky v clusteru. Další informace o omezení umístění a jejich konfiguraci najdete v [v tomto článku](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Metriky
Metriky jsou sadu prostředků, které jsou v dané s názvem služby. Konfigurace metrik služby zahrnuje jak velká část tohoto prostředku jednotlivých replik stavových nebo bezstavových instance této služby spotřebovávají ve výchozím nastavení. Metriky zahrnují také váhu, která určuje, jak důležité je vyrovnávání tuto metriku na tuto službu, v případě kompromisy jsou nezbytné.

## <a name="advanced-placement-rules"></a>Pravidla pro pokročilé umístění
Existují jiné druhy pravidla pro umístění, které jsou užitečné v méně běžné scénáře. Tady je několik příkladů:
- Omezení, které pomáhají s geograficky oddělenými clustery
- Některé architektury aplikace

Další pravidla pro umístění se konfigurují prostřednictvím korelace nebo zásad.

## <a name="next-steps"></a>Další postup
- Metriky se, jak spravuje správce prostředků Service Fabric Cluster využití a kapacitu v clusteru. Další informace o metrikách a způsob jejich konfigurace, podívejte se na [v tomto článku](service-fabric-cluster-resource-manager-metrics.md)
- Spřažení je jeden režim, které můžete nakonfigurovat pro služby. Není běžné, ale pokud ho potřebujete informace o jeho [zde](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Existují pravidla pro mnoho různých umístění, které lze konfigurovat pro vaši službu ke zpracování další scénáře. Můžete získat informace o těchto různých umístění zásad [zde](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Začít od začátku a [Úvod do Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Přečtěte si o tom, jak Cluster Resource Manager spravuje a vyrovnává zatížení v clusteru, přečtěte si článek na [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)
- Cluster Resource Manager má mnoho možností pro popis clusteru. Další informace o nich najdete v tomto článku na [popisující cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
