---
title: Požadavky na dotazy
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: 518865f78c170f1fbe4e65b96dc149c1b449a88b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631357"
---
V rámci dotazu použijte `@StartTime` parametr pro získání dat metriky pro jedno časové razítko. Poradce pro metriky nahradí parametr `yyyy-MM-ddTHH:mm:ss` řetězcem formátu při spuštění dotazu.

> [!IMPORTANT]
> Dotaz by měl vracet maximálně jeden záznam pro každou kombinaci dimenzí v každém časovém razítku. A všechny záznamy vrácené dotazem musí mít stejná časová razítka. Poradce metriky spustí tento dotaz pro každé časové razítko k ingestování vašich dat. Další informace a příklady najdete v [části Nejčastější dotazy k dotazům](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) . 