---
title: Jak používat volání rozhraní API s modelem Conversation Learner Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat volání rozhraní API s modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 261536932cc82a28ad4ee3ffc3575ea41fe9ec5b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703915"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Postup přidání volání rozhraní API do modelu Conversation Learner

V tomto kurzu se dozvíte, jak do modelu přidat volání rozhraní API. Volání rozhraní API jsou funkce, které definujete a píšete v robotu a které Conversation Learner mohou volat.

## <a name="video"></a>Video

[![Kurz volání rozhraní API ve verzi Preview](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěný robot "tutorialAPICalls. TS".

    npm run tutorial-api-calls

## <a name="details"></a>Podrobnosti

- Volání rozhraní API mohou číst a manipulovat s entitami.
- Volání rozhraní API mají přístup k objektu správce paměti.
- Volání rozhraní API mohou přebírat argumenty.

### <a name="open-the-demo"></a>Otevřete ukázku.

Ve webovém uživatelském rozhraní klikněte na "Import kurzů" a vyberte model s názvem "tutorial-14-APICalls".

### <a name="entities"></a>Entity

V modelu s názvem `number`jsme definovali jednu entitu.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Volání rozhraní API
Kód pro volání rozhraní API je definován v tomto souboru: `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- Zpětné volání vrátí náhodný pozdrav definovaný `greeting` v poli. `RandomGreeting`
- `Multiply` Zpětné volání vynásobí dvě čísla předaná akcí, která ji volá a vrátí výsledek, který lze vykreslit v uživatelském rozhraní.
    - Všimněte si, že je správce paměti prvním argumentem. 
    - Všimněte si, že zpětná volání rozhraní API mohou v tomto případě `num1string` a `num2string`využít více vstupů.
- `ClearEntities` Zpětné volání vymaže entitu číslo, aby uživatel mohl zadat jiné číslo. 
    - Ukazuje, jak můžou volání rozhraní API manipulovat s entitami.

### <a name="actions"></a>Akce
Vytvořili jsme čtyři akce. Tři z nich jsou akce rozhraní API bez čekání, čtvrtá je akce "text", která uživateli požádá o dotaz podobný tomu, co jsme viděli v jiných kurzech. Pokud chcete zjistit, jak se každý vytvořil, udělejte toto:
1. Na levém panelu klikněte na akce a potom klikněte na jednu ze čtyř akcí uvedených v mřížce.
2. Všimněte si hodnot každého pole ve formuláři, které se objeví.
3. Všimněte si `Refresh` tlačítka vedle pole rozhraní API.
    - Pokud jsme rádi zastavili a provedli změnu v rozhraních API, když je stránka uživatelského rozhraní zapnutá, můžete kliknutím `Refresh` na tlačítko Vybrat poslední změny.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, násobení a RandomGreeting
Všechny tři tyto akce jsou typu rozhraní API. Každý z nich se spoléhá na funkce zpětného volání rozhraní API k provedení určité práce a případně k vrácení hodnoty, která má být prezentována uživateli.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>"Jaké číslo chcete vynásobit 12"
Jedná se o akci "text", která jednoduše požádá o otázku uživatele. I když tato akce ve skutečnosti nekomunikuje s jedním z zpětných volání rozhraní API, vyzve uživatele, aby reagoval na číslo, které bude přecházet do paměti entity, kterou lze potom použít pomocí akce vynásobení, která používá jedno z zpětných volání rozhraní API.


### <a name="train-dialog"></a>Dialogové okno výuka

Pojďme si projít dialog "školení".

1. Na levém panelu klikněte na `Train Dialogs`tlačítko a `New Train Dialog` pak na tlačítko.
2. Zadejte "Hello".
3. Klikněte na tlačítko `Score Actions`.
4. Vyberte `RandomGreeting`. 
    - Tím se spustí volání rozhraní API náhodného pozdravu.
    - Tato akce nečeká na reakci uživatele.
5. Vyberte `What number to do you want to multiply by 12?`
6. Zadejte číslo, libovolné číslo a pouze číslo.
    - Všimněte si, že vaše číslo bylo automaticky označeno `number` jako entita.
7. Klikněte na tlačítko `Score Actions`.
8. `Multiply` Vyberte akci.
    - Všimněte si výsledku násobení 15.12.00.
    - Všimněte si, že paměť stále obsahuje hodnotu, kterou jste zadali pro`number`
9. `ClearEntities` Vyberte akci.
    - Všimněte si, že hodnota entity `number` pro byla vymazána z paměti.
10. Klikněte na tlačítko `Save`.

Nyní jste viděli, jak registrovat zpětná volání rozhraní API, jejich společné vzory a jak definovat argumenty a přidružit hodnoty a entity v nich.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Karty – část 1](./15-cards.md)
