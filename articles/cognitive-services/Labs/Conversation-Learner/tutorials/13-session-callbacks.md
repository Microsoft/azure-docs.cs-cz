---
title: Použití zpětných volání relace s modelem Conversation Learner Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat zpětná volání relace s modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 656d028082d56b8f094e83363e5189b163581c53
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703951"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Použití zpětných volání relace s modelem Conversation Learner

V tomto kurzu se seznámíte s relacemi, jak jsou zpracovávány, a Conversation Learner onSessionStart a onSessionEnd zpětných volání.

## <a name="video"></a>Video

[![Kurz pro zpětná volání relací – Preview](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěn robot "tutorialSessionCallbacks".

    npm run tutorial-session-callbacks

## <a name="details"></a>Podrobnosti
Tento kurz se zabývá konceptem relace, způsobu, jakým jsou ve výchozím nastavení zpracovávány relace, a způsobu, jakým toto chování můžete přepsat.

V Conversation Learner relace představuje jedno, nepřerušované interaktivní Exchange s robotem. Relace mohou mít vícenásobné zapínání, ale prostřednictvím kódu programu byly ukončeny z důvodu nečinnosti, pokud uplynulo více než 30 minut.  Informace o změně výchozí doby trvání časového limitu relace najdete na stránce s nápovědu o omezeních.

Tato dlouhodobá doba nečinnosti způsobí, že robot vytvoří novou relaci a obnoví znovu aktuální neuronové síť do původního stavu. Ve výchozím nastavení se všechny hodnoty entit vymažou. Toto výchozí chování při mazání hodnot entit může být změněno, jak je vidět níže.

### <a name="load-the-demo-model"></a>Načtení ukázkového modelu

Ve webovém uživatelském rozhraní klikněte na "Import kurzů" a vyberte model s názvem "tutorial-13-SessionCallbacks".

### <a name="code-for-the-callbacks"></a>Kód pro zpětná volání

Vzorový kód pro dvě zpětná volání tohoto modelu najdete tady: `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: Tato metoda nastavuje entitu BotName.
- OnSessionEndCallback: můžete určit, co chcete zachovat. Tím se vymažou všechny entity kromě uživatelského jména a uživatelského telefonu.

Každé zpětné volání je volitelné.

### <a name="actions"></a>Akce

V modelu jsou definovány čtyři akce. Existující akce se zobrazí v zobrazení mřížky pro položky akce.

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Vytvoření akce ukončení relace (pro vyvolání zpětného volání)

1. Na levém panelu klikněte na akce a pak na tlačítko Nová akce.
2. Jako typ entity vyberte "ENDSESSION".
3. V části data... Zadejte "Hotovo"
4. Klikněte na tlačítko vytvořit.

### <a name="edit-an-existing-action"></a>Upravit existující akci

1. V zobrazení mřížky vyberte "$UserName", jste v $UserLocation akci.
2. Zrušte kontrolu v poli Čekání na odpověď.
3. Klikněte na tlačítko Uložit.

### <a name="chaining-actions"></a>Řetězení akcí

1. Na levém panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko "nový výukový dialog".
2. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Dobrý den".
3. Klikněte na tlačítko "akce skóre".
4. Vyberte odpověď "Dobrý den, já jsem Botty. Jaké je vaše jméno? "
5. Na panelu chat, kde říká "zadejte zprávu..." zadejte "Lars".
6. Vyberte odpověď "dobrý Lars. Jaké je vaše telefonní číslo? "
7. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "555-555-5555".
8. Klikněte na tlačítko "akce skóre".
9. Vyberte odpověď, "můžete říct, že Botty své umístění, Lars?"
10. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Praha".
11. Klikněte na tlačítko "akce skóre".
12. Vyberte odpověď, "Takže, Lars, jste v Seattlu."
13. Vyberte odpověď, "Hotovo".
14. Klikněte na tlačítko Uložit.

### <a name="testing-the-model"></a>Testování modelu

1. Na levém panelu klikněte na možnost protokolovat dialogy a pak na tlačítko nový dialog protokolu.
2. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Dobrý den".
3. Na panelu chat, kde říká "zadejte zprávu..." zadejte "Lars".
4. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "555-555-5555".
5. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Praha".
    - V tomto okamžiku by měly být zachovány všechny hodnoty entit kromě umístění.
6. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Hello".
7. Na panelu chat, kde říká "zadejte zprávu..." zadejte "Detroit".
8. Klikněte na tlačítko časový limit relace.
    - Kliknutím na toto tlačítko se zobrazí odpověď robota na dlouhou dobu nečinnosti.
9. Klikněte na tlačítko OK.
10. Klikněte na tlačítko dokončené testování.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volání rozhraní API](./14-api-calls.md)
