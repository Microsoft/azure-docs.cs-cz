---
title: Převod dat – LUIS
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak se dá projevy změnit před předpovědi v Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 42a9caff0433808734ee853cbad90a2088bf4e1e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019241"
---
# <a name="convert-data-format-of-utterances"></a>Převést formát dat projevy
LUIS poskytuje následující převody uživatele utterance před předpovědi "

* Převod řeči na text pomocí služby [Cognitive Services Speech](../Speech-Service/overview.md)

## <a name="speech-to-text"></a>Řeč na text

Převod řeči na text je k dispozici jako integrace s LUIS.

### <a name="intent-conversion-concepts"></a>Koncepce převodu záměru
Převod řeči na text v LUIS umožňuje odeslat mluvené projevy na koncový bod a získat odpověď předpovědi LUIS. Proces je integrací služby pro [rozpoznávání řeči](/azure/cognitive-services/Speech) pomocí Luis. Přečtěte si další informace o rozpoznávání řeči k záměru pomocí [kurzu](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Klíčové požadavky
Pro tuto integraci nemusíte vytvářet **rozhraní API pro zpracování řeči Bingu** klíč. **Language Understanding** klíč vytvořený v Azure Portal funguje pro tuto integraci. Nepoužívejte počáteční klíč LUIS.

### <a name="pricing-tier"></a>Cenová úroveň
Tato integrace používá jiný [cenový](luis-limits.md#key-limits) model než obvyklé Language Understanding cenové úrovně.

### <a name="quota-usage"></a>Využití kvóty
Informace najdete v tématu [omezení klíčů](luis-limits.md#key-limits) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Extrakce dat](luis-concept-data-extraction.md)