---
title: Jak používat správu verzí a označování s aplikací konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Další informace o použití správy verzí a označování s aplikací student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ea013db078ff33f8597b0e15a8fc951e8ae320e8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343251"
---
# <a name="how-to-use-versioning-and-tagging"></a>Jak používat správu verzí a označování

Tento kurz ukazuje, jak označit verzích aplikace student konverzace a nastavte která verze je "živé".  

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje použití emulátoru robota k vytvoření dialogová okna protokolu, není protokol dialogové okno webového uživatelského rozhraní.  

Tento kurz vyžaduje, aby obecné kurz robota běží:

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Při úpravě, jsou vždy úpravy značky názvem "hlavní" – s příznakem verze můžete vytvořit z hlavního serveru (který je v podstatě pořízení snímku hlavní), ale s příznakem verze nelze upravit.

## <a name="steps"></a>Kroky

### <a name="install-the-bot-framework-emulator"></a>Nainstalujte si emulátor robota framework

- Přejděte do [ (Nastavení)https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) (Integrace a služby).
- Stáhněte a nainstalujte si emulátor.

### <a name="create-an-app"></a>Vytvoření aplikace

1. Klikněte na nové aplikace
2. V poli Název zadejte kurzu-16 – Správa verzí
3. Kliknutí na Vytvořit 
4. Klikněte na tlačítko Nastavení
5. Zkopírujte ID aplikace

### <a name="configure-the-emulator"></a>Konfigurace v emulátoru

- V kořenové složce konverzace student otevřete soubor .env.
- Vložit jako hodnota CONVERSATION_LEARNER_APP_ID ID aplikace
- Spusťte znovu službu konverzace student ukončení z příkazového řádku a znovu spustit:
 
    npm spustit kurzu – obecné 

### <a name="actions"></a>Akce

Umožňuje vytvořit akce:

1. Umožňuje přepnout do webového uživatelského rozhraní.
1. Klikněte na tlačítko akce, pak nová akce.
2. V odpovědi, zadejte "hi zde (verze 1)".
3. Klikněte na tlačítko Uložit.


![](../media/tutorial16_action1.PNG)

Vytvořte novou značku:

3. Klikněte na "nastavení" a vytvořte novou "značku".
    - Volání "verze 1"
4. Nastavte "verze 1" na "živé".  
    - Účinek nastavení značce za provozu, aby "verze 1" je, že bude používat kanály pomocí této robota "verze 1" značky.
    - S příznakem verze aplikací nejsou ovlivněny úpravy (Změna akce entity, přidání dialogová okna train).  
    - Ve značce "hlavní" budou vždy provádět úpravy aplikace (Změna akce entity, přidání dialogová okna train).  Jinými slovy "hlavní" je pouze značky, které můžete změnit; snímky jsou opraveny jiné značky.
    - Přihlaste se dialogová okna v uživatelském rozhraní student konverzace vždy použijte master (ne za provozu značky).

![](../media/tutorial16_v1_create.PNG)

Poznámka: verze byla vytvořena v nastavení:

![](../media/tutorial16_settings.PNG)

Umožňuje přidat druhou akci:

1. Klikněte na tlačítko akce, pak nová akce.
2. V odpovědi zadejte "bye bye (verze 2)".

Upravte první akcí:

1. Klikněte na tlačítko akce.
2. V části Akce, klikněte na "hi zde (verze 1)".
3. Změnit odpověď na "hi zde (verze 2)".

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>Přepnout na emulátoru robota

1. Robota uživatelského rozhraní zadejte "goodbye".
2. Poznámka: odpoví robota "hi zde (verze 1)".
    - Ukazuje to, že je verze 1 "živé". 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>Přepněte do webového uživatelského rozhraní

1. Klikněte na tlačítko dialogová okna protokolu (Pokud nevidíte všechny dialogy aktualizovat aplikaci).
2. Klikněte na "hi zde (verze 2)"

Všimněte si, že bychom mohli opravy, zvolíte všechny aktuálně dostupné akce. Tyto úpravy budou provedeny je hlavní server.

Viděli jste nyní, jak funguje správa verzí, a jak může komunikovat s robota pomocí emulátoru framework robota.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Demo - resetování hesla](./demo-password-reset.md)
