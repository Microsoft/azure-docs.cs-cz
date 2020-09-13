---
title: Přihlásí se Azure Monitor | Microsoft Docs
description: Popisuje protokoly v Azure Monitor, které se používají pro pokročilou analýzu dat monitorování.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 09/09/2020
ms.author: bwren
ms.openlocfilehash: e08c649b9a1d7e8b909a413ee435fce30a8d7e48
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032760"
---
# <a name="azure-monitor-logs-overview"></a>Přehled protokolů Azure Monitor
Protokoly Azure Monitor jsou funkcí Azure Monitor, která shromažďuje a uspořádává data protokolu z nejrůznějších zdrojů a zpřístupňuje je pro účely analýzy pomocí sofistikovaného dotazovacího jazyka. Data z různých zdrojů lze konsolidovat do jednoho pracovního prostoru a analyzovat je společně za účelem provádění takových úkolů a analýz trendů, upozorňování a vizualizací.

## <a name="relationship-to-azure-monitor-metrics"></a>Vztah k Azure Monitor metriky
Azure Monitor metriky ukládají číselná data do databáze časových řad, což usnadňuje ukládání těchto dat do protokolů Azure Monitor a umožňuje podporu téměř v reálném čase, což je zvláště užitečné pro upozorňování a rychlé zjišťování problémů. Metriky, i když můžou ukládat jenom číselná data do konkrétní struktury, zatímco protokoly můžou ukládat různé datové typy z každého s vlastní strukturou. Pomocí dotazů protokolu, které se nedají použít k analýze dat metrik, můžete také provádět komplexní analýzy dat protokolů.

Číselná data jsou často odesílána ze zdrojů dat do protokolů společně s metrikami. I když je za shromažďování a uchovávání těchto dat v protokolech dodatečné poplatky, umožňuje zahrnout do protokolů data metriky a analyzovat je s ostatními daty monitorování.

## <a name="relationship-to-azure-data-explorer"></a>Vztah k Azure Průzkumník dat
Protokoly Azure Monitor jsou založené na Azure Průzkumník dat. Log Analytics pracovní prostor má přibližně ekvivalent databáze ve službě Azure Průzkumník dat, tabulky jsou strukturované stejně a obě používají stejný dotazovací jazyk Kusto (KQL). Zkušenost s používáním Log Analytics pro práci s dotazy Azure Monitor v Azure Portal je podobná zkušenostem s využitím webového uživatelského rozhraní Azure Průzkumník dat. Můžete dokonce [zahrnout data z pracovního prostoru Log Analytics v dotazu Azure Průzkumník dat](/azure/data-explorer/query-monitor-data). 


## <a name="structure-of-data"></a>Struktura dat
Data shromážděná protokolem Azure Monitor se ukládají do [pracovního prostoru Log Analytics](./design-logs-deployment.md) , který obsahuje více tabulek, z nichž každý ukládá data z konkrétního zdroje. Pracovní prostor definuje geografické umístění dat a přístupová práva definující, kteří uživatelé mají přístup k datům, a nastavení konfigurace, jako je například cenová úroveň a uchovávání dat. V závislosti na vašich požadavcích můžete použít jeden pracovní prostor pro všechna data monitorování nebo vytvořit několik pracovních prostorů. Pokyny k vytvoření více pracovních prostorů najdete v tématu [navrhování Azure Monitorch protokolů nasazení](design-logs-deployment.md) .

Každý pracovní prostor obsahuje více tabulek, které jsou uspořádány do samostatných sloupců s více řádky dat. Každá tabulka je definována jedinečnou sadou sloupců, které jsou sdíleny řádky dat poskytovanými zdrojem dat. 

[![Struktura Azure Monitorch protokolů](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Data protokolu z Application Insights jsou také uložená v protokolech Azure Monitor, ale v závislosti na tom, jak je vaše aplikace nakonfigurovaná, se ukládají odlišně. V případě aplikace založené na pracovním prostoru se data ukládají do Log Analyticsho pracovního prostoru ve standardní sadě tabulek pro uchovávání dat, jako jsou například žádosti o aplikace, výjimky a zobrazení stránek. Stejný pracovní prostor může používat více aplikací. Pro klasickou aplikaci se data neukládají do Log Analyticsho pracovního prostoru. Používá stejný dotazovací jazyk a vytváří a spouští dotazy pomocí stejného Log Analytics nástroje v Azure Portal. Data pro klasické aplikace se sice ukládají odděleně od sebe. Jeho Obecná struktura je stejná jako aplikace založené na pracovních prostorech, i když se názvy tabulek a sloupců liší. Podrobné porovnání obou najdete v tématu [změny prostředků na základě pracovního prostoru](../app/apm-tables.md) .


> [!NOTE]
> Pořád zajišťujeme úplnou zpětnou kompatibilitu pro vaše Application Insights dotazy na prostředky, sešity a výstrahy založené na protokolu v rámci Application Insights prostředí. Chcete-li se dotazovat/zobrazit proti [nové struktuře nebo schématu tabulky založené na pracovním prostoru](../app/apm-tables.md) , musíte nejprve přejít do svého pracovního prostoru Log Analytics. V rámci verze Preview vám výběr **protokolů** z podoken Application Insights umožní přístup k prostředí klasických Application Insights dotazů. Další podrobnosti najdete v [oboru dotazů](../log-query/scope.md) .


[![Struktura protokolů Azure Monitor pro Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="log-queries"></a>Dotazy na protokoly
Data se načítají z Log Analyticsho pracovního prostoru pomocí [dotazu protokolu](../log-query/log-query-overview.md) , který je požadavkem na zpracování dat a vrácení výsledků, který je jen pro čtení. Dotazy protokolu se zapisují do [jazyka KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/), což je dotazovací jazyk používaný službou Azure Průzkumník dat. Pomocí Log Analytics, což je nástroj v Azure Portal k úpravám a spouštění dotazů protokolů a k interaktivní analýze jejich výsledků. Pak můžete pomocí dotazů, které vytvoříte, použít k podpoře dalších funkcí v Azure Monitor, jako jsou například výstrahy a sešity dotazů protokolu.


## <a name="sources-of-data-for-azure-monitor-logs"></a>Zdroje dat pro protokoly Azure Monitor
Azure Monitor shromažďuje data protokolu z nejrůznějších zdrojů, včetně prostředků v Azure Monitor a agentů spuštěných na virtuálních počítačích. Úplný seznam zdrojů dat, které odesílají data do pracovního prostoru Log Analytics, najdete v tématu [co je monitorované pomocí Azure monitor](../monitor-reference.md) .



## <a name="next-steps"></a>Další kroky

- Přečtěte si o [dotazech protokolu](../log-query/log-query-overview.md) pro načtení a analýzu dat z log Analyticsho pracovního prostoru.
- Seznamte [se s metrikami v Azure monitor](data-platform-metrics.md).
- Přečtěte si o [dostupných datech monitorování](data-sources.md) různých prostředků v Azure.

