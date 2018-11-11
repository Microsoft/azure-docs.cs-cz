---
title: Použití alternativní vstupy s konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití alternativních vstupy s Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3b38cc265b4adfb301dc1165e02b17a8aa7c9589
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252543"
---
# <a name="how-to-use-alternative-inputs"></a>Jak používat alternativní vstupy

Tento kurz ukazuje způsob použití pole "alternativní vstupy" pro uživatelský vstup ve výukovému rozhraní.

## <a name="video"></a>Video

[![Kurz 8 Preview](https://aka.ms/cl-tutorial-08-preview)](https://aka.ms/blis-tutorial-08)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
"Alternativní vstupy" jsou alternativní projevy, které uživatel může mít říká, že v určitém místě v dialogovém okně školení. Alternativní vstupy umožňují zadat více kompaktně variace co uživatel se může jednat, aniž byste museli seznamu každou změnu v dialogovém okně samostatné školení.

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. V webového uživatelského rozhraní klikněte na nový Model
2. Do pole Název zadejte AlternativeInputs. Pak klikněte na tlačítko vytvořit.

### <a name="create-an-entity"></a>Vytvoření entity

1. Klikněte na entity a pak novou entitu.
2. V názvu Entity zadejte město.
3. Klikněte na Vytvořit.

### <a name="create-three-actions"></a>Vytvořte tři akce

1. Klikněte na tlačítko akce a potom novou akci
2. V odpovědi zadejte "Město, který chcete?".
3. Vyřazení entity zadejte $city.
3. Kliknutí na Vytvořit

Vytvořte druhou akci:

1. Klikněte na tlačítko akce a potom novou akci.
3. V odpovědi zadejte "o počasí v $city je pravděpodobně hezky".
4. Zadejte požadované entity $city.
4. Klikněte na Vytvořit.

Vytvořte třetí akce:

1. Klikněte na tlačítko akce a potom novou akci.
3. V odpovědi zadejte "Zkuste si vyžádat počasí".
    - To může být odpověď na otázku uživatele, jako "co systému?"
4. Vyřazení entity zadejte $city.
4. Kliknutí na Vytvořit

Teď máte tři akce.

### <a name="train-the-bot"></a>Natrénování robota

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
2. Zadejte "co je počasí".
3. Klikněte na výsledek akce a vyberte "Město, který chcete?"
2. Zadejte "denver".
3. Dvakrát klikněte na "denver" a vyberte město.
    - To označuje jako město entity.
5. Klikněte na výsledek akce
    - "denver" je teď k dispozici v entitě město. 
6. Vyberte "o počasí v $city je pravděpodobně hezky".
7. Klikněte na Hotovo výuky.

Přidáte jiný příklad – dialogové okno:

1. Klikněte na tlačítko Nová akce a potom dialogové okno Nový trénování.
2. Zadejte "co jste?".
3. Klikněte na výsledek akce a vyberte 'Zkuste se zeptat pro počasí.
2. Zadejte "Co je o počasí v Praze".
3. Dvakrát klikněte na "seattle" a vyberte město.
    - To označuje jako město entity.
5. Klikněte na výsledek akce
    - "seattle" je teď k dispozici v entitě město. 
6. Vyberte "o počasí v $city je pravděpodobně hezky".
7. Klikněte na Hotovo výuky.

Podívejme se, co se stane, pokud uživatel řekne něco sémanticky podobné výše:

1. Klikněte na tlačítko Nová akce a potom dialogové okno Nový trénování.
2. Zadejte "Nápověda".
3. Klikněte na výsledek akce.
    - Hodnocení pro dva potenciální odpovědi jsou velmi podobné. Nám říká, že model je ztrácíte přehled o hranici mezi dvě akce.
6. Klikněte na tlačítko zrušení výuce a potvrďte.

![](../media/tutorial8_closescores.png)

V takovém případě by pomohl přidat alternativní vstupy pro dialogová okna. Můžete je přidat jako provádíte výuku. Můžete také přejít zpět a přidat později.

2. Klikněte na "Co jste?" v dialogových oknech trénování.
2. V dialogovém okně klikněte na "co jste?" a vyberte ji.
    1. V pravém podokně v části se zjišťování entit zadejte ve vstupu alternativní přidat několik alternativy:
    1. Zadejte "jaké jsou moje volby?"
    2. Zadejte "Chci Moje možnosti".
    3. Zadejte "Nápověda"
    1. Klikněte na tlačítko Odeslat změny.


![](../media/tutorial8_helpalternates.png)

2. Nyní, klikněte na "co je o počasí v Praze".
    1. Přidat alternativní vstup zadejte "prognózy pro seattle".
    2. Dvakrát klikněte na "seattle" a vyberte město. Entity pro alternativní vstupy by měla být k dispozici a mají stejnou sadu entit. Je v pořádku Pokud obsah entity, které se liší.
    3. Přidat alternativní vstup zadejte "se ho rain dnes v liberci".
    4. Klikněte na "denver" a vyberte město.
    5. Klikněte na tlačítko Odeslat změny a provést.


Přidejme alternativní vstupy do prvního dialogového okna:

1. Klikněte na tlačítko trénování dialogová okna.
2. Klikněte na tlačítko v dialogovém okně počínaje "co je počasí".
2. Klikněte v levém podokně vyberte "co je počasí":
    1. Přidat alternativní vstup zadejte "předpověď počasí".
    2. Zadejte "se ho rain?"
    3. Klikněte na tlačítko Odeslat změny.
4. Klikněte v levém podokně vyberte "denver":
    1. Ve vstupu alternativní přidat zadejte "denver".
    2. Zadejte "předpovídáním austin".
        - Úplné frázi je zvýrazněn. Klikněte na frázi a červený symbol x. Potom vyberte austin a klikněte na město.
        - Klikněte na tlačítko Odeslat změny
    1. Klikněte na Hotovo, což způsobí, že model, který má programovém přeučení.

![](../media/tutorial8_altcities.png)

Pojďme si vyzkoušet změny:

1. Kliknutím na dialogové okno Nový trénování.
2. Zadejte "co jste si možnosti".
3. Klikněte na výsledek akce.
    - Skóre jsou teď rychlejší rozhodování na další akci, která označuje jistoty modelu.
2. Vyberte "Zkuste si vyžádat počasí".
6. Klikněte na Hotovo výuky

Nyní máte zobrazené jak alternativní vstupy lze použít k označení další věci, které uživatel může mít říká, že. Mohou pomoci vyhnout se vytváření mnoho dialogových oken, které v mnoha ohledech jsou stejné, sloučeny do jednoho dialogové okno a výčet, co uživatel můžete říct, že.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Dialogová okna protokolu](./9-log-dialogs.md)
