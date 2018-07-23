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
ms.openlocfilehash: 0f51b232470e4e4da3f25d40d025dd3b09dd1204
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171911"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Jak použít zpětná volání relace s modelem Learner konverzace

Tento kurz ukazuje onSessionStart a onSessionEnd zpětná volání.

## <a name="video"></a>Video

[![Kurz 11 ve verzi Preview](http://aka.ms/cl-tutorial-11-preview)](http://aka.ms/blis-tutorial-11)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby `tutorialSessionCallbacks` bot běží.

    npm run tutorial-session-callbacks

## <a name="details"></a>Podrobnosti
Tento kurz se zabývá konceptu relaci zpracování relací ve výchozím nastavení a jak můžete přepsat toto chování.

Relace je jedna konverzace s roboty. Může mít více zapne, ale neobsahuje žádné dlouhé konce konverzace (například 30 minut).  Naleznete na stránce nápovědy na "Omezení" pro výchozí dobu trvání časového limitu relace.

Pokud existuje dlouhý konce, robota přejdete na svůj příští relaci.  Opakované neuronové sítě spuštěním nové relace umístí do původního stavu.  Ve výchozím nastavení rovněž vymaže všechny hodnoty entity, i když toto chování lze změnit (ukázáno níže).

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu Model klikněte na kurz-11-SessionCallbacks. 

### <a name="entities"></a>Entity

Čtyři entity jsou definovány v modelu.

![](../media/tutorial11_entities.PNG)

Poznámka je BotName programový Entity.  Tato entita nastavíte robota na čas spuštění relace.

### <a name="actions"></a>Akce

Čtyři akce jsou definovány v modelu.

![](../media/tutorial11_actions.PNG)

Nejprve tomto kurzu se dozvíte jak řídit hodnoty entit na začátku relace – například nastavení BotName entity předtím, než se mu říká něco.

Za druhé v tomto kurzu zobrazí zachování hodnot z jedné relace na další.  V tomto kurzu předpokládáme, že uživatelské jméno a telefonní číslo zůstávají stejné z jedné relace na další, ale, že se může změnit jejich umístění.  Proto jsme jméno a telefonní číslo zachová napříč relacemi, ale umístění vymazat uživatele.

### <a name="train-dialog"></a>Dialogové okno trénování

Tady je příklad dialogové okno. Toto je jedna relace – to znamená, že jsou žádné dlouhodobé výpadky v tomto dialogovém okně.

![](../media/tutorial11_traindialog.PNG)

### <a name="code-for-the-callbacks"></a>Kód pro zpětných dotazů

Kód pro metody zpětného volání je v souboru: c:\<installedpath > \src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial11_code.PNG)

Obě tyto metody jsou volitelné.

- OnSessionStartCallback: Tato metoda nastaví BotName entitu.
- OnSessionEndCallback: můžete určit, co byste chtěli zachovat. Tato operace vymaže všechny entity s výjimkou uživatelské jméno a Telefon uživatele.

### <a name="try-the-bot"></a>Zkuste robota

Přejděte do webového uživatelského rozhraní a klikněte na protokol dialogová okna.

1. Zadejte "hello".
2. System: "Dobrý den, Já jsem Botty. Jak se jmenuješ? " který má název Botty odesílaných z OnSessionStartCallback.
3. Zadejte "jason".
4. System: "Hi jason. Co je vaše telefonní číslo? "
5. Zadejte "555-555-5555".
6. System: "zjistit Botty vaši polohu, jason?"
7. Zadejte "Redmond".

Toto je jedna relace. Chcete-li zahájit novou relaci, musíme ukončit tuto relaci. 

1. Klikněte na časový limit relace. To se přesune na další relace.
    - "Časový limit relace" tlačítko je k dispozici pro účely ladění.  V relaci skutečné by bylo potřeba dlouhé pozastavení dojít přibližně 30 minut.  Naleznete na stránce nápovědy na "Omezení" pro výchozí dobu trvání časového limitu relace.
1. Zadejte "hi".
2. System: "zjistit Botty vaši polohu, jason?"
    - Systém se uloží, jméno a telefonní číslo.
2. Zadejte nové umístění: "Seattle".
3. System: "tedy jason jste v Praze".
4. Klikněte na tlačítko Hotovo testování.

Teď přepněte zpět do protokolu dialogová okna. Všimněte si, že poslední konverzace má rozdělit do dvou, protože každý dialogové okno Protokol odpovídá jedné relace.  

![](../media/tutorial11_splitdialogs.PNG)

- V první interakce Botty nastavena, ale nejsou jméno a telefonní číslo.
- Druhý interakce zobrazuje název a telefonní číslo.

Viděli jste nyní zpracování relací ve výchozím nastavení, a jak můžete přepsat výchozí chování. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Volání rozhraní API](./12-api-calls.md)
