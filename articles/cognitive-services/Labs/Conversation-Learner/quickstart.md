---
title: Postup vytvoření Conversation Learner modelu pomocí Node. js – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se vytvářet Conversation Learner model pomocí Node. js.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7ab32fb421a2c0db72652d1bbf12d312bffd5d1e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706536"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Vytvoření Conversation Learnerho modelu pomocí Node. js

Conversation Learner snižuje složitost sestavování roboty. Umožňuje hybridní vývojové pracovní toky, které umožňují ruční psaní kódu a strojového učení, aby snižovaly množství kódu potřebného k zápisu roboty. Některé pevné části modelu, jako je například kontrola, zda je uživatel přihlášen nebo že požadavek rozhraní API na kontrolu inventáře úložiště, lze nadále kódovat. Nicméně další změny stavu a výběru akcí se ale dají získat z ukázkových dialogových oken, které dodávají odborníci v doméně nebo vývojáři.

## <a name="invitation-required"></a>Vyžaduje se Pozvánka

*Pro přístup k projektu Conversation Learner je vyžadována Pozvánka.*

Conversation Learner projektu sestávají ze sady SDK, kterou přidáte do robota, a cloudové služby, ke které SDK přistupuje pro strojové učení.  V současné době přístup ke cloudové službě s produktem konverzace projektu vyžaduje pozvánku.  Pokud jste to ještě nepozvali, [požádejte o pozvánku](https://aka.ms/conversation-learner-request-invite).  Pokud jste pozvánku neobdrželi, nebudete moct získat přístup k cloudovým rozhraním API.

## <a name="prerequisites"></a>Požadavky

- Uzel 8.5.0 nebo vyšší a npm 5.3.0 nebo vyšší. Nainstalovat z [https://nodejs.org](https://nodejs.org).
  
- LUIS vytváření klíčů:

  1. Přihlaste se [https://www.luis.ai](https://www.luis.ai).

  2. V pravém horním rohu klikněte na jméno a pak na nastavení.

  3. Vytváření klíčového obsahu se zobrazí na výsledné stránce.

  (Váš LUIS Authoring Key obsluhuje 2 role.  Nejprve bude sloužit jako klíč pro vytváření Conversation Learner.  Druhý Conversation Learner k extrakci entit používá LUIS; LUIS Authoring Key slouží k vytváření modelů LUIS vaším jménem.)

- Webový prohlížeč Google Chrome. Nainstalovat z [https://www.google.com/chrome/index.html](https://www.google.com/chrome/index.html).

- Git. Nainstalovat z [https://git-scm.com/downloads](https://git-scm.com/downloads).

- VSCode. Nainstalovat z [https://code.visualstudio.com/](https://code.visualstudio.com/). Upozorňujeme, že toto se doporučuje, není to nutné.

## <a name="quick-start"></a>Rychlý start 

1. Nainstalovat a sestavit:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > `npm install`V takovém případě můžete tuto chybu ignorovat, pokud k ní dojde:`gyp ERR! stack Error: Can't find Python executable`

2. Konfigurovat:

   Vytvořte v adresáři `cl-bot-01`soubor `.env` s názvem.  Obsah souboru by měl být:

   ```
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Start bot:

    ```
    npm start
    ```

    Tím se v `cl-bot-01/src/app.ts`nástroji spustí obecná prázdná robota.

3. Otevřít prohlížeč pro`http://localhost:3978`

Nyní používáte Conversation Learner a můžete vytvořit a naučit model Conversation Learner.  

> [!NOTE]
> Při spuštění je Conversation Learner projektu k dispozici prostřednictvím pozvánky.  Pokud `http://localhost:3978/ui` se zobrazí chyba `403` protokolu HTTP, znamená to, že váš účet nebyl přizván.  Požádejte prosím [o pozvání](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Kurzy, ukázky a přepínání mezi roboty

Výše uvedené pokyny zahájily obecnou prázdnou bot.  Pokud místo toho chcete spustit kurz nebo demo bot:

1. Pokud máte otevřené webové uživatelské rozhraní Conversation Learner, vraťte se do seznamu modelů v `http://localhost:3978/ui/home`.
    
2. Pokud je spuštěný jiný robot ( `npm start` například `npm run demo-pizza`nebo), zastavte ho.  Nemusíte zastavovat proces uživatelského rozhraní nebo webový prohlížeč zavřít.

3. Spusťte demo bot z příkazového řádku (krok 2 výše).  K ukázkám patří:

   ```bash
   npm run tutorial-general
   npm run tutorial-entity-detection
   npm run tutorial-session-callbacks
   npm run tutorial-api-calls
   npm run tutorial-hybrid
   npm run demo-password
   npm run demo-pizza
   npm run demo-storage
   ```

4. Pokud jste tak ještě neučinili, přepněte do webového uživatelského rozhraní Conversation Learner v Chrome `http://localhost:3978/ui/home`načtením. 

5. Klikněte na tlačítko "Import kurzů" a v uživatelském rozhraní Conversation Learner vyberte ukázkový model, který odpovídá ukázce, kterou jste spustili.

Zdrojové soubory pro ukázky jsou v`cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Vytvoření robota, který obsahuje kód back-endu

1. Pokud máte otevřené webové uživatelské rozhraní Conversation Learner, vraťte se do seznamu modelů v `http://localhost:3978/ui/home`.
    
2. Pokud je robot spuštěný (jako `npm run demo-pizza`), zastavte ho.  Nemusíte zastavovat proces uživatelského rozhraní nebo webový prohlížeč zavřít.

3. V případě potřeby upravte kód v `cl-bot-01/src/app.ts`.

4. Znovu sestavte a znovu spusťte robot:

    ```bash    
    npm run build
    npm start
    ```

5. Pokud jste tak ještě neučinili, přepněte do webového uživatelského rozhraní Conversation Learner v Chrome `http://localhost:3978/ui/home`načtením. 

6. Vytvořte nový model Conversation Learner v uživatelském rozhraní a začněte s výukou.

7. Chcete-li provést změny `cl-bot-01/src/app.ts`kódu v, opakujte výše uvedené kroky počínaje krokem 2.

## <a name="vscode"></a>VSCode

V VSCode existují konfigurace spuštění pro každou ukázku a pro "prázdné bot" v `cl-bot-01/src/app.ts`.  `cl-bot-01` Otevřete složku v VSCode.

## <a name="advanced-configuration"></a>Pokročilá konfigurace

K dispozici je `.env.example` soubor šablony, který obsahuje proměnné prostředí, které můžete nastavit pro konfiguraci ukázek.

Tyto porty můžete upravit tak, aby nedocházelo ke konfliktům mezi ostatními službami běžícími na `.env` vašem počítači, a to přidáním souboru do kořenového adresáře projektu:

```bash
cp .env.example .env
```

Tato operace používá standardní konfiguraci, která vám umožní spustit robota místně a začít používat Conversation Learner.  (Později pro nasazení robota do rozhraní bot Framework budou potřeba některé úpravy tohoto souboru.)

## <a name="support"></a>Podpora

- Označování otázek na [Stack Overflow](https://stackoverflow.com) s využitím "rozpoznávání Microsoftu"
- Vyžádání funkce na [stránce hlasového uživatele](https://aka.ms/conversation-learner-uservoice)
- Otevření problému v našem [úložišti GitHubu](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Zvaní

Tento projekt přijal [pravidla chování pro Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Další informace najdete v [nejčastějších dotazech k pravidlům chování](https://opensource.microsoft.com/codeofconduct/faq/). S případnými dalšími dotazy nebo připomínkami se obraťte na adresu [opencode@microsoft.com](mailto:opencode@microsoft.com).

## <a name="source-repositories"></a>Zdrojová úložiště

- [conversationlearner – ukázky](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-models](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner – uživatelské rozhraní](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Hello World](./tutorials/01-hello-world.md)
