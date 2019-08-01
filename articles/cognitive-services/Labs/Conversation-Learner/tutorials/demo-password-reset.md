---
title: Ukázkový Conversation Learner model, resetování hesla-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se, jak vytvořit ukázkový model Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 51eab34f32a20a86445da0ac44d94a31d6694b40
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703394"
---
# <a name="demo-password-reset"></a>Ukázka: Resetování hesla
V tomto kurzu se dozvíte o jednoduchém robotovi technické podpory, který vám může pomoci s resetování hesla, které používá Conversation Learner. Model robota se může naučit netriviální dialogová toky a vícenásobné sekvence, včetně tříd mimo doménu. Úkol lze provést bez kódu nebo entit.

## <a name="video"></a>Video

[![Ukázka hesla Preview](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěn robot pro resetování hesla.

    npm run demo-password

### <a name="open-the-demo"></a>Otevřete ukázku.

V seznamu model webového uživatelského rozhraní klikněte na kurz ukázka resetování hesla. 

### <a name="actions"></a>Akce

Model obsahuje sadu akcí navržených pro pomoc uživatelům s řešením běžných problémů s heslem.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Dialogová okna pro školení

Model obsahuje také několik dialogů s výukou, včetně některých, které ukazují z odborného školení pro doménovou třídu. Například uživatelé, kteří si mohou vyžádat jako pokyny k řízení. Vzorový robot byl pro účely demonstračního školení vyškolený a jednoduše odpoví tím, že vám to nepomůže. Seznam existujících dialogových oken s vlaky najdete v části "výukové dialogy" na levém panelu.

![](../media/tutorial_pw_reset_entities.PNG)

1. Na levém panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko "nový výukový dialog".
2. Na panelu chat, kde uvádí text zpráva "zadejte zprávu...", zadejte "ztratil heslo."
3. Klikněte na tlačítko "akce skóre".
4. Vyberte odpověď, "je to pro váš místní účet nebo účet Microsoft?"
5. Na panelu chat, kde říká "zadejte zprávu..." zadejte "místní účet prosím".
6. Klikněte na tlačítko "akce skóre".
7. Vyberte odpověď "jakou verzi Windows máte?"
8. Na panelu chat, kde text "zadejte zprávu..." zadejte text "Windows XP".
9. Klikněte na tlačítko "akce skóre".
10. Klikněte na tlačítko + akce.
11. V "reakci na robota..." Zadejte "SOLUTION: Postup resetování hesla v systému Windows XP...
12. Klikněte na tlačítko vytvořit.

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Dialogová okna školení pro scénáře mimo doménu

1. Na levém panelu klikněte na "výuková dialogová okna" a pak na stávající "vlaková dialogová slova".
2. Na panelu chat klikněte na "obchody v hračkách" utterance.
3. V části Přidat alternativní vstup... Zadejte text "vyhledávání na webu" a stiskněte ENTER.
4. V části Přidat alternativní vstup... Zadejte do pole "rezerva letu" a stiskněte ENTER.
5. Klikněte na tlačítko Uložit změny.
6. Klikněte na tlačítko Uložit úpravy.
7. Na levém panelu klikněte na možnost protokolovat dialogy a pak na tlačítko nový dialog protokolu.
8. Na panelu chat, kde říká "napsat zprávu...", zadejte "Nemůžu najít heslo"
9. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "účet Microsoft".
10. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Děkuji".
11. Klikněte na tlačítko dokončené testování.
12. V zobrazení mřížky klikněte na dialogové okno protokol nemůžu najít heslo.
13. Na panelu chat klikněte na nesprávně vykreslené řešení: Nastavení odpovědi na heslo účtu Microsoft
14. Klikněte na tlačítko + akce.
15. V "reakci na robota..." Zadejte text "Vítá vás".
16. Klikněte na tlačítko vytvořit.
17. Klikněte na tlačítko Uložit jako výukový dialog.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ukázka – pořadí Pizza](./demo-pizza-order.md)
