---
title: Shromažďování údajů o monitorování dat v Azure | Dokumentace Microsoftu
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
ms.openlocfilehash: efbf0907f3ed75957159c38a536bd31e88a0dbb3
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213295"
---
# <a name="collecting-monitoring-data-in-azure"></a>Shromažďování dat monitorování v Azure
Tento článek obsahuje přehled shromážděných z aplikace a služby v Azure a nástroje používané k analýzám dat monitorování. 

## <a name="types-of-monitoring-data"></a>Typy dat monitorování
Všechna data monitorování se vejde do jedné ze dvou základních typů, metriky nebo protokoly. Každý typ má různé vlastnosti a je nejvhodnější pro konkrétní scénáře, jak je popsáno níže.

### <a name="metrics"></a>Metriky
Metriky jsou číselné hodnoty, které popisují některé aspekty systému v určitém místě v čase. Patří mezi ně jednotlivých datových včetně samotnou, hodnotu času, který byl shromážděn hodnota, typ měření, které představuje hodnotu a konkrétní prostředek, který je přidružen hodnotu. Metriky se budou shromažďovat v pravidelných intervalech, zda hodnoty. Například může shromažďování využití procesoru z virtuálního počítače každou minutu nebo počet uživatelů, přihlášení k aplikaci každých 10 minut.

Metriky jsou jednoduché a schopný zajistit podporu téměř v reálném čase scénáře. Jsou zvláště užitečná pro výstrahy, protože metriky může často Vzorkovaná a upozornění můžete aktivuje rychle s relativně jednoduché logikou. Například může vyvolat výstrahu, pokud metrika překročí prahovou hodnotu nebo vyvolat výstrahu při rozdíl mezi hodnotou dvě metriky dosáhne určitou hodnotu.

Jednotlivé metriky obvykle poskytují přehled o malou samostatně. Poskytuje jednu hodnotu bez kontextu než porovnání, které se jednoduché prahové hodnoty. Jsou však užitečné v kombinaci s dalšími metrikami k identifikaci trendů a vzorů nebo v kombinaci s protokoly, které poskytují kontext po konkrétní hodnoty. Například určitý počet uživatelů ve vaší aplikaci v daném okamžiku může zjistit málo o stavu aplikace. Uživatelé ale indikován více hodnot stejné metriky náhlému poklesu může značit potíže. Nadměrné výjimek vyvolaných aplikací a indikován samostatné metrika by mohly identifikovat aplikace problém způsobující rozevírací nabídku. Událostí vytvořených aplikací, zejména identifikaci selhání součásti aplikace vám může pomoci identifikovat hlavní příčinu.

Výstrahy založené na protokoly nejsou jako responzivní jako upozornění na základě metrik, ale mohou obsahovat složitější logiku. Můžete vytvořit výstrahy na základě výsledků jakýkoli dotaz, který provádí komplexní analýzu na datech z různých zdrojů.

### <a name="logs"></a>Logs
Protokoly obsahovat různé druhy dat uspořádaných do s různými sadami vlastností pro jednotlivé typy záznamů. Protokoly může obsahovat číselných hodnot, jako třeba metriky, ale obvykle obsahují textová data se podrobný popis. Tyto další se liší od metriky v tom, že se liší v jejich strukturu a často nejsou shromážděny v pravidelných intervalech.

Je běžný typ položky protokolu událostí. Události se shromažďují občasně při jejich vytvoření aplikace nebo služby a obvykle zahrnují dostatek informací, které poskytují úplný kontext na své vlastní.  Například událost může znamenat, že určitý prostředek vytvořil nebo změnil, nový hostitel spuštění v reakci na zvýšení provozu nebo v aplikaci došlo k chybě.

Protokoly jsou užitečné hlavně při kombinování dat z různých zdrojů pro komplexní analýzu a sledování trendů v čase. Vzhledem k tomu, že formát dat se může lišit, můžete vytvořit aplikace pomocí struktury, které požadují vlastní protokoly. Metriky je možné replikovat i v protokolech kombinovat s dalšími daty monitorování pro vytvoření trendu a ostatní data analýzy.


## <a name="monitoring-tools-in-azure"></a>Nástrojů pro monitorování v Azure
Sledování dat v Azure se shromažďují a analyzují pomocí více zdrojů, které jsou popsány v následujících částech.

