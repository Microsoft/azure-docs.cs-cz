---
title: Převod dat – LUIS
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak lze změnit projevy před předpovědi v porozumění jazykům (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: aeb9094db83b14af988f70485788934a7854200c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68619808"
---
# <a name="convert-data-format-of-utterances"></a>Převod datového formátu promluv
Služba LUIS poskytuje následující převody promluvy uživatele před předpověď"

* Převod řeči na text pomocí [služby Rozpoznávání řeči služby Cognitive Services.](../Speech-Service/overview.md) 

## <a name="speech-to-text"></a>Řeč na text

Převod řeči na text je k dispozici jako integrace s LUIS. 

### <a name="intent-conversion-concepts"></a>Koncepty převodu záměru
Převod řeči na text v LUIS umožňuje odesílat mluvené projevy do koncového bodu a přijímat odpověď předpověď LUIS. Tento proces je integrace služby [řeči](https://docs.microsoft.com/azure/cognitive-services/Speech) s LUIS. Další informace o řeči k záměru s [kurzem](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Klíčové požadavky
Pro tuto integraci není nutné vytvářet klíč **rozhraní API pro rozpoznávání řeči Bingu.** Klíč **Language Understanding** vytvořený na portálu Azure funguje pro tuto integraci. Nepoužívejte startovací klíč LUIS.

### <a name="pricing-tier"></a>Cenová úroveň
Tato integrace používá jiný [cenový](luis-boundaries.md#key-limits) model než obvyklé cenové úrovně Language Understanding. 

### <a name="quota-usage"></a>Využití kvóty
Informace naleznete [v tématu Omezení klíčů.](luis-boundaries.md#key-limits) 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Extrakce dat](luis-concept-data-extraction.md)

