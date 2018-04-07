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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 7a775b6d23c144c81650bb3608ee6a117475a9ba
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-containers-with-log-analytics"></a>Monitorování kontejnery s analýzy protokolů
 
Tento článek popisuje kroky potřebné k nastavení kontejneru monitorování pro váš cluster. Další informace najdete v tématu [monitorování kontejnery v Service Fabric](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers). Pokud chcete zobrazit podrobný kurz na to, můžete navíc dodržíte [monitorování Windows kontejnerů v Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Nastavení monitorování řešení kontejneru

> [!NOTE]
> Je nutné mít nastavenu analýzy protokolů pro svůj cluster a také s agentem OMS nasazené na uzly. Pokud ne, postupujte podle kroků v [nastavení analýzy protokolů](service-fabric-diagnostics-oms-setup.md) a [přidat agenta OMS do clusteru s podporou](service-fabric-diagnostics-oms-agent.md) první.

1. Až nastavíte cluster s analýzy protokolů a OMS Agent, nasazení kontejnerů. Počkejte, než budete pokračovat k dalšímu kroku nasazení kontejnerů.

2. V Azure Marketplace vyhledejte *řešení monitorování kontejneru* a klikněte na **řešení monitorování kontejneru** prostředku, který se zobrazí v oddíle monitorování a správu kategorie.

    ![Přidání řešení kontejnerů](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Vytvořte řešení uvnitř ve stejném pracovním prostoru, který již byl vytvořen pro cluster. Tato změna se automaticky aktivuje agenta spustit shromažďování dat docker na kontejnery. V asi 15 minut nebo tak měli byste vidět řešení světla až s příchozí protokoly a statistiky.

## <a name="next-steps"></a>Další postup
* Další informace o kontejneru orchestration v Service Fabric - [Service Fabric a kontejnery](service-fabric-containers-overview.md)
* Získat familiarized s [vyhledávání a dotazování protokolu](../log-analytics/log-analytics-log-searches.md) funkcím poskytovaným jako součást analýzy protokolů
* Konfigurace analýzy protokolů nastavit [automatizované výstrahy](../log-analytics/log-analytics-alerts.md) pravidla, která pomáhají při zjišťování a Diagnostika