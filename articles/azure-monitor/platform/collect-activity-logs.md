---
title: Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics | Dokumentace Microsoftu
description: Řešení protokolů aktivit Azure můžete použít k analýze a hledání v protokolu aktivit Azure napříč všemi předplatnými Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: magoedte
ms.openlocfilehash: 4476bb0a5a343fd43ce5ed70cf0e493d0ccae0e9
ms.sourcegitcommit: f24b62e352e0512dfa2897362021b42e0cb9549d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59505630"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics ve službě Azure Monitor

![Symbol protokolů aktivit Azure](./media/collect-activity-logs/activity-log-analytics.png)

Řešení Activity Log Analytics vám pomáhají analyzovat a Hledat [protokolu aktivit Azure](activity-logs-overview.md) napříč všemi předplatnými Azure. Protokol aktivit Azure je protokol, který nabízí přehled o operace prováděné s prostředky v rámci vašich předplatných. Protokol aktivit se dřív označovalo jako *protokoly auditu* nebo *operační protokoly* vzhledem k tomu, že sestavy událostí pro vaše předplatná.

Použití protokolu aktivit, můžete určit *co*, *kdo*, a *při* veškerých operací (PUT, POST, DELETE) provedených pro prostředky ve vašem předplatném zápisu. Můžete také zjištění stavu operace a další relevantní vlastnosti. Protokol aktivit neobsahuje operace čtení (GET) ani operace pro prostředky, které používají model nasazení Classic.

Když se připojíte protokolů aktivit Azure do pracovního prostoru Log Analytics, můžete:

- Analýza protokolů aktivit se předdefinované zobrazení
- Analýza a hledat protokoly aktivit z více předplatných Azure
- Protokoly aktivit déle než 90 dní<sup>1</sup>
- Protokoly aktivit je možné korelovat s jiné službě Azure data platformy a aplikace
- Zobrazit provozní aktivity agregované podle stavu
- Zobrazení trendů aktivit aktivit ve všech služeb Azure
- Zprávu o autorizaci změny na všech vašich prostředků Azure
- Identifikujte výpadku nebo služba problémy se stavem dopadu na vaše prostředky
- Korelovat aktivity uživatelů, operace automatického škálování, změny autorizace a stavy služeb do jiných protokolů nebo metrik z vašeho prostředí pomocí prohledávání protokolů

<sup>1</sup>ve výchozím nastavení, Azure Monitor sleduje vaše protokoly aktivit Azure v pracovním prostoru Log Analytics po dobu 90 dnů, i v případě, že jsou na úrovni Free. Nebo, pokud máte pracovní prostor nastavení uchování menší než 90 dnů. Pokud váš pracovní prostor má uchování delší než 90 dnů, se zachovají protokoly aktivit na základě na doby uchování pracovního prostoru.

Pracovní prostor Log Analytics shromažďuje protokoly aktivit je zdarma a ukládá protokoly po dobu 90 dnů zdarma. Pokud ukládání protokolů po dobu delší než 90 dnů vám budou účtovat poplatky uchovávání dat pro data uložená déle než 90 dní.

Pokud jste na cenové úrovni Free, protokoly aktivit se nevztahují na vaší denní spotřebu dat.

## <a name="connected-sources"></a>Připojené zdroje

Na rozdíl od většiny jiných řešení Azure Monitor není data shromážděná pro protokoly aktivit pomocí agentů. Všechna data, která používá řešení pochází přímo z Azure.

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| [Agenti systému Windows](agent-windows.md) | Ne | Řešení neshromažďuje informace z agentů Windows. |
| [Agenti systému Linux](../learn/quick-collect-linux-computer.md) | Ne | Řešení neshromažďuje informace z agentů Linuxu. |
| [Skupina pro správu nástroje System Center Operations Manager](om-agents.md) | Ne | Řešení neshromažďuje informace z agentů odesílajících sestavy do skupiny pro správu Operations Manageru. |
| [Účet služby Azure Storage](collect-azure-metrics-logs.md) | Ne | Řešení neshromažďuje informace ze služby Azure storage. |

## <a name="prerequisites"></a>Požadavky

Přístup k informacím protokolu aktivit Azure, musíte mít předplatné Azure.

Řešení také vyžaduje, že jsou tyto dva poskytovatelé prostředků ve vašem předplatném zaregistrovaný:

1. Microsoft.OperationalInsights
2. Microsoft.OperationsManagement

Zjistěte, jak zaregistrovat nebo ověření jsou registrované, najdete v článku [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/resource-manager-supported-services.md)

