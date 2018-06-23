---
title: Ukázka konverzace student aplikace, pořadí pizza - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informace o vytvoření ukázkové aplikace student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 99cd89c4f4430f2d65ed0963e3092d51a83842d7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343278"
---
# <a name="demo-pizza-order"></a>Ukázku: Pořadí Pizza
Tato ukázka znázorňuje pizza, řazení robota. Podporuje řazení jednoho pizza pomocí této funkce:

- rozpozná pizza toppings v utterances uživatele
- kontrole, jestli jsou pizza toppings stock nebo zrušení stock a odpovídajícím způsobem reagovat
- zapamatování pizza toppings z předchozí pořadí a nabídky k – nové pořadí začínat stejné toppings

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby robota pořadí pizza běží

    npm run demo-pizza

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu aplikací webového uživatelského rozhraní klikněte na TutorialDemo Pizza pořadí. 

## <a name="entities"></a>Entity

Vytvořili jsme tři entity.

- Toppings: budou se hromadit toppings, které se uživateli zobrazí výzva pro. Obsahuje platný toppings, které jsou ve skladu. Zkontroluje, jestli přičemž je v nebo mimo stock.
- OutofStock: používá se komunikovat s uživateli, že vybrané přičemž není ve skladu.
- LastToppings: po pořadí je umístěn, tuto entitu slouží k nabízejí uživatelům seznam toppings na jejich pořadí.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Akce

Vytvořili jsme sadu akcí, třeba požádat uživatele, chtějí na jejich pizza, upozorněním, co jste vložili, pokud atd.

Existují dvě volání rozhraní API:

- FinalizeOrder: umístit pořadí pizza
- UseLastToppings: migrace toppings z předchozí pořadí 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Školení dialogová okna
Jsme definovali několik dialogů školení. 

![](../media/tutorial_pizza_dialogs.PNG)

Jako příklad Zkusme vyučující relace.

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
1. Zadejte, řazení pizza'.
2. Klikněte na tlačítko akce skóre.
3. Kliknutím vyberte, co chcete použít na vaše pizza?"
4. Zadejte "hub a sýr".
    - Všimněte si, že oba jako Toppings označeném LEOŠ. Pokud, který nebyl zadán správně, může kliknutím zvýrazněte a potom ji opravte.
    - Znaménka '+' vedle entity znamená, že je právě přidaná do sady toppings.
5. Klikněte na tlačítko akce skóre.
    - Všimněte si hub a sýr nejsou v paměti pro Toppings.
3. Kliknutím vyberte "máte $Toppings na vaše pizza.
    - Všimněte si, že to je bez čekání akce, takže robota požádá pro další akce.
6. Vyberte, chcete cokoliv jiného?"
7. Zadejte "Odebrat hub a přidat zeleninová".
    - Všimněte si **hub** má '-' znaménkem pro její odebrání. A má zeleninová '+' tím ho přidáte do toppings.
2. Klikněte na tlačítko akce skóre.
    - Všimněte si **zeleninová** nyní v tučné, protože je nové. A **hub** byl překročen.
8. Kliknutím vyberte "máte $Toppings na vaše pizza.
6. Vyberte, chcete cokoliv jiného?"
7. Zadejte 'přidat hrách'.
    - Hrách jsou příklad přičemž, což je mimo stock. Všimněte si, že je stále označený jako přičemž.
2. Klikněte na tlačítko akce skóre.
    - Hrách zobrazují jako OutOfStock.
    - Informace o tom, jak se to stalo, otevřete kód na C:\<\installedpath > \src\demos\demoPizzaOrder.ts. A poznamenejte si EntityDetectionCallback metoda. Tato metoda je volána po každé přičemž zkontrolujte, jestli je na skladě. Pokud ne, se odstraní ze sadu toppings a přidá do OutOfStock entity. Proměnnou inStock je definovaný nad tuto metodu, která má seznam ve stock toppings.
6. Vyberte, ale nemáme $OutOfStock'.
7. Vyberte, chcete cokoliv jiného?"
8. Zadejte "žádné".
9. Klikněte na tlačítko akce skóre.
10. Vyberte volání 'FinalizeOrder' rozhraní API. 
    - To bude volat funkci 'FinalizeOrder' definované v kódu. Vymaže toppings a vrátí 'vaši objednávku je na cestě'. 
2. Zadejte 'pořadí jiné'. Nemůžeme se spouští nové pořadí.
9. Klikněte na tlačítko akce skóre.
    - Poznámka: sýr a zeleninová jsou v paměti jako toppings od poslední pořadí.
1. Vyberte, chcete $LastToppings'.
2. Zadejte "Ano"
3. Klikněte na tlačítko akce skóre.
    - Robota chce provést akci UseLastToppings. Je druhá tyto dvě metody zpětného volání. Bude kopírovat poslední pořadí toppings do toppings a vymazat poslední toppings. Tímto způsobem zapamatování poslední objednávky, a je-li uživatel informacemi o tom, že chtějí jiné pizza, poskytovat tyto toppings jako možnosti.
2. Kliknutím vyberte 'máte $Toppings na vaše pizza'.
3. Vyberte, chcete cokoliv jiného?"
8. Zadejte "žádné".
4. Klikněte na tlačítko Hotovo vyučující.

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Demo - Spouštěč aplikace VR.](./demo-vr-app-launcher.md)
