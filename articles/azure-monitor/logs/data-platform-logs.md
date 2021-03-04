---
title: Protokoly služby Azure Monitor
description: Popisuje Azure Monitor protokoly, které se používají pro pokročilou analýzu dat monitorování.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 5066264777c66369205489fb27a6f9206c5da521
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707995"
---
# <a name="azure-monitor-logs-overview"></a>Přehled protokolů Azure Monitoru
Protokoly Azure Monitor jsou funkcí Azure Monitor, která shromažďuje a uspořádává data protokolů a výkonu z [monitorovaných prostředků](../monitor-reference.md). Data z různých zdrojů, jako jsou [protokoly platforem](../essentials/platform-logs-overview.md) ze služeb Azure, data protokolů a výkonu z [agentů virtuálních počítačů](../agents/agents-overview.md)a data o využití a výkonu z [aplikací](../app/app-insights-overview.md) , se dají konsolidovat do jednoho pracovního prostoru, aby je bylo možné analyzovat společně pomocí sofistikovaného dotazovacího jazyka, který je schopný rychle analyzovat miliony záznamů. Můžete provést jednoduchý dotaz, který jednoduše načte konkrétní sadu záznamů, nebo provede propracované analýzy dat, které identifikují kritické vzory v datech monitorování. Pracujte s dotazy protokolů a jejich výsledky interaktivně pomocí Log Analytics, používejte je v pravidlech výstrah k proaktivnímu upozorňování na problémy nebo Vizualizujte jejich výsledky v sešitu nebo řídicím panelu.

> [!NOTE]
> Protokoly Azure Monitor tvoří jednu polovinu datové platformy, která podporuje Azure Monitor. Druhý je [Azure monitor metriky](../essentials/data-platform-metrics.md) , které ukládají číselná data do databáze časových řad. Díky tomu jsou tato data přehlednější než data v Azure Monitor protokoly a můžou podporovat scénáře téměř v reálném čase, které jsou zvláště užitečné pro upozorňování a rychlé zjišťování problémů. Metriky, i když můžou ukládat jenom číselná data do konkrétní struktury, zatímco protokoly můžou ukládat různé datové typy z každého s vlastní strukturou. Pomocí dotazů protokolu, které se nedají použít k analýze dat metrik, můžete také provádět komplexní analýzy dat protokolů.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Co se dá dělat s protokoly Azure Monitor?
Následující tabulka popisuje některé z různých způsobů, jak můžete použít protokoly v Azure Monitor:

