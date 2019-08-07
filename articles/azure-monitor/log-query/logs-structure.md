---
title: Struktura protokolů Azure Monitor | Microsoft Docs
description: Vyžadujete, aby dotaz protokolu načetl data protokolu z Azure Monitor.  Tento článek popisuje, jak se v Azure Monitor používají nové dotazy protokolu, a poskytuje koncepty, které musíte před vytvořením porozumět.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: 6f5ae426018c9e7fa2ac586a2886c8e5e609069b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68813850"
---
# <a name="structure-of-azure-monitor-logs"></a>Struktura protokolů Azure Monitor
Možnost rychle získat přehled o datech pomocí [dotazu protokolu](log-query-overview.md) je výkonná funkce Azure monitor. Chcete-li vytvořit efektivní a užitečné dotazy, měli byste pochopit některé základní koncepty, jako je například umístění dat, která potřebujete, a způsob jejího strukturování. Tento článek poskytuje základní koncepty, které potřebujete, abyste mohli začít.

## <a name="overview"></a>Přehled
Data v Azure Monitor protokoly se ukládají buď v pracovním prostoru Log Analytics, nebo v aplikaci Application Insights. Obě služby jsou napájené z [Azure Průzkumník dat](/azure/data-explorer/) to znamená, že využívají výkonný datový stroj a dotazovací jazyk.

Data v pracovních prostorech a aplikacích jsou uspořádána do tabulek, z nichž každý ukládá různé druhy dat a má svou vlastní jedinečnou sadu vlastností. Většina [zdrojů dat](../platform/data-sources.md) bude zapisovat do vlastních tabulek v Log Analytics pracovním prostoru, zatímco Application Insights zapisuje do předdefinované sady tabulek v Application Insights aplikaci. Dotazy protokolu jsou velmi flexibilní a umožňují snadno kombinovat data z více tabulek a dokonce použít dotaz na více prostředků ke kombinování dat z tabulek ve více pracovních prostorech nebo k zápisu dotazů, které kombinují data z pracovního prostoru a aplikace.

Následující obrázek ukazuje příklady zdrojů dat, které jsou zapsány do různých tabulek, které jsou používány v ukázkových dotazech.

![Tabulky](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Pracovní prostor Log Analytics
Všechna data shromážděná protokolem Azure Monitor s výjimkou Application Insights se ukládají do [log Analyticsho pracovního prostoru](../platform/manage-access.md). V závislosti na konkrétních požadavcích můžete vytvořit jeden nebo více pracovních prostorů. [Zdroje dat](../platform/data-sources.md) , jako jsou protokoly aktivit a diagnostické protokoly z prostředků Azure, agenti na virtuálních počítačích a data z přehledů a řešení pro monitorování, zapisují data do jednoho nebo více pracovních prostorů, které nakonfigurujete jako součást jejich zprovoznění. Jiné služby, například [Azure Security Center](/azure/security-center/) a [Azure Sentinel](/azure/sentinel/) , používají k ukládání dat Log Analytics pracovní prostor, aby je bylo možné analyzovat pomocí dotazů protokolu spolu s daty monitorování z jiných zdrojů.

Různé druhy dat jsou uloženy v různých tabulkách v pracovním prostoru a každá tabulka má jedinečnou sadu vlastností. Do pracovního prostoru se při vytvoření přidá standardní sada tabulek a při jejich zprovoznění se přidají nové tabulky pro různé zdroje dat, řešení a služby. Můžete také vytvořit vlastní tabulky pomocí [rozhraní API kolekce dat](../platform/data-collector-api.md).

Tabulky můžete procházet v pracovním prostoru a jejich schématu na kartě **schéma** v Log Analytics pracovního prostoru.

![Schéma pracovního prostoru](media/scope/workspace-schema.png)

Pomocí následujícího dotazu můžete zobrazit seznam tabulek v pracovním prostoru a počet záznamů, které byly v rámci předchozího dne shromážděny. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Podrobnosti o tabulkách, které vytvoří, najdete v dokumentaci k jednotlivým zdrojům dat. Mezi příklady patří články pro [zdroje dat agenta](../platform/agent-data-sources.md), [diagnostické protokoly](../platform/diagnostic-logs-schema.md)a [řešení monitorování](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Oprávnění k pracovnímu prostoru
Podrobnosti o tom, jak zajistit přístup k datům v pracovním prostoru, najdete v tématu [oprávnění a obor v pracovním prostoru](../platform/manage-access.md#manage-accounts-and-users) . Kromě udělení přístupu k samotnému pracovnímu prostoru můžete omezit přístup k jednotlivým tabulkám pomocí [RBAC na úrovni tabulky](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Application Insights aplikace
Když vytvoříte aplikaci v Application Insights, automaticky se vytvoří odpovídající aplikace v protokolech Azure Monitor. Ke shromažďování dat není nutná žádná konfigurace a aplikace bude automaticky zapisovat data monitorování, například zobrazení stránky, požadavky a výjimky.

Na rozdíl od Log Analytics pracovního prostoru má Application Insights aplikace pevnou sadu tabulek. Nelze nakonfigurovat jiné zdroje dat pro zápis do aplikace, takže nelze vytvořit žádné další tabulky. 

| Table | Popis | 
|:---|:---|
| availabilityResults | Souhrnná data z testů dostupnosti. |
| browserTimings      | Data o výkonu klienta, například čas potřebný ke zpracování příchozích dat. |
| customEvents        | Vlastní události vytvořené vaší aplikací |
| customMetrics       | Vlastní metriky vytvořené vaší aplikací |
| závislosti        | Volání z aplikace do externích součástí. |
| výjimky          | Výjimky vyvolané modulem runtime aplikace |
| pageViews           | Data o jednotlivých zobrazeních webu s informacemi v prohlížeči |
| Čítače výkonu | Měření výkonu z výpočetních prostředků, které podporují aplikaci. |
| požadavků            | Podrobnosti o jednotlivých žádostech o aplikaci  |
| trasování              | Výsledky z distribuovaného trasování. |

Schéma pro každou tabulku můžete zobrazit na kartě **schématu** v Log Analytics pro aplikaci.

![Schéma aplikace](media/scope/application-schema.png)

## <a name="standard-properties"></a>Standardní vlastnosti
Zatímco každá tabulka v protokolech Azure Monitor má vlastní schéma, existují standardní vlastnosti sdílené všemi tabulkami. Podrobnosti o každé z nich najdete [v tématu standardní vlastnosti v protokolech Azure monitor](../platform/log-standard-properties.md) .

| Pracovní prostor Log Analytics | Application Insights aplikace | Popis |
|:---|:---|:---|
| TimeGenerated | timestamp  | Datum a čas vytvoření záznamu |
| type          | itemType   | Název tabulky, ze které byl záznam načten. |
| _ResourceId   |            | Jedinečný identifikátor prostředku, ke kterému je záznam přidružen. |
| _IsBillable   |            | Určuje, zda jsou příjemovaná data fakturovatelná. |
| _BilledSize   |            | Určuje velikost (v bajtech) dat, která se budou fakturovat. |

## <a name="next-steps"></a>Další postup
- Přečtěte si o použití [Log Analytics k vytváření a úpravám prohledávání protokolů](../log-query/portals.md).
- Projděte si [kurz psaní dotazů](../log-query/get-started-queries.md) pomocí nového dotazovacího jazyka.
