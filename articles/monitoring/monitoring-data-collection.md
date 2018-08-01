---
title: Shromažďování monitorování dat v Azure | Dokumentace Microsoftu
description: Přehled shromážděných z aplikace a služby v Azure a nástroje používané k analýzám dat monitorování.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: bwren
ms.openlocfilehash: 35580d71aa2592fa94f42cfdbad3c192acc303c5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363981"
---
# <a name="collect-monitoring-data-in-azure"></a>Shromažďování dat monitorování v Azure
Tento článek obsahuje přehled monitorování data shromážděná z aplikací a služeb v Azure. Také popisuje nástroje, které vám umožní analyzovat data. 

## <a name="types-of-monitoring-data"></a>Typy dat monitorování
Všechna data monitorování se vejde do jedné ze dvou základních typů, metriky nebo protokoly. Každý typ má různé vlastnosti a je nejvhodnější pro určité scénáře.

### <a name="metrics"></a>Metriky
Metriky jsou číselné hodnoty, které popisují některé aspekty systému v určitou dobu. Patří mezi ně:

* Různé datové, včetně samotná hodnota.
* Čas, který byl shromážděn hodnotu.
* Typ měření, který představuje hodnotu.
* Prostředek, který je přidružen hodnotu. 

Metriky se budou shromažďovat v pravidelných intervalech, zda hodnoty. Například může shromažďování využití procesoru z virtuálního počítače každou minutu nebo počet uživatelů, přihlášení k aplikaci každých 10 minut.

Metriky jsou jednoduché a schopný zajistit podporu téměř v reálném čase scénáře. Jsou užitečné pro výstrahy, protože metriky může často Vzorkovaná a upozornění můžete aktivuje rychle s relativně jednoduché logikou. Například může vyvolat výstrahu, pokud metrika překročí prahovou hodnotu. Nebo může vyvolat výstrahu, dosáhne rozdíl mezi dvě metriky určitou hodnotu.

Jednotlivé metriky obvykle poskytují přehled o malou samostatně. Poskytuje jednu hodnotu bez kontextu než porovnání, které se jednoduché prahové hodnoty. Jsou užitečné v kombinaci s dalšími metrikami k identifikaci trendů a vzorů, nebo v kombinaci s protokoly, které poskytují kontext po konkrétní hodnoty. 

Například určitý počet uživatelů ve vaší aplikaci v daném okamžiku můžete zjistit málo o stavu aplikace. Ale náhlému poklesu u uživatelů, indikován více hodnot stejné metriky, by mohly poukazovat problém. Nadměrné výjimek vyvolaných aplikací a indikován samostatné metriku, by mohly identifikovat problém aplikace, který způsobuje, že rozevírací. Události, které aplikace se vytvoří pro identifikaci selhání v jeho součásti můžete zjistit původní příčinu.

Výstrahy založené na protokoly nejsou jako responzivní jako upozornění na základě metrik, ale mohou obsahovat složitější logiku. Můžete vytvořit výstrahy na základě výsledků jakýkoli dotaz, který provádí komplexní analýzu na datech z různých zdrojů.

### <a name="logs"></a>Logs
Protokoly obsahovat různé druhy dat uspořádaných do s různými sadami vlastností pro jednotlivé typy záznamů. Protokoly může obsahovat číselných hodnot, jako třeba metriky, ale obvykle obsahují textová data se podrobný popis. Tyto další se liší od metriky v tom, že se liší v jejich strukturu a často nejsou shromážděny v pravidelných intervalech.

Je běžný typ položky protokolu událostí. Události se shromažďují nedojde. Jsou už vytvořené aplikace nebo služby a obvykle zahrnují dostatek informací, které poskytují úplný kontext na své vlastní. Například událost může znamenat, že určitý prostředek vytvořil nebo změnil, nový hostitel spuštěna v reakci na zvýšení provozu, nebo v aplikaci došlo k chybě.

Protokoly jsou užitečné zejména pro kombinování dat z různých zdrojů pro komplexní analýzu a sledování trendů v čase. Vzhledem k tomu, že formát dat se může lišit, aplikace můžete vytvořit vlastní protokoly pomocí struktura, která potřebují. Metriky je možné replikovat i v protokolech kombinovat s dalšími daty monitorování pro vytvoření trendu a ostatní data analýzy.


## <a name="monitoring-tools-in-azure"></a>Nástrojů pro monitorování v Azure
Sledování dat v Azure se shromažďují a analyzují prostřednictvím následujících zdrojů.

### <a name="azure-monitor"></a>Azure Monitor
Metriky z prostředků Azure a aplikace se shromažďují do služby Azure Monitor. Data metriky je integrována do stránky na webu Azure Portal pro prostředky Azure. Pro virtuální počítače se zobrazí graf z těchto metrik jako využití procesoru a sítě pro vybraný počítač. 

