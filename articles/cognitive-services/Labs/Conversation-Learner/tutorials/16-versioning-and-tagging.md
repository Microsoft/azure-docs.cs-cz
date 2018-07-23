---
title: Použití správy verzí a označování pomocí modelu konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití správy verzí a označování s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c7f23d989cbfa0ece9e404a0fe0feb68cf5fddb2
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170541"
---
# <a name="how-to-use-versioning-and-tagging"></a>Použití správy verzí a označování

Tento kurz ukazuje, jak označit verze modelu Learner konverzace a nastavit verzi je "živé".  

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje použití emulátoru bot k vytvoření protokolu dialogová okna, ne protokolu dialogové okno webového uživatelského rozhraní.  

Tento kurz vyžaduje, zda je spuštěna obecné kurz bot:

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Při úpravách, vždy upravujete značky nazývá "hlavní"--označené verzí můžete vytvořit z hlavní větve (která v podstatě pořízení snímku hlavní větve), ale nemůžou upravovat označené verzí.

## <a name="steps"></a>Kroky

### <a name="install-the-bot-framework-emulator"></a>Nainstalujte si emulátor Bot framework

- Přejděte do [ (Nastavení)https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) (Integrace a služby).
- Stáhněte si a nainstalujte si emulátor.

### <a name="create-an-model"></a>Vytvoření modelu

1. Klikněte na nový Model
2. Do pole Název zadejte kurzu – 16 – Správa verzí
3. Kliknutí na Vytvořit 
4. Klikněte na tlačítko Nastavení
5. Zkopírujte ID modelu

### <a name="configure-the-emulator"></a>Konfigurace v emulátoru

- V kořenové složce konverzace Learner otevření souboru .env.
- Vložit jako hodnota CONVERSATION_LEARNER_MODEL_ID ID modelu
- Restartujte službu konverzace Learner ukončení z příkazového řádku a opětovné spuštění:
 
    Spustit kurz obecné npm 

### <a name="actions"></a>Akce

Pojďme vytvořit akci:

1. Přepnout do webového uživatelského rozhraní.
1. Klikněte na tlačítko akce a potom novou akci.
2. V odpovědi, zadejte "Dobrý den zde (verze 1)".
3. Klikněte na Uložit.


![](../media/tutorial16_action1.PNG)

Vytvořte novou značku:

3. Klikněte na "nastavení" a vytvořte novou "značku".
    - Říkat "verze 1"
4. Nastavení "verze 1" na "živé".  
    - Účinek nastavení značku živé "verze 1" je, že bude používat kanály pomocí této robota "1" značka verze.
    - Označený verzí modelů nejsou ovlivněny úpravy (změny akce, entity, přidání trénování dialogy).  
    - Ve značce "hlavní" jsou vždy provedeny změny na model (Změna akce, entity, přidání trénování dialogy).  Jinými slovy "hlavní" je pouze značka, která lze změnit. snímky jsou opraveny jiné značky.
    - Protokolovat dialogová okna v uživatelském rozhraní Learner konverzace vždy použijte master (ne za značku).

![](../media/tutorial16_v1_create.PNG)

V nastavení se vytvořila verze:

![](../media/tutorial16_settings.PNG)

Přidejme druhou akci:

1. Klikněte na tlačítko akce a potom novou akci.
2. V odpovědi zadejte "bye bye (verze 2)".

Upravte první akci:

1. Klikněte na tlačítko akce.
2. V části Akce, klikněte na "Dobrý den zde (verze 1)".
3. Změnit odpověď na "Dobrý den zde (verze 2)".

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>Přepnout na emulátor robota

1. Bot uživatelského rozhraní zadejte "goodbye".
2. Robot odpoví "Dobrý den zde (verze 1)".
    - Ukazuje to, že je "živé" verze 1. 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>Přepnout do webového uživatelského rozhraní

1. Klikněte na protokol dialogová okna (Pokud se nezobrazí všechna dialogová okna, klikněte na tlačítko Aktualizovat).
2. Klikněte na "Dobrý den zde (verze 2)"

> [!NOTE]
> Můžeme provádět opravy výběrem ze všech aktuálně dostupné akce. Tyto úpravy se pošle na hlavní server.

Viděli jste nyní fungování správy verzí a jak můžete pracovat s robotem pomocí emulátoru služby Bot framework.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ukázka – resetování hesla](./demo-password-reset.md)
