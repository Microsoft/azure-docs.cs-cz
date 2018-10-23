---
title: Koncepce převodu dat při LUIS – Language Understanding
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak lze změnit projevy před predikce v Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 9324f7b4f7bed844f16d17b8960878892be4b165
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638388"
---
# <a name="data-conversion-concepts-in-luis"></a>Koncepce převodu dat při LUIS
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

