---
title: Jak pomocí označování verze modelu Learner konverzace – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití správy verzí a označování s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 99b4712373423d30dc83408cc80eccc93b342c10
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55205996"
---
# <a name="how-to-use-version-tagging"></a>Jak používat označování verzí

Tento kurz ukazuje, jak označit verze modelu Learner konverzace a nastavit verzi je "živé".  

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje použití emulátoru Bot Framework k vytvoření protokolu dialogová okna, ne protokolu dialogové okno webového uživatelského rozhraní.  

Tento kurz vyžaduje, zda je spuštěna obecné kurzu Bot:

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Označený verzí modelu jsou statické; se nedají upravit ani změnit. Při úpravě modelu, který upravujete vždy hlavní verze. Když přidáte novou značku, konverzace Learner zaznamená snímek modelu v tomto okamžiku v čase. 

Váš robot používat verzi, kterou jste vybrali jako "Živé" verze modelu, ale všechny konverzace, které je bude možné zobrazit pouze v případě, že "úpravy" je značka nastavená na "Master". Pokud je vlastnost "Úpravy značka" modelu nastavena na jinou hodnotu než "Hlavní", pak se zobrazí snímek modelu, ale už ho nemůžete změnit žádným způsobem.

## <a name="steps"></a>Kroky

### <a name="install-the-bot-framework-emulator"></a>Nainstalujte si emulátor Bot Framework

1. Přejděte do [ (Nastavení)https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) (Integrace a služby).
2. Stáhněte si a nainstalujte si emulátor.

### <a name="create-a-model"></a>Vytvoření modelu

1. Z modelu domovské stránce seznamu, klikněte `New Model` tlačítko.
2. V `Name` typ, pole "Kurzu-18-Versioning", stiskněte enter.
4. Na levém panelu klikněte na "Nastavení".
5. Zkopírujte obsah pole CONVERSATION_LEARNER_MODEL_ID do schránky.

### <a name="configure-the-emulator"></a>Konfigurace v emulátoru

1. V kořenové složce konverzace Learner otevřete soubor ".env".
2. Přidá řádek do souboru ".env" tímto způsobem:
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. Restartujte službu konverzace Learner ukončení z příkazového řádku a opětovné spuštění:
    - `npm run tutorial-general`
4. V emulátoru Bot Framework vytvořit novou konfiguraci robota, nastavte adresu URL koncového bodu `http://localhost:3978/api/messages`

### <a name="version-1"></a>Verze 1

Vytvoříme jednu akci pro verze 1.

1. Na levém panelu webového uživatelského rozhraní, klikněte na tlačítko "Akce" a pak klikněte na tlačítko `New Action` tlačítko.
2. V odpovědi bodu robotů také"" zadejte "Dobrý den zde (verze 1)".
3. Klikněte na tlačítko `Save`.

Nyní jsme budete označit jako "Verze 1" z modelu.

4. Na levém panelu klikněte na "nastavení" a potom klikněte na ![](../media/tutorial18_version_tags.PNG)"Verze Tags" ikonu zobrazíte `New Tag` tlačítko, které by měl kliknete.
    - Pojmenujte ho "Verze 1"
4. V "Live značka" rozevírací nabídka vyberte "verze 1".  
    - Kanály pomocí této robota nyní použije "Verze 1" z našeho modelu.
    - Entit, akce a dialogová okna trénování modelu tato verze 1 můžete již změnit.
    - Pokud vyberete možnost "Verze 1" jako "Úpravy značky" bude pouze zobrazení modelu a nelze ji upravit.
    - Nechte "úpravy značka" nastavená na "Master", je jedinou verzí modelu, které lze upravovat.

"Verze 1" se teď zobrazí v mřížce "Tags verze".

### <a name="version-2"></a>Verze 2

Nyní upravíme náš Model, aby se odlišil od verze 1.

1. V levém panelu klikněte na "Akce".
2. V mřížce akce klikněte na "Dobrý den zde (verze 1)".
3. Změna "bodu robotů také odpověď" pole "Dobrý den zde (verze 2)".
4. Klikněte na tlačítko `Save`.
5. Klikněte na tlačítko `New Action`.
6. Pole v "bodu robotů také odpovědi" typ "bye bye (verze 2)".

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Potvrďte, že je emulátor Bot Framework pomocí verze 1

1. V emulátoru Bot Framework zadejte zprávu "Hey existuje".
2. Všimněte si, že odpoví Bot "Dobrý den zde (verze 1)".
    - Ověří, že je "živé" verze 1.

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Zobrazit protokoly konverzace v konverzaci Learner webového uživatelského rozhraní

1. V levém panelu klikněte na "Protokolu dialogů"
    - Pokud se nezobrazí všechna dialogová okna, klikněte na tlačítko Aktualizovat.
2. Všimněte si, že značka "Verze 1" v mřížce.
3. V mřížce, klikněte na "Dobrý den zde (verze 1)"

> [!NOTE]
> Můžeme provádět opravy výběrem ze všech aktuálně k dispozici funkce Learner konverzace, ale provedeny tyto změny do hlavní větve a verze 1.

Viděli jste nyní fungování správy verzí a jak můžete pracovat s robotem pomocí emulátoru služby Bot Framework.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ukázka – resetování hesla](./demo-password-reset.md)
