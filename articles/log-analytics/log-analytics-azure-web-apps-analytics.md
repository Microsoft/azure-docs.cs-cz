---
title: Zobrazení analýzy dat Azure Web Apps | Microsoft Docs
description: Můžete řešení Azure Web Apps analýzy a získáte přehled o webových aplikacích Azure shromažďováním jiné metriky mezi všechny prostředky webové aplikace Azure.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 20ff337f-b1a3-4696-9b5a-d39727a94220
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: magoedte
ms.openlocfilehash: c44546ffe65a0849b485fb0d2dd169e4216ae80a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261364"
---
# <a name="view-analytic-data-for-metrics-across-all-your-azure-web-app-resources"></a>Zobrazení analýzy dat pro metriky mezi všechny prostředky webové aplikace Azure

![Symbol webové aplikace](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-symbol.png)  

> [!NOTE]
> Řešení Azure Web Apps Analytics je zastaralá.  Zákazníci, kteří již nainstalovali řešení můžete nadále používat ji, ale Azure Web Apps Analytics nelze přidat do žádné nové pracovní prostory.  K monitorování vaší webové aplikace, doporučujeme, abyste použili [Application Insights](../application-insights/app-insights-overview.md). 

Řešení Azure Web Apps Analytics (Preview) poskytuje přehled vaší [Azure Web Apps](../app-service/app-service-web-overview.md) shromažďováním jiné metriky mezi všechny prostředky webové aplikace Azure. Pomocí tohoto řešení můžete analyzovat a vyhledejte metriky dat prostředků webové aplikace.

Pomocí řešení, můžete zobrazit:

- Hlavní webové aplikace s nejvyšším doba odezvy
- Počet požadavky v rámci vaší webové aplikace, včetně úspěšné i neúspěšné požadavky
- Hlavní webové aplikace s nejvyšším příchozí a odchozí provoz
- Plány nejvyšší služby s vysokou mírou využití procesoru a paměti
- Operace protokolu aktivit v Azure Web Apps

## <a name="connected-sources"></a>Připojené zdroje

Na rozdíl od většině ostatních řešení pro analýzu protokolu není data shromážděná pro Azure Web Apps pomocí agentů. Všechna data, která používá řešení pochází přímo z Azure.

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| [Agenti systému Windows](log-analytics-windows-agent.md) | Ne | Řešení neshromažďuje informace z agentů v systému Windows. |
| [Agenti systému Linux](log-analytics-linux-agents.md) | Ne | Řešení neshromažďuje informace od agentů systému Linux. |
| [Skupiny správy nástroje SCOM](log-analytics-om-agents.md) | Ne | Řešení neshromažďuje informace od agentů v připojené skupině pro správu SCOM. |
| [Účet služby Azure Storage](log-analytics-azure-storage.md) | Ne | Řešení nemá shromažďování informací z úložiště Azure. |

## <a name="prerequisites"></a>Požadavky

- Pro přístup k webové aplikaci Azure prostředku metriky informacím, musíte mít předplatné Azure.

## <a name="configuration"></a>Konfigurace

Proveďte následující postup pro konfiguraci Azure Web Apps Analytics řešení pro vaše pracovní prostory.

1. Povolit řešení Azure Web Apps Analytics z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) nebo pomocí procesu popsaného v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md).
2. [Povolit protokolování metriky prostředků Azure k analýze protokolů pomocí prostředí PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell).

Řešení Azure Web Apps analýzy shromažďuje dvě sady metriky z Azure:

- Metriky Azure Web Apps
  - Pracovní sada průměrné paměti
  - Průměrná doba odezvy
  - Počet přijatých nebo odeslaných bajtů
  - Čas procesoru
  - Požadavky
  - Paměť pracovní sada
  - Httpxxx
- Metriky plánu služby Service aplikace
  - Počet přijatých nebo odeslaných bajtů
  - Procento procesoru
  - Délka fronty disku
  - Délka fronty HTTP
  - Procento paměti

Metriky plánu služby App Service jsou shromažďovány pouze pokud používáte vyhrazené tarifu. To se nevztahuje na volné nebo sdílené plány služby App Service.

