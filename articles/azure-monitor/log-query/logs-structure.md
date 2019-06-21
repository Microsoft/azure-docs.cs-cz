---
title: Struktura protokoly Azure monitoru | Dokumentace Microsoftu
description: Vyžadujete protokol dotaz pro načtení data protokolů ze služby Azure Monitor.  Tento článek popisuje, jak nový protokol dotazů se používají ve službě Azure Monitor a poskytuje koncepty, které je třeba porozumět před vytvořením jedné.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: e243ebbc31f9e941678ac76a83738276995b5ba1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297355"
---
# <a name="structure-of-azure-monitor-logs"></a>Struktura protokoly Azure monitoru
Schopnost rychle získat přehled o vašich dat pomocí [dotaz protokolu](log-query-overview.md) je výkonná funkce služby Azure Monitor. Chcete-li vytvořit efektivní a užitečné dotazy, by měl pochopit některé základní pojmy jako je například kde se nachází data, která chcete a jakým způsobem je strukturovaná. Tento článek obsahuje základní koncepty, že které potřebujete, abyste mohli začít.

## <a name="overview"></a>Přehled
Data v protokolech sledování Azure se ukládají v pracovním prostoru Log Analytics nebo aplikaci Application Insights. Obě využívají [Průzkumník dat Azure](/azure/data-explorer/) to znamená, že mohou využít jeho výkonné datové modul a dotaz jazyka.

Data v pracovních prostorech a aplikace jsou uspořádány do tabulky, z nichž každý obsahuje různé druhy dat a má svou vlastní jedinečnou sadu vlastností. Většina [zdroje dat](../platform/data-sources.md) bude zapisovat do vlastní tabulky v pracovním prostoru Log Analytics, zatímco Application Insights bude zapisovat do předdefinovanou sadu tabulek v aplikaci Application Insights. Protokol dotazů jsou velmi flexibilní díky tomu můžete snadno kombinovat data z více tabulek a dokonce využít napříč prostředky dotazu kombinovat data z tabulek v několika pracovních prostorů a psát dotazy, které kombinují data pracovního prostoru a aplikace.

Následující obrázek ukazuje příklady zdrojů dat, které zapsat do různých tabulek, které se používají v ukázkové dotazy.

![Tabulky](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Pracovní prostor Log Analytics
Všechna data shromážděná službou Azure Monitor protokolů s výjimkou Application Insights je uložené v [pracovní prostor Log Analytics](../platform/manage-access.md). Můžete vytvořit jeden nebo více pracovních prostorů v závislosti na vaše konkrétní požadavky. [Zdroje dat](../platform/data-sources.md) jako jsou protokoly aktivit a diagnostické protokoly z prostředků Azure, agenty na virtuální počítače a data z přehledů a monitorování řešení bude zapisovat data do jednoho nebo více pracovních prostorů, které nakonfigurujete v rámci jejich registrace. Jiné služby, jako [Azure Security Center](/azure/security-center/) a [Azure Sentinelu](/azure/sentinel/) použít také pro ukládání dat, takže se dají analyzovat pomocí dotazů protokolu společně s monitorování dat z jiných pracovnímu prostoru Log Analytics zdroje.

Různé druhy dat se ukládají v různých tabulek v pracovním prostoru a každá tabulka má jedinečnou sadu vlastností. Standardní sadu tabulek jsou přidány do pracovního prostoru, když se vytvoří a nové tabulky jsou přidány pro různé datové zdroje, řešení a služby, jako jsou připojili. Můžete také vytvořit vlastní tabulky pomocí [rozhraní API kolekce dat](../platform/data-collector-api.md).

Můžete procházet tabulek v pracovním prostoru a jejich schéma v **schématu** kartu v Log Analytics pro pracovní prostor.

![Schéma pracovního prostoru](media/scope/workspace-schema.png)

Použijte tento dotaz na seznam tabulek v pracovním prostoru a počet záznamů, které se shromažďují do každé za předchozí den. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Najdete v dokumentaci pro každý zdroj dat. Podrobnosti tabulek, který vytvoří. Mezi příklady patří články pro [zdroje dat agenta](../platform/agent-data-sources.md), [diagnostické protokoly](../platform/diagnostic-logs-schema.md), a [řešení monitorování](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Oprávnění pracovního prostoru.
Zobrazit [oprávnění pracovního prostoru a oboru](../platform/manage-access.md#workspace-permissions-and-scope) podrobnosti o poskytování přístupu k datům v pracovním prostoru. Kromě udělení přístupu k pracovní prostor, můžete omezit přístup pro jednotlivé tabulky pomocí [tabulky úroveň RBAC](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Aplikace Application Insights
Při vytváření aplikace ve službě Application Insights v protokolech sledování Azure se automaticky vytvoří příslušné aplikace. Není nutná žádná konfigurace pro shromažďování dat a aplikace bude automaticky zapisovat data, jako je například zobrazení stránek, požadavků a výjimek monitorování.

Na rozdíl od pracovního prostoru Log Analytics Application Insights aplikaci má pevnou sadu tabulek. Nelze nakonfigurovat jiné zdroje dat pro zápis do aplikace tak možné vytvářet žádné další tabulky. 

| Table | Popis | 
|:---|:---|
| availabilityResults | Souhrnná data z testů dostupnosti. |
| browserTimings      | Data o výkonu klienta, jako je doba zpracování příchozí data. |
| customEvents        | Vlastní události vytvořené v aplikaci. |
| customMetrics       | Vlastní metriky, vytvořené vaší aplikace. |
| Závislosti        | Volání z aplikace na externí komponenty. |
| Výjimky          | Výjimky vyvolané doba spuštění aplikace. |
| pageViews           | Zobrazení dat o jednotlivých webů se informace o prohlížeči. |
| performanceCounters | Měření výkonu z výpočetní prostředky, které podporuje aplikace. |
| požadavků            | Podrobnosti o každém z aplikace.  |
| trasování              | Výsledky z distribuované trasování. |

Můžete zobrazit schéma pro každou tabulku **schématu** kartu v Log Analytics pro aplikaci.

![Schéma aplikace](media/scope/application-schema.png)

## <a name="standard-properties"></a>Standardní vlastnosti
Každá tabulka v protokolech sledování Azure obsahuje vlastní schéma, jsou standardní vlastnosti sdílí všechny tabulky. Zobrazit [standardní vlastnosti v Azure Monitor protokoly](../platform/log-standard-properties.md) podrobnosti o každém.

| Pracovní prostor Log Analytics | Aplikace Application Insights | Popis |
|:---|:---|:---|
| TimeGenerated | timestamp  | Datum a čas vytvoření záznamu. |
| Type          | itemType   | Název tabulky záznam byl načten z. |
| _ResourceId   |            | Jedinečný identifikátor záznamu prostředků přidružené. |
| _IsBillable   |            | Určuje, zda přijatých dat se dají fakturovat. |
| _BilledSize   |            | Určuje velikost v bajtech data, která se bude účtovat. |

## <a name="next-steps"></a>Další postup
- Další informace o použití [prohledávání protokolu Log Analytics můžete vytvářet a upravovat](../log-query/portals.md).
- Podívejte se [kurz o psaní dotazů](../log-query/get-started-queries.md) pomocí nového dotazovacího jazyka.
