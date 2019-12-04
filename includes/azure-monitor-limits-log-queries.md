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
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795802"
---
| škálování | Popis |
|:---|:---|
| Dotazovací jazyk | Azure Monitor používá stejný [dotazovací jazyk Kusto](/azure/kusto/query/) jako Azure Průzkumník dat. Viz [rozdíly v jazyce Azure monitor protokolu dotazu](../articles/azure-monitor/log-query/data-explorer-difference.md) pro prvky jazyka KQL, které nejsou v Azure monitor podporovány. |
| Oblasti Azure | Dotazy protokolů můžou mít nadměrné nároky na to, kdy data jsou Log Analytics pracovní prostory ve více oblastech Azure. Podrobnosti najdete v tématu [omezení dotazů](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Dotazy na různé prostředky | Maximální počet Application Insightsch prostředků a Log Analytics pracovních prostorů v jednom dotazu omezený na 100.<br>Dotaz mezi prostředky není v Návrháři zobrazení podporován.<br>Dotaz na více prostředků v upozorněních protokolu se podporuje v novém rozhraní scheduledQueryRules API.<br>Podrobnosti najdete v tématu [omezení dotazů mezi prostředky](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) . |
| Omezování dotazů | Uživatel je omezený na 200 dotazů na 30 sekund pro libovolný počet pracovních prostorů. Toto omezení se vztahuje na programové dotazy nebo na dotazy iniciované částmi vizualizace, jako jsou řídicí panely Azure a stránka se souhrnem Log Analytics pracovního prostoru. |