Po dokončení konfigurace řešení, začněte dat předávaných do pracovního prostoru do 15 minut.

## <a name="using-the-solution"></a>Použití řešení

Když přidáte řešení Azure Web Apps analýzy do pracovního prostoru **Azure Web Apps Analytics** dlaždice se přidá na řídicí panel Přehled. Tuto dlaždici zobrazí počet Azure Web Apps, řešení má přístup k ve vašem předplatném Azure.

![Azure dlaždice analýzy webové aplikace](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-tile.png)

### <a name="view-azure-web-apps-analytics-information"></a>Zobrazit informace o Azure Web Apps Analytics

Klikněte **Azure Web Apps Analytics** dlaždici otevřete **Azure Web Apps Analytics** řídicí panel. Řídicí panel obsahuje okna v následující tabulce. Každý okno uvádí až deset položky odpovídající kritériím tohoto okna pro zadaný obor a časový rozsah. Můžete spustit vyhledávání protokolu, který vrátí všechny záznamy kliknutím **zobrazit všechny** v dolní části okna, nebo kliknutím na záhlaví okna.


| Sloupec | Popis |
| --- | --- |
| Azure Webapps |   |
| Trendy webové žádosti o aplikace | Ukazuje spojnicový graf trendu žádost webové aplikace pro rozsah, kterou jste vybrali a seznam top deset webových požadavků. Klikněte na spojnicový graf ke spuštění vyhledávání protokolu pro <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SITES/" and (MetricName == "Requests" or MetricName startswith_cs "Http") &#124; summarize AggregatedValue = avg(Average) by MetricName, bin(TimeGenerated, 1h)</code> <br>Klikněte na položku webové žádosti o spuštění vyhledávání protokolu pro trend metriky webové žádosti, který požadují. |
| Doba odezvy aplikací webové | Zobrazuje spojnicový graf doby odezvy webové aplikace pro rozsah, kterou jste vybrali. Také ukazuje seznam seznam horní deset webové aplikace odpovědi časový limit. Klikněte na graf ke spuštění vyhledávání protokolu pro <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SITES/" and MetricName == "AverageResponseTime" &#124; summarize AggregatedValue = avg(Average) by Resource, bin(TimeGenerated, 1h)</code><br> Klikněte na webovou aplikaci spustit vyhledávání protokolu vrácení dobu odezvy pro webovou aplikaci. |
| Provoz webové aplikace | Spojnicový graf pro provoz webové aplikace se zobrazí v MB a uvádí horní provoz webové aplikace. Klikněte na graf ke spuštění vyhledávání protokolu pro <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SITES/" and (MetricName == "BytesSent" or MetricName == "BytesReceived") &#124; summarize AggregatedValue = sum(Average) by Resource, bin(TimeGenerated, 1h)</code><br> Zobrazuje všechny webové aplikace s přenosy dat za poslední minutu. Klikněte na webovou aplikaci spustit vyhledávání protokolu zobrazující bajtů přijatých a odeslaných pro webovou aplikaci. |
| Plánů služby Azure App Service |   |
| S využitím výkonu procesoru plánů služby App Service &gt; 80 % | Zobrazí celkový počet plány služby App Service, které mají větší než 80 % využití procesoru a jsou uvedeny top 10 plány služby App Service podle využití procesoru. Klikněte na tlačítko oblasti celkový ke spuštění vyhledávání protokolu pro <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SERVERFARMS/" and MetricName == "CpuPercentage" &#124; summarize AggregatedValue = avg(Average) by Resource</code><br> Zobrazuje seznam vaše plány služby App Service a jejich průměrné využití procesoru. Klikněte na tlačítko plán služby App Service ke spuštění vyhledávání protokolu zobrazující jeho průměrné využití procesoru. |
| Plánů služby App Service se využití paměti &gt; 80 % | Zobrazí celkový počet plány služby App Service, které mají větší než 80 % využití paměti a jsou uvedeny top 10 plány služby App Service podle využití paměti. Klikněte na tlačítko oblasti celkový ke spuštění vyhledávání protokolu pro <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SERVERFARMS/" and MetricName == "MemoryPercentage" &#124; summarize AggregatedValue = avg(Average) by Resource</code><br> Zobrazuje seznam vaše plány služby App Service a jejich průměrné využití paměti. Klikněte na tlačítko plán služby App Service ke spuštění vyhledávání protokolu zobrazující jeho průměrné využití paměti. |
| Protokoly aktivity službě Azure Web Apps |   |
| Službě Azure Web Apps aktivity auditu | Zobrazuje celkový počet webové aplikace s [protokoly aktivity](log-analytics-activity.md) a seznam operací protokolu aktivit prvních 10. Klikněte na tlačítko oblasti celkový ke spuštění vyhledávání protokolu pro <code>AzureActivity #124; where ResourceProvider == "Azure Web Sites" #124; summarize AggregatedValue = count() by OperationName</code><br> Zobrazuje seznam operací protokolu aktivit. Klikněte na tlačítko Spustit hledání protokolů, který obsahuje seznam záznamů pro operaci operace protokolu aktivit. |



