---
title: Ukázka konverzace Learner model, objednávka pizza – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak vytvořit model konverzace Learner ukázku.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 9b35c0fd412dd48137a3cb362f20fae067c80461
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792624"
---
# <a name="demo-pizza-order"></a>Ukázka: Pizza pořadí
Tato ukázka znázorňuje pizza řazení robota, podporuje jeden pizza řazení podle:

- uvědomili si pizza toppings z projevy uživatele
- Správa inventáře toppings a odpovídajícím způsobem reagovat
- pamatovat předchozí objednávky a tím i urychlení změny pořadí identické pizza

## <a name="video"></a>Video

[![Ukázka Pizza ve verzi Preview](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, že pořadí bot pizza běží

    npm run demo-pizza

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu modelu ve webovém uživatelském rozhraní kliknutím na TutorialDemo Pizza pořadí. 

## <a name="entities"></a>Entity

Model obsahuje tři entity:

- "Toppings" nahromadí toppings zadané uživatele, pokud je k dispozici.
- "OutofStock" signalizuje, že vybrané přičemž je vyčerpány zásoby uživateli
- "LastToppings" obsahuje Historický toppings z jejich vztahuje dřívější objednávka

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Akce

Model obsahuje sadu akcí, která vyzve uživatele k zadání jejich výběr přičemž, nahromaděné toppings a další.

Také poskytuje dvě volání rozhraní API:

- "FinalizeOrder" zpracovává vyřizování objednávek
- "UseLastToppings" zpracovává historická toppings informace

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Dialogová okna školení

Několik dialogů školení se nacházejí v modelu.

![](../media/tutorial_pizza_dialogs.PNG)

Pojďme trénování modelu o trochu výkonnější vytvořením další Dialog trénování.

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Order pizza s produkci minipivovarů ve wisconsinu."
    - Slovo "produkci minipivovarů ve wisconsinu" byl extrahován Extraktor entity.
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte odpověď, a to "Na vaše pizza mají produkci minipivovarů ve wisconsinu."
5. Vyberte odpověď na "Chcete cokoli?"
6. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Přidání hub a papriky"
7. Klikněte na tlačítko "Skóre akce".
8. Vyberte odpověď na "Máte produkci minipivovarů ve wisconsinu, hub a papriky na vaše pizza."
9. Vyberte odpověď na "Chcete cokoli?"
10. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "odeberte papriky a přidejte salám"
11. Klikněte na tlačítko "Skóre akce".
12. Vyberte odpověď na "Máte produkci minipivovarů ve wisconsinu, hub a salám na vaše pizza."
13. Vyberte odpověď na "Chcete cokoli?"
14. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Přidání yam"
15. Klikněte na tlačítko "Skóre akce".
    - Hodnota "yam" byl přidán do "OutofStock" kódem detekce zpětného volání entity jako text neodpovídá žádné podporované složek.
16. Vyberte odpověď "OutOfStock"
17. Vyberte odpověď na "Chcete cokoli?"
18. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Ne"
    - "Ne" není označen jako libovolný typ záměr. Místo toho vybereme příslušnou akci na základě aktuálního kontextu.
19. Klikněte na tlačítko "Skóre akce".
20. Vyberte odpověď "FinalizeOrder"
    - Vyberte tuto akci za následek zákazníka aktuální toppings získávání uloží do "LastToppings" Entity a odstranění entit "Toppings" kódem FinalizeOrder zpětného volání.
21. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "order jiného"
22. Klikněte na tlačítko "Skóre akce".
23. Vyberte odpověď na "Chcete produkci minipivovarů ve wisconsinu, hub a salám?"
    - Tato akce je teď dostupný z důvodu se sada entit "LastToppings".
24. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Ano"
25. Klikněte na tlačítko "Skóre akce".
26. Vyberte odpověď "UseLastToppings"
27. Vyberte odpověď na "Máte produkci minipivovarů ve wisconsinu, hub a salám na vaše pizza."
28. Vyberte odpověď na "Chcete cokoli?"

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nasazení robota Learner konverzace](../deploy-to-bf.md)
