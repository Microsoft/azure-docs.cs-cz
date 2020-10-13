---
title: Testování aplikace v LUIS
titleSuffix: Azure Cognitive Services
description: Testování je proces poskytování vzorových projevyů LUIS a získání odpovědi na LUISy a entity, které byly rozpoznány.
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: ef63bf68fec592b768986a1329663508443c514b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535844"
---
# <a name="testing-example-utterances-in-luis"></a>Příklad testu projevy v LUIS

Testování je proces poskytování vzorových projevyů LUIS a získání odpovědi na LUISy a entity, které byly rozpoznány. 

LUIS můžete testovat interaktivně, jeden utterance v jednom okamžiku nebo poskytnout sadu projevy. Při testování můžete porovnat odpověď předpovědi aktuálního aktivního modelu s odezvou předpovědi publikovaného modelu. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Co je skóre při testování?
Další informace o skóre předpovědi najdete v tématu Koncepty [skóre předpovědi](luis-concept-prediction-score.md) .

## <a name="interactive-testing"></a>Interaktivní testování
Interaktivní testování se provádí na panelu **test** na portálu Luis. Můžete zadat utterance a podívat se, jak se identifikují záměry a entity a jejich skóre. Pokud LUIS nepředpověď záměrů a entit podle očekávání na utterance na panelu testování, zkopírujte ji na stránku **záměru** jako novou utterance. Pak označte části tohoto utterance entit a LUIS. 

## <a name="batch-testing"></a>Dávkové testování
Pokud testujete více než jeden utterance najednou, podívejte se na [dávkové testování](luis-concept-batch-test.md) .

## <a name="endpoint-testing"></a>Testování koncových bodů
Pomocí [koncového bodu](luis-glossary.md#endpoint) můžete testovat maximálně dvě verze vaší aplikace. V rámci vaší hlavní nebo živé verze vaší aplikace jako **produkčního** koncového bodu přidejte druhou verzi do **pracovního** koncového bodu. Tento přístup poskytuje tři verze utterance: aktuální model v podokně Test webu [Luis](luis-reference-regions.md) a dvě verze na dvou různých koncových bodech. 

Všechna testování koncových bodů se počítají k vaší kvótě využití. 

## <a name="do-not-log-tests"></a>Neprotokolovat testy
Pokud testujete u koncového bodu a nechcete, aby se utterance protokoloval, nezapomeňte použít `logging=false` konfiguraci řetězce dotazu.

## <a name="where-to-find-utterances"></a>Kde najít projevy
LUIS ukládá všechny protokolované projevy v protokolu dotazů, které jsou k dispozici ke stažení na portálu LUIS ze stránky seznamu **aplikací** , a také [rozhraní API pro vytváření](https://go.microsoft.com/fwlink/?linkid=2092087)Luis. 

Jakékoli projevy LUIS si nejste jisti, že jsou uvedené na stránce **[zkontrolovat koncový bod projevy](luis-how-to-review-endpoint-utterances.md)** webu [Luis](luis-reference-regions.md) . 

## <a name="remember-to-train"></a>Nezapomeňte vlak
Nezapomeňte po provedení změn v modelu [naučit](luis-how-to-train.md) Luis. Změny aplikace LUIS se v testování neúčtují, dokud se aplikace nevyškole. 

## <a name="best-practices"></a>Osvědčené postupy
Seznamte se s [osvědčenými postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [testování](luis-interactive-test.md) projevy.
