---
title: Konfigurace monitorování a metrik pomocí Azure Monitor
titleSuffix: Azure Bastion
description: Přečtěte si o monitorování Azure bastionu a metrikách pomocí Azure Monitor, řešení pro metriky, výstrahy a diagnostické protokoly v Azure.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/12/2021
ms.author: mialdrid
ms.openlocfilehash: 3a5beba3938b5a845a378ede155f2f64e6baac7a
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417939"
---
# <a name="how-to-configure-monitoring-and-metrics-for-azure-bastion-using-azure-monitor"></a>Jak nakonfigurovat monitorování a metriky pro Azure bastionu pomocí Azure Monitor

Tento článek vám pomůže pracovat s monitorováním a metrikami pro Azure bastionu pomocí Azure Monitor.

>[!NOTE]
>Použití **klasických metrik** se nedoporučuje.
>

## <a name="about-metrics"></a>O metrikách

Azure bastionu má různé metriky, které jsou k dispozici. V následující tabulce jsou uvedeny kategorie a dimenze pro jednotlivé dostupné metriky.

|**Metrika**|**Kategorie**|**Dimenze (y)**|
| --- | --- | --- |
|Stav komunikace bastionu * *|[Dostupnost](#availability)|–|
|Celková paměť|[Dostupnost](#availability)|Instance|
|Použitý procesor|[Provoz](#traffic)|Instance
|Využitá paměť|[Provoz](#traffic)|Instance
|Počet relací|[Výkon](#performance)|Instance|

* * Stav komunikace bastionu se vztahuje pouze na hostitele bastionu nasazené po listopadu 2020.

### <a name="availability-metrics"></a><a name="availability"></a>Metriky dostupnosti

#### <a name="bastion-communication-status"></a><a name="communication-status"></a>Stav komunikace bastionu

Stav komunikace pro Azure bastionu můžete zobrazit agregované napříč všemi instancemi, které tvoří hostitele bastionu.

* Hodnota **1** znamená, že je bastionu k dispozici.
* Hodnota **0** značí, že služba bastionu není k dispozici.

:::image type="content" source="./media/metrics-monitor-alert/communication-status.png" alt-text="Snímek obrazovky znázorňující stav komunikace":::

#### <a name="total-memory"></a><a name="total-memory"></a>Celková paměť

Celkovou paměť Azure bastionu můžete zobrazit v rámci každé instance bastionu.

:::image type="content" source="./media/metrics-monitor-alert/total-memory.png" alt-text="Snímek obrazovky zobrazující celkovou paměť":::

### <a name="traffic-metrics"></a><a name="traffic"></a>Metriky provozu

#### <a name="used-cpu"></a><a name="used-cpu"></a>Použitý procesor

Využití CPU v Azure bastionu můžete zobrazit rozdělené mezi jednotlivé instance bastionu. Monitorování těchto metrik vám pomůže posoudit dostupnost a kapacitu instancí, které tvoří Azure bastionu.

:::image type="content" source="./media/metrics-monitor-alert/used-cpu.png" alt-text="Snímek obrazovky znázorňující využitý procesor":::

#### <a name="used-memory"></a><a name="used-memory"></a>Využitá paměť

Využití paměti můžete zobrazit napříč každou instancí bastionu a rozdělit napříč každou instanci bastionu. Monitorování těchto metrik vám pomůže posoudit dostupnost a kapacitu instancí, které tvoří Azure bastionu.

:::image type="content" source="./media/metrics-monitor-alert/used-memory.png" alt-text="Snímek obrazovky znázorňující využitou paměť":::

### <a name="performance-metrics"></a><a name="performance"></a>Metriky výkonu

#### <a name="session-count"></a>Počet relací

Můžete zobrazit počet aktivních relací na instanci bastionu agregované napříč jednotlivými typy relací (RDP a SSH). Každý bastionu Azure může podporovat rozsah aktivních relací RDP a SSH. Monitorování této metriky vám pomůže pochopit, jestli potřebujete upravit počet instancí, na kterých běží služba bastionu. Další informace o počtu relací, které Azure bastionu může podporovat, najdete v tématu [Nejčastější dotazy k Azure bastionu](bastion-faq.md).

:::image type="content" source="./media/metrics-monitor-alert/session-count.png" alt-text="Snímek obrazovky znázorňující počet relací":::

## <a name="how-to-view-metrics"></a><a name="metrics"></a>Jak zobrazit metriky

1. Pokud chcete zobrazit metriky, přejděte na svého hostitele bastionu.
1. V seznamu **monitorování** vyberte **metriky**.
1. Vyberte parametry. Pokud nejsou nastavené žádné metriky, klikněte na **Přidat metriku** a potom vyberte parametry.

   * **Rozsah:** Ve výchozím nastavení je obor nastaven na hostitele bastionu.
   * **Obor názvů metriky:** Standardní metriky.
   * **Metrika:** Vyberte metriku, kterou chcete zobrazit.

1. Po výběru metriky se použije výchozí agregace. Volitelně můžete použít rozdělení, které zobrazí metriku s různými dimenzemi.

## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md).
  
