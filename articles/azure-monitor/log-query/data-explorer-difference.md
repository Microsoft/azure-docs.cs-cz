---
title: Monitorování protokolu dotazu jazyka rozdíly ve službě Azure | Dokumentace Microsoftu
description: Referenční informace pro jazyk dotazu Průzkumník dat používaný službou Azure Monitor. Zahrnuje další elementy, které jsou specifické pro Azure Monitor a prvky není podporována v dotazech protokolu Azure Monitor.
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
ms.openlocfilehash: 9c58796fa19ffb6d38582c809f7bb6ca948bd92c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003618"
---
# <a name="azure-monitor-log-query-language-differences"></a>Monitorování protokolu dotazu jazyka rozdíly ve službě Azure

Při [protokolů ve službě Azure Monitor](log-query-overview.md) je postavená na [Průzkumník dat Azure](/azure/data-explorer) a používá [stejné dotazovací jazyk](/azure/kusto/query), verzi jazyka mají určité odlišnosti. Tento článek identifikuje elementy, které se liší mezi verzí jazyk používaný pro Průzkumníka dat a je verze použitá pro dotazů na protokoly Azure monitoru.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="data-explorer-elements-not-supported-in-azure-monitor"></a>Datové prvky Explorer není podporována ve službě Azure Monitor
Následující části popisují elementy dotazovacího jazyka Průzkumník dat, které nejsou podporovány službou Azure Monitor.

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
- Přístup ke kompletní [referenční dokumentaci pro Průzkumníka dat dotazovací jazyk](/azure/kusto/query/).