|  |  |
|:---|:---|
| **Analýza** | Použití [Log Analytics](./log-analytics-tutorial.md) v Azure Portal k zápisu [dotazů protokolu](./log-query-overview.md) a interaktivní analýze dat protokolu pomocí výkonného analytického modulu |
| **Výstraha** | Nakonfigurujte [pravidlo upozornění protokolu](../alerts/alerts-log.md) , které pošle oznámení, nebo provede [automatizovanou akci](../alerts/action-groups.md) , když výsledky dotazu odpovídají konkrétnímu výsledku. |
| **Vizualizace** | Připněte výsledky dotazu vykreslené jako tabulky nebo grafy na [řídicí panel Azure](../../azure-portal/azure-portal-dashboards.md).<br>Vytvoří [sešit](../visualize/workbooks-overview.md) pro kombinování s více sadami dat v interaktivní sestavě. <br>Exportujte výsledky dotazu do [Power BI](../visualize/powerbi.md) , abyste mohli používat různé vizualizace a sdílet s uživateli mimo Azure.<br>Exportujte výsledky dotazu do [Grafana](../visualize/grafana-plugin.md) a využijte jeho řídicí panely a kombinaci s jinými zdroji dat.|
| **Přehledy** | Podpora [přehledů](../monitor-reference.md#insights-and-core-solutions) , které poskytují přizpůsobené možnosti monitorování pro konkrétní aplikace a služby.  |
| **Stahovat** | Přístup k výsledkům dotazu protokolu z příkazového řádku pomocí [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Přístup k výsledkům dotazu protokolu z příkazového řádku pomocí [rutin prostředí PowerShell](/powershell/module/az.operationalinsights).<br>Přístup k výsledkům dotazu protokolu z vlastní aplikace pomocí [REST API](https://dev.loganalytics.io/). |
| **Export** | Konfigurace [automatizovaného exportu dat protokolu](./logs-data-export.md) na účet služby Azure Storage nebo Azure Event Hubs.<br>Sestavte pracovní postup, který načte data protokolu a zkopíruje ho do externího umístění pomocí [Logic Apps](./logicapp-flow-connector.md). |

![Přehled protokolů](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Shromažďování dat
Po vytvoření pracovního prostoru Log Analytics musíte nakonfigurovat různé zdroje pro posílání svých dat. Nejsou shromažďována žádná data automaticky. Tato konfigurace se liší v závislosti na zdroji dat. [Vytvořte například nastavení diagnostiky](../essentials/diagnostic-settings.md) pro odesílání protokolů prostředků z prostředků Azure do pracovního prostoru. [Umožněte, aby přehledy virtuálních počítačů](../vm/vminsights-enable-overview.md) shromáždily data z virtuálních počítačů. Nakonfigurujte [zdroje dat v pracovním prostoru](../agents/data-sources.md) , abyste mohli shromažďovat další události a data o výkonu.

- Úplný seznam zdrojů dat, které můžete nakonfigurovat tak, aby odesílala data do protokolů Azure Monitor, najdete v tématu [co je monitorované pomocí Azure monitor](../monitor-reference.md) .


## <a name="log-analytics-workspaces"></a>Pracovní prostory služby Log Analytics
Data shromážděná protokolem Azure Monitor se ukládají do jednoho nebo více [Log Analytics pracovních prostorů](./design-logs-deployment.md). Pracovní prostor definuje geografické umístění dat a přístupová práva definující, kteří uživatelé mají přístup k datům, a nastavení konfigurace, jako je například cenová úroveň a uchovávání dat.  

Aby bylo možné používat protokoly Azure Monitor, je nutné vytvořit alespoň jeden pracovní prostor. Jeden pracovní prostor může být dostatečný pro všechna vaše data monitorování, nebo se může rozhodnout vytvořit několik pracovních prostorů v závislosti na vašich požadavcích. Například můžete mít jeden pracovní prostor pro vaše produkční data a další pro testování. 

- Pokud chcete vytvořit nový pracovní prostor, přečtěte si téma [Vytvoření pracovního prostoru Log Analytics v Azure Portal](./quick-create-workspace.md) .
- Pokyny k vytvoření více pracovních prostorů najdete v tématu [navrhování Azure Monitorch protokolů nasazení](design-logs-deployment.md) .

## <a name="data-structure"></a>Struktura dat
Dotazy protokolu načítají svá data z pracovního prostoru Log Analytics. Každý pracovní prostor obsahuje více tabulek, které jsou uspořádány do samostatných sloupců s více řádky dat. Každá tabulka je definována jedinečnou sadou sloupců, které jsou sdíleny řádky dat poskytovanými zdrojem dat. 

[![Struktura Azure Monitorch protokolů](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Data protokolu z Application Insights jsou také uložená v protokolech Azure Monitor, ale v závislosti na tom, jak je vaše aplikace nakonfigurovaná, se ukládají odlišně. V případě aplikace založené na pracovním prostoru se data ukládají do Log Analyticsho pracovního prostoru ve standardní sadě tabulek pro uchovávání dat, jako jsou například žádosti o aplikace, výjimky a zobrazení stránek. Stejný pracovní prostor může používat více aplikací. Pro klasickou aplikaci se data neukládají do Log Analyticsho pracovního prostoru. Používá stejný dotazovací jazyk a vytváří a spouští dotazy pomocí stejného Log Analytics nástroje v Azure Portal. Data pro klasické aplikace se sice ukládají odděleně od sebe. Jeho Obecná struktura je stejná jako aplikace založené na pracovních prostorech, i když se názvy tabulek a sloupců liší. Podrobné porovnání schématu pro aplikace založené na pracovních prostorech a klasických aplikacích najdete v tématu [změny prostředků na základě pracovního prostoru](../app/apm-tables.md) .


> [!NOTE]
> Pořád zajišťujeme úplnou zpětnou kompatibilitu pro vaše Application Insights dotazy na prostředky, sešity a výstrahy založené na protokolu v rámci Application Insights prostředí. Chcete-li se dotazovat/zobrazit proti [nové struktuře nebo schématu tabulky založené na pracovním prostoru](../app/apm-tables.md) , musíte nejprve přejít do svého pracovního prostoru Log Analytics. V rámci verze Preview vám výběr **protokolů** z podoken Application Insights umožní přístup k prostředí klasických Application Insights dotazů. Další podrobnosti najdete v [oboru dotazů](./scope.md) .


[![Struktura protokolů Azure Monitor pro Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Dotazy na protokoly
Data se načítají z Log Analyticsho pracovního prostoru pomocí dotazu protokolu, který je požadavkem na zpracování dat a vrácení výsledků, který je jen pro čtení. Dotazy protokolu se zapisují do [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/), což je stejný dotazovací jazyk, který používá Azure Průzkumník dat. Dotazy protokolu v Log Analytics můžete zapsat k interaktivní analýze jejich výsledků, jejich použití v pravidlech výstrah k proaktivnímu upozorňování na problémy nebo zahrnutí jejich výsledků do sešitů nebo řídicích panelů. Přehledy obsahují předem připravené dotazy na podporu jejich zobrazení a sešitů.

- V tématu věnovaném [dotazům protokolu v Azure monitor](./log-query-overview.md) najdete seznam, kde se používají dotazy protokolu, a odkazy na kurzy a další dokumentaci, které vám pomohou začít.

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Pomocí Log Analytics, což je nástroj v Azure Portal, můžete upravovat a spouštět dotazy protokolů a interaktivně analyzovat jejich výsledky. Pak můžete pomocí dotazů, které vytvoříte, použít k podpoře dalších funkcí v Azure Monitor, jako jsou například výstrahy a sešity dotazů protokolu. Přístup k Log Analytics z možnosti **protokoly** v nabídce Azure monitor nebo z většiny ostatních služeb v Azure Portal.

- Popis Log Analytics najdete [v tématu přehled Log Analytics v Azure monitor](./log-analytics-overview.md) . 
- Log Analytics v tomto [kurzu](./log-analytics-tutorial.md) se dozvíte, jak pomocí Log Analytics funkcí vytvořit jednoduchý dotaz protokolu a analyzovat jeho výsledky.



## <a name="relationship-to-azure-data-explorer"></a>Vztah k Azure Průzkumník dat
Protokoly Azure Monitor jsou založené na Azure Průzkumník dat. Log Analytics pracovní prostor má přibližně ekvivalent databáze ve službě Azure Průzkumník dat, tabulky jsou strukturované stejně a obě používají stejný dotazovací jazyk Kusto (KQL). Zkušenost s používáním Log Analytics pro práci s dotazy Azure Monitor v Azure Portal je podobná zkušenostem s využitím webového uživatelského rozhraní Azure Průzkumník dat. Můžete dokonce [zahrnout data z pracovního prostoru Log Analytics v dotazu Azure Průzkumník dat](/azure/data-explorer/query-monitor-data). 


## <a name="next-steps"></a>Další kroky

- Přečtěte si o [dotazech protokolu](./log-query-overview.md) pro načtení a analýzu dat z log Analyticsho pracovního prostoru.
- Seznamte [se s metrikami v Azure monitor](../essentials/data-platform-metrics.md).
- Přečtěte si o [dostupných datech monitorování](../agents/data-sources.md) různých prostředků v Azure.