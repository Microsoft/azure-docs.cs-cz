---
title: Otestujte aplikaci LUIS – Azure | Dokumentace Microsoftu
description: Language Understanding (LUIS) umožňuje neustále pracovat na aplikaci zpřesnit jej a zvýšit jeho umožňující porozumět jazyku.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 46aa23ee359fd1f2f5a1cf8a05854d5c4cc7c739
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170612"
---
# <a name="testing-in-luis"></a>Testování v LUIS

Testování je proces poskytování ukázka projevy LUIS a získat odpověď ze služby LUIS rozpoznán záměry a entity. 

Můžete [testování](luis-interactive-test.md) LUIS interaktivně, jeden utterance najednou, nebo poskytněte [batch](luis-concept-batch-test.md) z projevy. Při testování, porovnávat aktuální [aktivní](luis-concept-version.md#active-version) modelů do publikovaného modelu. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>
## <a name="what-is-a-score-in-testing"></a>Co je skóre v testování?
V tématu [předpovědi skóre](luis-concept-prediction-score.md) koncepty, které další informace o předpovědi skóre.

## <a name="interactive-testing"></a>Interaktivní testování
Interaktivní testování probíhá prostřednictvím **Test** panelu na webu. Můžete zadat utterance zobrazíte jak záměry a entity jsou identifikovány a skóre. Pokud služba LUIS není podle očekávání v utterance v podokně testování předpověď záměry a entity, zkopírujte ho do **záměr** stránku jako nové utterance. Pak označte části této utterance a trénování služby LUIS. 

## <a name="batch-testing"></a>Dávkové testování
Zobrazit [batch testování](luis-concept-batch-test.md) Pokud testujete utterance více než jeden po druhém.

## <a name="endpoint-testing"></a>Testování koncového bodu
Můžete otestovat pomocí [koncový bod](luis-glossary.md#endpoint) s délkou maximálně dvě verze vaší aplikace. Ve vaší verzi aplikace nastavit jako hlavní nebo za provozu **produkční** koncový bod, přidejte druhý verzi **pracovní** koncový bod. Tento přístup poskytuje tři verze utterance: aktuální model v podokně Test [LUIS](luis-reference-regions.md) webu a dvě verze na dvou různých koncových bodů. 

Všechny koncový bod testování počty směrem k kvótu využití. 

## <a name="do-not-log-tests"></a>Neprotokolujte testy
Pokud testování koncového bodu a nechcete, aby utterance protokolována, nezapomeňte použít `logging=false` konfigurace řetězce dotazu.

## <a name="where-to-find-utterances"></a>Kde najít projevy
LUIS ukládá do protokolu dotazu, který je k dispozici ke stažení na všechny zaznamenané projevy [LUIS](luis-reference-regions.md) webu **aplikace** stránka seznamu, jakož i LUIS [rozhraní API pro vytváření](https://aka.ms/luis-authoring-apis). 

Jakékoli projevy služba LUIS je jistí, jaké jsou uvedeny v **[zkontrolujte koncový bod projevy](luis-how-to-review-endoint-utt.md)** stránku [LUIS](luis-reference-regions.md) webu. 

![Kontrola promluv koncového bodu](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>Mějte na paměti k trénování
Nezapomeňte [trénování](luis-how-to-train.md) LUIS, když provedete změny modelu. Změny v aplikaci LUIS neuvidí při testování, dokud se trénuje aplikace. 

## <a name="best-practices"></a>Osvědčené postupy
Přečtěte si [osvědčené postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další postup

* Další informace o [testování](luis-interactive-test.md) vaše projevy.