---
title: Postup nasazení konverzace student robota - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informace o nasazení robota student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 77cc998227d996a6e52b1b5629204da5dc735ede
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343224"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Postup nasazení konverzace student robota

Tento dokument vysvětluje, jak nasadit konverzace student robota – buď místně, nebo do Azure.

## <a name="prerequisite-determine-the-application-id"></a>Předpoklad: Určete ID aplikace 

Pokud chcete spustit robota mimo rozhraní student konverzace, musíte nastavit konverzace student ID aplikace, které budou používat robota – tj ID model strojového učení v cloudu student konverzace.  (Oproti tomu při spuštění robota prostřednictvím uživatelského rozhraní student konverzace, uživatelské rozhraní zvolí které ID aplikace).  

Chcete-li získat číslo ID aplikace:

1. Spuštění uživatelského rozhraní student konverzace a vaše robota.  Naleznete v Průvodci rychlý start pro úplné pokyny; Shrnutí:

    V okně jednoho příkazu:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    V jiném příkazové okno

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Otevřete prohlížeč http://localhost:5050 

3. Klikněte na konverzace student aplikace, které chcete získat ID

4. Klikněte na "Nastavení" v navigačního panelu na levé straně.

5. Identifikátor GUID "ID aplikace" se zobrazí v horní části stránky.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Možnost 1: Nasazení konverzace student robota spustit místně

To nasadí robotu do místního počítače a ukazuje, jak můžete přistupovat pomocí emulátoru robota Framework.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Konfigurace vaší robota pro přístup mimo rozhraní student konverzace

Při místním spuštění robotu, přidat do robota ID aplikace `.env` souboru:

    ```
    CONVERSATION_LEARNER_APP_ID=<YOUR_APP_ID>
    ```

Spusťte vaší robota:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Robota je nyní spuštěna místně.  Můžete k němu přístup pomocí emulátoru robota Framework.

### <a name="download-and-install-the-emulator"></a>Stáhněte a nainstalujte si emulátor

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Připojení k vaší robota emulátoru

1. V levém horním emulátoru, v poli "zadejte adresu URL vašeho koncového bodu", zadejte `http://127.0.0.1:3978/api/messages`.  Ostatní pole ponechte prázdné a klikněte na tlačítko "Připojit".

2. Jsou nyní konverzaci s vaší robota.

## <a name="option-2-deploy-to-azure"></a>Možnost 2: Nasazení do Azure

Publikujte vaší konverzace student robota podobná stejným způsobem jako jiné robota bude publikována. Na vysoké úrovni nahrát kód na hostované web, nastavte hodnoty odpovídající konfiguraci a potom proveďte registraci robota s různými kanály. Podrobné pokyny naleznete v tomto videu znázorňující postup publikování vašeho robota pomocí služby Azure robota.

Jakmile je nasazený robota a je spuštěna různé kanály k nim mohla připojit jako je Facebook, týmů, Skype atd pomocí Azure robota kanálu registrace. Dokumentaci najdete v tématu proces: https://docs.microsoft.com/en-us/bot-framework/bot-service-quickstart-registration

Níže jsou uvedeny podrobné pokyny pro nasazení student robota konverzace do Azure.  Tyto pokyny předpokládají, že váš zdroj robota je k dispozici z cloudové zdroje například služby VSTS, Githubu, BitBucket nebo OneDrive a nakonfiguruje vaše robota pro průběžné nasazování.

1. Přihlaste se k portálu Azure v https://portal.azure.com

2. Vytvoření nového prostředku "Robota webové aplikace" 

    1. Pojmenujte robota
    2. Klikněte na "Robota šablona", zvolte "Node.js", zvolte "Základní" a pak klikněte na tlačítko "Vyberte"
    3. Klikněte na "vytvořit" k vytvoření robota aplikaci Web.
    4. Počkejte, než pro webové aplikace robota prostředek má být vytvořen.

3. Na portálu Azure upravte zdroj robota webové aplikace, který jste právě vytvořili.

    1. Kliknutím na položku "Nastavení aplikace" navigaci vlevo
    1. Přejděte do části "Nastavení aplikace."
    2. Přidejte tato nastavení:

        Proměnná prostředí | hodnota
        --- | --- 
        CONVERSATION_LEARNER_SERVICE_URI | https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/
        CONVERSATION_LEARNER_APP_ID      | Identifikátor GUID Id aplikace, získané z uživatelského rozhraní student konverzace v části "nastavení" aplikace >
        LUIS_AUTHORING_KEY               | LEOŠ vytváření klíč pro tuto aplikaci.
    
    4. V horní části stránky klikněte na "Uložit"
    5. Otevřete položky "Sestavení" navigaci vlevo
    6. Klikněte na "Konfigurace průběžné nasazování" 
    7. Klikněte na ikonu "Instalaci" v části nasazení
    8. Klikněte na "Požadované nastavení"
    9. Vyberte zdroje, kde je k dispozici robota kódu a nastavit zdroj.
