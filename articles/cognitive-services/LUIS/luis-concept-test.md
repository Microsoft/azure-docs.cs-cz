---
title: Testování aplikace LEOŠ - Azure | Microsoft Docs
description: Pomocí jazyka Principy (LEOŠ) nepřetržitě fungovat ve vaší aplikaci ji upravit a zlepšovat její znalosti jazyka.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 8c702d2adbadd2736eed05c7580e8aabf69affbf
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266324"
---
# <a name="testing-in-luis"></a>Testování v LEOŠ

Testování je proces poskytování ukázka utterances LEOŠ a odezvy rozpoznána LEOŠ tříd Intent a entity. 

Můžete [testování](interactive-test.md) LEOŠ interaktivně, jeden utterance najednou, nebo zadejte [batch](luis-concept-batch-test.md) z utterances. Při testování, porovnat aktuální [active](luis-concept-version.md#active-version) modelu do publikovaného modelu. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>
## <a name="what-is-a-score-in-testing"></a>Co je skóre při testování?
V tématu [předpovědi skóre](luis-concept-prediction-score.md) koncepty, které další informace o předpovědi skóre.

## <a name="interactive-testing"></a>Interaktivní testování
Interaktivní testování se provádí z **Test** panelu webu. Můžete zadat utterance na tom, jak jsou identifikovány a skóre pro magnitudu tříd Intent a entity. Pokud LEOŠ není predikci tříd Intent a entity podle očekávání na utterance v podokně testování, zkopírujte ho do **záměr** stránce jako nový utterance. Pak označte části tohoto utterance a cvičení LEOŠ. 

## <a name="batch-testing"></a>Testování batch
V tématu [testování batch](luis-concept-batch-test.md) Pokud testujete více než jeden utterance najednou.

## <a name="endpoint-testing"></a>Koncový bod testování
Můžete otestovat pomocí [koncový bod](luis-glossary.md#endpoint) s maximálně dvě verze vaší aplikace. Ve vaší verzi aplikace nastavit jako hlavní nebo za provozu **produkční** koncový bod, přidejte druhý verzi **pracovní** koncový bod. Tento přístup vám tři verze utterance: na aktuální model, v podokně Test [LEOŠ] [ LUIS] webu a dvě verze na dva různé koncové body. 

Všechny koncový bod testování počty směrem k vaší kvóty využití. 

## <a name="do-not-log-tests"></a>Neprotokolujte testů
Pokud testování proti koncový bod a nechcete, aby utterance protokolována, nezapomeňte použít `logging=false` konfigurace řetězec dotazu.

## <a name="where-to-find-utterances"></a>Kde najít utterances
LEOŠ ukládá do protokolu dotazu, který je k dispozici ke stažení na všechny zaznamenané utterances [LEOŠ] [ LUIS] webu **aplikace** seznamu stránku, stejně jako LEOŠ [vytváření rozhraní API ](https://aka.ms/luis-authoring-apis). 

Všechny utterances LEOŠ je jisti, jaké jsou uvedeny v **[zkontrolujte koncový bod utterances](label-suggested-utterances.md)** stránky [LEOŠ] [ LUIS] webu. 

![Zkontrolujte utterances koncový bod](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>Mějte na paměti, ke cvičení
Nezapomeňte [cvičení](luis-how-to-train.md) LEOŠ po provedení změn do modelu. Změny aplikace LEOŠ nevidí při testování, dokud je vycvičena aplikace. 

## <a name="best-practices"></a>Osvědčené postupy
Další informace [osvědčené postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další postup

* Další informace o [testování](interactive-test.md) vaše utterances.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions