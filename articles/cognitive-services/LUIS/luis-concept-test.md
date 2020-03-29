---
title: Testování aplikace LUIS
titleSuffix: Azure Cognitive Services
description: Testování je proces poskytování ukázkové projevy luis a získání odpovědi luis rozpoznaných záměrů a entit.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 25b360f90a0920aad2ea5e68cda31a68be5d37a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73486676"
---
# <a name="testing-example-utterances-in-luis"></a>Testování ukázkových projevů v LUIS

Testování je proces poskytování ukázkové projevy luis a získání odpovědi luis rozpoznaných záměrů a entit. 

Můžete testovat LUIS interaktivně, jeden utterance najednou nebo poskytnout projevy. Při testování můžete porovnat aktuální aktivní model předpověď odpověď na odpověď předpověď publikovaný model. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Co je skóre v testování?
Další informace o skóre předpovědi najdete v [tématu Koncepty skóre předpovědi.](luis-concept-prediction-score.md)

## <a name="interactive-testing"></a>Interaktivní testování
Interaktivní testování se provádí z panelu **Test** na portálu LUIS. Můžete zadat utterance zobrazíte, jak jsou identifikovány záměry a entity a skóre. Pokud služba LUIS nepředpovídá záměry a entity podle očekávání na utterance v panelu testování, zkopírujte ji na stránku **záměru** jako nový utterance. Potom označte části tohoto utterance pro entity a trénování LUIS. 

## <a name="batch-testing"></a>Dávkové testování
Viz [testování dávky,](luis-concept-batch-test.md) pokud testujete více než jeden utterance najednou.

## <a name="endpoint-testing"></a>Testování koncového bodu
Můžete otestovat pomocí [koncového bodu](luis-glossary.md#endpoint) s maximálně dvěma verzemi aplikace. S hlavní nebo živou verzí sady aplikací jako koncový bod **produkčního** prostředí přidejte druhou verzi do **pracovního** koncového bodu. Tento přístup poskytuje tři verze utterance: aktuální model v podokně test webu [LUIS](luis-reference-regions.md) a dvě verze na dva různé koncové body. 

Všechny testování koncového bodu se započítává do kvóty využití. 

## <a name="do-not-log-tests"></a>Neprotokolovat testy
Pokud testujete proti koncovému bodu a nechcete, aby byla `logging=false` zaznamenána utterance, nezapomeňte použít konfiguraci řetězce dotazu.

## <a name="where-to-find-utterances"></a>Kde najít projevy
Služba LUIS ukládá všechny protokolované projevy do protokolu dotazů, které jsou k dispozici ke stažení na portálu LUIS ze stránky se seznamem **Aplikace,** a také [ve vytváření rozhraní API služby](https://go.microsoft.com/fwlink/?linkid=2092087)LUIS . 

Všechny projevy LUIS není jistý jsou **[uvedeny](luis-how-to-review-endpoint-utterances.md)** na stránce zkontrolovat koncový bod projevy webu [LUIS.](luis-reference-regions.md) 

## <a name="remember-to-train"></a>Nezapomeňte trénovat
Nezapomeňte [trénovat](luis-how-to-train.md) LUIS po provádění změn v modelu. Změny aplikace LUIS se při testování nezoají, dokud nebude aplikace trénovaná. 

## <a name="best-practices"></a>Osvědčené postupy
Seznamte se s [doporučenými postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další kroky

* Další informace o [testování](luis-interactive-test.md) promluvy.
