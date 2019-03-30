---
title: Monitorování kontejnerů v Azure Service Fabric s využitím protokolů Azure Monitor | Dokumentace Microsoftu
description: Použití protokolů Azure Monitor pro monitorování kontejnerů, které běží na clustery Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: d03d68560502821b9c343be983d9f7b5a83ed977
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663179"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitorování kontejnerů s protokoly Azure monitoru
 
Tento článek popisuje kroky potřebné k nastavení řešení pro monitorování kontejnerů protokoly Azure monitoru, chcete-li zobrazit události kontejneru. K nastavení clusteru pro shromažďování událostí v kontejneru, najdete v tomto [podrobný kurz](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Nastavit řešení pro monitorování kontejnerů

> [!NOTE]
> Musíte mít Azure Monitor protokoly nastavení pro váš cluster také mít nasazené na uzly agenta Log Analytics. Pokud ne, postupujte podle kroků v [nastavení protokoly Azure monitoru](service-fabric-diagnostics-oms-setup.md) a [přidat agenta Log Analytics pro cluster](service-fabric-diagnostics-oms-agent.md) první.

1. Jakmile je váš cluster nastavený pomocí agenta Log Analytics a Azure Monitor protokolování, nasaďte kontejnery. Počkejte, kontejnerů mají být nasazeny před přechodem k dalšímu kroku.

2. Na webu Azure Marketplace vyhledejte *řešení pro monitorování kontejnerů* a klikněte na **řešení pro monitorování kontejnerů** prostředek, který se zobrazí v části monitorování a správa kategorie.

    ![Přidání řešení kontejnerů](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Vytvoření řešení uvnitř stejného pracovního prostoru, který již byl vytvořen pro cluster. Tato změna automaticky aktivuje agenta spustit shromažďování dat docker na kontejnery. Přibližně 15 minut nebo tak měli byste vidět řešení osvětlí s příchozí protokoly a statistiky, jak je znázorněno na následujícím obrázku.

    ![Řídicí panel základní Log Analytics](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agenta umožňuje výběr několika protokoly týkající se kontejnerů, které jde dotazovat v protokolech Azure Monitor nebo použít k vizualizaci ukazatele výkonu. Typy protokolů, které byly shromážděny jsou:

* ContainerInventory: obsahuje informace o umístění kontejneru, název a imagí
* ContainerImageInventory: informace o nasazené bitové kopie, včetně ID nebo velikostí
* ContainerLog: konkrétní chybové protokoly, protokoly dockeru (stdout atd.) a další položky
* ContainerServiceLog: příkazy démona dockeru, které byly spuštěny
* Výkonu: čítače výkonu včetně kontejneru procesoru, paměti, síťového provozu, diskové vstupně-výstupní operace a vlastní metriky z hostitelských počítačů



## <a name="next-steps"></a>Další postup
* Další informace o [protokoly Azure monitoru řešení kontejnerů](../azure-monitor/insights/containers.md).
* Další informace o orchestraci kontejnerů v Service Fabric – [Service Fabric a kontejnery](service-fabric-containers-overview.md)
* Seznamte se s [prohledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) funkce nabízí jako součást protokoly Azure monitoru
* Konfigurace protokolů Azure Monitor k nastavení [automatické upozorňování](../log-analytics/log-analytics-alerts.md) pravidla, která vám pomůže se zjišťováním a diagnostikou