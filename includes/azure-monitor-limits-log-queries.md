---
title: zahrnout soubor
description: zahrnout soubor
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74795802"
---
| Omezení | Popis |
|:---|:---|
| Dotazovací jazyk | Azure Monitor používá stejný [dotazovací jazyk Kusto](/azure/kusto/query/) jako Průzkumník dat Azure. Viz [Rozdíly v dotazovacím jazyce protokolu Azure Monitor](../articles/azure-monitor/log-query/data-explorer-difference.md) pro prvky jazyka KQL, které nejsou ve službě Azure Monitor podporované. |
| Oblast Azure | Dotazy protokolu může dojít k nadměrné režii, když data zahrnuje log Analytics pracovníprostory ve více oblastech Azure. Podrobnosti najdete v [tématu Omezení dotazů.](../articles/azure-monitor/log-query/scope.md#query-limits) |
| Dotazy na křížové prostředky | Maximální počet prostředků Application Insights a pracovních prostorů Analýzy protokolů v jednom dotazu omezeném na 100.<br>V návrháři zobrazení není podporován dotaz mezi prostředky.<br>Dotaz mezi prostředky ve výstrahách protokolu je podporován v novém rozhraní API scheduledQueryRules.<br>Podrobnosti najdete [v tématu Limity dotazů mezi zdroji.](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) |
| Omezení dotazu | Uživatel je omezen na 200 dotazů za 30 sekund v libovolném počtu pracovních prostorů. Toto omezení platí pro programové dotazy nebo dotazy iniciované vizualizačními částmi, jako jsou řídicí panely Azure a stránka souhrnu pracovního prostoru Analýzy protokolů. |
