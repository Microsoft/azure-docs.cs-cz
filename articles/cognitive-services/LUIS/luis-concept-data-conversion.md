---
title: Převod dat
titleSuffix: Language Understanding - Azure Cognitive Services
description: Zjistěte, jak lze změnit projevy před predikce v Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: a148c849d0935978f049e01dd254c4c18800ee3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66496987"
---
# <a name="convert-data-format-of-utterances"></a>Převést formát data projevy
LUIS pomocí služby Cognitive Services řeči převést projevy projevy mluvené slovo na text projevy před předpovědi. 

## <a name="speech-to-intent-conversion-concepts"></a>Převod řeči na záměr převod koncepty
Převod řeči na text v LUIS umožňuje odeslat mluvené slovo projevy na koncový bod a trvá příjem odpovědi na predikce služby LUIS. Tento proces je integrace produktů [řeči](https://docs.microsoft.com/azure/cognitive-services/Speech) service pomocí služby LUIS. Další informace o převodu řeči na záměr s [kurzu](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Klíčové požadavky
Není potřeba vytvořit **rozhraní API pro zpracování řeči Bingu** klíče pro tuto integraci. A **Language Understanding** klíče vytvořeného na webu Azure Portal se dá použít pro integraci. Nepoužívejte klávesu starter LUIS.

### <a name="pricing-tier"></a>Cenová úroveň
Tato integrace používá jiný [ceny](luis-boundaries.md#key-limits) model než běžné Language Understanding cenové úrovně. 

### <a name="quota-usage"></a>Využití kvóty
Zobrazit [klíče omezení](luis-boundaries.md#key-limits) informace. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Extrahování dat](luis-concept-data-extraction.md)