### <a name="azure-web-apps"></a>Azure Web Apps

Na řídicím panelu můžete k podrobnostem a další pohled na vaše webové aplikace metriky. To první sada okna zobrazení trendu požadavků webové aplikace, počet chyb (například HTTP404), provozu a průměrná doba odezvy v čase. Také ukazuje rozpis tyto metriky pro jiné webové aplikace.

![Oken Azure Webapps](./media/log-analytics-azure-web-apps-analytics/web-apps-dash01.png)

Hlavním důvodem pro zobrazující, že data jsou tak, aby mohli identifikovat webové aplikace s vysokou odezvu a prozkoumejte, hlavní příčinu. Prahová hodnota omezení platí také pro abyste snadno identifikovat těm, které jsou s problémy.

- Zobrazené červeně webových aplikací mít doba odezvy vyšší než 1 sekunda.
- Webové aplikace, které jsou uvedené v oranžová mít odezvu vyšší než 0.7 sekundu a menší než 1 sekundu.
- Zobrazené zeleně webových aplikací mít doba odezvy menší než 0,7 druhý.

V následující protokolu vyhledávání obrázek příkladu, můžete uvidíte, že *anugup3* webové aplikace měla dobou odezvy mnohem vyšší než jiné webové aplikace.

![Příklad protokolu vyhledávání](./media/log-analytics-azure-web-apps-analytics/web-app-search-example.png)

### <a name="app-service-plans"></a>Plány služby App Service

Pokud používáte vyhrazené plány služby, může taky shromažďovat metriky pro vaše plány služby App Service. V tomto zobrazení uvidíte vaše plány služby App Service s vysokou mírou využití procesoru nebo paměti (&gt; 80 %). Také vám ukazuje nejvyšší aplikační služby s vysokou mírou využití paměti nebo procesoru. Podobně limit prahové hodnoty je použita abyste snadno identifikovat těm, které jsou s problémy.

- Plány služby App Service zobrazené červeně mít využití procesoru nebo paměti, která je vyšší než 80 %.
- Plány služby App Service, které jsou uvedené v oranžová mít využití procesoru nebo paměti, vyšší než 60 % a nižší než 80 %.
- Plány služby App Service zobrazené zeleně mít nižší než 60 % využití procesoru nebo paměti.

![Oken Azure plány služby App Service](./media/log-analytics-azure-web-apps-analytics/web-apps-dash02.png)

## <a name="azure-web-apps-log-searches"></a>Hledání protokolu Azure Web Apps

**Seznamu z oblíbených Azure webové aplikace vyhledávací dotazy** ukazuje všechny související aktivity protokoly pro Web Apps, který poskytuje přehledy o činnosti, které byly provedeny v prostředkům webových aplikací. V něm také všechny související operace a počet opakovaných mít došlo k chybě.

Pomocí kteréhokoli vyhledávací dotazy protokolu jako počáteční bod, můžete snadno vytvořit výstrahu. Můžete například vytvořit upozornění, když metrika Průměrná doba odezvy je větší než každou 1 sekundu.

## <a name="next-steps"></a>Další postup

- Vytvoření [výstraha](log-analytics-alerts-creating.md) pro určité metriky.
- Použití [hledání protokolů](log-analytics-log-searches.md) k zobrazení podrobných informací z protokolů aktivity.
