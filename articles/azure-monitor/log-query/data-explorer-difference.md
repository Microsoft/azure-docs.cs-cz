---
title: Rozdíly v dotazu na protokol Azure Monitor | Dokumenty společnosti Microsoft
description: Referenční informace pro dotazovací jazyk Kusto používaný službou Azure Monitor. Zahrnuje další prvky specifické pro Azure Monitor a prvky, které nejsou podporovány v dotazech protokolu Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/01/2020
ms.openlocfilehash: 265179909c8ae4a6fa630b835bc9993f042d6460
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585696"
---
# <a name="azure-monitor-log-query-language-differences"></a>Rozdíly v dotazu na protokol Azure Monitor

Zatímco [protokoly ve službě Azure Monitor](log-query-overview.md) jsou postavené na Azure Data [Exploreru](/azure/data-explorer) a používají stejný [dotazovací jazyk Kusto](/azure/kusto/query), verze jazyka má určité rozdíly. Tento článek identifikuje prvky, které se liší mezi verzí jazyka používaného pro Průzkumníka dat a verzí používanou pro dotazy protokolu Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Prvky KQL nejsou v Azure Monitoru podporované
Následující části popisují prvky dotazovacího jazyka Kusto, které Azure Monitor nepodporuje.

### <a name="statements-not-supported-in-azure-monitor"></a>Příkazy, které nejsou ve službě Azure Monitor podporované

* [Alias](/azure/kusto/query/aliasstatement)
* [Parametry dotazu](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funkce, které nejsou ve službě Azure Monitor podporované

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [databáze()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operátoři, kteří nejsou ve službě Azure Monitor podporovaní

* [Spojení mezi clustery](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Pluginy, které nejsou ve službě Azure Monitor podporované

* [Python plugin](/azure/kusto/query/pythonplugin)
* [sql_request plugin](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Další operátoři ve službě Azure Monitor
Následující operátoři podporují konkrétní funkce Azure Monitoru a nejsou k dispozici mimo Azure Monitor.

* [aplikace()](app-expression.md)
* [pracovní prostor()](workspace-expression.md)

## <a name="next-steps"></a>Další kroky

- Získejte odkazy na různé [prostředky pro zápis dotazů protokolu Azure Monitor](query-language.md).
- Získejte přístup k úplné [referenční dokumentaci pro dotazovací jazyk Kusto](/azure/kusto/query/).
