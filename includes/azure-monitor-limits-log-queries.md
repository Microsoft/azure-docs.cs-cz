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
ms.openlocfilehash: f007cf0d46d6cbee39a950b9784bbc9bde702ff5
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "69657749"
---
| Omezení | Popis |
|:---|:---|
| Dotazovací jazyk | Azure Monitor používá stejný [dotazovací jazyk Kusto](/azure/kusto/query/) jako Azure Průzkumník dat. Viz [rozdíly v jazyce Azure monitor protokolu dotazu](../articles/azure-monitor/log-query/data-explorer-difference.md) pro prvky jazyka KQL, které nejsou v Azure monitor podporovány. |
| Oblasti Azure | Dotazy protokolů můžou mít nadměrné nároky na to, kdy data jsou Log Analytics pracovní prostory ve více oblastech Azure. Podrobnosti najdete v tématu [omezení dotazů](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Dotazy na různé prostředky | Maximální počet Application Insightsch prostředků a Log Analytics pracovních prostorů v jednom dotazu omezený na 100.<br>Dotaz mezi prostředky není v Návrháři zobrazení podporován.<br>Dotaz na více prostředků v upozorněních protokolu se podporuje v novém rozhraní scheduledQueryRules API.<br>Podrobnosti najdete v tématu [omezení dotazů mezi prostředky](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) . |