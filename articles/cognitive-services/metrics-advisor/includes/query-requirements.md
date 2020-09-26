---
title: Požadavky na dotazy
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 68b5946cb21bef1c8979a94c8780682cfe9defa6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376418"
---
V rámci dotazu použijte `@StartTime` parametr pro získání dat metriky pro konkrétní časové razítko. Tato akce bude nahrazena `yyyy-MM-ddTHH:mm:ss` řetězcem formátu. 

> [!IMPORTANT]
> Ujistěte se, že dotaz bude vracet pouze data metriky z **jednoho časového razítka** . Poradce pro metriky spustí dotaz pro každé časové razítko, aby získal odpovídající data metriky. Například dotaz na metriku s *denní* členitosti by měl obsahovat pouze jedno časové razítko, například `2020-06-21T00:00:00Z` při spuštění dotazu jednou. 
