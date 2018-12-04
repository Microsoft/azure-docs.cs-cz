---
title: Dotazovací jazyk Azure Log Analytics monitorování | Dokumentace Microsoftu
description: Odkazy na zdroje informací, jak psát dotazy v Log Analytics.
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
ms.date: 10/29/2018
ms.author: bwren
ms.openlocfilehash: 493c48fe87158ee357d98eb0b0d1bd2a4fad5b2f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52843780"
---
# <a name="log-analytics-query-language"></a>Protokolovat dotazovací jazyk Analytics
Log Analytics poskytuje shromažďování protokolů a analýzu pro monitorování Azure. Je založená na Průzkumník dat Azure a používá verzi stejné dotazovací jazyk. [Dokumentace k jazyku dotazu Průzkumník dat Azure](/azure/kusto/query) má všechny informace pro jazyk a musí být váš primární prostředek pro zápis dotazy Log Analytics. Tato stránka obsahuje odkazy na další zdroje pro učit, jak psát dotazy a na rozdíly s implementací jazyka Log Analytics.

## <a name="getting-started"></a>Začínáme

- [Začínáme se službou Log Analytics na portálu Azure portal](get-started-analytics-portal.md) je lekce pro zápis dotazů a práci s výsledky na webu Azure Portal.
-  [Začínáme s dotazy v Log Analytics](get-started-queries.md) je lekce pro psaní dotazů pomocí dat Log Analytics.

## <a name="concepts"></a>Koncepty
- [Analýza dat Log Analytics ve službě Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) poskytuje stručný přehled protokolu dotazuje a popisuje, jakým způsobem je strukturovaná data služby Log Analytics.
- [Zobrazení a analýza dat v Log Analytics](../log-analytics-log-search-portals.md) vysvětluje portály, kde můžete vytvářet a spouštět dotazy Log Analytics.

## <a name="reference"></a>Referenční informace

- [Referenční informace k jazyku dotazování](/azure/kusto/query) je kompletní jazykovou referenci pro Průzkumníka dat dotazovací jazyk.
- [Protokolovat rozdíly jazyk dotazu Analytics](data-explorer-difference.md) popisuje rozdíly mezi verzemi nástroje Průzkumník dat dotazovací jazyk.
- [Standardní vlastnosti v Log Analytics záznamy](../log-analytics-standard-properties.md) popisuje vlastnosti, které jsou standardní pro všechna data Log Analytics.
- [Provedení prohledávání protokolů napříč prostředky ve službě Log Analytics](../log-analytics-cross-workspace-search.md) popisuje, jak psát dotazy, které používají data z více aplikací služby Application Insights a pracovní prostory Log Analytics.


## <a name="examples"></a>Příklady

- [Příklady dotazů Analytics protokolovat](examples.md) poskytuje příklady dotazů použití dat Log Analytics.



## <a name="lessons"></a>Lekce

- [Práce s řetězci v dotazech Log Analytics](string-operations.md) popisuje, jak pracovat s řetězcovými daty.
- [Práce s hodnotami data a času v dotazy Log Analytics](datetime-operations.md) popisuje, jak pracovat s daty datum a čas. 
- [Agregace v dotazy Log Analytics](aggregations.md) a [Advanced agregace v dotazy Log Analytics](advanced-aggregations.md) popisují, jak agregují a shrnují data.
- [Spojení v dotazy Log Analytics](joins.md) popisuje, jak propojit data z více tabulek.
- [Práce s formátem JSON a datovými struktury v dotazy Log Analytics](json-data-structures.md) popisuje, jak analyzovat json data.
- [Zápis pokročilé dotazy v Log Analytics](advanced-query-writing.md) popisuje strategie pro vytváření složitých dotazů a opětovné použití kódu.
- [Vytváření grafů a diagramů z dotazy Log Analytics](charts.md) popisuje, jak vizualizovat data z dotazu.

## <a name="cheatsheets"></a>Přehledy

-  [SQL do Log Analytics dotazovacímu jazyku](sql-cheatsheet.md) pomáhá zajistit uživatelům, kteří jsou již obeznámeni s SQL.
-  [Splunk do Log Analytics dotazovacímu jazyku](sql-cheatsheet.md) pomáhá zajistit uživatelům, kteří jsou již znáte Splunk.
 
## <a name="next-steps"></a>Další postup

- Přístup ke kompletní [referenční dokumentaci pro dotazovací jazyk Průzkumník dat](/azure/kusto/query/).