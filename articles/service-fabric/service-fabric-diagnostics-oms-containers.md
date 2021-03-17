---
title: Monitorování kontejnerů pomocí Azure Monitorch protokolů
description: Použijte protokoly Azure Monitor pro monitorování kontejnerů, které běží na clusterech Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 6539815875b87a0d0f525d7e89464fa7d2505746
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570210"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitorování kontejnerů pomocí Azure Monitorch protokolů
 
Tento článek popisuje kroky potřebné k nastavení Azure Monitor protokolů monitorování kontejnerů pro zobrazení událostí kontejneru. Pokud chcete nastavit cluster pro shromažďování událostí kontejneru, přečtěte si tento [podrobný kurz](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Nastavení řešení pro monitorování kontejnerů

> [!NOTE]
> Musíte mít pro svůj cluster nastavené protokoly Azure Monitor a zároveň mají na svých uzlech nasazeného agenta Log Analytics. Pokud to neuděláte, postupujte podle kroků v části [nastavení protokolů Azure monitor](service-fabric-diagnostics-oms-setup.md) a nejdřív [přidejte agenta Log Analytics do clusteru](service-fabric-diagnostics-oms-agent.md) .

1. Jakmile se cluster nastaví s protokoly Azure Monitor a agentem Log Analytics, nasaďte kontejnery. Před přechodem k dalšímu kroku počkejte na nasazení kontejnerů.

2. V Azure Marketplace vyhledejte řešení pro *monitorování kontejnerů* a klikněte na prostředek **řešení monitorování kontejnerů** , který se zobrazí pod kategorií monitorování a správa.

    ![Přidání řešení kontejnerů](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Vytvořte řešení v rámci stejného pracovního prostoru, který již byl pro cluster vytvořen. Tato změna automaticky aktivuje agenta, aby zahájil shromažďování dat Docker na kontejnerech. Během 15 minut byste měli vidět řešení až na základě příchozích protokolů a statistik, jak je znázorněno na následujícím obrázku.

    ![Základní Log Analytics řídicí panel](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agent umožňuje shromažďování několika protokolů specifických pro kontejner, které lze dotazovat v protokolech Azure Monitor nebo které slouží k vizualizaci ukazatelů výkonu. Shromažďované typy protokolů jsou:

* ContainerInventory: zobrazí informace o umístění kontejneru, názvu a obrázcích.
* ContainerImageInventory: informace o nasazených bitových kopiích, včetně ID a velikostí
* ContainerLog: konkrétní protokoly chyb, protokoly Docker (stdout atd.) a další položky
* ContainerServiceLog: příkazy démona Docker, které byly spuštěny
* Výkon: čítače výkonu, včetně CPU kontejneru, paměti, síťového provozu, vstupně-výstupních operací disku a vlastních metrik z hostitelských počítačů



## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [řešení kontejnerů pro Azure monitor protokoly](../azure-monitor/containers/containers.md).
* Přečtěte si další informace o orchestraci kontejnerů v Service Fabric- [Service Fabric a kontejnerech](service-fabric-containers-overview.md) .
* Seznámení s funkcemi [prohledávání protokolů a dotazování](../azure-monitor/logs/log-query-overview.md) , které nabízí jako součást protokolů Azure monitor
* Konfigurace protokolů Azure Monitor pro nastavení [automatizovaných pravidel upozorňování](../azure-monitor/alerts/alerts-overview.md) na pomoc při zjišťování a diagnostice
