---
title: Protokolování dialogová okna v aplikaci konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Naučte se přihlásit k aplikaci konverzace student dialogová okna.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 477545c48aeca05d56fdae28ac65a8f381a482fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343275"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-application"></a>K přihlášení k aplikaci konverzace student dialogová okna

Tento kurz ukazuje, jak provést testování v rámci rozhraní konverzace student; koncového uživatele jak se protokolují dialogová okna; a jak můžete opravit protokolovat dialogová okna za účelem zlepšení modelu.

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby obecné kurz robota běží

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
V dialogových oknech protokolu můžete zkontrolovat a provést opravy prováděné s koncovými uživateli, dialogová okna.  Konkrétně můžete je vyřešit popisky entity a výběr akce ke zlepšení výkonu trained model a celého systému. 

## <a name="steps"></a>Kroky

### <a name="create-the-application"></a>Vytvoření aplikace

1. Ve webovém uživatelském rozhraní klikněte na nové aplikace
2. Do pole Název zadejte LogDialogs. Klikněte na tlačítko vytvořit.

### <a name="create-an-entity"></a>Vytvořte entitu

1. Klikněte na tlačítko entity, pak novou entitu.
2. Název Entity zadejte města.
3. Klikněte na Vytvořit.

### <a name="create-two-actions"></a>Vytvořte dvě akce

1. Klikněte na tlačítko akce, pak nová akce
2. V odpovědi zadejte, které města?".
3. Vyřazení entity zadejte $city.
3. Kliknutí na Vytvořit

Pak vytvořte druhou akci:

1. Klikněte na tlačítko akce, pak nová akce.
3. V odpovědi zadejte, počasí v $city je pravděpodobně slunečného'.
4. Požadované entity, zadejte $city.
4. Klikněte na Vytvořit.

Nyní máte dvě akce.

### <a name="train-the-bot"></a>Cvičení robota

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
2. Zadejte, co je počasí'.
3. Klikněte na tlačítko akce skóre a vyberte, které města?"
2. Zadejte "Seattle".
3. Dvakrát klikněte na "Seattle" a vyberte města.
    - To označí jako entita města.
5. Klikněte na tlačítko akce skóre
6. Vyberte, počasí v $city je pravděpodobně slunečného'.
7. Klikněte na tlačítko Hotovo vyučující.

Přidejte další dialog příklad:

1. Klikněte na tlačítko Nová akce a potom dialogové okno Nový vlaku.
2. Zadejte, co je počasí v Praze?". Všimněte si, že Praha se označí jako entity.
5. Klikněte na tlačítko akce skóre 
6. Vyberte, počasí v $city je pravděpodobně slunečného'.
7. Klikněte na tlačítko Hotovo vyučující.

### <a name="try-the-bot-as-the-user"></a>Zkuste robota jako uživatel
Pojďme Představte si, že jsme tento robota nasadili pro uživatele.

1. Klikněte na protokol dialogová okna.
2. Klikněte na tlačítko Nová relace konverzace.
    - To představuje robota jako uživatel by prostředí v ovládacím prvku webového chatu na levé straně uživatelského rozhraní. Prázdný oblast na pravé straně, můžete ignorovat.
3. Zadejte text "hello".
4. Odpověď robota:, které města?"
4. Typ 'Boston'.
5. Odpověď robota:, které města?"
    - To asi není pravé. To umožňuje uložit toto dialogové okno.
2. Klikněte na tlačítko Hotovo testování.

Začněme novou relaci:

2. Klikněte na tlačítko Nová relace konverzace.
3. Typ 'prognózy pro Boston'.
4. Odpověď robota:, které města?"
2. Klikněte na tlačítko Hotovo vyučující.

Teď umožňuje provést opravy druhé dialogové okno:

1. Klikněte na 'prognózy pro Boston' v dialogových oknech v protokolu.
    - Otevře se konverzace.
    - Pokud kliknete na straně uživatele konverzace (sem na 'prognózy pro Boston'), můžete změnit popisky entity.
    - Pokud kliknete na straně systému (sem na které města), akci, která je vybraná, můžete změnit.
5. Klikněte na 'prognózy pro Boston'. 
    - Hlavní příčinou je, že nebyla Boston označené jako entity. Chcete-li změnit, potřebujeme.
    - Dvakrát klikněte na 'Boston, a potom vyberte města.
    - Klikněte na tlačítko Odeslat změny a klikněte na tlačítko Uložit. Tato akce vytvoří dialogové okno školení na základě změn, které jste provedli a vyřadit je do školení dialogová okna v místě změny, které jste provedli.
6. Vyberte, počasí v $city je pravděpodobně slunečného."
7. Klikněte na tlačítko Hotovo vyučující. Pokud teď přejdete do vlaku v dialogových oknech, zobrazí se, že byla přidána nová akce.

![](../media/tutorial9_logdiag1.PNG)

Teď umožňuje provést opravy jiné dialogové okno:

1. Klikněte na "hello" v dialogových oknech v protokolu.
    - Otevře se konverzace.
3. Poznámka: odpověď na "hello" je které města. Ale chceme, změnit na něco, co vám víc vyhovuje. Lepší odpověď by být něco jako 'hello, jsem robota počasí'. Ale neexistuje žádná akce, který nemá, abychom měli k jeho vytvoření.
4. Klikněte na akci.
    - V odpovědi, zadejte "jsem počasí robota. Můžete zvýšit prognózy. "
6. Zrušte zaškrtnutí čekání odpovědi políčko aby akce bez čekání.
7. Klikněte na Vytvořit.
8. Klikněte na tlačítko vybrat tuto novou akci. Potom klikněte na Uložit.
    - To přináší je zpět do bodu v relaci školení.
6. Kliknutím vyberte, které města?"
7. Typ v 'Boston'. Dvakrát klikněte na značku Boston jako entity není-li již.
8. Klikněte na tlačítko akce skóre.
9. Kliknutím vyberte, počasí v $city je pravděpodobně slunečného."
10. Klikněte na tlačítko Hotovo vyučující.

![](../media/tutorial9_addnewaction.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zpětné volání detekce entity](./10-entity-detection-callback.md)
