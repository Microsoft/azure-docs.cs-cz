---
title: Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics | Microsoft Docs
description: Shromažďovat protokol aktivit Azure v Azure Monitor protokoly a používat řešení pro monitorování k analýze a hledání protokolu aktivit Azure napříč všemi předplatnými Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 07062ea9ce10b99e0f03a66247bb97795b45aedc
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212593"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics v Azure Monitor

> [!NOTE]
> Nyní můžete protokol aktivit shromažďovat do Log Analytics pracovního prostoru pomocí nastavení diagnostiky podobného způsobu, jakým shromažďujete protokoly prostředků. Přečtěte si téma [shromáždění a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics v Azure monitor](diagnostic-settings-subscription.md).

[Protokol aktivit Azure](activity-logs-overview.md) poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo ve vašem předplatném Azure. Tento článek popisuje, jak shromažďovat protokol aktivit do pracovního prostoru Log Analytics a jak používat [řešení Activity Log Analytics monitoring](../insights/solutions.md), které poskytuje dotazy a zobrazení protokolů pro analýzu těchto dat. 

Připojení protokolu aktivit k pracovnímu prostoru Log Analytics přináší následující výhody:

- Konsolidujte protokol aktivit z více předplatných Azure do jednoho místa pro analýzu.
- Ukládání záznamů protokolu aktivit po dobu delší než 90 dní.
- Korelujte data protokolu aktivit s dalšími daty monitorování shromážděnými pomocí Azure Monitor.
- Pomocí [dotazů protokolu](../log-query/log-query-overview.md) můžete provádět komplexní analýzy a získat podrobné přehledy o položkách protokolu aktivit.

## <a name="connect-to-log-analytics-workspace"></a>Připojení k pracovnímu prostoru Log Analytics
Jeden pracovní prostor může být připojen k protokolu aktivit pro více předplatných ve stejném tenantovi Azure. Informace o shromažďování v rámci více tenantů najdete v tématu [shromáždění protokolů aktivit Azure do Log Analytics pracovního prostoru napříč předplatnými v různých klientech Azure Active Directory](activity-log-collect-tenants.md).

> [!IMPORTANT]
> Pokud nejsou poskytovatelé prostředků Microsoft. OperationalInsights a Microsoft. OperationsManagement u vašeho předplatného zaregistrovaní, může se zobrazit chyba s následujícím postupem. Pokud chcete zaregistrovat tyto poskytovatele, přečtěte si téma [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/resource-manager-supported-services.md) .

K připojení protokolu aktivit k pracovnímu prostoru Log Analytics použijte následující postup:

1. V nabídce **Log Analytics pracovní prostory** v Azure Portal vyberte pracovní prostor pro shromáždění protokolu aktivit.
1. V části **zdroje dat pracovního prostoru** v nabídce pracovního prostoru vyberte **Protokol aktivit Azure**.
1. Klikněte na předplatné, které chcete připojit.

    ![Pracovní prostory](media/activity-log-export/workspaces.png)

1. Kliknutím na **připojit** připojte protokol aktivit v předplatném k vybranému pracovnímu prostoru. Pokud je předplatné už připojené k jinému pracovnímu prostoru, odpojte ho kliknutím na **Odpojit** .

    ![Připojit pracovní prostory](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analyzovat v pracovním prostoru Log Analytics
Když připojíte Protokol aktivit k pracovnímu prostoru Log Analytics, položky se zapisují do pracovního prostoru do tabulky s názvem **AzureActivity** , kterou můžete načíst pomocí [dotazu protokolu](../log-query/log-query-overview.md). Struktura této tabulky se liší v závislosti na [kategorii položky protokolu](activity-logs-overview.md#categories-in-the-activity-log). Popis každé kategorie najdete v tématu [schéma událostí protokolu aktivit Azure](activity-log-schema.md) .

## <a name="activity-logs-analytics-monitoring-solution"></a>Řešení monitorování analýz protokolů aktivit
Řešení Azure Log Analytics monitoring zahrnuje několik dotazů a zobrazení protokolů pro analýzu záznamů protokolu aktivit ve vašem pracovním prostoru Log Analytics.

### <a name="install-the-solution"></a>Instalace řešení
Pro instalaci řešení **Activity Log Analytics** použijte postup v části [instalace řešení monitorování](../insights/solutions.md#install-a-monitoring-solution) . Není vyžadována žádná další konfigurace.

### <a name="use-the-solution"></a>Použití řešení
K monitorování řešení se dostanete z nabídky **monitor** v Azure Portal. V části **přehledy** vyberte **Další** a otevřete stránku **Přehled** s dlaždicemi řešení. Dlaždice **protokoly aktivit Azure** zobrazuje počet záznamů **AzureActivity** ve vašem pracovním prostoru.

![Dlaždice protokolů aktivit Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Kliknutím na dlaždici **protokoly aktivit Azure** otevřete zobrazení **protokolů aktivit Azure** . Zobrazení obsahuje části vizualizace v následující tabulce. Každá část obsahuje až 10 položek, které odpovídají kritériím této části pro zadaný časový rozsah. Kliknutím na **Zobrazit vše** v dolní části části můžete spustit dotaz protokolu, který vrátí všechny odpovídající záznamy.

![Řídicí panel protokolů aktivit Azure](media/collect-activity-logs/activity-log-dash.png)

| Součást vizualizace | Popis |
| --- | --- |
| Položky protokolu aktivit Azure | Zobrazuje pruhový graf celkových součtů záznamu položky protokolu aktivit Azure pro vybraný rozsah dat a zobrazuje seznam prvních 10 volajících aktivit. Kliknutím na pruhový graf spustíte prohledávání protokolu pro `AzureActivity`. Kliknutím na položku volajícího spustíte hledání v protokolu, které vrátí všechny položky protokolu aktivit pro tuto položku. |
| Protokoly aktivit podle stavu | Zobrazuje prstencový graf stavu protokolu aktivit Azure pro vybraný rozsah dat a seznam prvních deseti záznamů o stavu. Kliknutím na graf spustíte dotaz protokolu pro `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`. Kliknutím na položku stavu spustíte hledání v protokolu, které vrátí všechny položky protokolu aktivit pro daný záznam o stavu. |
| Protokoly aktivit podle prostředku | Zobrazuje celkový počet prostředků s protokoly aktivit a seznam prvních deseti prostředků s počty záznamů pro jednotlivé prostředky. Kliknutím na oblast celkem spustíte prohledávání protokolu pro `AzureActivity | summarize AggregatedValue = count() by Resource`, které zobrazuje všechny prostředky Azure dostupné pro řešení. Kliknutím na prostředek spustíte dotaz protokolu, který vrátí všechny záznamy aktivit pro daný prostředek. |
| Protokoly aktivit přes poskytovatele prostředků | Zobrazuje celkový počet poskytovatelů prostředků, které vytváří protokoly aktivit, a seznam prvních deseti. Kliknutím na oblast celkem spustíte dotaz protokolu pro `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`, který zobrazuje všechny poskytovatele prostředků Azure. Kliknutím na poskytovatele prostředků spusťte dotaz protokolu, který vrátí všechny záznamy aktivit pro daného zprostředkovatele. |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [protokolu aktivit](activity-logs-overview.md).
- Přečtěte si další informace o [Azure monitor datovou platformu](data-platform.md).
- Pomocí [dotazů protokolu](../log-query/log-query-overview.md) můžete zobrazit podrobné informace z protokolu aktivit.
