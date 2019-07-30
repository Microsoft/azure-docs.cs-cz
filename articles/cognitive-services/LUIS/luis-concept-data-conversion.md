---
title: Převod dat – LUIS
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak lze změnit projevy před predikce v Language Understanding (LUIS)
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
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619808"
---
# <a name="convert-data-format-of-utterances"></a>Převést formát data projevy
LUIS poskytuje následující převody uživatele utterance před předpovědi "

* Převod řeči na text pomocí služby [Cognitive Services Speech](../Speech-Service/overview.md) 

## <a name="speech-to-text"></a>Řeč na text

Převod řeči na text je k dispozici jako integrace s LUIS. 

### <a name="intent-conversion-concepts"></a>Koncepce převodu záměru
Převod řeči na text v LUIS umožňuje odeslat mluvené slovo projevy na koncový bod a trvá příjem odpovědi na predikce služby LUIS. Tento proces je integrace produktů [řeči](https://docs.microsoft.com/azure/cognitive-services/Speech) service pomocí služby LUIS. Přečtěte si další informace o rozpoznávání řeči k záměru pomocí [kurzu](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Klíčové požadavky
Není potřeba vytvořit **rozhraní API pro zpracování řeči Bingu** klíče pro tuto integraci. A **Language Understanding** klíče vytvořeného na webu Azure Portal se dá použít pro integraci. Nepoužívejte počáteční klíč LUIS.

### <a name="pricing-tier"></a>Cenová úroveň
Tato integrace používá jiný [cenový](luis-boundaries.md#key-limits) model než obvyklé Language Understanding cenové úrovně. 

### <a name="quota-usage"></a>Využití kvóty
Zobrazit [klíče omezení](luis-boundaries.md#key-limits) informace. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Extrakce dat](luis-concept-data-extraction.md)

