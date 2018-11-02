---
title: Referenční informace k jazyk Azure Log Analytics monitorování | Dokumentace Microsoftu
description: Referenční informace pro jazyk dotazu Průzkumník dat používaný službou Log Analytics. Zahrnuje další elementy, které jsou specifické pro Log Analytics a prvky není podporována v dotazech Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 65f3388074ff7a84c6e0516a64665019d5883ab1
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913168"
---
# <a name="log-analytics-query-language-differences"></a>Protokolovat rozdíly jazyk dotazu Analytics

Zatímco [Log Analytics](../log-analytics-queries.md) je postavená na [Průzkumník dat Azure](/azure//data-explorer) a používá [stejné dotazovací jazyk](/azure/kusto/query), verzi jazyka mají určité odlišnosti. Tento článek identifikuje elementy, které se liší mezi verzemi jazyk používaný pro Průzkumníka dat a je verze použitá pro dotazy Log Analytics.

## <a name="data-explorer-elements-not-supported-in-log-analytics"></a>Datové prvky Explorer není podporována v Log Analytics
Následující části popisují elementy dotazovacího jazyka Průzkumník dat, které nejsou podporované službou Log Analytics.

### <a name="statements-not-supported-in-log-analytics"></a>Příkazy nejsou podporovány v Log Analytics

* [Alias](/kusto/query/aliasstatement)
* [Parametry dotazu.](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-log-analytics"></a>Funkce není podporována v Log Analytics

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [Database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-log-analytics"></a>Operátory nejsou podporovány v Log Analytics

* [Spojení mezi clusterem](/azure/kusto/query/joincrosscluster)
* [externaldata – operátor](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-log-analytics"></a>Moduly plug-in není podporován v Log Analytics

* [modul plug-in sql_request](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-log-analytics"></a>Další operátory ve službě Log Analytics
Následující operátory podporoval konkrétní funkce Log Analytics a nejsou k dispozici mimo službu Log Analytics.

* [App() použijte](app-expression.md)
* [Workspace()](workspace-expression.md)

## <a name="next-steps"></a>Další postup

- Získání odkazů na jiné [prostředky pro psaní dotazy Log Analytics](query-language.md).
- Přístup ke kompletní [referenční dokumentaci pro Průzkumníka dat dotazovací jazyk](/azure/kusto/query/).
