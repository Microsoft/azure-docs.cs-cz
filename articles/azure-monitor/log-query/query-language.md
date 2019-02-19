---
title: Azure Monitor vyžaduje protokol | Dokumentace Microsoftu
description: Odkazy na zdroje informací o psaní dotazů protokolu ve službě Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: bwren
ms.openlocfilehash: 01d452f97134093e9b40b6ec32f1c9fed41564aa
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343047"
---
# <a name="azure-monitor-log-queries"></a>Dotazů na protokoly Azure monitoru
Protokoly služby Azure Monitor jsou postavené na Průzkumník dat Azure a použít verzi jazyka stejný dotaz Kusto dotazů na protokoly Azure monitoru. [Dokumentace k jazyku dotaz Kusto](/azure/kusto/query) má všechny informace pro jazyk a musí být váš primární prostředek pro psaní dotazů na protokoly Azure monitoru. Tato stránka obsahuje odkazy na další zdroje pro učit, jak psát dotazy a rozdíly mezi Azure Monitor implementace jazyka.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Začínáme

- [Začínáme se službou Azure Monitor log analytics](get-started-portal.md) je lekce pro zápis dotazů a práci s výsledky na webu Azure Portal.
- [Začínáme s Azure Monitor protokolu dotazy](get-started-queries.md) je lekce pro psaní dotazů pomocí Azure monitoru data protokolu.

## <a name="concepts"></a>Koncepty
- [Analyzuje data protokolů ve službě Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) poskytuje stručný přehled protokolu dotazuje a popisuje, jak strukturovaná data protokolů Azure Monitor.
- [Zobrazení a analýza dat protokolu ve službě Azure Monitor](../../azure-monitor/log-query/portals.md) vysvětluje portály, kde vytvářet a spouštět dotazy log.

## <a name="reference"></a>Referenční informace

- [Referenční informace k jazyku dotazování](/azure/kusto/query) je kompletní jazykovou referenci pro Kusto dotazovací jazyk.
- [Monitorování protokolu dotazu jazyka rozdíly ve službě Azure](data-explorer-difference.md) popisuje rozdíly mezi verzemi Kusto dotazovací jazyk.
- [Standardní vlastnosti ve službě Azure Monitor protokolování záznamů](../../azure-monitor/platform/log-standard-properties.md) popisuje vlastnosti, které jsou standardní pro všechna data protokolů Azure Monitor.
- [Provádění dotazů protokolů napříč prostředky ve službě Azure Monitor](../../azure-monitor/log-query/cross-workspace-query.md) popisuje postup při psaní dotazů na protokoly, které používají data z více aplikací služby Application Insights a pracovní prostory Log Analytics.


## <a name="examples"></a>Příklady

- [Příklady dotazů protokolu Azure Monitor](examples.md) poskytuje příklady dotazů pomocí Azure monitoru data protokolu.



## <a name="lessons"></a>Lekce

- [Práce s řetězci v dotazech protokolu Azure Monitor](string-operations.md) popisuje, jak pracovat s řetězcovými daty.
- [Práce s hodnotami data a času v dotazů na protokoly Azure monitoru](datetime-operations.md) popisuje, jak pracovat s daty datum a čas. 
- [Agregace ve službě Azure Monitor protokolu dotazy](aggregations.md) a [Advanced agregace v dotazů na protokoly Azure monitoru](advanced-aggregations.md) popisují, jak agregují a shrnují data.
- [Spojení v dotazů na protokoly Azure monitoru](joins.md) popisuje, jak propojit data z více tabulek.
- [Práce s formátem JSON a datovými struktury v dotazů na protokoly Azure monitoru](json-data-structures.md) popisuje, jak analyzovat json data.
- [Zápis rozšířeného protokolu dotazů ve službě Azure Monitor](advanced-query-writing.md) popisuje strategie pro vytváření složitých dotazů a opětovné použití kódu.
- [Vytváření grafů a diagramů z dotazů na protokoly Azure monitoru](charts.md) popisuje, jak vizualizovat data z dotazu protokolu.

## <a name="cheatsheets"></a>Přehledy

-  [SQL k dotazování protokolu Azure Monitor](sql-cheatsheet.md) pomáhá zajistit uživatelům, kteří jsou již obeznámeni s SQL.
-  [Splunk dotazu protokolu Azure Monitor](sql-cheatsheet.md) pomáhá zajistit uživatelům, kteří jsou již znáte Splunk.
 
## <a name="next-steps"></a>Další postup

- Přístup ke kompletní [referenční dokumentace pro jazyk dotaz Kusto](/azure/kusto/query/).