---
title: Jak se připojit dialogová okna v modelu konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak protokolovat dialogová okna v modelu Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 6ceeb9683a979256a8a52347fc74ab758fd1d348
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171431"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Jak se připojit dialogová okna v modelu Learner konverzace

Tento kurz ukazuje, jak provést testování v rámci rozhraní konverzace Learner; koncového uživatele jak se protokolují dialogová okna; úspěšné a jak provádět opravy dialogová okna za účelem zlepšení vašeho modelu.

## <a name="video"></a>Video

[![Kurz 9 Preview](http://aka.ms/cl-tutorial-09-preview)](http://aka.ms/blis-tutorial-09)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Dialogová okna protokolu můžete použít ke kontrole a provádět opravy pro dialogová okna regulovat s koncovými uživateli.  Konkrétně můžete vyřešit entitu popisky a výběr akce pro zlepšení výkonu trénovaného modelu a celého systému. 

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. V webového uživatelského rozhraní klikněte na nový Model
2. Do pole Název zadejte LogDialogs. Pak klikněte na tlačítko vytvořit.

### <a name="create-an-entity"></a>Vytvoření entity

1. Klikněte na entity a pak novou entitu.
2. V názvu Entity zadejte město.
3. Klikněte na Vytvořit.

### <a name="create-two-actions"></a>Vytvořte dvě akce

1. Klikněte na tlačítko akce a potom novou akci
2. V odpovědi zadejte "Které Město?".
3. Vyřazení entity zadejte $city.
3. Kliknutí na Vytvořit

Vytvořte druhou akci:

1. Klikněte na tlačítko akce a potom novou akci.
3. V odpovědi zadejte "o počasí v $city je pravděpodobně hezky".
4. Zadejte požadované entity $city.
4. Klikněte na Vytvořit.

Teď máte dvě akce.

### <a name="train-the-bot"></a>Natrénování robota

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
2. Zadejte "co je počasí".
3. Klikněte na tlačítko akce skóre a vyberte "Které city?"
2. Zadejte "Seattle".
3. Dvakrát klikněte na "Seattle" a vyberte město.
    - To označuje jako město entity.
5. Klikněte na výsledek akce
6. Vyberte "o počasí v $city je pravděpodobně hezky".
7. Klikněte na Hotovo výuky.

Přidáte jiný příklad – dialogové okno:

1. Klikněte na tlačítko Nová akce a potom dialogové okno Nový trénování.
2. Zadejte "co je o počasí v Praze?". Všimněte si, že Praha je označený jako entity.
5. Klikněte na výsledek akce 
6. Vyberte "o počasí v $city je pravděpodobně hezky".
7. Klikněte na Hotovo výuky.

### <a name="try-the-bot-as-the-user"></a>Zkuste robota jako uživatel
Představte si, že jsme tento robot nasadili pro uživatele.

1. Klikněte na protokol dialogová okna.
2. Klikněte na novou relaci konverzace.
    - To představuje robota, protože uživatel by docházet v řízení webového chatu na levé straně uživatelského rozhraní. Ignorovat prázdné oblasti na pravé straně.
3. Zadejte "hello".
4. Odpovědi robotů: "které Město?"
4. Typ "Bostonu".
5. Odpovědi robotů: "které Město?"
    - To vypadá, že nepodporuje správné. Pojďme uložit toto dialogové okno.
2. Klikněte na tlačítko Hotovo testování.

Pojďme zahájit novou relaci:

2. Klikněte na novou relaci konverzace.
3. Typ "předpovídáním Bostonu".
4. Odpovědi robotů: "které Město?"
2. Klikněte na Hotovo výuky.

Teď vytvoříme opravy druhé dialogové okno:

1. Klikněte na "předpovědi pro Bostonu" v rámci protokolu dialogová okna.
    - Otevře se do konverzace.
    - Pokud kliknete na straně uživatelů konverzace (tady v "předpovědi pro Bostonu"), můžete změnit popisky entity.
    - Pokud kliknete na straně systému (tady v které Město), můžete změnit akci, která je vybrána.
5. Klikněte na "předpovědi pro Bostonu". 
    - Hlavní příčinou je, Boston nebyl označí entitu. Potřebujeme to Změníme.
    - Dvakrát klikněte na "Bostonu" a pak vyberte město.
    - Klikněte na tlačítko Odeslat změny a klikněte na Uložit. To vytvoří dialogové okno školení na základě změn, které jste provedli a vyřadit můžete do dialogových oknech školení Přejme během změny, které jste provedli.
6. Vyberte "o počasí v $city je pravděpodobně hezky."
7. Klikněte na Hotovo výuky. Pokud teď přejdete na dialogová okna trénování, zobrazí se, že je přidána nová akce.

![](../media/tutorial9_logdiag1.PNG)

Teď vytvoříme opravy jiné dialogové okno:

1. Klikněte na "hello" v rámci protokolu dialogová okna.
    - Otevře se do konverzace.
3. Odpověď na zprávu "hello" je které město. Ale chceme změnit na něco, co má větší smysl. Lepší odpověď by něco jako "Dobrý den, jmenuji se počasí bot". Ale neexistuje žádná akce, která provádí, abychom měli k jejímu vytvoření.
4. Klikněte na akci.
    - V odpovědi, zadejte "jsem bot počasí. Já můžeme pomoci s předpovědí. "
6. Zrušte zaškrtnutí políčka čekat na odpověď zaškrtávací políčko k němu akce bez čekání.
7. Klikněte na Vytvořit.
8. Klepněte na tuto novou akci. Potom klikněte na Uložit.
    - To přináší je zpět do tohoto bodu celodenního školení.
6. Kliknutím vyberte "které Město?"
7. Zadejte "Bostonu". Dvakrát klikněte na značku Bostonu jako entity Nepřihlášený.
8. Klikněte na výsledek akce.
9. Kliknutím vyberte "o počasí v $city je pravděpodobně hezky."
10. Klikněte na Hotovo výuky.

![](../media/tutorial9_addnewaction.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zpětné volání zjišťování entit](./10-entity-detection-callback.md)
