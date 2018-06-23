---
title: Jak používat rozhraní API pomocí volání s aplikací konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Další informace o použití volání rozhraní API pro použití s aplikací student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ec752cbadfac7a47e08ed7b0ffe8bb475969fac5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343249"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-application"></a>Postup přidání volání rozhraní API k aplikaci konverzace student

Tento kurz ukazuje, jak přidat volání rozhraní API do vaší aplikace. Volání rozhraní API je funkce, které definují a zápisu v robota, a který můžete volat student konverzace.

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna robota "tutorialAPICalls.ts".

    npm run tutorial-api-calls

## <a name="details"></a>Podrobnosti

- Volání rozhraní API můžete číst a manipulaci s entity.
- Volání rozhraní API mít přístup k objektu správce paměti.
- Volání rozhraní API můžete taky využít argumenty – to umožňuje znovu pomocí stejné volání rozhraní API slouží k jiným účelům.

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu aplikací webového uživatelského rozhraní klikněte na kurz-12-APICalls. 

### <a name="entities"></a>Entity

V aplikaci označuje jsme definovali jednu entitu.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Volání rozhraní API
Kód pro volání rozhraní API je definován v této souboru: C:\<installedpath\>\src\demos\tutorialAPICalls.ts.

![](../media/tutorial12_apicalls.PNG)

- První zpětné volání rozhraní API je RandomGreeting. Vrátí náhodné pozdravu definované v proměnné pozdrav.
- Zpětné volání rozhraní API násobení: ho bude násobení dvě číslice zadané uživatelem. Pak vrátí výsledek násobení dvou čísel. Ukazuje to, že zpětná volání rozhraní API může trvat vstupy. Všimněte si, že správce paměti je první argument. 
- Zpětné volání rozhraní API ClearEntities: Vymaže číslo entity, aby mohl uživatel zadá další číslo. To ukazuje, jak volání rozhraní API můžete upravit entity.

### <a name="actions"></a>Akce

Vytvořili jsme čtyř akcí. 

![](../media/tutorial12_actions.PNG)

- Kromě, jaké číslo chcete vynásobit 12?" Existují tři různé volání rozhraní API, které ilustrují typické vzory volání rozhraní API, která je communicative akce

- RandomGreeting: je akce bez čekání. Nastavit to, v dialogovém okně Vytvořit akce, jsme vybrali API_LOCAL typ akce a potom vybrané RandomGreeting. 

![](../media/tutorial12_setupapicall.PNG)

Tlačítko Aktualizovat vedle rozhraní API se používá, pokud nám chcete zastavit robota a proveďte požadované změny k rozhraním API. Kliknutím na Aktualizovat by vybrat nejnovější změny.

Zde je, jak jsme vytvořili násobení akce: Po výběru API_Local a rozhraní API, jsme zadané entity ($number) pro první vstupní hodnotu (num1string) a hodnotu (12) pro druhý vstupní hodnoty (num2string). To poskytuje úroveň dereference mezi robota a volá rozhraní API, tak, aby stejné zpětného volání lze mapovat na několik akcí v systému a liší se v tom, jak jsou přiřazeny určité akce.

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>Dialogové okno Train

Projděme vyučující dialogové okno.

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
1. Zadejte "hi".
2. Klikněte na tlačítko akce skóre.
3. Kliknutím vyberte RandomGreeting. Tato funkce spustí volání rozhraní API náhodných pozdrav.
3. Kliknutím vyberte, jaké číslo chcete vynásobit 12?"
4. Zadejte "8". Klikněte na tlačítko akce skóre.
4. Vyberte, vynásobte $number 12'. Poznámka: výsledkem násobení.
5. Vyberte, zrušte entity'.
    - Všimněte si, že byl vymazán hodnota číslo entity.
3. Kliknutím vyberte, jaké číslo chcete vynásobit 12?"
4. Klikněte na tlačítko Hotovo testování.

![](../media/tutorial12_dialog.PNG)

Nyní jste viděli postup registrace zpětných volání rozhraní API, jejich běžných vzorů a jak definovat argumenty a přiřadit hodnoty a entity v nich.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Karty část 1](./13-cards-1.md)
