---
title: Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics | Dokumentace Microsoftu
description: Shromažďovat protokol aktivit Azure v Azure monitorování protokolů a řešení pro monitorování používat k analýze a hledání v protokolu aktivit Azure napříč všemi předplatnými Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 5839fd40a128097e400f13acbe4fb6ef90c656b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248126"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics ve službě Azure Monitor
[Protokolu aktivit Azure](activity-logs-overview.md) poskytuje podrobné informace o události na úrovni předplatného, ke kterým došlo ve vašem předplatném Azure. Tento článek popisuje, jak shromažďovat protokolu aktivit do pracovního prostoru Log Analytics a jak používat Activity Log Analytics [řešení pro monitorování](../insights/solutions.md), který poskytuje protokol dotazů a zobrazení pro analýzu těchto dat. 

Připojení protokolu aktivit do pracovního prostoru Log Analytics poskytuje následující výhody:

- Konsolidace protokolu aktivit z více předplatných Azure na jednom místě pro účely analýzy.
- Store položky protokolu aktivit déle než 90 dní.
- Je možné korelujte data protokolu aktivit s dalšími daty monitorování shromážděná službou Azure Monitor.
- Použití [protokolu dotazy](../log-query/log-query-overview.md) provádět komplexní analýzy a získat podrobný přehled u položek protokolu aktivit.

## <a name="connect-to-log-analytics-workspace"></a>Připojení k pracovnímu prostoru Log Analytics
Protokol aktivit může být připojena k pouze jednomu pracovnímu prostoru, ale jeden pracovní prostor může být připojen k protokolu aktivit pro více předplatných ve stejném tenantovi Azure. Shromažďování více tenantů, naleznete v tématu [shromažďování protokolů aktivit Azure do pracovního prostoru Log Analytics napříč předplatnými v různých Azure Active Directory klienty](activity-log-collect-tenants.md).

Pomocí následujícího postupu připojení protokolu aktivit do pracovního prostoru Log Analytics:

1. Z **pracovních prostorů Log Analytics** nabídky na webu Azure Portal, vyberte pracovní prostor ke shromažďování protokolů aktivit.
1. V **zdroje dat pracovního prostoru** část nabídky pracovním prostoru, vyberte **protokol aktivit Azure**.
1. Klikněte na předplatné, které chcete připojit.

    ![Pracovní prostory](media/activity-log-export/workspaces.png)

1. Klikněte na tlačítko **připojit** pro připojení protokolu aktivit v rámci předplatného k vybranému pracovnímu prostoru. Pokud předplatné je již připojen k jiným pracovním prostorem, klikněte na tlačítko **odpojit** nejprve k jej odpojte.

    ![Připojení pracovních prostorů](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analýza v pracovním prostoru Log Analytics
Protokol aktivit, které připojíte k pracovnímu prostoru Log Analytics, položky, se zapíšou do pracovního prostoru do tabulky nazvané **AzureActivity** , který lze načíst s [dotaz protokolu](../log-query/log-query-overview.md). Struktura v této tabulce se liší v závislosti na tom [kategorie Položka protokolu](activity-logs-overview.md#categories-in-the-activity-log). Zobrazit [schéma událostí protokolu aktivit Azure](activity-log-schema.md) popis jednotlivých kategorií.

## <a name="activity-logs-analytics-monitoring-solution"></a>Monitorování řešení analýzy protokolů aktivit
Řešení pro monitorování Azure Log Analytics zahrnuje několik dotazů na protokoly a zobrazení pro analýzu záznamů protokolu aktivit ve vašem pracovním prostoru Log Analytics.

### <a name="install-the-solution"></a>Instalace řešení
Použijte postup uvedený v [nainstalovat řešení pro monitorování](../insights/solutions.md#install-a-monitoring-solution) k instalaci **Activity Log Analytics** řešení. Není nutná žádná další konfigurace.

### <a name="use-the-solution"></a>Použití řešení
Řešení monitorování lze zobrazit **monitorování** nabídky na webu Azure Portal. Vyberte **Další** v **Insights** části pro otevření **přehled** stránky s dlaždicemi řešení. **Protokolů aktivit Azure** dlaždici se zobrazuje počet počet **AzureActivity** záznamy v pracovním prostoru.

![Dlaždice protokolů aktivit Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Klikněte na tlačítko **protokolů aktivit Azure** otevřete dlaždici **protokolů aktivit Azure** zobrazení. Zobrazení obsahuje části vizualizace v následující tabulce. Každá část uvádí až 10 položky kritériím této části pro zadaný časový rozsah. Můžete spustit dotaz protokolu, který vrátí všechny odpovídající záznamy kliknutím **zobrazit všechny** v dolní části.

![Řídicí panel protokolů aktivit Azure](media/collect-activity-logs/activity-log-dash.png)

| Části vizualizace | Popis |
| --- | --- |
| Položky protokolu aktivit Azure | Ukazuje záznamu součty pro rozsah, který jste vybrali pruhový graf na horní položku protokolu aktivit Azure a seznam nejvyšší volající 10 aktivity. Klikněte na pruhový graf a spustíte hledání v protokolu pro `AzureActivity`. Klikněte na položku volající ke spuštění hledání v protokolu všech položek protokolu aktivit pro danou položku. |
| Protokoly aktivit podle stavu | Ukazuje prstencový graf na stav ve vybraném rozsahu dat a seznam prvních deseti stav záznamů protokolu aktivit Azure. Klepněte na graf spuštění protokolu dotazu pro `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`. Klikněte na položku Stav spustit prohledávání protokolů vrácení všech položek protokolu aktivit u záznamu daného stavu. |
| Protokoly aktivit podle prostředku | Zobrazuje celkový počet prostředků pomocí protokolů aktivit a uvádí top deseti prostředků pomocí záznamu počty pro jednotlivé prostředky. Klikněte na tlačítko spustíte hledání v protokolu pro oblasti celkový `AzureActivity | summarize AggregatedValue = count() by Resource`, který se zobrazí všechny prostředky Azure k dispozici pro řešení. Klikněte na prostředek pro spuštění protokolu dotazu vrátí všechny záznamy aktivit pro daný prostředek. |
| Protokoly aktivit přes poskytovatele prostředků | Zobrazí celkový počet poskytovatelů prostředků, které vytvářejí protokoly aktivit a uvádí deset největších. Kliknutím na oblasti celkový počet spuštění protokolu dotazu pro `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`, který zobrazuje všechny poskytovatele prostředků Azure. Klikněte na tlačítko zprostředkovatele prostředků ke spuštění protokolu dotazu vrátí všechny záznamy aktivit pro zprostředkovatele. |

## <a name="next-steps"></a>Další postup

- Další informace o [protokolu aktivit](activity-logs-overview.md).
- Další informace o [datová Platforma Azure Monitor](data-platform.md).
- Použití [protokolu dotazy](../log-query/log-query-overview.md) Chcete-li zobrazit podrobné informace z vašeho protokolu aktivit.
