---
title: Ukázkový Conversation Learner model, pořadí Pizza – Microsoft Cognitive Services | Microsoft Docs
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
ms.openlocfilehash: 4d42121468b8a875ecd13fb936d810728bd4b644
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703430"
---
# <a name="demo-pizza-order"></a>Ukázka: Pizza pořadí
Tato ukázka ilustruje robot objednávání pizza, který podporuje jednotné řazení Pizza podle:

- rozpoznávání Pizza toppings od uživatele projevy
- Správa inventáře toppings a řádné reakce
- pamatuje si předchozí objednávky a urychlí změnu pořadí identického Pizza

## <a name="video"></a>Video

[![Ukázka Pizza Preview](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěn robot Pizza objednávky.

    npm run demo-pizza

### <a name="open-the-demo"></a>Otevřete ukázku.

V seznamu model webového uživatelského rozhraní klikněte na TutorialDemo Pizza Order. 

## <a name="entities"></a>Entity

Model obsahuje tři entity:

- "Toppings" shromažďuje zadaný toppings uživatele, pokud je k dispozici.
- "OutofStock" signalizuje uživateli, že vybraný Topping je mimo sklad.
- "LastToppings" obsahuje historické toppings z předchozích objednávek.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Akce

Model obsahuje sadu akcí, které požádá uživatele o jejich Topping výběr, Akumulovaná toppings a další.

Jsou k dispozici také dvě volání rozhraní API:

- "FinalizeOrder" zpracovává splnění pořadí
- "UseLastToppings" zpracovává historické informace o toppings

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Dialogová okna pro školení

V modelu se nachází několik dialogů školení.

![](../media/tutorial_pizza_dialogs.PNG)

Vydejte si model trochu více vytvořením dalšího výukového dialogového okna.

1. Na levém panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko "nový výukový dialog".
2. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "ORDER by Pizza with sýra".
    - Slovo "sýr" bylo extrahováno nástrojem pro extrakci entit.
3. Klikněte na tlačítko "akce skóre".
4. Vyberte odpověď "máte k dispozici svůj sýr na Pizza."
5. Vyberte odpověď "Přejete si něco jiného?"
6. Na panelu chat, kde text "zadejte zprávu..." zadejte text "Přidat žampiony a papriky".
7. Klikněte na tlačítko "akce skóre".
8. Vyberte odpověď "v Pizza máte sýry, žampiony a papriky."
9. Vyberte odpověď "Přejete si něco jiného?"
10. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "odebrat papriky a přidat uzenku".
11. Klikněte na tlačítko "akce skóre".
12. Vyberte odpověď "v Pizza máte sýry, houby a uzené."
13. Vyberte odpověď "Přejete si něco jiného?"
14. Na panelu chat, kde říká "zadejte zprávu..." zadejte do pole "Přidat Yam".
15. Klikněte na tlačítko "akce skóre".
    - Hodnota "Yam" byla do "OutofStock" přidána pomocí kódu zpětného volání pro detekci entity, protože text neodpovídal žádné podporované komponentě.
16. Vyberte odpověď "OutOfStock"
17. Vyberte odpověď "Přejete si něco jiného?"
18. Na panelu chat, kde uvádí text zpráva "zadejte zprávu...", zadejte "ne".
    - Možnost No není označena jako žádný typ záměru. Místo toho vybereme relevantní akci na základě aktuálního kontextu.
19. Klikněte na tlačítko "akce skóre".
20. Vyberte odpověď "FinalizeOrder"
    - Po výběru této akce dojde k tomu, že aktuální toppings zákazníka se uloží do entity LastToppings a odstraní se entita "toppings" kódem zpětného volání FinalizeOrder.
21. Na panelu chat, kde uvádí text zpráva "zadejte zprávu...", zadejte "Order jinou".
22. Klikněte na tlačítko "akce skóre".
23. Vyberte odpověď, "Přejete si jako sýr, houby a uzení?"
    - Tato akce je nyní k dispozici, protože je nastavena entita "LastToppings".
24. Na panelu chat, kde říká "zadejte zprávu..." zadejte "Ano".
25. Klikněte na tlačítko "akce skóre".
26. Vyberte odpověď "UseLastToppings"
27. Vyberte odpověď "v Pizza máte sýry, houby a uzené."
28. Vyberte odpověď "Přejete si něco jiného?"

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nasazení Conversation Learner robota](../deploy-to-bf.md)
