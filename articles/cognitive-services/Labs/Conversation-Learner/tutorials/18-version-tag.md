---
title: Jak používat označování verzí s modelem Conversation Learner – Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat správu verzí a označování pomocí Conversation Learnerho modelu.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 0279363c039e3ec3c2deac3bc7f71c32c547e9d1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703380"
---
# <a name="how-to-use-version-tagging"></a>Jak používat označování verzí

Tento kurz ukazuje, jak označit verze modelu Conversation Learner a nastavit, která verze je "Live".  

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby se při vytváření dialogových oken protokolu, nikoli webového uživatelského rozhraní dialogového okna protokolu, používal emulátor rozhraní bot.  

Tento kurz vyžaduje, aby byl spuštěný robot pro obecné kurzy:

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Označené verze modelu jsou statické; nemůžete je upravovat ani měnit. Při úpravách modelu vždy upravujete hlavní verzi. Když přidáte novou značku, Conversation Learner zachycuje snímek modelu v daném časovém okamžiku. 

Vaše robot bude používat verzi modelu, kterou jste vybrali jako "živou" verzi, ale všechny konverzace, které mají, budou viditelné pouze v případě, že je "značka úprav" nastavena na "Master". Pokud je vlastnost "Upravit značku" modelu nastavena na jinou hodnotu než "Master", pak můžete zobrazit snímek modelu, ale nemůžete ho nijak změnit.

## <a name="steps"></a>Kroky

### <a name="install-the-bot-framework-emulator"></a>Instalace emulátoru rozhraní bot Framework

1. Přejděte do [ (Nastavení)https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) (Integrace a služby).
2. Stáhněte a nainstalujte emulátor.

### <a name="create-a-model"></a>Vytvoření modelu

1. Na domovské stránce Seznam modelů klikněte `New Model` na tlačítko.
2. `Name` V poli Typ pole "kurz – 18 – Správa verzí" stiskněte ENTER.
4. Na levém panelu klikněte na nastavení.
5. Zkopírujte obsah pole CONVERSATION_LEARNER_MODEL_ID do schránky.

### <a name="configure-the-emulator"></a>Konfigurace emulátoru

1. V kořenové složce Conversation Learner otevřete soubor ". env".
2. Přidejte řádek do souboru. env jako:
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. Restartujte službu Conversation Learner ukončením z příkazového řádku a znovu spusťte:
    - `npm run tutorial-general`
4. V emulátoru robot Framework vytvořte novou konfiguraci robota, nastavte adresu URL koncového bodu na.`http://localhost:3978/api/messages`

### <a name="version-1"></a>Verze 1

Vytvoříme jednu akci pro verzi 1.

1. Na levém panelu webového uživatelského rozhraní klikněte na akce a potom klikněte na `New Action` tlačítko.
2. V poli "odpověď robota" zadejte "Dobrý den (verze 1)".
3. Klikněte na tlačítko `Save`.

Nyní tuto možnost označíte jako "verzi 1" modelu.

1. Na levém panelu klikněte na nastavení a potom kliknutím na ![](../media/tutorial18_version_tags.PNG)ikonu `New Tag` značky verze zobrazíte tlačítko, na které byste měli kliknout.
    - Pojmenujte ho "verze 1"
1. V rozevíracím seznamu "živá značka" vyberte "verze 1".  
    - Kanály s využitím tohoto robota teď použijí verzi 1 našeho modelu.
    - Entity, akce a výukové dialogy tohoto modelu verze 1 se už nedají změnit.
    - Pokud jako značku úpravy vyberete "verze 1", budete moct zobrazit jenom model a neupravovat ho.
    - Ponechte "Upravit značku" nastavenou na "Master", jedná se o jedinou verzi modelu, kterou lze upravovat.

Nyní se v mřížce "značky verze" zobrazí zpráva "verze 1".

### <a name="version-2"></a>Verze 2

Nyní upravíte náš model pro odlišení od verze 1.

1. Na levém panelu klikněte na akce.
2. V mřížce akce klikněte na "Dobrý den (verze 1)".
3. Změňte pole "odpověď robota" na "Dobrý den" (verze 2).
4. Klikněte na tlačítko `Save`.
5. Klikněte na tlačítko `New Action`.
6. V poli "odpověď robota" zadejte "Bye Bye (verze 2)".

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Potvrďte, že emulátor rozhraní robot Framework používá verzi 1.

1. V emulátoru robot Framework zadejte zprávu "Hey tam".
2. Všimněte si, že robot odpoví na "Dobrý den (verze 1)".
    - Tím se ověří, že verze 1 je "Live".

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Zobrazení protokolů konverzace v Conversation Learner webové uživatelské rozhraní

1. Na levém panelu klikněte na dialogová okna protokolu.
    - Pokud nevidíte žádná dialogová okna, klikněte na tlačítko Aktualizovat.
2. Všimněte si značky "verze 1" v mřížce.
3. V mřížce klikněte na "Dobrý den (verze 1)"

> [!NOTE]
> Opravy můžeme udělat na základě všech aktuálně dostupných Conversation Learner funkcí, ale tyto úpravy se provedou v hlavní verzi a nikoli na verzi 1.

Nyní jste viděli, jak funguje Správa verzí a jak můžete s robotem pracovat pomocí emulátoru bot Framework.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vyčíslení entit a nastavení akcí entit](./tutorial-enum-set-entity.md)
