---
title: Jak používat rozhraní API volá s modelem konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití volání rozhraní API s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 33869ed71cbcdef454c9fcee8b4e6279ad5dfe05
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796734"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Jak přidat volání rozhraní API do modelu Learner konverzace

Tento kurz ukazuje, jak přidat volání rozhraní API do modelu. Volání rozhraní API jsou funkce, které definují a zápis svého robota, a které můžete volat Learner konverzace.

## <a name="video"></a>Video

[![Kurz Preview volání rozhraní API](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna bot "tutorialAPICalls.ts".

    npm run tutorial-api-calls

## <a name="details"></a>Podrobnosti

- Volání rozhraní API může číst a manipulaci s entitami.
- Volání rozhraní API mají přístup k objektu správce paměti.
- Volání rozhraní API můžete převzít argumenty.

### <a name="open-the-demo"></a>Otevřete ukázku

Ve webovém uživatelském rozhraní klikněte na "Import kurzy" a vyberte model s názvem "Kurzu-14-APICalls".

### <a name="entities"></a>Entity

Jsme definovali jednu entitu v modelu volá `number`.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Volání rozhraní API
Kód pro volání rozhraní API je definován v tomto souboru: `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- `RandomGreeting` Zpětné volání se vrátí náhodné pozdrav definované v `greeting` pole.
- `Multiply` Zpětné volání se vynásobí dvě čísla předaných v akci, která jej volá a vrátí výsledek, který lze vykreslit v uživatelském rozhraní.
    - Všimněte si, že správce paměti je první argument. 
    - Všimněte si, že zpětná volání rozhraní API může trvat více vstupů, v tomto případě `num1string` a `num2string`.
- `ClearEntities` Zpětného volání vymaže číselné Entity, takže uživatel může zadat jinou hodnotu. 
    - Ukazuje, jak se volání rozhraní API můžete pracovat s entitami.

### <a name="actions"></a>Akce
Vytvořili jsme čtyři akce. Tři z nich jsou akce "Non-Wait" rozhraní API, přičemž čtvrtá kategorie je akce "Text", které uživatele vyzve, dotaz podobný co jsme viděli v dalších kurzech. Pokud chcete zobrazit, jak každou vytvořil postupujte takto:
1. Na levém panelu klikněte na tlačítko "Akce" a potom klikněte na jednu ze čtyř akcí, které jsou uvedeny v mřížce.
2. Všimněte si, že hodnoty jednotlivých polí ve formuláři, která se otevře.
3. Všimněte si, že `Refresh` tlačítko vedle pole rozhraní API.
    - Pokud bychom měli zastavit robota a ujistěte se, změnit k rozhraním API, zatímco stránka uživatelského rozhraní je v provozu, pak můžete kliknout `Refresh` tlačítko, aby se získaly nejnovější změny.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>Vynásobit ClearEntities a RandomGreeting
Všechny tři tyto akce jsou typ rozhraní API. Každý spoléhají na funkce zpětného volání rozhraní API a provádět určitou práci a potenciálně vrátit hodnoty, které se budou zobrazovat uživateli.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>"Jaké číslo chcete vynásobit 12"
Jedná se o akci "Text" a to jednoduše zeptá na otázku uživatele. Zatímco tato akce ve skutečnosti nekomunikuje s jedním z zpětná volání rozhraní API, vyzve uživatele reagovat s číslem, která přejde do paměti entita, která je pak možné "Násobení" akcí, které používá jeden z zpětná volání rozhraní API.


### <a name="train-dialog"></a>Dialogové okno trénování

Projděme si "Dialogové okno školení".

1. Na levém panelu klikněte na tlačítko `Train Dialogs`, pak bude `New Train Dialog` tlačítko.
2. Typ "hello".
3. Klikněte na tlačítko `Score Actions`.
4. Vyberte `RandomGreeting`. 
    - Tato funkce spustí volání rozhraní API náhodné pozdrav.
    - NENÍ to čekat na odpověď uživatele.
5. Vyberte `What number to do you want to multiply by 12?`
6. Zadejte číslo, libovolné číslo a pouze čísla.
    - Všimněte si, že své číslo se automaticky přiřadil popisek, jako `number` entity.
7. Klikněte na tlačítko `Score Actions`.
8. Vyberte `Multiply` akce.
    - Všimněte si, že výsledek násobení hodnotou 12.
    - Všimněte si, že tuto paměť stále obsahuje hodnotu jste zadali pro `number`
9. Vyberte `ClearEntities` akce.
    - Všimněte si, že hodnota Entity `number` se vymazala z paměti.
10. Klikněte na tlačítko `Save`.

Nyní jste viděli postup registrace zpětných volání rozhraní API, jejich běžných vzorů a tom, jak definovat argumenty a přidružit hodnoty a entity v nich.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [1. část karty](./15-cards.md)
