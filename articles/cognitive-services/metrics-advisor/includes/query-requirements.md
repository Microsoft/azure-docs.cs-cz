---
title: Požadavky na dotazy
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 777e3261bd6f842861ed6622b78589eedaaf65d7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043189"
---
V rámci dotazu použijte `@StartTime` parametr pro získání dat metriky pro jedno časové razítko. Poradce pro metriky nahradí parametr `yyyy-MM-ddTHH:mm:ss` řetězcem formátu při spuštění dotazu.

> [!IMPORTANT]
> Dotaz by měl vracet maximálně jeden záznam pro každou kombinaci dimenzí v každém časovém razítku. A všechny záznamy vrácené dotazem musí mít stejná časová razítka. Poradce metriky spustí tento dotaz pro každé časové razítko k ingestování vašich dat. Další informace a příklady najdete v [části Nejčastější dotazy k dotazům](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) . 