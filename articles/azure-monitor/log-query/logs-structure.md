---
title: Struktura protokolů monitorování Azure | Dokumenty společnosti Microsoft
description: K načtení dat protokolu z Azure Monitoru vyžadujete dotaz protokolu.  Tento článek popisuje, jak se nové dotazy protokolu používají ve službě Azure Monitor a poskytuje koncepty, které je třeba pochopit před vytvořením.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 1d647ba7e8d4f0e29252dfff95099e39bab87895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662072"
---
# <a name="structure-of-azure-monitor-logs"></a>Struktura protokolů monitorování Azure
Možnost rychle získat přehled o vašich datech pomocí [dotazu protokolu](log-query-overview.md) je výkonná funkce Azure Monitoru. Chcete-li vytvořit efektivní a užitečné dotazy, měli byste pochopit některé základní pojmy, například kde jsou umístěna data, která chcete a jak je strukturována. Tento článek obsahuje základní koncepty, které potřebujete začít.

## <a name="overview"></a>Přehled
Data v protokolech monitorování Azure se ukládají v pracovním prostoru Log Analytics nebo v aplikaci Application Insights. Obě jsou založené na [Azure Data Exploreru,](/azure/data-explorer/) což znamená, že využívají jeho výkonný datový stroj a dotazovací jazyk.

Data v pracovních prostorech a aplikacích jsou uspořádána do tabulek, z nichž každá ukládá různé druhy dat a má vlastní jedinečnou sadu vlastností. Většina [zdrojů dat](../platform/data-sources.md) bude zapisovat do vlastních tabulek v pracovním prostoru Log Analytics, zatímco Application Insights bude zapisovat do předdefinované sady tabulek v aplikaci Application Insights. Dotazy protokolu jsou velmi flexibilní, což umožňuje snadno kombinovat data z více tabulek a dokonce použít dotaz mezi prostředky kombinovat data z tabulek ve více pracovních prostorech nebo psát dotazy, které kombinují data pracovního prostoru a aplikace.

Následující obrázek ukazuje příklady zdrojů dat, které zapisují do různých tabulek, které se používají v ukázkových dotazech.

![Tabulky](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Pracovní prostor služby Log Analytics
Všechna data shromážděná protokoly monitorování Azure s výjimkou Application Insights jsou uložena v [pracovním prostoru Log Analytics](../platform/manage-access.md). V závislosti na konkrétních požadavcích můžete vytvořit jeden nebo více pracovních prostorů. [Zdroje dat,](../platform/data-sources.md) jako jsou protokoly aktivit a protokoly prostředků z prostředků Azure, agenti na virtuálních počítačích a data z přehledů a řešení monitorování budou zapisovat data do jednoho nebo více pracovních prostorů, které nakonfigurujete jako součást jejich registrace. Další služby, jako je [Azure Security Center](/azure/security-center/) a Azure [Sentinel,](/azure/sentinel/) také používají pracovní prostor Log Analytics k ukládání svých dat, aby je bylo možné analyzovat pomocí dotazů protokolu spolu s daty monitorování z jiných zdrojů.

Různé druhy dat jsou uloženy v různých tabulkách v pracovním prostoru a každá tabulka má jedinečnou sadu vlastností. Standardní sada tabulek se při vytváření přidá do pracovního prostoru a nové tabulky se přidají pro různé zdroje dat, řešení a služby, které jsou na palubě. Můžete také vytvořit vlastní tabulky pomocí [rozhraní API pro sběr dat](../platform/data-collector-api.md).

Tabulky v pracovním prostoru a jejich schéma můžete procházet na kartě **Schéma** v log analytics pro pracovní prostor.

![Schéma pracovního prostoru](media/scope/workspace-schema.png)

Následující dotaz slouží k zobrazení seznamu tabulek v pracovním prostoru a počtu záznamů shromážděných do každého z nich za předchozí den. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Podrobnosti o tabulkách, které vytvářejí, naleznete v dokumentaci ke každému zdroji dat. Příklady zahrnují články pro [zdroje dat agenta](../platform/agent-data-sources.md), [protokoly prostředků](../platform/diagnostic-logs-schema.md)a řešení [monitorování](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Oprávnění pracovního prostoru
V [tématu Návrh nasazení protokolů monitorování Azure](../platform/design-logs-deployment.md) pochopit strategii řízení přístupu a doporučení k poskytování přístupu k datům v pracovním prostoru. Kromě udělení přístupu k samotnému pracovnímu prostoru můžete omezit přístup k jednotlivým tabulkám pomocí [rbac úrovně tabulky](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Aplikace Application Insights
Když vytvoříte aplikaci v Application Insights, odpovídající aplikace se automaticky vytvoří v protokolech monitorování Azure. Ke shromažďování dat není vyžadována žádná konfigurace a aplikace automaticky zapíše data monitorování, jako jsou zobrazení stránek, požadavky a výjimky.

Na rozdíl od pracovního prostoru Log Analytics má aplikace Application Insights pevnou sadu tabulek. Nelze nakonfigurovat jiné zdroje dat pro zápis do aplikace, takže nelze vytvořit žádné další tabulky. 

| Table | Popis | 
|:---|:---|
| availabilityResults | Souhrnná data z testů dostupnosti. |
| prohlížečČasování      | Data o výkonu klienta, například čas na zpracování příchozích dat. |
| customEvents        | Vlastní události vytvořené vaší aplikací. |
| vlastnímetriky       | Vlastní metriky vytvořené vaší aplikací. |
| závislosti        | Volání z aplikace do externích součástí. |
| Výjimky          | Výjimky vyzývané za běhu aplikace. |
| Zobrazení           | Údaje o každém zobrazení webu s informacemi o prohlížeči. |
| performanceCounters | Měření výkonu z výpočetních prostředků podporujících aplikaci. |
| Požadavky            | Podrobnosti o každé žádosti o aplikaci.  |
| Stopy              | Výsledky z distribuovaného trasování. |

Schéma pro každou tabulku můžete zobrazit na kartě **Schéma** v log analytics pro aplikaci.

![Schéma aplikace](media/scope/application-schema.png)

## <a name="standard-properties"></a>Standardní vlastnosti
Zatímco každá tabulka v protokolech monitorování Azure má vlastní schéma, existují standardní vlastnosti sdílené všemi tabulkami. Podrobnosti o jednotlivých službách najdete v tématu [Standardní vlastnosti v protokolech monitorování Azure.](../platform/log-standard-properties.md)

| Pracovní prostor služby Log Analytics | Aplikace Application Insights | Popis |
|:---|:---|:---|
| TimeGenerated | časové razítko  | Datum a čas vytvoření záznamu. |
| Typ          | Itemtype   | Název tabulky, ze které byl záznam načten. |
| _ResourceId   |            | Jedinečný identifikátor prostředku, ke který je záznam přidružen. |
| _IsBillable   |            | Určuje, zda je možné fakturovat ingovaná data. |
| _BilledSize   |            | Určuje velikost dat v bajtech, která budou fakturována. |

## <a name="next-steps"></a>Další kroky
- Přečtěte si o použití [analýzy protokolů k vytváření a úpravám vyhledávání v protokolech](../log-query/portals.md).
- Podívejte se [na kurz psaní dotazů](../log-query/get-started-queries.md) pomocí nového dotazovacího jazyka.
