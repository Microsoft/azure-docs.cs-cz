---
title: Postup používání zpětných volání relace s aplikací konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Další informace o použití zpětná volání relace s aplikací student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f8970620c1f0f87ccae13d031092a048144ffb19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343243"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-application"></a>Postup používání zpětných volání relace s konverzace student aplikací

Tento kurz ukazuje, zpětná volání onSessionStart a onSessionEnd.

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna robota "tutorialSessionCallbacks.ts".

    npm run tutorial-session-callbacks

## <a name="details"></a>Podrobnosti
Tento kurz se zaměřuje na základní informace o relaci, zpracování relace ve výchozím nastavení a jak můžete přepsat toto chování.

Relace je jedna konverzace s robota. Může mít několik zapne, ale neobsahuje žádné dlouho konce konverzace (například 30 minut).  Naleznete na stránce nápovědy na "Omezení" Výchozí doba vypršení časového limitu relace.

Pokud jsou dlouho konce, robota přejde na jeho další relace.  Spuštěním nové relace umístí opakující neuronové sítě do původního stavu.  Ve výchozím nastavení rovněž vymaže všechny entity hodnoty, i když toto chování lze změnit (ilustrované níže).

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu aplikací klepněte na kurz. 11 SessionCallbacks. 

### <a name="entities"></a>Entity

V aplikaci jsme definovali čtyři entity.

![](../media/tutorial11_entities.PNG)

Poznámka je, že BotName je programový Entity.  Tento se nastavuje pomocí robota při spuštění relace.

### <a name="actions"></a>Akce

Vytvořili jsme čtyř akcí. 

![](../media/tutorial11_actions.PNG)

Nejprve v tomto kurzu se ukazují, jak řídit hodnot entity na začátku relace – například nastavení BotName entity před uživateli říká nic.

V tomto kurzu bude druhý, ukazují, jak ukládat hodnoty z jedné relace na další.  V tomto návodu předpokládáme, že uživatelské jméno a telefonní číslo zůstávají stejné z jedné relace na další, ale, že může změnit jejich umístění.  Proto jsme přetrvávat název a telefonní číslo v relace, ale umístění zrušte uživatele.

### <a name="train-dialog"></a>Dialogové okno Train

Tady je příklad dialogové okno. Toto je jedna relace – to znamená, že jsou žádné dlouho konce v tomto dialogu.

![](../media/tutorial11_traindialog.PNG)

### <a name="code-for-the-callbacks"></a>Kód zpětných volání

Kód pro metody zpětného volání je v souboru: c:\<installedpath > \src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial11_code.PNG)

Obě tyto metody jsou volitelné.

- OnSessionStartCallback: Tato metoda nastaví BotName entitu.
- OnSessionEndCallback: můžete zadat, co si přejete vymazat. Tímto dojde k vymazání všech entit s výjimkou uživatelské jméno a Telefon uživatele.

### <a name="try-the-bot"></a>Zkuste robota

Přepněte do webového uživatelského rozhraní a klikněte na protokolu v dialogových oknech.

1. Zadejte text "hello".
2. System: ' Dobrý den, jsem Botty. Jaké je vaše jméno? " který má název Botty vstupu z OnSessionStartCallback.
3. Zadejte 'jason'.
4. System: ' jason HIS použití. Co je vaše telefonní číslo? "
5. Zadejte ' 555-555-5555'.
6. System: 'zjistit Botty vaši polohu, jason?"
7. Zadejte "Redmond".

Toto je jedna relace. Chcete-li zahájit novou relaci, musíme ukončení této relace. 

1. Klikněte na tlačítko časový limit relace. To se přesune do další relace.
    - Tlačítko "Časový limit relace" se poskytuje pro účely ladění.  V relaci skutečné dlouho pozastavení potřebovat proběhnout přibližně 30 minut.  Naleznete na stránce nápovědy na "Omezení" Výchozí doba vypršení časového limitu relace.
1. Zadejte "hi".
2. System: 'zjistit Botty vaši polohu, jason?"
    - Systém má zapamatovaných název a telefonní číslo.
2. Zadejte nové umístění: "Seattle".
3. System: 'tedy jason jste v Praze'.
4. Klikněte na tlačítko Hotovo testování.

Umožňuje vrátit zpět do protokolu v dialogových oknech. Všimněte si, že poslední konverzace se rozdělila na dvě, protože každý protokol dialogu odpovídá na jednu relaci.  

![](../media/tutorial11_splitdialogs.PNG)

- V první interakce Botty nastavená, ale nejsou název a telefonní číslo.
- Druhý interakce zobrazuje název a telefonní číslo.

Viděli jste nyní zpracování relace ve výchozím nastavení, a jak můžete přepsat výchozí chování. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Volání rozhraní API](./12-api-calls.md)
