---
title: Monitorování Log Analytics a Kusto jazyk rozdíly ve službě Azure | Dokumentace Microsoftu
description: Popisuje rozdíly mezi dotazy Log Analytics a základní jazyk Kusto.
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
ms.date: 09/25/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 109ffa6abb34dad6a00210a5c2c726bdfdde094f
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184757"
---
# <a name="log-analytics-and-kusto-language-differences"></a>Rozdíly v jazyce log Analytics a Kusto
[Protokolovat analytických dotazů](../log-analytics-queries.md) jsou vytvářeny s použitím [Kusto jazyka](/azure/kusto/query). I když, jak je popsáno v tomto článku jsou určité rozdíly ze standardní jazyk a implementaci Log Analytics.


## <a name="statements-not-supported-in-log-analytics"></a>Příkazy nejsou podporovány v Log Analytics
Následující příkazy nejsou podporovány v Log Analytics.

* [Alias](/kusto/query/aliasstatement)
* [Parametry dotazu.](/azure/kusto/query/queryparametersstatement)

## <a name="functions-not-supported-in-log-analytics"></a>Funkce není podporována v Log Analytics
Následující funkce nejsou podporované ve službě Log Analytics.

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [Database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

## <a name="operators-not-supported-in-log-analytics"></a>Operátory nejsou podporovány v Log Analytics
Následující operátory nejsou podporovány v Log Analytics.

* [Spojení mezi clusterem](/azure/kusto/query/joincrosscluster)
* [externaldata – operátor](/azure/kusto/query/externaldata-operator)

## <a name="plugins-not-supported-in-log-analytics"></a>Moduly plug-in není podporován v Log Analytics
Následující moduly plug-in nejsou podporovány v Log Analytics.
* [modul plug-in sql_request](/azure/kusto/query/sqlrequestplugin)


## <a name="log-analytics-specific-operators"></a>Operátory konkrétní log Analytics
* [App() použijte](app-expression.md)
* [Workspace()](workspace-expression.md)

## <a name="next-steps"></a>Další postup

- Přečtěte si informace o dotazech v [Log Analytics](../log-analytics-queries.md).
- Provede lekce na zápis na [dotazu Log Analytics](/log-analytics/query-language/get-started-queries.md).
- Přístup ke kompletní [referenční dokumentaci pro Kusto](/azure/kusto/query/).