### <a name="azure-metrics"></a>Metriky Azure
Shromažďování metrik z prostředků Azure a aplikací do Azure metriky. Data metriky je integrována do stránky na webu Azure Portal pro konkrétní prostředky Azure, jako jsou virtuální počítače, které zahrnují grafy tyto metriky, jako je využití procesoru a sítě pro vybraný počítač. To může být analyzována s [Průzkumníka metrik](../monitoring-and-diagnostics/monitoring-metric-charts.md) který graf hodnoty několika metrik v čase.  Můžete zobrazit grafy interaktivně nebo je připnout na řídicí panel k zobrazení se ostatní vizualizace. Můžete také načíst metriky ve službě [Azure, rozhraní REST API pro monitorování](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

Na data metriky, které shromažďují různé druhy prostředků v Azure můžete získat podrobnosti o [zdroje dat v Azure monitorování](monitoring-data-sources.md). 

![Průzkumník metrik](media/monitoring-data-collection/metrics-explorer.png)


### <a name="azure-activity-log"></a>Protokol aktivit Azure 
[Protokolu aktivit Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) ukládají protokoly o konfiguraci a stavu služeb Azure. Průzkumník protokolu aktivit můžete použít k zobrazení těchto protokolů na webu Azure Portal, ale jsou to obvykle [zkopírován do Log Analytics](../log-analytics/log-analytics-activity.md) bude analyzovaný se další data protokolu.

Zobrazení protokolu aktivit vyfiltrovaný tak, aby splňují určitá kritéria, můžete použít Průzkumníka protokolu aktivit.  Většinu prostředků má také protokol aktivit v jejich nabídce na portálu Azure portal, který se zobrazí v Průzkumníku protokolu aktivit filtrovaný pro daný prostředek. Můžete také načíst protokoly aktivit se [Azure, rozhraní REST API pro monitorování](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Průzkumník protokolu aktivit](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
Log Analytics poskytuje společnou datovou platformu pro správu v Azure. Je to primární služba používá pro ukládání a analýza protokolů v Azure, shromažďování dat z různých zdrojů, včetně agenti na virtuálních počítačích, řešení pro správu a prostředky Azure. Data z jiných zdrojů, včetně metrik a protokolů aktivit je zkopírovat do Log Analytics, chcete-li vytvořit kompletní centrálním úložišti dat monitorování.

Log Analytics má Bohatý dotazovací jazyk pro analýzu dat, která shromažďuje.  Můžete použít [portály prohledávání protokolů](../log-analytics/log-analytics-log-search-portals.md) pro interaktivní psaní a testování dotazů a analyzovat jejich výsledky. Můžete také [vytvořit zobrazení](../log-analytics/log-analytics-view-designer.md) k vizualizaci výsledků hledání v protokolu nebo vložte výsledky dotazu přímo na řídicí panel Azure.  Řešení pro správu zahrnují zobrazení a prohledávání protokolů v Log Analytics pro analýzu dat, která shromažďují. Jiné služby, jako jsou Application Insights ukládat data ve službě Log Analytics a poskytují další nástroje pro analýzu.  

![Logs](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Application Insights shromažďuje telemetrii pro webové aplikace, které jsou nainstalované na různých platformách. Ukládá data do Azure metriky a Log Analytics a poskytuje rozsáhlou sadu bohaté možnosti nástrojů nad stávajícími nástroji pro analýzu těchto dat pro analýzy a vizualizace svá data. To umožňuje využití běžné sady služeb, jako jsou výstrahy, prohledávání protokolů a řídicí panely, které použijete pro další sledování.


![App Insights](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>Mapa služeb
Řešení Service Map poskytuje vizuální reprezentaci těchto virtuálních počítačů s jejich procesy a závislosti. Ukládá většinu těchto dat v Log Analytics, takže je můžete analyzovat s ostatními daty správy. Konzola Service Map také načte data ze služby Log Analytics a Stihneme ji v rámci virtuálního počítače analyzován.

![Mapa služeb](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>Přenos dat monitorování

### <a name="metrics-to-logs"></a>Metriky, které protokoly
Metriky je možné replikovat také do Log Analytics k analýze komplexní s jinými datovými typy pomocí jeho Bohatý dotazovací jazyk. Můžete také zachovat data protokolu pro delší dobu než metriky, které umožňuje provádět sledování trendů v čase. Když metriky nebo jiná data výkonu je uložená ve službě Log Analytics, která data funguje jako protokol. Použití metrik pro podporu téměř v reálném čase analýzy a upozornění při používání protokolů pro sledování trendů a analýzy s ostatními daty.

Pokyny pro shromažďování metrik z prostředků v Azure můžete získat [metriky pro použití v Log Analytics a Azure shromažďovat protokoly služby](../log-analytics/log-analytics-azure-storage.md). Získejte pokyny pro shromažďování metrik prostředků z prostředků Azure PaaS za [konfigurace shromažďování metrik prostředků Azure PaaS s využitím Log Analytics](../log-analytics/log-analytics-collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Protokoly a metriky
Jak je popsáno výše, metriky reagovaly více než tento počet protokolů umožňuje vytvářet výstrahy s nižší latencí a s nižšími náklady. Log Analytics shromažďuje značné množství číselná data, která je vhodná pro metriky, ale není uložený v Azure metriky. Běžným příkladem jsou data o výkonu shromážděná z agentů a řešení pro správu. Některé z těchto hodnot je možné zkopírovat do Azure metrik, kde jsou k dispozici pro výstrahy a analýzy s Průzkumníkem metrik.

Vysvětlení této funkce je k dispozici na [rychleji metrika výstrahy pro protokoly teď v omezené verzi public preview](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/). Seznam hodnot podpora je k dispozici na [podporované metody metrik a vytváření nových metrik výstrah](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md).

### <a name="event-hub"></a>Centrum událostí
Kromě použití nástroje pro analýzu dat monitorování v Azure, můžete chtít předat ho do externího nástroje a bezpečnostní informace a produktů pro správu (SIEM) události. To se obvykle provádí pomocí [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/). 

Získejte pokyny různých typů údajů o monitorování na [pomocí externího nástroje pro monitorování data do centra událostí pro používání Azure Stream](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Další postup

- Další informace o [data k dispozici monitorování](monitoring-data-sources.md) pro různé prostředky v Azure. 