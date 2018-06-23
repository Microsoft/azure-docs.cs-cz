---
title: Postup vytvoření konverzace student aplikace pomocí Node.js – kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Zjistěte, jak vytvořit aplikaci konverzace student pomocí Node.js.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a3a51aa86a30b060c8dc4113da69462904d7df54
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343599"
---
# <a name="create-a-conversation-learner-application-using-nodejs"></a>Vytvoření aplikace konverzace student pomocí Node.js

Konverzace student snižuje složitost vytváření robotů. Umožňuje hybridní vývoj pracovní postup povolení ručně psané kód a strojového učení ke snížení objemu kód potřebný k zápisu robotů. Určité pevné částí aplikace, jako je kontrola, pokud je uživatel přihlášen, nebo vytváření požadavek rozhraní API ke kontrole skladové zásoby, může být stále programového. Jiné změny ve výběru stavu a akce, ale je možné zjistit z dialogová okna příklad poskytují domény odborníka nebo vývojáře.

## <a name="invitation-required"></a>Pozvánka vyžaduje

*Pozvánka je nutné pro přístup k projektu student konverzace.*

Konverzace student projektu se skládá ze sady SDK, které přidáte do vašeho robota a cloudové služby, který přistupuje k sadě SDK pro machine learning.  V současné době vyžaduje přístup ke cloudové službě projektu konverzace Leaner pozvánku.  Pokud jste to ještě neudělali, pozváni [požadavku Pozvánka](https://aka.ms/conversation-learner-request-invite).  Pokud jste neobdrželi pozvánku, nebude možné získat přístup k rozhraní API cloudu.

## <a name="prerequisites"></a>Požadavky

- Uzel 8.5.0 nebo vyšší a npm 5.3.0 nebo vyšší. Nainstalujte z [ https://nodejs.org ](https://nodejs.org).
  
- Klíč pro tvorbu LEOŠ:

  1. Přihlaste se k [ http://www.luis.ai ](http://www.luis.ai).

  2. Klikněte na název v pravém horním rohu klikněte na "nastavení"

  3. Vytváření klíč je zobrazený na stránce výsledné

  (Váš LEOŠ vytváření klíč slouží 2 role.  Nejprve bude sloužit jako vaší konverzace student vytváření klíč.  Za druhé konverzace student používá LEOŠ pro extrakci entity; LEOŠ vytváření klíč slouží k vytváření modelů LEOŠ vaším jménem)

- Webový prohlížeč Google Chrome. Nainstalujte z [ https://www.google.com/chrome/index.html ](https://www.google.com/chrome/index.html).

- Git. Nainstalujte z [ https://git-scm.com/downloads ](https://git-scm.com/downloads).

- VSCode. Nainstalujte z [ https://code.visualstudio.com/ ](https://code.visualstudio.com/). Poznámka: Toto nastavení se doporučuje, není vyžadována.

## <a name="quick-start"></a>Rychlý start 

1. Instalace a sestavení:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Během `npm install`, tuto chybu můžete ignorovat, pokud k němu dojde: `gyp ERR! stack Error: Can't find Python executable`

2. Konfigurace:

   Vytvořte soubor s názvem `.env` v adresáři `cl-bot-01`.  Obsah souboru by měla být:

   ```
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Spusťte robota:

    ```
    npm start
    ```

    Toto spouští obecné prázdný robota `cl-bot-01/src/app.ts`.

3. Spusťte konverzace student uživatelského rozhraní:

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

4. Otevřete prohlížeč http://localhost:5050 

Teď používáte student konverzace a můžete vytvořit a naučit model student konverzace.  

> [!NOTE]
> Při spuštění je k dispozici ve pozvánku student konverzace projektu.  Pokud http://localhost:5050 ukazuje HTTP `403` chybu, to znamená, váš účet není dostali pozvánku.  Prosím [požadavku Pozvánka](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Kurzy, ukázky a přepínání mezi robotů

Výše uvedené pokyny spustit obecné prázdný robota.  Spustit kurz nebo místo toho ukázku robota:

1. Pokud máte webové konverzace student otevřete uživatelské rozhraní, vrátit do seznamu aplikací na http://localhost:5050/home.
    
2. Pokud je spuštěn jiný robota (jako je `npm start` nebo `npm run demo-pizza`), případně ji zastavte.  Není potřeba zastavit proces uživatelského rozhraní nebo zavřete webový prohlížeč.

3. Spusťte ukázku robota z příkazového řádku (z kroku 2 výše).  Ukázky patří:

  ```bash
  npm run tutorial-general
  npm run tutorial-entity-detection
  npm run tutorial-session-callbacks
  npm run tutorial-api-calls
  npm run demo-password
  npm run demo-pizza
  npm run demo-storage
  npm run demo-vrapp
  ```

4. Pokud ještě nejste, přepněte do konverzace student webového uživatelského rozhraní v prohlížeči Chrome načtením http://localhost:5050/home. 

5. Klikněte na "Import kurzy" (jenom je nutné provést jednou).  To bude trvat o několik minut a zkopíruje konverzace student modely pro všechny kurzy k účtu student konverzace.

6. Klikněte na ukázkový model v uživatelském rozhraní student konverzace, která odpovídá ukázku, kterou jste zahájili.

Zdrojové soubory pro ukázkách jsou v `cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Vytvoření robota, která zahrnuje kód back endu

1. Pokud máte webové konverzace student otevřete uživatelské rozhraní, vrátit do seznamu aplikací na http://localhost:5050/home.
    
2. Pokud běží robota (jako je `npm run demo-pizza`), případně ji zastavte.  Není potřeba zastavit proces uživatelského rozhraní nebo zavřete webový prohlížeč.

3. V případě potřeby upravit kód v `cl-bot-01/src/app.ts`.

4. Znovu sestavte a spusťte znovu robota:

    ```bash    
    npm run build
    npm start
    ```

5. Pokud ještě nejste, přepněte do konverzace student webového uživatelského rozhraní v prohlížeči Chrome načtením http://localhost:5050/home. 

6. Vytvořte novou aplikaci student konverzace v uživatelském rozhraní a spusťte vyučující.

7. Aby se změny kódu v `cl-bot-01/src/app.ts`, opakujte postup uvedený výš, spouštění z kroku 2.

## <a name="vscode"></a>VSCode

V VSCode, že se spouštějí konfigurace pro každý demo a "prázdný robota" v `cl-bot-01/src/app.ts`.  Otevřete `cl-bot-01` složky v VSCode.

## <a name="advanced-configuration"></a>Pokročilá konfigurace

Je šablonu `.env.example` souboru ukazuje, jaké prostředí proměnné může nastavit konfigurace ukázky.

Můžete upravit tyto porty, aby nedocházelo ke konfliktům mezi jiných služeb spuštěných na váš počítač přidáním `.env` souboru do kořenového adresáře projektu:

```bash
cp .env.example .env
```

Používá standardní konfiguraci, která vám umožní místní spuštění vaší robota a začít používat student konverzace.  (Později na vaše robota nasadit do rozhraní robota, některé úpravy pro tento soubor bude potřeba.)

## <a name="support"></a>Podpora

- Dotazy označte [Stack Overflow](https://stackoverflow.com) s "microsoft kognitivní"
- Žádosti o funkce na našem [User Voice stránky](https://aka.ms/conversation-learner-uservoice)
- Otevřete problému na naší [úložiště github](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Přidání

Tento projekt přijal [pravidla chování pro Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Další informace najdete v článku [chování](https://opensource.microsoft.com/codeofconduct/faq/) nebo se obraťte na [ opencode@microsoft.com ](mailto:opencode@microsoft.com) s případnými dalšími dotazy nebo připomínkami.

## <a name="source-repositories"></a>Zdrojová úložiště

- [conversationlearner – ukázky](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner modely](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner uživatelského rozhraní](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ahoj světe](./tutorials/1-hello-world.md)
