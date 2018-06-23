---
title: Použití alternativní vstupy s konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat alternativní vstupy s student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2214436b193932e5b3b80c190f7754a0436b7ed8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343277"
---
# <a name="how-to-use-alternative-inputs"></a>Použití alternativní vstupy

Tento kurz ukazuje, jak použít pole "alternativní vstupy" na vstup uživatele v vyučující rozhraní.

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby obecné kurz robota běží

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
"Alternativní vstupy" jsou utterances alternativní uživatele, které uživatel může mít uvedená na určitém místě v dialogovém okně školení. Alternativní vstupy umožňují určit více kompaktně variace co uživatel říci, aniž by museli seznamu každý variace v dialogu samostatný školení.

## <a name="steps"></a>Kroky

### <a name="create-the-application"></a>Vytvoření aplikace

1. Ve webovém uživatelském rozhraní klikněte na nové aplikace
2. Do pole Název zadejte AlternativeInputs. Klikněte na tlačítko vytvořit.

### <a name="create-an-entity"></a>Vytvořte entitu

1. Klikněte na tlačítko entity, pak novou entitu.
2. Název Entity zadejte města.
3. Klikněte na Vytvořit.

### <a name="create-three-actions"></a>Vytvořte tři akce

1. Klikněte na tlačítko akce, pak nová akce
2. V odpovědi zadejte, které města chcete?".
3. Vyřazení entity zadejte $city.
3. Kliknutí na Vytvořit

Pak vytvořte druhou akci:

1. Klikněte na tlačítko akce, pak nová akce.
3. V odpovědi zadejte, počasí v $city je pravděpodobně slunečného'.
4. Požadované entity, zadejte $city.
4. Klikněte na Vytvořit.

Vytvořte třetí akce:

1. Klikněte na tlačítko akce, pak nová akce.
3. V odpovědi typ, zkuste žádostí o počasí'.
    - To by být v odpovědi na otázky uživatele, jako "co můžete systému?"
4. Vyřazení entity zadejte $city.
4. Kliknutí na Vytvořit

Nyní máte tři akce.

### <a name="train-the-bot"></a>Cvičení robota

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
2. Zadejte, co je počasí'.
3. Klikněte na tlačítko akce skóre a vyberte, které města chcete?"
2. Zadejte 'Ostravy'.
3. Dvakrát klikněte na 'Ostravy' a vyberte města.
    - To označí jako entita města.
5. Klikněte na tlačítko akce skóre
    - Všimněte si, že je teď k dispozici v entitě města Ostravy. 
6. Vyberte, počasí v $city je pravděpodobně slunečného'.
7. Klikněte na tlačítko Hotovo vyučující.

Přidejte další dialog příklad:

1. Klikněte na tlačítko Nová akce a potom dialogové okno Nový vlaku.
2. Zadejte, co můžete dělat?".
3. Klikněte na tlačítko akce skóre a vyberte, zkuste žádostí o počasí.
2. Zadejte, co je počasí v Praze'.
3. Dvakrát klikněte na "seattle" a vyberte města.
    - To označí jako entita města.
5. Klikněte na tlačítko akce skóre
    - Všimněte si, že je teď k dispozici v entitě města seattle. 
6. Vyberte, počasí v $city je pravděpodobně slunečného'.
7. Klikněte na tlačítko Hotovo vyučující.

Podívejme se, co se stane, když uživatel zvolí podobný sémanticky výše:

1. Klikněte na tlačítko Nová akce a potom dialogové okno Nový vlaku.
2. Zadejte "Nápověda".
3. Klikněte na tlačítko akce skóre.
    - Všimněte si, že skóre pro dva potenciální odpovědi jsou velmi zavřít. Tato hodnota informuje nás, že je model nerozumíte o hranice mezi dvě akce.
6. Klikněte na tlačítko Abandon výukových a potvrďte.

![](../media/tutorial8_closescores.png)

V takovém případě by pomoci přidat alternativní vstupů pro dialogová okna. Můžete je přidat jako vedete výuku. Můžete také přejít zpět a přidat později.

2. Klikněte na "Co můžete dělat?" v dialogových oknech vlaku.
2. V dialogovém okně klikněte na "co můžete dělat?" Vyberte.
    1. V pravém podokně v části Entity detekce zadejte ve vstupu alternativní přidat pár alternativy:
    1. Zadejte, co jsou moje volby?"
    2. Zadejte, chci se dozvědět Moje volby'.
    3. Zadejte, pomůže.
    1. Klikněte na tlačítko Odeslat změny.


![](../media/tutorial8_helpalternates.png)

2. Nyní klikněte na 'co je počasí v Praze'.
    1. Přidat alternativní vstup zadejte "prognózy pro seattle".
    2. Dvakrát klikněte na "seattle" a vyberte města. Všimněte si, že by měl být k dispozici a mají stejnou sadu entit entity pro alternativní vstupy. Je v pořádku Pokud obsah entit se liší.
    3. Přidat alternativní vstup zadejte, bude ho rain dnes v liberci'.
    4. Klikněte na 'Ostravy' a vyberte města.
    5. Klikněte na tlačítko Odeslat změny a provést.


Umožňuje přidat alternativní vstupy do první dialogového okna:

1. Klikněte na tlačítko Train dialogová okna.
2. Klikněte v dialogovém okně počínaje, co je počasí'.
2. Klikněte v levém podokně vyberte "co je počasí":
    1. Přidat alternativní vstup zadejte 'předpověď počasí'.
    2. Zadejte, bude ho rain?"
    3. Klikněte na tlačítko Odeslat změny.
4. Klikněte v levém podokně vyberte 'Ostravy':
    1. Přidat alternativní vstup zadejte "pro Ostravy'.
    2. Zadejte 'prognózy pro Austinu'.
        - Všimněte si, že úplné fráze je označen. Klikněte na frázi a červený symbol x. Poté vyberte Austinu a klikněte na města.
        - Klikněte na tlačítko Odeslat změny
    1. Klikněte na tlačítko Hotovo, což způsobí, že model přeučování.

![](../media/tutorial8_altcities.png)

Nyní si vyzkoušíte variace:

1. Kliknutím na dialogové okno Nový vlaku.
2. Zadejte, co jste si možnosti'.
3. Klikněte na tlačítko akce skóre.
    - Upozorňujeme, že skóre jsou teď Další klíčovou na další akci, která určuje jistoty modelu.
2. Vyberte, zkuste žádostí o počasí'.
6. Klikněte na tlačítko Hotovo vyučující

Nyní máte zaznamenané jak alternativní vstupy slouží k označení jiných věcí, uživatel může mít uvedená. Mohou pomoci vyhnout se vytváření spoustu dialogů, které jsou v mnoha směrech jsou stejné, tak, že je sloučeny do jednoho dialogové okno a výčet, co uživatel moct.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Dialogová okna protokolu](./9-log-dialogs.md)
