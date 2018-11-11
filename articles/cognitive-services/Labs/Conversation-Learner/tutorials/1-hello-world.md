---
title: Jak vytvořit model "Hello World" konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak vytvořit model Learner konverzace "Hello World".
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 271141f24ff729fc99210af67ad769a5ef83a65c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242717"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Jak vytvořit model "Hello World" pomocí Learner konverzace

Tento kurz ukazuje, jak začít pracovat s Learner konverzace, včetně vytváření akcí, interaktivně vyučují a provádění oprav zaznamenané dialogová okna koncovým uživatelům.

## <a name="video"></a>Video

[![Tutoriál 1 ve verzi Preview](https://aka.ms/cl-tutorial-01-preview)](https://aka.ms/blis-tutorial-01)


## <a name="requirements"></a>Požadavky
Pokud jste tak dosud neučinili, nejdřív ověřte všechny instalační kroky nejsou dokončeny, včetně vytváření `.env` soubor vaše služba luis vytváření klíč.  Zobrazit [rychlý Start](https://github.com/Microsoft/ConversationLearner-Samples) podrobnosti.

Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="steps"></a>Kroky

Začněte na domovskou stránku webového uživatelského rozhraní.

### <a name="create-the-model"></a>Vytvoření modelu
1. Klikněte na nový Model
2. Do pole Název zadejte Hello World
3. Kliknutí na Vytvořit

### <a name="create-an-action"></a>Vytvoření akce

1. Klikněte na Hello World modelu jej spustit
2. Klikněte na tlačítko akce a potom novou akci
    - Akce může být textová zpráva, která vrací Learner konverzace na uživatele, volání rozhraní API nebo karty.
3. V odpovědi zadejte "Hello World!"
    - Jedná se o odpověď, která vrátí robota
4. Kliknutí na Vytvořit

Vytvoříte nejprve robot dokáže to znamená vrácení text odpovědi.

### <a name="train-the-bot"></a>Natrénování robota

#### <a name="create-the-first-dialog"></a>Vytvoření prvního dialogového okna

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování
2. Zadejte příklad co uživatel se dozvíte v účelem úspěchu při žebrání konverzace, například "hello".
3. Klikněte na výsledek akce
4. Vyberte "Hello World!"
    - Tím se vytvoří dialogové okno vypnout jeden příklad. 
2. Zadejte "goodbye"
3. Klikněte na výsledek akce
4. Klikněte na tlačítko Přidat akci, a pak zadejte "Goodbye"! v odpovědi pak klikněte na tlačítko 'Vytvořit'
5. Klikněte na Hotovo výuky. To se ukončí tento dialog školení.

Nyní máte jeden výuky dialogového okna v systému.

#### <a name="continue-teaching-the-bot"></a>Pokračovat výuce, robota
Pojďme provést jeden další školení a naleznete v tématu Jak robota reaguje.

1. Kliknutím na dialogové okno Nový trénování
2. Zadejte "Dobrý den existuje.
    - Je to podobné jako první dialogového okna, a Očekáváme, že k získání dobré skóre z robota.
2. Klikněte na akci skóre
    - Pozice a skóre pravděpodobně dosud dostatečně přesný a bude vyžadovat další výuky.
3. Klikněte na Vybrat vedle "Hello World!"
4. Zadejte "bye"
5. Klikněte na výsledek akce
6. Vyberte "Goodbye"!
7. Klikněte na Hotovo výuky

![](../media/tutorial1_actions.PNG)

Budeme provádět jiná relace výuky zobrazíte fungování robota.

Opakujte předchozí postup pomocí "hi" a "byebye" a Všimněte si změny v pozici a skóre odpovědi robotů, po kliknutí na skóre akce.

Můžete teď zopakujte kroky použitím "Hele" a "good bye" a Všimněte si, že vyhodnocování ukazuje zdokonalení skóre označující robota zaznamenala tato interakce.

![](../media/tutorial1_dialogs.PNG)

### <a name="test-the-bot-as-an-end-user"></a>Testování robota jako koncový uživatel

1. Klikněte na protokol dialogová okna, dialogové okno pak nový protokol
2. Zadejte "Ahoj"
3. Potom "bye"

Můžete také zkusit spustit konverzace s "bye" a poznamenejte si bodu robotů také odpovědi.

### <a name="view-conversations-in-the-log-dialogs"></a>Zobrazení konverzace v protokolu dialogová okna

V dialogových oknech protokolu, se zobrazí v seznamu konverzací, aktualizovat a uložit interakce jako dialogová okna školení. Postup:

1. Klikněte na protokol konverzaci
2. Pokud se do konverzace vypadá v pořádku, klikněte na poslední akci. např "Goodbye".
3. Kliknutím vyberte navržené odpovědi. 
    - Můžete také vybrat nebo přidat další akce.
4. Pak klikněte na Hotovo a uložit jako dialogové okno školení.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Počkejte a bez čekání akce](./2-wait-vs-nonwait-actions.md)