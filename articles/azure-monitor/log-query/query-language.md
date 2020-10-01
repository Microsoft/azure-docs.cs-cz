---
title: Azure Monitor dotazy protokolu | Microsoft Docs
description: Odkazy na zdroje informací o tom, jak vytvářet dotazy protokolu v Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: 080af271870362e6e0633aaf590820f2fadb6144
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607339"
---
# <a name="azure-monitor-log-queries"></a>Dotazy na protokoly Azure Monitor

Protokoly Azure Monitor jsou založené na službě Azure Průzkumník dat a Azure Monitor dotazy protokolu používají verzi stejného dotazovacího jazyka Kusto. [Dokumentace jazyka dotazů Kusto](/azure/kusto/query) má všechny podrobnosti o jazyku a měl by být vaším primárním prostředkem pro zápis Azure Monitorch dotazů protokolu. Tato stránka obsahuje odkazy na další zdroje informací o tom, jak zapisovat dotazy a rozdíly v Azure Monitor implementaci tohoto jazyka.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Začínáme

- [Začínáme s Azure Monitor Log Analytics](get-started-portal.md) je lekce pro psaní dotazů a práce s výsledky v Azure Portal.
- [Začínáme s Azure monitor dotazy protokolu](get-started-queries.md) jsou lekce pro zápis dotazů využívajících data protokolu Azure monitor.

## <a name="concepts"></a>Koncepty

- [Analýza dat protokolu v Azure monitor](./log-query-overview.md) poskytuje stručný přehled dotazů protokolu a popisuje, jak se strukturují data protokolu Azure monitor.
- [Zobrazení a analýza dat protokolu v Azure monitor](./log-query-overview.md) vysvětluje portály, ve kterých vytváříte a spouštíte dotazy protokolu.

## <a name="reference"></a>Referenční informace

- [Odkaz na jazyk dotazů](/azure/kusto/query)  je úplný referenční příručka jazyka pro dotazovací jazyk Kusto.
- [Rozdíly v jazyce Azure monitor protokolu dotazů](data-explorer-difference.md) popisují rozdíly mezi verzemi dotazovacího jazyka Kusto.
- [Standardní vlastnosti v Azure monitor záznamy protokolu](../platform/log-standard-properties.md) popisují vlastnosti, které jsou standardní pro všechna Azure monitor data protokolu.
- [Provádění dotazů protokolu meziprostředků v Azure monitor](./cross-workspace-query.md) popisuje, jak zapisovat dotazy protokolů, které používají data z více Log Analytics pracovních prostorů a Application Insights aplikací.

## <a name="examples"></a>Příklady

- [Příklady dotazů protokolu Azure monitor poskytují příklady](examples.md) dotazů využívajících data protokolu Azure monitor.

## <a name="lessons"></a>Lekce

- [Práce s řetězci v Azure Monitorch dotazech protokolu](string-operations.md) popisuje, jak pracovat s řetězcovými daty.
- Práce [s hodnotami data a času v Azure Monitorch dotazech protokolu](datetime-operations.md) popisuje, jak pracovat s daty data a času.
- [Agregace v Azure monitor dotazy protokolu](aggregations.md) a [rozšířené agregace v Azure monitor dotazech protokolu](advanced-aggregations.md) popisují způsob agregace a sumarizace dat.
- [Spojení v Azure monitor dotazy protokolu](joins.md) popisují, jak propojit data z více tabulek.
- [Práce s JSON a datovými strukturami v Azure Monitorch dotazech protokolu](json-data-structures.md) popisuje, jak analyzovat data JSON.
- [Zápis rozšířených dotazů protokolu v Azure monitor](advanced-query-writing.md) popisuje strategie pro vytváření složitých dotazů a opakované použití kódu.
- [Vytváření grafů a diagramů z Azure Monitorch dotazů protokolu](charts.md) popisuje, jak vizualizovat data z dotazu protokolu.

## <a name="cheatsheets"></a>Přehledy

- [Dotaz SQL na Azure monitor log](sql-cheatsheet.md) pomáhá uživatelům, kteří jsou již obeznámeni s SQL.
- [Splunk do Azure monitor dotazování protokolu pro](splunk-cheatsheet.md) uživatele, kteří už znají Splunk.

## <a name="next-steps"></a>Další kroky

- Přístup k kompletní [dokumentaci k odkazům pro dotazovací jazyk Kusto](/azure/kusto/query/).

