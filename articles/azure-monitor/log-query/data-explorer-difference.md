---
title: Monitorování protokolu dotazu jazyka rozdíly ve službě Azure | Dokumentace Microsoftu
description: Referenční informace pro jazyk dotaz Kusto používaný službou Azure Monitor. Zahrnuje další elementy, které jsou specifické pro Azure Monitor a prvky není podporována v dotazech protokolu Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: bwren
ms.openlocfilehash: 1185f3f96fd39f168d138d7dbf66e7780884b1fa
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56266726"
---
# <a name="azure-monitor-log-query-language-differences"></a>Monitorování protokolu dotazu jazyka rozdíly ve službě Azure

Zatímco [protokolů ve službě Azure Monitor](log-query-overview.md) je postavená na [Průzkumník dat Azure](/azure/data-explorer) a používá stejná [Kusto dotazovací jazyk](/azure/kusto/query), verzi jazyka mají určité odlišnosti. Tento článek identifikuje elementy, které se liší mezi verzí jazyk používaný pro Průzkumníka dat a je verze použitá pro dotazů na protokoly Azure monitoru.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>KQL elementů nejsou podporovány ve službě Azure Monitor
Následující části popisují elementy Kusto dotazovacího jazyka, které nejsou podporovány službou Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Příkazy nejsou podporovány ve službě Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Parametry dotazu.](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funkce není podporována ve službě Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Ve službě Azure Monitor nejsou podporovány operátory

* [Spojení mezi clusterem](/azure/kusto/query/joincrosscluster)
* [externaldata – operátor](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Moduly plug-in není podporován ve službě Azure Monitor

* [sql_request plugin](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Další operátory ve službě Azure Monitor
Následující operátory podporoval konkrétní funkce Azure monitoru a nejsou k dispozici mimo Azure Monitor.

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Další postup

- Získání odkazů na jiné [prostředky pro psaní Azure Monitor protokolu dotazy](query-language.md).
- Přístup ke kompletní [referenční dokumentaci jazyka dotaz Kusto](/azure/kusto/query/).
