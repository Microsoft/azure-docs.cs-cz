---
title: Monitorování kontejnerů pomocí protokolů Azure Monitoru
description: Protokoly Azure Monitoru slouží k monitorování kontejnerů spuštěných v clusterech Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8d4231de13da3f8b2960bd4852136f803a97a546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614430"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Monitorování kontejnerů pomocí protokolů Azure Monitoru
 
Tento článek popisuje kroky potřebné k nastavení azure monitoru protokoly kontejnermonitorování řešení pro zobrazení událostí kontejneru. Pokud chcete nastavit cluster pro shromažďování událostí kontejneru, [přečtěte](service-fabric-tutorial-monitoring-wincontainers.md)si tento podrobný kurz . 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Nastavení řešení monitorování kontejneru

> [!NOTE]
> Musíte mít protokoly Azure Monitor nastavit pro váš cluster, stejně jako agent Log Analytics nasazené na vaše uzly. Pokud tak nechcete, postupujte podle kroků v [nastavení protokolů Azure Monitor](service-fabric-diagnostics-oms-setup.md) a [nejprve přidejte agenta Analýzy protokolů do clusteru.](service-fabric-diagnostics-oms-agent.md)

1. Jakmile je váš cluster nastaven pomocí protokolů Azure Monitor a agenta Analýzy protokolů, nasaďte své kontejnery. Před přechodem k dalšímu kroku počkejte na nasazení kontejnerů.

2. Na Azure Marketplace vyhledejte *řešení monitorování kontejnerů* a klikněte na prostředek **řešení monitorování kontejnerů,** který se zobrazuje v kategorii Monitorování + správa.

    ![Přidání řešení kontejnerů](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Vytvořte řešení uvnitř stejného pracovního prostoru, který již byl vytvořen pro cluster. Tato změna automaticky aktivuje agenta začít shromažďovat data dockeru na kontejnerech. Asi za 15 minut nebo tak, měli byste vidět řešení rozsvítí s příchozí protokoly a statistiky, jak je znázorněno na obrázku níže.

    ![Základní řídicí panel analýzy protokolů](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agent umožňuje shromažďování několika protokolů specifických pro kontejner, které mohou být dotazovány v protokolech Azure Monitor nebo slouží k vizualizaci ukazatelů výkonu. Shromažďované typy protokolů jsou:

* ContainerInventory: zobrazuje informace o umístění kontejneru, název a obrázky
* ContainerImageInventory: informace o nasazených irecích, včetně ID nebo velikostí
* ContainerLog: specifické protokoly chyb, protokoly dockeru (stdout atd.) a další položky
* ContainerServiceLog: příkazy demonu dockeru, které byly spuštěny
* Perf: čítače výkonu včetně procesoru kontejneru, paměti, síťového provozu, i/o disků a vlastních metrik z hostitelských počítačů



## <a name="next-steps"></a>Další kroky
* Další informace o [řešení Azure Monitor protokoly kontejnery](../azure-monitor/insights/containers.md).
* Další informace o orchestraci kontejnerů v service fabric – [service fabric a kontejnerech](service-fabric-containers-overview.md)
* Seznamte se s funkcemi [pro vyhledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) nabízenými jako součást protokolů Azure Monitoru
* Konfigurace protokolů Azure Monitor u nastavení automatických pravidel [upozorňování,](../log-analytics/log-analytics-alerts.md) která pomohou při zjišťování a diagnostice