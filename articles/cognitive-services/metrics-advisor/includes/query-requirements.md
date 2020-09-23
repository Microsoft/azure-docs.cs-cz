---
title: Požadavky na dotazy
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 23da214f4ffc9e589523f7ada00a77494183eb15
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936499"
---
V rámci dotazu použijte `@StartTime` parametr pro získání dat metriky pro konkrétní časové razítko. Tato akce bude nahrazena `yyyy-MM-ddTHH:mm:ss` řetězcem formátu. 

> [!IMPORTANT]
> Ujistěte se, že dotaz bude vracet pouze data metriky z **jednoho časového razítka** . Poradce pro metriky spustí dotaz pro každé časové razítko, aby získal odpovídající data metriky. Například dotaz na metriku s *denní* členitosti by měl obsahovat pouze jedno časové razítko, například `2020-06-21T00:00:00Z` při spuštění dotazu jednou. 
