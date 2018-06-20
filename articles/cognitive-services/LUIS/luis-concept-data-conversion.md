---
title: Základními pojmy převodu dat v LEOŠ - Azure | Microsoft Docs
description: Zjistěte, jak lze změnit utterances před předpovědi znalosti jazyka (LEOŠ)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 56de113b41be419a5e39d705a22466139c1c29ce
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266092"
---
# <a name="data-conversion-concepts-in-luis"></a>Koncepty převodu dat v LEOŠ
LEOŠ poskytuje způsob, jak převést utterances mluvené utterances na text utterances před předpovědi. 

## <a name="speech-to-intent-conversion-concepts"></a>Převod řeči na záměrné převod koncepty
Převod řeči na text v LEOŠ umožňuje odesílat mluvené utterances koncový bod a obdrží odpověď LEOŠ předpovědi. Proces je integraci [řeči](https://docs.microsoft.com/azure/cognitive-services/Speech) služby s LEOŠ. 

### <a name="key-requirements"></a>Klíčové požadavky
Není potřeba vytvořit **rozhraní API pro rozpoznávání řeči Bing** klíče pro této integrace. Klíč LEOŠ funguje u této integrace.

### <a name="new-endpoint"></a>Nový koncový bod 
Tato integrace vytvoří nový koncový bod a [ceny](luis-boundaries.md#key-limits) modelu. Koncový bod je schopný přijímat obě oznamována a text utterances budete moci použít jako jeden koncový bod. 

### <a name="quota-usage"></a>Využití kvóty
V tématu [klíče omezení](luis-boundaries.md#key-limits) informace. 

### <a name="data-retention"></a>Uchovávání dat
Data odeslaná ke koncovému bodu, bez ohledu na to, je-li řeči nebo text, se používá pouze pro zlepšení rozpoznávání řeči modelu. Nad rámec modelu se nepoužije pro zlepšení rozpoznávání řeči nebo LEOŠ v obecné kapacity. Při odstranění aplikace LEOŠ uchovaná data je taky odstranit.

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správné pravopisné chyby v tomto kurzu](luis-tutorial-bing-spellcheck.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions