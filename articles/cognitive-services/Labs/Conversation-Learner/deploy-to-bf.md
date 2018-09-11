---
title: Jak nasadit konverzace Learner bot – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak nasadit robota Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 83e1fbfeee75534757dcb3c0275ca881e1eea517
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297991"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Jak nasadit robota Learner konverzace

Tento dokument vysvětluje, jak nasadit konverzace Learner bot – buď místně, nebo do Azure.

## <a name="prerequisite-determine-the-model-id"></a>Předpoklad: určení ID modelu 

Spustit mimo rozhraní Learner konverzace robota, musíte nastavit konverzace Learner ID modelu, který bude používat robota – to znamená ID modelu strojového učení v cloudu Learner konverzace.  (Oproti tomu při spuštění robota prostřednictvím uživatelského rozhraní Learner konverzace, uživatelské rozhraní zvolí které ID modelu.).  

Tady je postup získání ID modelu:

1. Spuštění vašeho robota a uživatelského rozhraní Learner konverzace.  Zobrazit úvodní příručky pro úplné pokyny; Shrnutí:

    V okně jednoho příkazu:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    V příkazovém okně jiného

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Otevřít v prohlížeči na http://localhost:5050 

3. Klikněte na modelu konverzace Learner budete chtít získat ID

4. Na navigačním panelu na levé straně klikněte na "Nastavení".

5. Identifikátor GUID "ID modelu" se zobrazí v horní části stránky.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Možnost 1: Nasazení konverzace Learner bot spustit místně

To nasadí robota do místního počítače a ukazuje, jak k němu máte přístup pomocí emulátoru služby Bot Framework.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Konfigurace vašeho robota pro přístup mimo rozhraní Learner konverzace

Při místním spuštění robota, přidat ID aplikace do bodu robotů také `.env` souboru:

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

Potom spusťte váš robot:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Robot je nyní spuštěna místně.  Můžete k němu přístup s emulátorem Bot Framework.

### <a name="download-and-install-the-emulator"></a>Stáhněte si a nainstalujte si emulátor

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Pro svého robota připojit emulátoru

1. V levém horním rohu emulátor v poli "Zadejte vaši adresu URL koncového bodu", zadejte `http://127.0.0.1:3978/api/messages`.  Ostatní pole nechte prázdné a klikněte na tlačítko "Připojit".

2. Jsou nyní rozhovory s vaším robotem.

## <a name="option-2-deploy-to-azure"></a>Možnost 2: Nasazení do Azure

Publikování podobné stejným způsobem, jakým bude publikována libovolnému robotu konverzace Learner robota. Na vysoké úrovni Nahrajte svůj kód na hostovaný web, nastavte odpovídající konfiguraci hodnoty a pak zaregistrujte robot s různými kanály. Podrobné pokyny jsou toto video ukazuje, jak publikovat robotů pomocí služeb Azure Bot Service.

Po nasazení robota a spuštěná, můžete různé kanály k němu můžete připojit například Facebook, Teams, Skype atd pomocí Azure Bot kanálu registrace. Dokumentaci najdete v tématu proces: https://docs.microsoft.com/en-us/bot-framework/bot-service-quickstart-registration

Níže jsou uvedeny podrobné pokyny pro nasazení do Azure Bot Learner konverzace.  Tyto pokyny předpokládají, že váš robot zdroj je k dispozici z cloudové zdroje, jako Azure DevOps Services, GitHub, BitBucket nebo OneDrive a nakonfiguruje svého robota pro průběžné nasazování.

1. Přihlaste se na webu Azure portal https://portal.azure.com

2. Vytvořit nový prostředek "Web App Bot" 

    1. Název robota
    2. Klikněte na "Bot Template", zvolte "Node.js", zvolte možnost "Základní" a potom klikněte na tlačítko "Vyberte"
    3. Klikněte na "vytvořit", chcete-li vytvořit Web App Bot.
    4. Počkejte Web App Bot prostředku.

3. Na webu Azure Portal upravte prostředek Web App Bot, kterou jste právě vytvořili.

    1. Klikněte na "Nastavení aplikace" navigační prvek na levé straně
    1. Posuňte se dolů do části "Nastavení aplikace"
    2. Přidejte tato nastavení:

        Proměnná prostředí | hodnota
        --- | --- 
        CONVERSATION_LEARNER_SERVICE_URI | https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/
        CONVERSATION_LEARNER_MODEL_ID      | Aplikace identifikátor GUID získaný v uživatelském rozhraní Learner konverzace v části "nastavení" pro model >
        LUIS_AUTHORING_KEY               | Vytváření klíč pro tento model LUIS
    
    4. V horní části stránky klikněte na "uložit.
    5. Otevřít navigační prvek "Sestavení" na levé straně
    6. Klikněte na "Konfigurace průběžného nasazování" 
    7. Klikněte na ikonu "Nastavení" v části nasazení
    8. Klikněte na "Required nastavení"
    9. Vyberte zdroj, kde je k dispozici bot kódu a konfigurace zdroje.
