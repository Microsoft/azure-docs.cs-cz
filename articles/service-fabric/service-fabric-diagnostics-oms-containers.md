---
title: Monitorování kontejnerů v Azure Service Fabric s Log Analytics | Microsoft Docs
description: Pomocí analýzy protokolů pro monitorování kontejnery systémem Azure Service Fabric clustery.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 79d30a47b017379107b63b0006a35534f68c43b9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="monitor-containers-with-log-analytics"></a>Monitorování kontejnery s analýzy protokolů
 
Tento článek popisuje kroky potřebné k nastavení analýzy protokolů OMS kontejneru řešení monitorování, chcete-li zobrazit události kontejneru. Cluster pro shromažďování událostí v kontejneru, naleznete v tématu to [podrobný kurz](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Nastavení monitorování řešení kontejneru

> [!NOTE]
> Je nutné mít nastavenu analýzy protokolů pro svůj cluster a také s agentem OMS nasazené na uzly. Pokud ne, postupujte podle kroků v [nastavení analýzy protokolů](service-fabric-diagnostics-oms-setup.md) a [přidat agenta OMS do clusteru s podporou](service-fabric-diagnostics-oms-agent.md) první.

1. Až nastavíte cluster s analýzy protokolů a OMS Agent, nasazení kontejnerů. Počkejte, než budete pokračovat k dalšímu kroku nasazení kontejnerů.

2. V Azure Marketplace vyhledejte *řešení monitorování kontejneru* a klikněte na **řešení monitorování kontejneru** prostředku, který se zobrazí v oddíle monitorování a správu kategorie.

    ![Přidání řešení kontejnerů](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Vytvořte řešení uvnitř ve stejném pracovním prostoru, který již byl vytvořen pro cluster. Tato změna se automaticky aktivuje agenta spustit shromažďování dat docker na kontejnery. V asi 15 minut nebo tak měli byste vidět řešení světla až s příchozí protokoly a statistiky. jak je znázorněno na obrázku níže.

    ![Řídicí panel základní OMS](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agenta umožňuje kolekce několik specifické pro kontejner protokoly, které mohou být dotazována v OMS, nebo použít k ukazatele vizualizovaných výkonu. Typy protokolu, které byly shromážděny jsou:

* ContainerInventory: obsahuje informace o umístění kontejneru, název a obrázků
* ContainerImageInventory: informace o nasazené bitové kopie, včetně ID nebo velikosti
* ContainerLog: specifické chybové protokoly, protokoly docker (stdout atd.) a ostatní položky
* ContainerServiceLog: docker démon příkazy, které byly spuštěny
* Výkonu: čítače včetně kontejneru vstupně-výstupních operací a vlastní metriky z hostitelských počítačích disku procesoru, paměti, síťového provozu,



## <a name="next-steps"></a>Další postup
* Další informace o [řešení kontejnery na OMS](../log-analytics/log-analytics-containers.md).
* Další informace o kontejneru orchestration v Service Fabric - [Service Fabric a kontejnery](service-fabric-containers-overview.md)
* Získat familiarized s [vyhledávání a dotazování protokolu](../log-analytics/log-analytics-log-searches.md) funkcím poskytovaným jako součást analýzy protokolů
* Konfigurace analýzy protokolů nastavit [automatizované výstrahy](../log-analytics/log-analytics-alerts.md) pravidla, která pomáhají při zjišťování a Diagnostika