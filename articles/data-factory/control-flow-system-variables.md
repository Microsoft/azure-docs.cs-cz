---
title: Systémové proměnné v Azure Data Factory | Microsoft Docs
description: Tento článek popisuje systémové proměnné podporovaných službou Azure Data Factory. Tyto proměnné můžete použít ve výrazech při definování entit služby Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 77285692846d2a301e091426801e20203816181a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systémové proměnné podporovaných službou Azure Data Factory
Tento článek popisuje systémové proměnné podporovaných službou Azure Data Factory. Tyto proměnné můžete použít ve výrazech při definování entit služby Data Factory. 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [funkce a proměnné v datové továrně verze 1](v1/data-factory-functions-variables.md).


## <a name="pipeline-scope"></a>Obor kanál:

| Název proměnné | Popis |
| --- | --- |
| @pipeline().DataFactory |Název objektu pro vytváření dat kanál spustit běží v rámci | 
| @pipeline().Pipeline |Název kanálu |
| @pipeline().RunId | ID specifické kanálu spustit | 
| @pipeline().TriggerType | Typ aktivační událost, která volá kanálu (ruční, Scheduler) | 
| @pipeline().TriggerId| ID aktivační události, která volá kanálu |
| @pipeline().TriggerName| Název aktivační události, která volá kanálu |
| @pipeline().TriggerTime| Čas, kdy aktivační událost, která volá kanálu. Čas aktivace je skutečný čas aktivní, nejsou naplánovaném čase. Například `13:20:08.0149599Z` je vrácena místo `13:20:00.00Z` |

## <a name="trigger-scope"></a>Obor aktivační události:

| Název proměnné | Popis |
| --- | --- |
| trigger().scheduledTime |Čas naplánovaného aktivační události k vyvolání kanálu spustit. Například pro aktivační událost, která aktivuje každých 5 minut, by vrátit tuto proměnnou `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z` v uvedeném pořadí.|
| trigger().startTime |Čas, kdy aktivační událost **ve skutečnosti** aktivováno pro vyvolání kanálu spustit. Například pro aktivační událost, která aktivuje každých 5 minut, tato proměnná může vrátit přibližně toto `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z` v uvedeném pořadí.|

## <a name="next-steps"></a>Další postup
Informace o tom, jak jsou tyto proměnné použít ve výrazech naleznete v tématu [výrazu jazyka & funkce](control-flow-expression-language-functions.md). 
