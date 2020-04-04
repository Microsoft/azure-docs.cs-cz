---
title: Dotazy protokolu Azure Monitor | Dokumenty společnosti Microsoft
description: Odkazy na prostředky pro učení, jak psát dotazy protokolu v Azure Monitoru.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: 6b81aba553fc775821c80631aa83bbb3e8ac63b5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631797"
---
# <a name="azure-monitor-log-queries"></a>Dotazy protokolu Azure Monitor

Protokoly Azure Monitoru jsou postavené na Azure Data Explorer a dotazy protokolu Azure Monitor použít verzi stejného dotazovacího jazyka Kusto. [Kusto dotaz jazyka dokumentace](/azure/kusto/query) obsahuje všechny podrobnosti pro jazyk a měl by být primární prostředek pro psaní dotazů protokolu Azure Monitor. Tato stránka obsahuje odkazy na další prostředky pro učení, jak psát dotazy a na rozdíly s implementací Azure Monitor jazyka.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Začínáme

- [Začínáme s Azure Monitor Log Analytics](get-started-portal.md) je lekce pro psaní dotazů a práci s výsledky na webu Azure Portal.
- [Začínáme s dotazy protokolu Azure Monitor](get-started-queries.md) je lekce pro psaní dotazů pomocí dat protokolu Azure Monitor.

## <a name="concepts"></a>Koncepty

- [Analýza dat protokolu v Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) poskytuje stručný přehled dotazů protokolu a popisuje, jak azure monitor data protokolu je strukturována.
- [Zobrazení a analýza dat protokolu v Azure Monitorvysvětluje](../../azure-monitor/log-query/portals.md) portály, kde můžete vytvářet a spouštět dotazy protokolu.

## <a name="reference"></a>Referenční informace

- [Referenční příručka pro dotazovací jazyk](/azure/kusto/query) je úplný mj.
- [Azure Monitor dotaz dotazu na rozdíly](data-explorer-difference.md) popisuje rozdíly mezi verzemi dotazovacího jazyka Kusto.
- [Standardní vlastnosti v záznamech protokolu Azure Monitor](../../azure-monitor/platform/log-standard-properties.md) popisuje vlastnosti, které jsou standardní pro všechna data protokolu Azure Monitor.
- [Provádění dotazů protokolu mezi prostředky v Azure Monitor](../../azure-monitor/log-query/cross-workspace-query.md) popisuje, jak psát dotazy protokolu, které používají data z více pracovních prostorů Log Analytics a aplikací Přehledy aplikací.

## <a name="examples"></a>Příklady

- [Příklady dotazů protokolu Azure Monitor](examples.md) poskytuje příklady dotazů pomocí dat protokolu Azure Monitor.

## <a name="lessons"></a>Lekce

- [Práce s řetězci v dotazech protokolu Azure Monitor](string-operations.md) popisuje, jak pracovat s řetězcovými daty.
- [Práce s hodnotami času data v dotazech protokolu Azure Monitor](datetime-operations.md) popisuje, jak pracovat s daty data a času.
- [Agregace v dotazech protokolu Azure Monitor](aggregations.md) a [rozšířené agregace v dotazech protokolu Azure Monitor](advanced-aggregations.md) popisují, jak agregovat a sumarizovat data.
- [Spojení v dotazech protokolu Azure Monitor](joins.md) popisuje, jak se připojit data z více tabulek.
- [Práce s JSON a datové struktury v dotazech protokolu Azure Monitor](json-data-structures.md) popisuje, jak analyzovat json data.
- [Psaní rozšířené dotazy protokolu v Azure Monitor](advanced-query-writing.md) popisuje strategie pro vytváření složitých dotazů a opětovné použití kódu.
- [Vytváření grafů a diagramů z dotazů protokolu Azure Monitor](charts.md) popisuje, jak vizualizovat data z dotazu protokolu.

## <a name="cheatsheets"></a>Přehledy

- [Dotaz protokolu SQL to Azure Monitor](sql-cheatsheet.md) pomáhá uživatelům, kteří už jsou obeznámeni s SQL.
- [Splunk to Azure Monitor dotaz protokolu](splunk-cheatsheet.md) pomáhá uživatelům, kteří jsou již obeznámeni s Splunk.

## <a name="next-steps"></a>Další kroky

- Získejte přístup k úplné [referenční dokumentaci pro dotazovací jazyk Kusto](/azure/kusto/query/).
