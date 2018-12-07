---
title: Sledování dat shromážděných službou Azure Monitor | Dokumentace Microsoftu
description: Monitorování data shromážděná službou Azure Monitor je rozdělené na metriky, které jsou jednoduché a je schopný zajistit podporu téměř v reálném čase scénáře a protokoly, které jsou uložené ve službě Log Analytics pro pokročilé analýzy.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: bwren
ms.openlocfilehash: 19d448634998f01505c0e274377e1aa8cd788728
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993799"
---
# <a name="monitoring-data-collected-by-azure-monitor"></a>Sledování dat shromážděných službou Azure Monitor
[Azure Monitor](../../azure-monitor/overview.md) je služba, která vám pomůže sledovat vaše aplikace a prostředky, které spoléhají na to. Centrální pro tuto funkci je úložiště dat a další data z monitorovaných prostředků. Tento článek poskytuje úplný popis toho, jak tato data se ukládají a používat Azure Monitor.

Všechna data shromážděná službou Azure Monitor zapadá do jednoho ze dvou základních typů, [metriky](#metrics) a [protokoly](#logs). Metriky jsou číselné hodnoty, které popisují některé aspekty systému v určitém místě v čase. Jsou jednoduché a schopný zajistit podporu téměř v reálném čase scénáře. Protokoly obsahovat různé druhy dat uspořádaných do s různými sadami vlastností pro jednotlivé typy záznamů. Telemetrická data, jako je například událostmi a sledováním jsou uloženy jako protokoly kromě na data výkonu tak, aby se všechny zkombinovat pro analýzu.

![Přehled služby Azure Monitor](media/data-collection/overview.png)

## <a name="metrics"></a>Metriky
Metriky jsou číselné hodnoty, které popisují některé aspekty systému v určitou dobu. Jsou jednoduché a schopný zajistit podporu téměř v reálném čase scénáře. Metriky se budou shromažďovat v pravidelných intervalech, zda hodnoty. Jsou užitečné pro výstrahy, protože můžou být často Vzorkovaná a upozornění můžete aktivuje rychle s relativně jednoduché logikou. 

Například může shromažďování využití procesoru z virtuálního počítače každou minutu nebo počet uživatelů, přihlášení k aplikaci každých 10 minut. Upozornění může vyvolat, když jedna z těchto hodnot shromažďovaným nebo dokonce rozdíl mezi dvěma hodnotami překročí definované prahové hodnoty.

Konkrétní atributy metrik v Azure, patří:

* Pokud není určeno jinak v definici tuto metriku, shromážděných v minutových intervalech.
* Jednoznačně identifikují pomocí název metriky a obor názvů, který funguje jako kategorie.
* Ukládají 93 dnů. Metriky můžete zkopírovat do služby Log Analytics pro dlouhodobé trendy.

Každá hodnota metriky má následující vlastnosti:
* Čas, kdy byl shromážděn hodnotu.
* Představuje typ měření hodnotu.
* Hodnota materiál náleží.
* Vlastní hodnota.
* Některé metriky může mít více rozměrů, jak je popsáno v další části. Vlastní metriky můžete mít až 10 dimenze.

### <a name="multi-dimensional-metrics"></a>Vícedimenzionálních metrik
Dimenze metriky jsou páry název hodnota, které přenášejí další data k popisu hodnota metriky. Například metrika _volného místa na disku_ může mít dimenzi volá _jednotka_ hodnotami _C:_, _D:_, který by umožnil zobrazení buď volného místa na disku ve všech jednotek nebo jednotlivě pro každý disk. 

Následující příklad ukazuje dvě datové sady pro hypotetické metriku s názvem _propustnost sítě_. První datová sada nemá žádné dimenze. Druhý objekt dataset zobrazuje hodnoty pomocí dvou dimenzí I_P Address_ a _směr_:

### <a name="network-throughput"></a>Propustnost sítě

 |Timestamp        | Hodnota metriky | 
   | ------------- |:-------------| 
   | 8/9/2017 8:14 | 1,331.8 kb/s | 
   | 8/9/2017 8:15 | 1,141.4 kb/s |
   | 8/9/2017 8:16 | 1,110.2 kb/s |

Tato metrika-rozměrné lze pouze jako odpověď základní otázky "jaké byly Moje propustnost sítě v daném okamžiku?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Propustnost sítě a dvě dimenze ("IP" a "Směr")

| Timestamp          | Dimenze "IP" | Dimenze "Směr" | Hodnota metriky| 
   | ------------- |:-----------------|:------------------- |:-----------|  
   | 8/9/2017 8:14 | IP = "192.168.5.2" | Směr = "Odeslat"    | 646.5 kb/s |
   | 8/9/2017 8:14 | IP = "192.168.5.2" | Směr = "Zobrazit" | 420.1 kB/s |
   | 8/9/2017 8:14 | IP = "10.24.2.15"  | Směr = "Odeslat"    | 150.0 kb/s | 
   | 8/9/2017 8:14 | IP = "10.24.2.15"  | Směr = "Zobrazit" | 115,2 kb/s |
   | 8/9/2017 8:15 | IP = "192.168.5.2" | Směr = "Odeslat"    | 515.2 kb/s |
   | 8/9/2017 8:15 | IP = "192.168.5.2" | Směr = "Zobrazit" | 371.1 kB/s |
   | 8/9/2017 8:15 | IP = "10.24.2.15"  | Směr = "Odeslat"    | 155.0 kb/s |
   | 8/9/2017 8:15 | IP = "10.24.2.15"  | Směr = "Zobrazit" | 100.1 kB/s |

Tato metrika může odpovídat na otázky, jako je například "jaké byly propustnost sítě pro každou IP adresu?" a "a byla odeslána jak velký objem dat přijatých?" Vícedimenzionálních metrik provádět další analýzy a diagnostiku hodnotu ve srovnání s-jednodimenzionální metriky.

### <a name="value-of-metrics"></a>Hodnoty metrik
Jednotlivé metriky obvykle poskytují přehled o malou samostatně. Poskytuje jednu hodnotu bez kontextu než porovnání, které se jednoduché prahové hodnoty. Jsou užitečné v kombinaci s dalšími metrikami k identifikaci trendů a vzorů, nebo v kombinaci s protokoly, které poskytují kontext po konkrétní hodnoty. 

Například určitý počet uživatelů ve vaší aplikaci v daném okamžiku můžete zjistit málo o stavu aplikace. Ale náhlému poklesu u uživatelů, indikován více hodnot stejné metriky, by mohly poukazovat problém. Nadměrné výjimek vyvolaných aplikací a indikován samostatné metriku, by mohly identifikovat problém aplikace, který způsobuje, že rozevírací. Události, které aplikace se vytvoří pro identifikaci selhání v jeho součásti můžete zjistit původní příčinu.

### <a name="sources-of-metric-data"></a>Zdroje dat metriky
Existují tři základní zdroje z metrik shromážděných službou Azure Monitor. Všechny tyto metriky jsou k dispozici v úložišti metrik, kde lze vyhodnotit jejich společně bez ohledu na jejich zdroj.

**Platforma metriky** vytvářejí prostředky Azure a získáte přehled o jejich stav a výkon. Vytvoří jednotlivých typů prostředků [odlišnou sadu metriky](../../monitoring-and-diagnostics/monitoring-supported-metrics.md) bez veškeré požadované konfigurace. 

**Metriky aplikací** jsou vytvořeny pomocí Application Insights pro monitorované aplikace a pomáhají detekovat problémy s výkonem a sledování trendů ve využití vaší aplikace. To zahrnuje tyto hodnoty jako _doba odezvy serveru_ a _výjimky prohlížeče_.

**Vlastní metriky** metriky, které definujete kromě standardních metrik, které jsou automaticky dostupné. Vlastní metriky se musí vytvořit na jediném prostředku ve stejné oblasti jako prostředek. Můžete vytvořit vlastní metriky pomocí následujících metod:
    - [Definujte vlastní metriky v aplikaci](../../application-insights/app-insights-api-custom-events-metrics.md) , který je monitorován pomocí Application Insights. Tyto jsou kromě standardních sadu metriky aplikací.
    - Publikovat vlastní metriky z vašich virtuálních počítačů Windows pomocí [Windows diagnostické rozšíření (WAD)](../../monitoring-and-diagnostics/azure-diagnostics.md).
    - Publikovat vlastní metriky z vašich virtuálních počítačů s Linuxem pomocí [InfluxData Telegraf agenta](https://www.influxdata.com/time-series-platform/telegraf/).
    - Zápis vlastních metrik ze služby Azure pomocí rozhraní API pro vlastní metriky.
    
![Přehled metrik](media/data-collection/metrics-overview.png)

### <a name="what-can-you-do-with-metrics"></a>Co můžete dělat s metrikami?
Úlohy, které můžete provádět pomocí metrik, patří:

- Použití [Průzkumníka metrik](../../monitoring-and-diagnostics/monitoring-metric-charts.md) k analýze shromážděných metrik a vykreslit v grafu. Sledování výkonu prostředků (jako je například virtuální počítač, webu nebo logiky aplikace) tak, že připnete grafy [řídicí panel Azure](../../azure-portal/azure-portal-dashboards.md).
- Konfigurace [metriky pravidlo upozornění](../../monitoring-and-diagnostics/alert-metric.md) , který odešle oznámení, nebo má [automatizované akce](../../monitoring-and-diagnostics/monitoring-action-groups.md) Pokud metrika překročí mezní hodnotu.
- Použití [automatického škálování](../../monitoring-and-diagnostics/monitoring-overview-autoscale.md) zvýšení nebo snížení prostředků na základě metriky překročení prahové hodnoty.
- Metriky tras do Log Analytics k analýze dat metriky spolu s daty log a k uložení hodnoty metrik déle než 93 dní. 
- Stream metrik [centra událostí](../../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) směrovat je do [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) nebo k externím systémům.
- [Archiv](../../monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data.md) historii výkon nebo stav prostředku pro dodržování předpisů, auditování, nebo v režimu offline pro účely vykazování.
- Přístup k hodnoty metrik z příkazového řádku nebo vlastní aplikace s využitím [rutin prostředí PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/?view=azurermps-6.7.0) nebo [rozhraní REST API](../../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).



### <a name="viewing-metrics"></a>Zobrazení metrik
Metriky Azure jsou shromážděny v databázi metrik Azure monitoru. Toto je časové řady databáze optimalizovaný pro rychlé načítání a ukládá hodnoty metrik 93 dní. Kopírování metrik do Log Analytics pro dlouhodobé analýzy a sledování trendů.

Data metriky se používá v mnoha různými způsoby, jak je popsáno výše. Použití [Průzkumníka metrik](../../monitoring-and-diagnostics/monitoring-metric-charts.md) přímo analyzovat data v úložišti metriky a graf hodnoty několika metrik v čase. Můžete zobrazit grafy interaktivně nebo je připnout na řídicí panel k zobrazení se ostatní vizualizace. Můžete také načíst metriky pomocí [Azure, rozhraní REST API pro monitorování](../../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Průzkumník metrik](media/data-collection/metrics-explorer.png)





## <a name="logs"></a>Logs
Protokoly obsahovat různé druhy dat uspořádaných do s různými sadami vlastností pro jednotlivé typy záznamů. Protokoly mohou obsahovat číselných hodnot, jako třeba metriky, ale obvykle obsahují textová data se podrobný popis. Tyto další se liší od metriky v tom, že se liší v jejich strukturu a často nejsou shromážděny v pravidelných intervalech.

Běžný typ položky protokolu je událost, která se shromažďují nedojde. Jsou už vytvořené aplikace nebo služby a obvykle zahrnují dostatek informací, které poskytují úplný kontext na své vlastní. Například událost může znamenat, že určitý prostředek vytvořil nebo změnil, nový hostitel spuštěna v reakci na zvýšení provozu, nebo v aplikaci došlo k chybě.

Protokoly jsou zvláště užitečné pro kombinování dat z různých zdrojů, pro komplexní analýzu a trendy v čase. Vzhledem k tomu, že formát dat se může lišit, aplikace můžete vytvořit vlastní protokoly pomocí struktura, která potřebují. Metriky jsou replikovány i v protokolech kombinovat s dalšími daty monitorování pro vytvoření trendu a ostatní data analýzy.



### <a name="log-analytics"></a>Log Analytics
Shromážděné službou Azure Monitor protokoly se ukládají ve službě Log Analytics, která shromažďuje telemetrii a další data z různých zdrojů. Poskytuje Bohatý dotazovací jazyk a analytics modul, který poskytuje přehled o fungování vašich aplikací a prostředků. Služby, jako ostatní Azure [Azure Security Center](../../security-center/security-center-intro.md) ukládají data v Log Analytics, aby bylo možné poskytovat společnou datovou platformu napříč správu Azure.

> [!IMPORTANT]
> Data ze služby Application Insights se ukládají ve službě Log Analytics jako jiná data protokolu s tím rozdílem, že je uložena v samostatném oddílu. Tento atribut podporuje stejné funkce jako ostatní data služby Log Analytics, ale je nutné použít [konzoly Application Insights](../../application-insights/app-insights-analytics.md) nebo [API pro Application Insights](https://dev.applicationinsights.io/) pro přístup k těmto datům. Můžete použít [napříč prostředky dotazu](../../azure-monitor/log-query/cross-workspace-query.md) k analýze dat aplikací společně s další data protokolu.


### <a name="sources-of-log-data"></a>Zdroje dat protokolu
Log Analytics může shromažďovat data z široké škály zdrojů i v rámci Azure i z místních prostředků. Zdroje dat zapsaných do Log Analytics patří následující:

- [Protokoly aktivit](../../azure-monitor/platform/collect-activity-logs.md) z prostředků Azure, které obsahují informace o své konfiguraci a stavu a [diagnostické protokoly](../../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) , které poskytují přehled o jejich operace.
- Agenty na [Windows](../../log-analytics/log-analytics-windows-agent.md) a [Linux](../../log-analytics/log-analytics-quick-collect-linux-computer.md) virtuálních počítačů, které odesílají telemetrii z hostovaného operačního systému a aplikací do Log Analytics podle [zdroje dat](../../azure-monitor/platform/agent-data-sources.md) , který můžete nakonfigurovat.
- Aplikace data shromážděná pomocí [Application Insights](https://docs.microsoft.com/azure/application-insights/).
- Data a poskytují přehled o konkrétní aplikaci nebo službu [řešení monitorování](../insights/solutions.md) nebo funkce, jako jsou přehledy o kontejnerech, Insights virtuálního počítače nebo Insights skupiny prostředků.
- Shromážděná data zabezpečení [Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- [Metriky](#metrics) z prostředků Azure. To umožňuje ukládat metriky po dobu delší než 93 dnů a analyzujte je pomocí jiných dat protokolu.
- Telemetrická data zapsána do [služby Azure Storage](../../azure-monitor/platform/azure-storage-iis-table.md).
- Vlastní data z jakéhokoli klienta REST API pomocí [rozhraní API kolekce dat HTTP](../../azure-monitor/platform/data-collector-api.md) klienta nebo z [aplikace logiky Azure](https://docs.microsoft.com/azure/logic-apps/) pracovního postupu.

![Komponenty služby Log Analytics](media/data-collection/logs-overview.png)




### <a name="what-can-you-do-with-logs"></a>Co můžete dělat s protokoly?
Úlohy, které můžete provést pomocí protokolů zahrnují následující:

- Použití [stránce Log Analytics](../../azure-monitor/log-query/get-started-portal.md) na webu Azure Portal psát dotazy, analýza dat protokolu.  Připnout výsledky se vykresluje jako tabulky nebo grafy [řídicí panel Azure](../../azure-portal/azure-portal-dashboards.md).
- Konfigurace [pravidel upozornění protokolů](../../monitoring-and-diagnostics/alert-log.md) , který odešle oznámení, nebo má [automatizované akce](../../monitoring-and-diagnostics/monitoring-action-groups.md) při výsledky dotazu splňují konkrétní výsledek.
- Vytvořit pracovní postup na základě dat v Log Analytics pomocí [Logic Apps](~/articles/logic-apps/index.yml).
- Export výsledků dotazu na [Power BI](../../azure-monitor/platform/powerbi.md) a použít různé vizualizace a sdílet s uživateli mimo Azure.
- Přístup k hodnoty metrik z příkazového řádku nebo vlastní aplikace s využitím [rutin prostředí PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/?view=azurermps-6.8.1) nebo [rozhraní REST API](https://dev.loganalytics.io/).

### <a name="viewing-log-data"></a>Zobrazení dat protokolu
Všechna data ze služby Log Analytics jsou načítány s použitím [dotaz protokolu](../../azure-monitor/log-query/log-query-overview.md) , který určuje konkrétní sady dat. Dotazy jsou zapsány pomocí [dotazovací jazyk Log Analytics](../../azure-monitor/log-query/get-started-queries.md) tedy Bohatý dotazovací jazyk rychle načíst, konsolidovat a analyzovat shromážděná data. Použití [stránce Log Analytics](../../azure-monitor/log-query/portals.md) na webu Azure Portal přímo analyzovat data ve vašich metrik, ukládání a graf hodnoty několika metrik v čase. Můžete zobrazit grafy interaktivně nebo je připnout na řídicí panel k zobrazení se ostatní vizualizace. Můžete také načíst metriky pomocí [Azure, rozhraní REST API pro monitorování](../../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Logs](media/data-collection/logs.png)

## <a name="convert-monitoring-data"></a>Převod dat monitorování

### <a name="metrics-to-logs"></a>Metriky, které protokoly
Metriky můžete zkopírovat do provádět komplexní analýzy s jinými datovými typy s použitím jeho Bohatý dotazovací jazyk Log Analytics. Můžete také zachovat data protokolu pro delší dobu než metriky, které umožňuje provádět sledování trendů v čase. Když metriky nebo jiná data výkonu je uložená ve službě Log Analytics, která data funguje jako protokol. Použití metrik pro podporu téměř v reálném čase analýzy a upozornění při používání protokolů pro sledování trendů a analýzy s ostatními daty.

Pokyny pro shromažďování metrik z prostředků v Azure můžete získat [metriky pro použití v Log Analytics a Azure shromažďovat protokoly služby](../../azure-monitor/platform/collect-azure-metrics-logs.md). Získejte pokyny pro shromažďování metrik prostředků z prostředků Azure PaaS za [konfigurace shromažďování metrik prostředků Azure PaaS s využitím Log Analytics](../../azure-monitor/platform/collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Protokoly a metriky
Jak je popsáno výše, jsou responzivní více než tento počet protokolů, metrik, takže můžete vytvářet upozornění s nižší latencí a s nižšími náklady. Log Analytics shromažďuje značné množství číselná data, která je vhodná pro metriky, ale není uložený v databázi Azure metriky.  Běžným příkladem jsou data o výkonu shromážděná z agentů a řešení pro správu. Některé z těchto hodnot je možné zkopírovat do databáze metrik, kde jsou k dispozici pro výstrahy a analýzy s Průzkumníkem metrik.

Vysvětlení této funkce je k dispozici na [vytvořit upozornění metriky pro protokoly ve službě Azure Monitor](../../monitoring-and-diagnostics/monitoring-metric-alerts-logs.md). Seznam hodnot podpora je k dispozici na [podporované metriky ve službě Azure Monitor](../../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces).

## <a name="stream-data-to-external-systems"></a>Datový Stream k externím systémům
Kromě použití nástroje pro analýzu dat monitorování v Azure, můžete mít povinnost předat externího nástroje, jako jsou informace o zabezpečení a událostí produktů pro správu (SIEM). Toto přesměrování se obvykle provádí přímo z monitorovaných prostředků prostřednictvím [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/). 

Získejte pokyny různých typů údajů o monitorování na [pomocí externího nástroje pro monitorování data do centra událostí pro používání Azure Stream](../../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Další postup

- Další informace o [data k dispozici monitorování](data-sources.md) pro různé prostředky v Azure.