## <a name="configuration"></a>Konfigurace

Proveďte následující postup pro konfiguraci řešení Activity Log Analytics pro váš pracovní prostory.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

2. Povolte řešení Activity Log Analytics z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) nebo pomocí postupu popsaného v článku [Přidání řešení Log Analytics z galerie řešení](../insights/solutions.md).

2. Konfigurace protokolů aktivit do přejděte do pracovního prostoru Log Analytics.
    1. Na webu Azure Portal, vyberte pracovní prostor a pak klikněte na tlačítko **protokol aktivit Azure**.
    2. Pro každé předplatné klikněte na název předplatného.  
        
        ![Přidat předplatné](./media/collect-activity-logs/add-subscription.png)
    
    3. V *SubscriptionName* okna, klikněte na tlačítko **připojit**.  
    
        ![Připojit předplatné](./media/collect-activity-logs/subscription-connect.png)

## <a name="using-the-solution"></a>Použití řešení

Když přidáte řešení Activity Log Analytics do pracovního prostoru **protokolů aktivit Azure** se přidá dlaždice na řídicí panel Přehled. Tuto dlaždici se zobrazuje počet záznamů aktivit v Azure pro předplatná Azure, které má přístup k řešení.

![Dlaždice protokolů aktivit Azure](./media/collect-activity-logs/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Protokoly aktivit Azure zobrazení

Klikněte na tlačítko **protokolů aktivit Azure** otevřete dlaždici **protokolů aktivit Azure** řídicího panelu. Tento řídicí panel obsahuje okna popsaná v následující tabulce. V každém okně je seznam až 10 položek, které vyhovují kritériím oboru a časového rozsahu daného okna. Kliknutím na **Zobrazit vše** v dolní části okna nebo na záhlaví okna můžete spustit hledání v protokolu, které vrátí všechny záznamy.

Data protokolu aktivit se zobrazí jenom *po* konfiguraci vašich protokolů aktivit do přejít na řešení, takže té nelze zobrazit žádná data.

| Okno | Popis |
| --- | --- |
| Položky protokolu aktivit Azure | Ukazuje záznamu součty pro rozsah, který jste vybrali pruhový graf horní položky protokolu aktivit Azure a seznam nejvyšší volající 10 aktivity. Klikněte na pruhový graf a spustíte hledání v protokolu pro <code>AzureActivity</code>. Klikněte na položku volající ke spuštění hledání v protokolu všechny položky protokolu aktivit pro danou položku. |
| Protokoly aktivit podle stavu | Ukazuje prstencového grafu pro stav protokolu aktivit Azure pro rozsah, který jste vybrali. Seznam také zobrazuje seznam prvních deseti stav záznamů. Klikněte na graf na spustíte hledání v protokolu pro <code>AzureActivity &#124; summarize AggregatedValue = count() by ActivityStatus</code>. Klikněte na položku Stav spustit prohledávání protokolů vrací všechny položky protokolu aktivit u záznamu daného stavu. |
| Protokoly aktivit podle prostředku | Zobrazuje celkový počet prostředků pomocí protokolů aktivit a uvádí top deseti prostředků pomocí záznamu počty pro jednotlivé prostředky. Klikněte na tlačítko spustíte hledání v protokolu pro oblasti celkový <code>AzureActivity &#124; summarize AggregatedValue = count() by Resource</code>, který se zobrazí všechny prostředky Azure k dispozici pro řešení. Klikněte na prostředek ke spuštění hledání v protokolu všech záznamů aktivit pro daný prostředek. |
| Protokoly aktivit přes poskytovatele prostředků | Zobrazí celkový počet poskytovatelů prostředků, které vytvářejí aktivity se přihlásí a uvádí nejlepších deset. Klikněte na tlačítko spustíte hledání v protokolu pro oblasti celkový <code>AzureActivity &#124; summarize AggregatedValue = count() by ResourceProvider</code>, který zobrazuje všechny poskytovatele prostředků Azure. Klikněte na tlačítko zprostředkovatele prostředků ke spuštění hledání v protokolu všech záznamů aktivit pro zprostředkovatele. |

![Řídicí panel protokolů aktivit Azure](./media/collect-activity-logs/activity-log-dash.png)

## <a name="next-steps"></a>Další postup

- Vytvoření [výstraha](../platform/alerts-metric.md) když se stane konkrétní aktivitu.
- Použití [prohledávání protokolů](../log-query/log-query-overview.md) k zobrazení podrobných informací z protokolů aktivit.
