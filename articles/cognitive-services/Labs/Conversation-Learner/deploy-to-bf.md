---
title: Jak nasadit Conversation Learner robot-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Přečtěte si, jak nasadit Conversation Learner robot.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 05fd83506aac26df33f18bec83dcadac8dee2d90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705288"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Nasazení Conversation Learner bot

Tento dokument vysvětluje, jak nasadit Conversation Learner robota – buď místně, nebo do Azure.

## <a name="prerequisite-determine-the-model-id"></a>Předpoklad: určení ID modelu 

Pokud chcete spustit robota mimo Conversation Learner uživatelského rozhraní, musíte nastavit ID modelu Conversation Learner, které robot bude používat – tzn. ID modelu Machine Learning v cloudu Conversation Learner.  (Naopak při spuštění robota přes Conversation Learner uživatelské rozhraní zvolí uživatelské rozhraní, které ID modelu.)  

Tady je postup, jak získat ID modelu:

1. Zahajte robot a Conversation Learner uživatelské rozhraní.  Úplné pokyny najdete v průvodci rychlým startem. sumarizace:

    V jednom příkazovém okně:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    V okně příkazového řádku jiném

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Otevřít prohlížeč pro`http://localhost:5050` 

3. Klikněte na model Conversation Learner, pro který chcete získat ID.

4. Na navigačním panelu vlevo klikněte na nastavení.

5. V horní části stránky se zobrazí identifikátor GUID "ID modelu".

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Možnost 1: Nasazení Conversation Learner robota pro místní spuštění

Nasadí robot na váš místní počítač a ukáže, jak k němu přistupovat pomocí emulátoru rozhraní bot Framework.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Konfigurace robota pro přístup mimo Conversation Learner uživatelského rozhraní

Pokud lokálně spustíte robota, přidejte ID aplikace do `.env` souboru robota:

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

Pak začněte s robotem:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Robot je teď spuštěný místně.  K němu máte přístup pomocí emulátoru bot Framework.

### <a name="download-and-install-the-emulator"></a>Stažení a instalace emulátoru

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Připojte emulátor k robotovi

1. V levém horním rohu emulátoru zadejte `http://127.0.0.1:3978/api/messages`do pole "zadejte adresu URL koncového bodu".  Ostatní pole ponechte prázdné a klikněte na připojit.

2. Nyní se konverzující s robotem.

## <a name="option-2-deploy-to-azure"></a>Možnost 2: Nasazení do Azure

Publikujte Conversation Learner robot podobným způsobem, jako byste publikovali ostatní roboty. Na vysoké úrovni nahrajete kód na hostovaný web, nastavíte příslušné konfigurační hodnoty a pak zaregistrujte robota s různými kanály. Podrobné pokyny najdete v tomto videu, které ukazují, jak pomocí Azure Bot Service publikovat robota.

Jakmile se robot Nasadí a spustí, můžete k němu připojit různé kanály, jako je Facebook, týmy, Skype atd. použití registrace kanálu Azure bot. Dokumentaci k tomuto procesu najdete v těchto tématech: https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration

Níže najdete podrobné pokyny pro nasazení Conversation Learner bot do Azure.  Tyto pokyny předpokládají, že váš zdroj robota je k dispozici z cloudového zdroje, jako je Azure DevOps Services, GitHub, BitBucket nebo OneDrive, a nakonfiguruje robota pro průběžné nasazování.

1. Přihlaste se k Azure Portal v https://portal.azure.com

2. Vytvoří nový prostředek webové aplikace bot. 

    1. Pojmenování robota
    2. Klikněte na Šablona robota, zvolte Node. js, zvolte základní a pak klikněte na tlačítko vybrat.
    3. Pokud chcete vytvořit robota webové aplikace, klikněte na vytvořit.
    4. Počkejte, než se vytvoří prostředek robota webové aplikace.

3. V Azure Portal upravte prostředek robota webové aplikace, který jste právě vytvořili.

   1. Klikněte na položku navigace nastavení aplikace na levé straně.
   1. Posuňte se dolů k části nastavení aplikace.
   2. Přidejte tato nastavení:

       Proměnná prostředí | value
       --- | --- 
       CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
       CONVERSATION_LEARNER_MODEL_ID      | Identifikátor GUID ID aplikace získaný z Conversation Learner uživatelského rozhraní pod položkou nastavení pro model >
       LUIS_AUTHORING_KEY               | LUIS vytváření klíčů pro tento model
       LUIS_SUBSCRIPTION_KEY            | Nepožadováno, ale doporučuje se pro publikovaná roboty, aby nedocházelo k použití kvóty vytváření obsahu.
    
   4. V horní části stránky klikněte na Uložit.
   5. Otevřít položku pro sestavení navigace vlevo
   6. Klikněte na konfigurovat průběžné nasazování. 
   7. Klikněte na ikonu "nastavení" v části nasazení.
   8. Klikněte na požadovaná nastavení.
   9. Vyberte zdroj, kde je k dispozici váš robotový kód, a nakonfigurujte zdroj.
