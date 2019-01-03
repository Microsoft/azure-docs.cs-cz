---
title: Jak použít zpětná volání relace s modelem konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak použít zpětná volání relace s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 5458d83fcc9fad50a2a52273ea6b5573ed90a97e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796709"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Jak použít zpětná volání relace s modelem Learner konverzace

Tento kurz představuje relace, jak se zpracovává a konverzace Learner onSessionStart a onSessionEnd zpětná volání.

## <a name="video"></a>Video

[![Relace zpětná volání kurz ve verzi Preview](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna bot "tutorialSessionCallbacks".

    npm run tutorial-session-callbacks

## <a name="details"></a>Podrobnosti
Tento kurz se zabývá konceptu relaci zpracování relací ve výchozím nastavení a jak můžete přepsat toto chování.

V konverzaci Learner relaci představuje jeden, bez přerušení interaktivní exchange s roboty. Relace může mít více zapne, ale být programově ukončeno z důvodu nečinnosti delší než 30 minut nim připojit.  Naleznete na stránce nápovědy na "Omezení" informace o změně tuto dobu trvání časového limitu relace výchozí.

Tato delší době nečinnosti způsobí, že bot vytvořit novou relaci a opakované neuronové sítě obnovit do původního stavu. Ve výchozím nastavení se vymažou všechny hodnoty entity. Toto výchozí chování rušit zaškrtnutí hodnot entity můžete změnit, jak je vidět níže.

### <a name="load-the-demo-model"></a>Načíst ukázku Model

Ve webovém uživatelském rozhraní klikněte na "Import kurzy" a vyberte model s názvem "Kurzu SessionCallbacks 13".

### <a name="code-for-the-callbacks"></a>Kód pro zpětných dotazů

Ukázkový kód pro tento Model dvě zpětná volání můžete najít v: `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: Tato metoda nastaví BotName entitu.
- OnSessionEndCallback: můžete určit, co byste chtěli zachovat. Tato operace vymaže všechny entity s výjimkou uživatelské jméno a Telefon uživatele.

Každý zpětného volání je volitelný.

### <a name="actions"></a>Akce

Čtyři akce jsou definovány v modelu. Existující akce, které jsou zobrazeny v zobrazení mřížky pro "Akce"

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Vytvoření akce relace End (pro vyvolání zpětného volání)

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. Vyberte "ENDSESSION" u "Typu Entity."
3. V poli "Data …" typ "Hotovo"
4. Klikněte na tlačítko "Vytvořit".

### <a name="edit-an-existing-action"></a>Upravit existující akce

1. Vyberte "tedy $UserName, které jsou v $UserLocation" akce z mřížkového zobrazení.
2. Zrušte zaškrtnutí políčka zaškrtávací políčko "Čekat pro odpověď".
3. Klikněte na tlačítko "Save".

### <a name="chaining-actions"></a>Řetězení akce

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Dobrý den."
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte odpověď na "Dobrý den, já jsem Botty. Jak se jmenuješ?"
5. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Lars"
6. Vyberte odpověď na "Hi Lars. Co je vaše telefonní číslo?"
7. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "555-555-5555"
8. Klikněte na tlačítko "Skóre akce".
9. Vyberte odpověď na "Zjistit Botty vaši polohu, Lars?"
10. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Seattle"
11. Klikněte na tlačítko "Skóre akce".
12. Vyberte odpověď na "Tak, Lars, jsou v Praze"
13. Vyberte odpověď na "Hotovo"
14. Klikněte na tlačítko "Save".

### <a name="testing-the-model"></a>Testování modelu

1. Na levém panelu klikněte na tlačítko "Protokolu dialogů" a potom na tlačítko "Dialogové okno Nový protokol".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "hi"
3. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Lars"
4. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "555-555-5555"
5. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Seattle"
    - V tomto okamžiku všechny entity hodnoty s výjimkou umístění by měl mít byla zachována.
6. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "hello"
7. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Detroit"
8. Klikněte na tlačítko "Časový limit relace".
    - Kliknutím na toto tlačítko vykonává bodu robotů také odpovědi na dlouhé době nečinnosti
9. Klikněte na tlačítko "OK".
10. Klikněte na tlačítko "Testování Hotovo".

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Volání rozhraní API](./14-api-calls.md)
