---
title: Shromažďování protokolu aktivit Azure v pracovním prostoru Log Analytics
description: Shromážděte protokol aktivit Azure v protokolech monitorování Azure a použijte řešení monitorování k analýze a prohledáváme protokol aktivit Azure ve všech vašich předplatných Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 407bff10e2480c5210d3057bcccd6c60e591c165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055308"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics v Azure Monitoru

> [!WARNING]
> Nyní můžete shromažďovat protokol aktivit do pracovního prostoru Log Analytics pomocí diagnostického nastavení podobného způsobu shromažďování protokolů prostředků. Viz [Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics v Azure Monitoru](diagnostic-settings-legacy.md).

[Protokol aktivit Azure](platform-logs-overview.md) poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo ve vašem předplatném Azure. Tento článek popisuje, jak shromažďovat protokol aktivit do pracovního prostoru Analýzy protokolů a jak používat [řešení monitorování](../insights/solutions.md)analýzy protokolů aktivit , které poskytuje dotazy protokolu a zobrazení pro analýzu těchto dat. 

Připojení protokolu aktivit k pracovnímu prostoru Log Analytics poskytuje následující výhody:

- Konsolidovat protokol aktivit z více předplatných Azure do jednoho umístění pro analýzu.
- Uložit položky protokolu aktivit po dobu delší než 90 dní.
- Korelujte data protokolu aktivit s dalšími daty monitorování shromážděnými službou Azure Monitor.
- Pomocí [dotazů protokolu](../log-query/log-query-overview.md) můžete provádět komplexní analýzy a získat podrobné přehledy o položkách protokolu aktivit.

## <a name="connect-to-log-analytics-workspace"></a>Připojení k pracovnímu prostoru Log Analytics
Jeden pracovní prostor lze připojit k protokolu aktivit pro více předplatných ve stejném tenantovi Azure. Kolekce mezi více klienty najdete [v tématu Shromažďování protokolů aktivit Azure do pracovního prostoru Analýzy protokolů napříč předplatnými v různých tenantech Azure Active Directory](activity-log-collect-tenants.md).

> [!IMPORTANT]
> Pokud pro vaše předplatné nejsou registrováni zprostředkovatelé prostředků Microsoft.OperationalInsights a Microsoft.OperationsManagement, může se zobrazit chyba s následujícím postupem. Viz [Zprostředkovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md) k registraci těchto poskytovatelů.

Pomocí následujícího postupu připojte protokol aktivit k pracovnímu prostoru Log Analytics:

1. Z nabídky **pracovníprostory Analýzy protokolů** na portálu Azure vyberte pracovní prostor pro shromažďování protokolu aktivit.
1. V části **Zdroje dat pracovního prostoru** v nabídce pracovního prostoru vyberte protokol aktivit **Azure**.
1. Klikněte na předplatné, které chcete připojit.

    ![Pracovní prostory](media/activity-log-export/workspaces.png)

1. Kliknutím na **Připojit** připojíte protokol aktivit v předplatném k vybranému pracovnímu prostoru. Pokud je předplatné již připojeno k jinému pracovnímu prostoru, odpojte ho nejprve kliknutím na **Odpojit.**

    ![Připojení pracovních prostorů](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analýza v pracovním prostoru Log Analytics
Když připojíte protokol aktivit k pracovnímu prostoru Analýzy protokolů, položky se zapisují do pracovního prostoru do tabulky s názvem **AzureActivity,** kterou můžete načíst pomocí [dotazu protokolu](../log-query/log-query-overview.md). Struktura této tabulky se liší v závislosti na [kategorii položky protokolu](activity-log-view.md#categories-in-the-activity-log). Informace o popisu jednotlivých kategorií najdete v [tématu schéma událostí protokolu aktivit Azure.](activity-log-schema.md)

## <a name="activity-logs-analytics-monitoring-solution"></a>Řešení monitorování protokolů aktivit Analytics
Řešení monitorování Azure Log Analytics obsahuje více dotazů protokolu a zobrazení pro analýzu záznamů protokolu aktivit v pracovním prostoru Log Analytics.

### <a name="install-the-solution"></a>Instalace řešení
Postup použijte v [části Instalace monitorovacího řešení](../insights/solutions.md#install-a-monitoring-solution) k instalaci řešení Activity Log **Analytics.** Není vyžadována žádná další konfigurace.

### <a name="use-the-solution"></a>Použijte řešení
Řešení monitorování jsou přístupná z nabídky **Monitorování** na webu Azure Portal. V části **Přehledy** vyberte **Další** a otevřete stránku **Přehled** s dlaždicemi řešení. Dlaždice **Protokoly aktivit Azure** zobrazuje počet záznamů **AzureActivity** ve vašem pracovním prostoru.

![Dlaždice Protokoly aktivit Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Kliknutím na dlaždici **Protokoly aktivit Azure** otevřete zobrazení **protokolů aktivit Azure.** Zobrazení obsahuje vizualizační části v následující tabulce. Každá část obsahuje až 10 položek odpovídajících kritériím těchto dílů pro zadaný časový rozsah. Dotaz protokolu, který vrátí všechny odpovídající záznamy, můžete spustit kliknutím na **zobrazit vše** v dolní části dílu.

![Řídicí panel Protokoly aktivit Azure](media/collect-activity-logs/activity-log-dash.png)

| Vizualizační část | Popis |
| --- | --- |
| Položky protokolu aktivit Azure | Zobrazuje pruhový graf celkových součtů záznamů nejvyšších záznamů protokolu aktivit Azure pro vybrané období a seznam 10 hlavních volajících aktivit. Klepnutím na pruhový graf spusťte hledání protokolu pro aplikaci `AzureActivity`. Klepnutím na položku volajícího spustíte hledání protokolu vracející všechny položky protokolu aktivit pro tuto položku. |
| Protokoly aktivit podle stavu | Zobrazuje prstencový graf pro stav protokolu aktivit Azure pro vybrané období a seznam deseti nejvyšších záznamů o stavu. Klepnutím na graf spusťte `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`dotaz protokolu pro aplikaci . Klepnutím na položku stavu spustíte hledání protokolu vracející všechny položky protokolu aktivit pro tento záznam stavu. |
| Protokoly aktivit podle zdroje | Zobrazuje celkový počet zdrojů pomocí protokolů aktivit a uvádí prvních deset zdrojů s počtem záznamů pro každý zdroj. Kliknutím na celkovou oblast spusťte hledání protokolu `AzureActivity | summarize AggregatedValue = count() by Resource`, které zobrazuje všechny prostředky Azure, které jsou k dispozici pro řešení. Klepnutím na prostředek spustíte dotaz protokolu, který vrátí všechny záznamy aktivit pro tento prostředek. |
| Protokoly aktivit podle zprostředkovatele prostředků | Zobrazuje celkový počet poskytovatelů prostředků, kteří vytvářejí protokoly aktivit, a uvádí prvních deset. Kliknutím na celkovou oblast spusťte dotaz protokolu pro `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`aplikaci , která zobrazuje všechny poskytovatele prostředků Azure. Klepnutím na zprostředkovatele prostředků spusťte dotaz protokolu vracející všechny záznamy aktivit zprostředkovatele. |

## <a name="next-steps"></a>Další kroky

- Další informace o [protokolu aktivit](platform-logs-overview.md).
- Další informace o [datové platformě Azure Monitor](data-platform.md).
- Pomocí [dotazů protokolu](../log-query/log-query-overview.md) můžete zobrazit podrobné informace z protokolu aktivit.
