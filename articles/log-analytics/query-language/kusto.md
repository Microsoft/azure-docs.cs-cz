---
title: Referenční informace k jazyk Azure Log Analytics monitorování | Dokumentace Microsoftu
description: Referenční informace pro Kusto jazyk používaný službou Log Analytics. Zahrnuje další elementy, které jsou specifické pro Log Analytics a prvky není podporována v dotazech Log Analytics.
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
ms.openlocfilehash: 5173790436a29fa9947346d711da1a2ddb32bf62
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451064"
---
# <a name="log-analytics-query-language-reference"></a>Protokolovat referenční dokumentace jazyka dotazu Analytics
[Protokolovat analytických dotazů](../log-analytics-queries.md) použít stejné dotazovací jazyk a modul používaný [Průzkumník dat Azure](/azure/data-explorer/). Referenční dokumentace jazyka a další podrobnosti o jazyku můžete přistupovat z následujícího umístění: [referenční informace k jazyku Kusto](/azure/kusto/query)



## <a name="kusto-elements-not-support-in-log-analytics"></a>Prvky Kusto nepodporuje v Log Analytics
Zatímco dotazy Log Analytics se používají provádění Kusto, existují některé prvky Kusto, který nepodporuje jak je popsáno v následujících částech.

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
Aby bylo možné podporoval konkrétní funkce Log Analytics, následující další Kusto operátory jsou k dispozici, které nejsou k dispozici mimo službu Log Analytics. 

* [App() použijte](app-expression.md)
* [Workspace()](workspace-expression.md)

## <a name="next-steps"></a>Další postup

- Přečtěte si informace o dotazech v [Log Analytics](../log-analytics-queries.md).
- Provede lekce na zápis na [dotazu Log Analytics](/log-analytics/query-language/get-started-queries.md).
- Přístup ke kompletní [referenční dokumentaci pro Kusto](/azure/kusto/query/).