Můžete také analyzovat data s využitím [Průzkumníka metrik](../monitoring-and-diagnostics/monitoring-metric-charts.md), který grafy hodnoty několika metrik v čase. Můžete zobrazit grafy interaktivně nebo je připnout na řídicí panel k zobrazení se ostatní vizualizace. Můžete také načíst metriky pomocí [Azure, rozhraní REST API pro monitorování](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

Další informace o data metriky, které shromažďují různé druhy prostředků Azure najdete v tématu [zdroje dat v Azure monitorování](monitoring-data-sources.md). 

![Průzkumník metrik](media/monitoring-data-collection/metrics-explorer.png)


### <a name="activity-log"></a>Protokol aktivit 
[Protokolu aktivit Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) ukládají protokoly o konfiguraci a stavu služeb Azure. Průzkumník protokolu aktivit můžete použít k zobrazení těchto protokolů na webu Azure Portal, ale jsou to obvykle [zkopírovat do služby Azure Log Analytics](../log-analytics/log-analytics-activity.md) bude analyzovaný se další data protokolu.

Zobrazení protokolu aktivit vyfiltrovaný tak, aby splňují určitá kritéria, můžete použít Průzkumníka protokolu aktivit. Většinu prostředků je navíc **protokolu aktivit** možnost v jejich nabídek na webu Azure Portal. Zobrazuje Průzkumníka protokolu aktivit filtrovaný pro daný prostředek. Protokoly aktivit můžete také načíst s použitím [rozhraním API REST pro monitorování Azure](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Průzkumník protokolu aktivit](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
Log Analytics poskytuje společnou datovou platformu pro správu v Azure. Je primární služby, který se používá pro ukládání a analýza protokolů v Azure. Shromažďuje data z nejrůznějších zdrojů, včetně agenti na virtuálních počítačích, řešení pro správu a prostředků Azure. Kopírování dat z jiných zdrojů, včetně metrik a protokolu aktivity, chcete-li vytvořit kompletní centrálním úložišti dat monitorování.

Log Analytics má Bohatý dotazovací jazyk pro analýzu dat, která shromažďuje. Můžete použít [portály prohledávání protokolů](../log-analytics/log-analytics-log-search-portals.md) pro interaktivní psaní a testování dotazů a analyzovat jejich výsledky. Můžete také [vytvořit zobrazení](../log-analytics/log-analytics-view-designer.md) k vizualizaci výsledků hledání v protokolu nebo vložte výsledky dotazu přímo na řídicí panel Azure.  

Řešení pro správu zahrnují zobrazení a prohledávání protokolů v Log Analytics pro analýzu dat, která shromažďují. Jiné služby, jako je Azure Application Insights, ukládání dat v Log Analytics a poskytují další nástroje pro analýzu.  

![Logs](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Application Insights shromažďuje telemetrii pro webové aplikace, které jsou nainstalované na různých platformách. Ukládá svoje data v Azure Monitor a Log Analytics. A poskytuje rozsáhlou sadu nástrojů pro analýzu a vizualizaci svá data. Tyto možnosti umožňují používat společnou sadu služeb, například výstrahy, prohledávání protokolů a řídicí panely, které použijete pro další sledování.


![Application Insights](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>Mapa služeb
Řešení Service Map poskytuje vizuální reprezentaci těchto virtuálních počítačů s jejich procesy a závislosti. Ukládá většinu těchto dat v Log Analytics, takže je můžete analyzovat s ostatními daty správy. Konzola Service Map také načte data ze služby Log Analytics a Stihneme ji v rámci virtuálního počítače, který je analyzován.

![Mapa služeb](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>Přenos dat monitorování

### <a name="metrics-to-logs"></a>Metriky, které protokoly
Můžete replikovat metriky v provádět komplexní analýzy s jinými datovými typy s použitím jeho Bohatý dotazovací jazyk Log Analytics. Můžete také zachovat data protokolu pro delší dobu než metriky, které umožňuje provádět sledování trendů v čase. Když metriky nebo jiná data výkonu je uložená ve službě Log Analytics, která data funguje jako protokol. Použití metrik pro podporu téměř v reálném čase analýzy a upozornění při používání protokolů pro sledování trendů a analýzy s ostatními daty.

Pokyny pro shromažďování metrik z prostředků v Azure můžete získat [metriky pro použití v Log Analytics a Azure shromažďovat protokoly služby](../log-analytics/log-analytics-azure-storage.md). Získejte pokyny pro shromažďování metrik prostředků z prostředků Azure PaaS za [konfigurace shromažďování metrik prostředků Azure PaaS s využitím Log Analytics](../log-analytics/log-analytics-collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Protokoly a metriky
Jak je popsáno výše, jsou responzivní více než tento počet protokolů, metrik, takže můžete vytvářet upozornění s nižší latencí a s nižšími náklady. Log Analytics shromažďuje značné množství číselná data, která je vhodná pro metriky, ale není uložený ve službě Azure Monitor. 

Běžným příkladem jsou data o výkonu shromážděná z agentů a řešení pro správu. Některé z těchto hodnot je možné zkopírovat do služby Azure Monitor, kde jsou k dispozici pro výstrahy a analýzy s Průzkumníkem metrik.

Vysvětlení této funkce je k dispozici na [rychleji metrika výstrahy pro protokoly teď v omezené verzi public preview](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/). Seznam hodnot podpora je k dispozici na [podporované metody metrik a vytváření nových metrik výstrah](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md).

### <a name="event-hubs"></a>Event Hubs
Kromě použití nástroje pro analýzu dat monitorování v Azure, můžete chtít přeposlat externího nástroje, jako jsou informace o zabezpečení a událostí produktů pro správu (SIEM). Toto přesměrování se obvykle provádí prostřednictvím [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/). 

Získejte pokyny různých typů údajů o monitorování na [pomocí externího nástroje pro monitorování data do centra událostí pro používání Azure Stream](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Další postup

- Další informace o [data k dispozici monitorování](monitoring-data-sources.md) pro různé prostředky v Azure. 