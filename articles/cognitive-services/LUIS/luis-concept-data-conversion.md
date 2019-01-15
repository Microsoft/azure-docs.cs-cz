---
title: Převod dat
titleSuffix: Language Understanding - Azure Cognitive Services
description: Zjistěte, jak lze změnit projevy před predikce v Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 46ad8beb81f42fa19b832c63355488f31da023f8
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259372"
---
# <a name="convert-data-format-of-utterances"></a>Převést formát data projevy
LUIS pomocí služby Cognitive Services řeči převést projevy projevy mluvené slovo na text projevy před předpovědi. 

## <a name="speech-to-intent-conversion-concepts"></a>Převod řeči na záměr převod koncepty
Převod řeči na text v LUIS umožňuje odeslat mluvené slovo projevy na koncový bod a trvá příjem odpovědi na predikce služby LUIS. Tento proces je integrace produktů [řeči](https://docs.microsoft.com/azure/cognitive-services/Speech) service pomocí služby LUIS. 

### <a name="key-requirements"></a>Klíčové požadavky
Není potřeba vytvořit **rozhraní API pro zpracování řeči Bingu** klíče pro tuto integraci. A **Language Understanding** klíče vytvořeného na webu Azure Portal se dá použít pro integraci. Nepoužívejte klávesu starter LUIS, nebude fungovat pro integraci.

### <a name="new-endpoint"></a>Nový koncový bod 
Tato integrace vytvoří nový koncový bod a [ceny](luis-boundaries.md#key-limits) modelu. Koncový bod, přes [sadou SDK pro řeč](https://github.com/Azure-Samples/cognitive-services-speech-sdk), může přijímat obě slyšet a text projevy, abyste mohli používat jako jeden koncový bod. 

### <a name="quota-usage"></a>Využití kvóty
Zobrazit [klíče omezení](luis-boundaries.md#key-limits) informace. 

### <a name="data-retention"></a>Uchovávání dat
Data odeslaná do koncového bodu prostřednictvím sadou SDK pro řeč, bez ohledu na to, pokud se jedná o řeči nebo text, slouží pouze ke zvýšení modelu řeči. Nad rámec modelu není použit k vylepšení řeči nebo také LUIS v obecné kapacity. Při odstranění aplikace LUIS uchovaná data se také odstraní.

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Umožňuje využít řeč na text](luis-tutorial-speech-to-intent.md)

