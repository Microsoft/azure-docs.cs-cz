---
title: Jak vytvořit aplikaci "Hello World" konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Zjistěte, jak vytvořit aplikaci student konverzace "Hello World".
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 30026285ac6dda45d2f5e3718aae741b928cf242
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343270"
---
# <a name="how-to-create-a-hello-world-application-with-conversation-learner"></a>Jak vytvořit aplikaci "Hello World" s konverzace student

Tento kurz ukazuje, jak začít pracovat s student konverzace, včetně vytváření akce, výuky interaktivně a provádění oprav zaznamenané dialogů koncovým uživatelům.

## <a name="requirements"></a>Požadavky
Pokud jste to ještě neudělali, nejdříve se ujistěte, byly dokončeny všechny instalační kroky, včetně vytváření `.env` soubor s vaší LEOŠ vytváření klíč.  V tématu [rychlý Start](https://github.com/Microsoft/ConversationLearner-Samples) podrobnosti.

Tento kurz vyžaduje, aby obecné kurz robota běží

    npm run tutorial-general

## <a name="steps"></a>Kroky

Spusťte na domovskou stránku ve webovém uživatelském rozhraní.

### <a name="create-the-app"></a>Vytvoření aplikace
1. Klikněte na nové aplikace
2. V poli Název zadejte Hello World
3. Kliknutí na Vytvořit

### <a name="create-an-action"></a>Vytvoří akci

1. Klikněte na aplikace Hello World se ho spustit
2. Klikněte na tlačítko akce, pak nová akce
    - Akce může být textová zpráva, která konverzace student vrací uživatele, volání rozhraní API nebo na kartě.
3. V odpovědi zadejte "Hello, World!"
    - Jedná se o odpověď, který vrátí robota
4. Kliknutí na Vytvořit

Jste vytvořili první věc, kterou robota provést tj vrátí text odpovědi.

### <a name="train-the-bot"></a>Cvičení robota

#### <a name="create-the-first-dialog"></a>První dialogové okno vytvořit

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový Train
2. Zadejte příklad co uživatel se dozvíte v účelem úspěchu při žebrání konverzace, například "hello".
3. Klikněte na tlačítko akce skóre
4. Vyberte "Hello, World!"
    - Tím se vytvoří jedna zapnout příklad dialogové okno. 
2. Zadejte "goodbye"
3. Klikněte na tlačítko akce skóre
4. Klikněte na tlačítko Přidat akci a potom zadejte "Goodbye!. v odpovědi pak klikněte na 'Vytvořit'
5. Klikněte na tlačítko Hotovo vyučující. To se ukončí toto dialogové okno školení.

Nyní máte jeden dialogové okno vyučující v systému.

#### <a name="continue-teaching-the-bot"></a>Pokračovat výuky robota
Pojďme neprovádějte jeden další školení a jak bude reagovat robota.

1. Kliknutím na dialogové okno Nový Train
2. Zadejte "hi existuje.
    - Toto je podobná první dialogové okno a Očekáváme, že k získání dobrý skóre z robota.
2. Klikněte na tlačítko akce skóre
    - Pozice a skóre nemusí stále být dostatečně přesné a vyžadovat další vyučující.
3. Klikněte na Vybrat vedle "Hello, World!"
4. Pak zadejte "bye"
5. Klikněte na tlačítko akce skóre
6. Vyberte "Goodbye!.
7. Klikněte na tlačítko Hotovo vyučující

![](../media/tutorial1_actions.PNG)

Uděláme jiná relace vyučující jak funguje robota.

Zopakujte výše uvedené kroky, pomocí "hi" a "byebye a poznamenejte si změny v pozici a skóre odpovědi robotů po kliknutí na tlačítko akce skóre.

Teď můžete opakováním kroků pomocí "Hele" a "good bye" a Všimněte si, že má vyhodnocování ukazuje vylepšení v skóre označující robota naučili tato interakce.

![](../media/tutorial1_dialogs.PNG)

### <a name="test-the-bot-as-an-end-user"></a>Testovací robota jako koncový uživatel

1. Klikněte na tlačítko protokolu dialogová okna, dialogové okno pak nový protokolu
2. Typ 'text hello.
3. Pak 'bye

Taky můžete pokusit konverzace s 'bye"a poznamenejte si odpovědi robota.

### <a name="view-conversations-in-the-log-dialogs"></a>Zobrazení konverzací v dialogových oknech protokolu

V dialogových oknech protokolu, můžete zobrazit v seznamu konverzace, aktualizovat a uložit interakce jako školení dialogová okna. Postup:

1. Klikněte na protokol konverzace
2. Pokud konverzace spokojeni, klikněte na poslední akci. např "Goodbye".
3. Kliknutím vyberte navrhované odpovědi. 
    - Můžete také vybrat nebo přidat další akci.
4. Klikněte na tlačítko Hotovo ji uložit jako dialogové okno školení.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Počkejte a bez čekání akce](./2-wait-vs-nonwait-actions.md)