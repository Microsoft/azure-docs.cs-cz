---
title: Koncepce převodu dat při LUIS – Language Understanding
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak lze změnit projevy před predikce v Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: e1d0e0a0205190846612d727fbf34404e33c3ad4
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027074"
---
# <a name="data-conversion-concepts-in-luis"></a>Koncepce převodu dat při LUIS
Služba LUIS poskytuje způsob, jak převést projevy projevy mluvené slovo na text projevy před předpovědi. 

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

