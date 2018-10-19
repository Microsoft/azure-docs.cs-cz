---
title: Monitorování kontejnerů v Azure Service Fabric pomocí Log Analytics | Dokumentace Microsoftu
description: Použití Log Analytics pro monitorování kontejnerů, které běží na clustery Azure Service Fabric.
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
ms.openlocfilehash: aabdae370c28f8fa633372be4505c00c25254408
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49403246"
---
# <a name="monitor-containers-with-log-analytics"></a>Monitorování kontejnerů pomocí Log Analytics
 
Tento článek popisuje kroky potřebné k nastavení řešení pro monitorování kontejnerů Azure Log Analytics, chcete-li zobrazit události kontejneru. K nastavení clusteru pro shromažďování událostí v kontejneru, najdete v tomto [podrobný kurz](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Nastavit řešení pro monitorování kontejnerů

> [!NOTE]
> Je nutné mít nastavený Log Analytics pro váš cluster, stejně jako máte nasazené na uzly agenta Log Analytics. Pokud ne, postupujte podle kroků v [nastavení Log Analytics](service-fabric-diagnostics-oms-setup.md) a [přidat agenta Log Analytics pro cluster](service-fabric-diagnostics-oms-agent.md) první.

1. Jakmile je váš cluster nastavený pomocí Log Analytics a agenta Log Analytics, nasaďte kontejnery. Počkejte, kontejnerů mají být nasazeny před přechodem k dalšímu kroku.

2. Na webu Azure Marketplace vyhledejte *řešení pro monitorování kontejnerů* a klikněte na **řešení pro monitorování kontejnerů** prostředek, který se zobrazí v části monitorování a správa kategorie.

    ![Přidání řešení kontejnerů](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Vytvoření řešení uvnitř stejného pracovního prostoru, který již byl vytvořen pro cluster. Tato změna automaticky aktivuje agenta spustit shromažďování dat docker na kontejnery. Přibližně 15 minut nebo tak měli byste vidět řešení osvětlí s příchozí protokoly a statistiky, jak je znázorněno na následujícím obrázku.

    ![Řídicí panel základní Log Analytics](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agenta umožňuje výběr několika protokoly týkající se kontejnerů, které můžete dotazovat v Log Analytics, nebo použít k vizualizaci ukazatele výkonu. Typy protokolů, které byly shromážděny jsou:

* ContainerInventory: obsahuje informace o umístění kontejneru, název a imagí
* ContainerImageInventory: informace o nasazené bitové kopie, včetně ID nebo velikostí
* ContainerLog: konkrétní chybové protokoly, protokoly dockeru (stdout atd.) a další položky
* ContainerServiceLog: příkazy démona dockeru, které byly spuštěny
* Výkonu: čítače výkonu včetně kontejneru procesoru, paměti, síťového provozu, diskové vstupně-výstupní operace a vlastní metriky z hostitelských počítačů



## <a name="next-steps"></a>Další postup
* Další informace o [řešení Log Analytics kontejnerů](../log-analytics/log-analytics-containers.md).
* Další informace o orchestraci kontejnerů v Service Fabric – [Service Fabric a kontejnery](service-fabric-containers-overview.md)
* Seznamte se s [prohledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) funkce nabízená v rámci služby Log Analytics
* Nakonfigurujte Log Analytics a nastavit [automatické upozorňování](../log-analytics/log-analytics-alerts.md) pravidla, která vám pomůže se zjišťováním a diagnostikou