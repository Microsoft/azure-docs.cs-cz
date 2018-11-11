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
ms.openlocfilehash: e23ff60a0a2ea10ace09130ba115e72b4e1c9ad7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249808"
---
# <a name="demo-pizza-order"></a>Ukázka: Pizza pořadí
Tato ukázka znázorňuje pizza řazení robota. Podporuje řazení jednoho pizza pomocí této funkce:

- uvědomili si pizza toppings v projevy uživatele
- Probíhá kontrola, zda jsou pizza toppings v zásobách nebo vyčerpány zásoby a odpovídajícím způsobem reagovat
- zapamatování pizza toppings z vztahuje dřívější objednávka a nabídky ke – nová objednávka začínat stejné toppings

## <a name="video"></a>Video

[![Ukázka Pizza ve verzi Preview](https://aka.ms/cl-demo-pizza-preview)](https://aka.ms/blis-demo-pizza)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, že pořadí bot pizza běží

    npm run demo-pizza

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu modelu ve webovém uživatelském rozhraní kliknutím na TutorialDemo Pizza pořadí. 

## <a name="entities"></a>Entity

Vytvoříte tři entity.

- Toppings: tuto entitu budou se hromadit toppings, které se uživateli zobrazí výzva pro. Její součástí jsou platné toppings, které jsou v zásobách. Zkontroluje, jestli přičemž je v nebo vyčerpány zásoby.
- OutofStock: Tato entita se používá ke komunikaci zpět uživateli, že vybrané přičemž není na skladě.
- LastToppings: Jakmile objednávka je umístěn, tato entita se používá k nabízejí uživateli seznam toppings na jejich pořadí.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Akce

Vytvořili jste sadu akcí, včetně s žádostí o to, co chtějí na jejich pizza sděluje, co jste vložili zatím, a tak dále.

Existují dvě volání rozhraní API:

- FinalizeOrder: objednávku pro pizza
- UseLastToppings: migrace toppings z vztahuje dřívější objednávka 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Dialogová okna školení
Definovali jste několik dialogů, školení. 

![](../media/tutorial_pizza_dialogs.PNG)

Jako příklad si vyzkoušíme relaci výuky.

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
1. Zadejte "pořadí pizza".
2. Klikněte na akci skóre.
3. Kliknutím vyberte "co chcete na vaše pizza?"
4. Zadejte "hub a produkci minipivovarů ve wisconsinu".
    - Všimněte si, že služba LUIS je označeno jako Toppings. Pokud, který nebyl zadán správně, může kliknutím zvýrazněte a pak ho opravit.
    - Znaménka '+' vedle entity znamená, že ho přidáte do sady toppings.
5. Klikněte na výsledek akce.
    - Všimněte si, že `mushrooms` a `cheese` nejsou v paměti pro Toppings.
3. Kliknutím vyberte "máte $Toppings na vaše pizza.
    - Všimněte si, že jde o bez čekání akce tak budou robota požádat o další akci.
6. Vyberte možnost "Chcete cokoli?"
7. Zadejte "odeberte hub a přidejte papriky".
    - Všimněte si, že `mushroom` má "-" sign vedle něj, aby se odeberou. A `peppers` má '+' znaménko vedle něj, přidejte do toppings.
2. Klikněte na akci skóre.
    - Všimněte si, že `peppers` je nyní v tučné písmo, protože se nové. A `mushrooms` byl překročen.
8. Kliknutím vyberte "máte $Toppings na vaše pizza.
6. Vyberte možnost "Chcete cokoli?"
7. Zadejte "Přidat hrách".
    - `Peas` je příkladem přičemž, což je vyčerpány zásoby. Stále je označen jako přičemž.
2. Klikněte na akci skóre.
    - `Peas` Zobrazí se jako OutOfStock.
    - Chcete-li zjistit, jak se to stalo, otevřete kód v `C:\<\installedpath>\src\demos\demoPizzaOrder.ts`. Podívejte se na metodu EntityDetectionCallback. Tato metoda je volána po každé přičemž, jestli je na skladě. Pokud ne, vymaže ze sady toppings a přidá do OutOfStock entity. InStock proměnná je definována nad tuto metodu, která obsahuje seznam toppings ve skladu.
6. Vyberte "My tak nemusíme $OutOfStock".
7. Vyberte možnost "Chcete cokoli?"
8. Zadejte "žádný".
9. Klikněte na akci skóre.
10. Vyberte volání 'FinalizeOrder"rozhraní API. 
    - To bude volat "FinalizeOrder" funkci definovanou v kódu. Vymaže toppings a vrátí "vaši objednávku je na cestě". 
2. Zadejte "order jiné". Začínáme novou objednávku.
9. Klikněte na akci skóre.
    - "produkci minipivovarů ve wisconsinu" a "papriky" jsou jako toppings z poslední objednávky v paměti.
1. Vyberte "Přejete si $LastToppings".
2. Zadejte "yes"
3. Klikněte na akci skóre.
    - Robot chce udělat UseLastToppings. Tím dokončím dvou metod zpětného volání. Bude kopírovat do toppings toppings poslední objednávky a vymazat poslední toppings. Toto je způsob, jak pamatuje poslední objednávky a pokud uživatel zvolí, že chtějí jiného pizza, nabízí tyto toppings jako možnosti.
2. Kliknutím vyberte "máte $Toppings na vaše pizza".
3. Vyberte možnost "Chcete cokoli?"
8. Zadejte "žádný".
4. Klikněte na Hotovo výuky.

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Demo - VR Spouštěč aplikací](./demo-vr-app-launcher.md)
