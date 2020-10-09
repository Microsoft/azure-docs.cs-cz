---
title: Rozdíly v jazyce pro dotazování protokolu Azure Monitor | Microsoft Docs
description: Referenční informace k dotazovacímu jazyku Kusto, který používá Azure Monitor. Obsahuje další prvky specifické pro Azure Monitor a elementy, které nejsou podporovány v dotazech protokolu Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/01/2020
ms.openlocfilehash: 265179909c8ae4a6fa630b835bc9993f042d6460
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80585696"
---
# <a name="azure-monitor-log-query-language-differences"></a>Rozdíly v jazyce dotazů protokolu Azure Monitor

I když jsou [protokoly v Azure monitor](log-query-overview.md) postaveny na [Azure Průzkumník dat](/azure/data-explorer) a používají stejný [dotazovací jazyk Kusto](/azure/kusto/query), verze jazyka obsahuje několik rozdílů. Tento článek identifikuje prvky, které se liší v rámci verze jazyka používaného pro Průzkumník dat a verze používané pro dotazy protokolu Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Prvky KQL nejsou podporované v Azure Monitor
Následující části popisují prvky dotazovacího jazyka Kusto, které nejsou podporovány nástrojem Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Příkazy nejsou podporované v Azure Monitor

* [Zástupný](/azure/kusto/query/aliasstatement)
* [Parametry dotazů](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funkce, které nejsou podporované v Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
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


## <a name="additional-operators-in-azure-monitor"></a>Další operátory v Azure Monitor
Následující operátory podporují konkrétní Azure Monitor funkce a nejsou k dispozici mimo Azure Monitor.

* [aplikace ()](app-expression.md)
* [pracovní prostor ()](workspace-expression.md)

## <a name="next-steps"></a>Další kroky

- Získejte odkazy na různé [prostředky pro zápis Azure Monitorch dotazů protokolu](query-language.md).
- Přístup k kompletní [referenční dokumentaci pro dotazovací jazyk Kusto](/azure/kusto/query/).
