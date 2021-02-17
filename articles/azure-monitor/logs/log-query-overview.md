---
title: Dotazy protokolu v Azure Monitor
description: Referenční informace k dotazovacímu jazyku Kusto, který používá Azure Monitor. Obsahuje další prvky specifické pro Azure Monitor a elementy, které nejsou podporovány v dotazech protokolu Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 623230b59d4f479b20b9b8532135bb2b70885ecb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610123"
---
# <a name="log-queries-in-azure-monitor"></a>Dotazy protokolu v Azure Monitor
Protokoly Azure Monitor jsou založené na Azure Průzkumník dat a dotazy protokolu se zapisují pomocí stejného dotazovacího jazyka KQL (Kusto Query Language). Jedná se o bohatý jazyk navržený tak, aby bylo snadné ho číst a vytvářet, takže byste měli být schopni začít psát dotazy s některými základními pokyny.

Mezi oblasti v Azure Monitor, kde budete používat dotazy, patří následující:

- [Log Analytics:](../logs/log-analytics-overview.md) Primární nástroj v Azure Portal pro úpravy dotazů protokolu a interaktivní analýze jejich výsledků. I v případě, že máte v úmyslu používat dotaz protokolu jinde v Azure Monitor, obvykle ho zapíšete a otestujete v Log Analytics před jeho zkopírováním do konečného umístění.
- [Pravidla upozornění protokolů](../platform/alerts-overview.md). Proaktivně Identifikujte problémy z dat ve vašem pracovním prostoru.  Každé pravidlo výstrahy je založené na dotazu protokolu, který se automaticky spouští v pravidelných intervalech.  Výsledky jsou zkontrolovány, aby bylo možné určit, zda má být vytvořena výstraha.
- [Sešity](../visualize/workbooks-overview.md). Zahrňte výsledky dotazů protokolu pomocí různých vizualizací v interaktivních vizuálních sestavách v Azure Portal.
- [Řídicí panely Azure](../learn/tutorial-logs-dashboards.md). Připněte výsledky jakéhokoli dotazu na řídicí panel Azure, který vám umožní vizualizovat data protokolů a metrik společně a volitelně sdílet s ostatními uživateli Azure.
- [Logic Apps](../logs/logicapp-flow-connector.md).  Výsledky dotazu protokolu můžete použít v automatizovaných pracovních postupech pomocí Logic Apps.
- [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult). Použijte výsledky dotazu protokolu ve skriptu PowerShellu z příkazového řádku nebo Azure Automation Runbooku, který používá rutinu Get-AzOperationalInsightsSearchResults.
- [Rozhraní API protokolů Azure monitor](https://dev.loganalytics.io). Načte data protokolu z pracovního prostoru z libovolného klienta REST API.  Požadavek rozhraní API obsahuje dotaz, který se spouští proti Azure Monitor k určení dat, která se mají načíst.

## <a name="getting-started"></a>Začínáme
Nejlepším způsobem, jak začít vytvářet dotazy protokolu pomocí KQL, je využití dostupných kurzů a ukázek.

- [Log Analytics kurz](../log-query/log-analytics-tutorial.md) – kurz použití funkcí Log Analytics což je nástroj, který budete používat v Azure Portal k úpravám a spouštění dotazů. Umožňuje také psát jednoduché dotazy bez nutnosti přímo pracovat s dotazovacím jazykem. Pokud jste Log Analytics ještě nepoužili, začněte tady, abyste porozuměli nástroji, který budete používat s dalšími kurzy a ukázkami.
- [Kurz KQL](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) – průvodce s asistencí na základě základních konceptů KQL a běžných operátorů. Toto je nejlepší místo, kde se můžete pustit do rychlosti s využitím samotného jazyka a struktury dotazů protokolu. 
- [Příklady dotazů](../logs/example-queries.md) – popis ukázkových dotazů, které jsou k dispozici v Log Analytics. Dotazy můžete použít beze změny nebo je použít jako ukázky a získat tak KQL.
- [Ukázky](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor) dotazů – Ukázkové dotazy ilustrují celou řadu různých konceptů.



## <a name="reference-documentation"></a>Referenční dokumentace
[Dokumentace k KQL](/azure/data-explorer/kusto/query/) , včetně reference pro všechny příkazy a operátory, je k dispozici v dokumentaci k Azure Průzkumník dat. I když získáte zdatní pomocí KQL, budete stále pravidelně používat tento odkaz k prozkoumání nových příkazů a scénářů, které jste předtím nepoužili.


## <a name="language-differences"></a>Rozdíly v jazycích
I když Azure Monitor používá stejný KQL jako Azure Průzkumník dat, existují určité rozdíly. V dokumentaci k KQL se určí operátory, které nepodporují Azure Monitor nebo které mají různé funkce. V obsahu Azure Monitor jsou popsány operátory specifické pro Azure Monitor. Následující části obsahují seznam rozdílů mezi verzemi jazyka pro rychlé reference.

### <a name="statements-not-supported-in-azure-monitor"></a>Příkazy nejsou podporované v Azure Monitor

* [Zástupný](/azure/kusto/query/aliasstatement)
* [Parametry dotazů](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funkce, které nejsou podporované v Azure Monitor

* [cluster ()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current (), current_cursor ()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operátory nejsou podporované v Azure Monitor

* [Spojení mezi clustery](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Moduly plug-in nejsou v Azure Monitor podporované

* [Modul plug-in Python](/azure/kusto/query/pythonplugin)
* [Modul plug-in sql_request](/azure/kusto/query/sqlrequestplugin)


### <a name="additional-operators-in-azure-monitor"></a>Další operátory v Azure Monitor
Následující operátory podporují konkrétní Azure Monitor funkce a nejsou k dispozici mimo Azure Monitor.

* [aplikace ()](../logs/app-expression.md)
* [prostředek ()](../log-query/resource-expression.md)
* [pracovní prostor ()](../logs/workspace-expression.md)

## <a name="next-steps"></a>Další kroky
- Projděte si [kurz týkající se psaní dotazů](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor).
- Přístup k kompletní [referenční dokumentaci pro dotazovací jazyk Kusto](/azure/kusto/query/).

