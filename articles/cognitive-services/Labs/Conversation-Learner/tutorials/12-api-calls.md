---
title: Jak používat rozhraní API použijte volání s modelem konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití volání rozhraní API pro použití s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: d16f36a70ac176a895d9ba44d42d3fae5730f7ea
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173260"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Jak přidat volání rozhraní API do modelu Learner konverzace

Tento kurz ukazuje, jak přidat volání rozhraní API do modelu. Volání rozhraní API jsou funkce, které definují a zápis svého robota, a které můžete volat Learner konverzace.

## <a name="video"></a>Video

[![Kurz 12 ve verzi Preview](http://aka.ms/cl-tutorial-12-preview)](http://aka.ms/blis-tutorial-12)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna bot "tutorialAPICalls.ts".

    npm run tutorial-api-calls

## <a name="details"></a>Podrobnosti

- Volání rozhraní API může číst a manipulaci s entitami.
- Volání rozhraní API mají přístup k objektu správce paměti.
- Volání rozhraní API můžete taky využít argumenty – to umožňuje znovu použít stejné volání rozhraní API slouží k jiným účelům.

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu modelu ve webovém uživatelském rozhraní kliknutím na kurz-12-APICalls. 

### <a name="entities"></a>Entity

V modelu volá číslo jsme definovali jednu entitu.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Volání rozhraní API
Kód pro volání rozhraní API je definován v tomto souboru: C:\<installedpath\>\src\demos\tutorialAPICalls.ts.

![](../media/tutorial12_apicalls.PNG)

- První zpětné volání rozhraní API je RandomGreeting. Vrátí náhodné pozdrav definované proměnné pozdrav.
- Zpětné volání rozhraní API vynásobit: ho bude Násobení dvou čísel je uživatel poskytl. Pak vrátí výsledek násobení dvou čísel. Ukazuje to, že zpětná volání rozhraní API může přijímat vstupy. Všimněte si, že tento správce paměti je první argument. 
- Zpětné volání rozhraní API ClearEntities: Vymaže číselná entita, která má uživatelům povolit, zadejte následující číslo. To ukazuje, jak se volání rozhraní API můžete pracovat s entitami.

### <a name="actions"></a>Akce

Vytvořili jsme čtyři akce. 

![](../media/tutorial12_actions.PNG)

- Kromě "jaké číslo chcete vynásobit 12?" Existují tři různé volání rozhraní API, které znázorňují typické způsoby volání rozhraní API, která je communicative akce

- RandomGreeting: je akce bez čekání. Nastavit to, v dialogovém okně Vytvořit akci, jsme vybrali typ API_LOCAL akce a potom vybrané RandomGreeting. 

![](../media/tutorial12_setupapicall.PNG)

Tlačítko Aktualizovat vedle rozhraní API se používá, pokud bychom měli zastavit robota a proveďte požadované změny k rozhraním API. Kliknutím na Aktualizovat bude pokračovat nejnovější změny.

Zde je, jak jsme vytvořili vynásobit akce: Po výběru API_Local a rozhraní API, jsme zadali entity ($number) pro první vstupní hodnotu (num1string) a hodnotu (12) druhé vstupní hodnoty (num2string). To poskytuje určitou úroveň dereference mezi robota a volá rozhraní API tak, aby stejné zpětného volání lze namapovat na několik akcí v systému a liší se v jak akce, které jsou přiřazeny.

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>Dialogové okno trénování

Projděme si dialogové okno výuky.

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
1. Zadejte "hi".
2. Klikněte na akci skóre.
3. Kliknutím vyberete RandomGreeting. Tato funkce spustí volání rozhraní API náhodné pozdrav.
3. Kliknutím vyberte "jaké číslo chcete vynásobit 12?"
4. Zadejte "8". Klikněte na tlačítko akce skóre.
4. Vyberte "násobení $number 12'. Všimněte si výsledek násobení.
5. Vyberte "Clear entity".
    - `number` Hodnota entity se vymazala.
3. Kliknutím vyberte "jaké číslo chcete vynásobit 12?"
4. Klikněte na tlačítko Hotovo testování.

![](../media/tutorial12_dialog.PNG)

Nyní jste viděli postup registrace zpětných volání rozhraní API, jejich běžných vzorů a tom, jak definovat argumenty a přidružit hodnoty a entity v nich.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [1. část karty](./13-cards-1.md)
