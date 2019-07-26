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
ms.openlocfilehash: ed840352096f1a1739bc8f655be42096456d3c33
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405663"
---
| Omezení | Popis |
|:---|:---|
| Dotazovací jazyk | Azure Monitor používá stejný [dotazovací jazyk Kusto](/azure/kusto/query/) jako Azure Průzkumník dat. Viz [rozdíly v jazyce Azure monitor protokolu dotazu](../articles/azure-monitor/log-query/data-explorer-difference.md) pro prvky jazyka KQL, které nejsou v Azure monitor podporovány. |
| Oblasti Azure | Dotazy protokolů můžou mít nadměrné nároky na to, kdy data jsou Log Analytics pracovní prostory ve více oblastech Azure. Podrobnosti najdete v tématu [omezení dotazů](../articles/azure-monitor/log-query/scope.md#query-limits) . |
