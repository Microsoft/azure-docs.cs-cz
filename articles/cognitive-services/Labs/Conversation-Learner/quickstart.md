---
title: Jak vytvořit model konverzace Learner využívající Node.js – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak vytvořit model konverzace Learner pomocí Node.js.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: b98b528d2d78d2285977dec49da18081df47e9d6
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166128"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Vytvoření modelu konverzace Learner pomocí Node.js

Konverzace Learner snižuje složitost vytváření robotů. Umožňuje hybridní vývoj pracovní postup povolení ručně psanou kódu a strojové učení a snížit množství kódu potřebného k zápisu robotů. Některé části pevné modelu, jako je například kontrolu, pokud je uživatel přihlášen, nebo zajištěním, požadavek rozhraní API ke kontrole skladových zásob, stále možné zakódovat. Ale jiné změny ve stavu a akce výběru je možné zjistit z dialogových oken příklad Dal doménu nebo pro vývojáře.

## <a name="invitation-required"></a>Pozvánku požadováno

*Pozvánka se vyžaduje pro přístup k projektu Learner konverzace.*

Konverzace Learner projekt se skládá ze sady SDK je přidat do vašeho robota a cloudové služby, která má přístup k sadě SDK pro službu machine learning.  V současné době vyžaduje přístup ke cloudové službě projektu konverzace Štíhlejší pozvánku.  Pokud jste nebyli pozváni již, [požádat o pozvání](https://aka.ms/conversation-learner-request-invite).  Pokud jste neobdrželi pozvánku, nebude možné přistupovat ke cloudovým rozhraní API.

## <a name="prerequisites"></a>Požadavky

- Uzel 8.5.0 nebo novější a npm 5.3.0 nebo novější. Nainstalujte z [ https://nodejs.org ](https://nodejs.org).
  
- Služba LUIS vytváření klíč:

  1. Přihlaste se do [ http://www.luis.ai ](http://www.luis.ai).

  2. Klikněte na své jméno v pravém horním rohu klikněte na "nastavení"

  3. Vytváření klíče se zobrazí na stránce výsledný

  (Váš LUIS vytváření klíč slouží 2 role.  Nejprve bude sloužit jako vaše konverzace Learner vytváření klíč.  Za druhé konverzace Learner využívá LUIS pro extrakci entity; Služba LUIS vytváření klíč slouží k vytváření modelů služby LUIS vás cestovat oprávněný)

- Webový prohlížeč Google Chrome. Nainstalujte z [ https://www.google.com/chrome/index.html ](https://www.google.com/chrome/index.html).

- Git. Nainstalujte z [ https://git-scm.com/downloads ](https://git-scm.com/downloads).

- VSCode. Nainstalujte z [ https://code.visualstudio.com/ ](https://code.visualstudio.com/). Poznámka: Tato možnost se doporučuje, není nutné.

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

2. Konfigurovat:

   Vytvořte soubor s názvem `.env` v adresáři `cl-bot-01`.  Obsah souboru by měl vypadat:

   ```
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Bot spuštění:

    ```
    npm start
    ```

    To spustí obecný prázdný robotů v `cl-bot-01/src/app.ts`.

3. Spusťte konverzace Learner uživatelského rozhraní:

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

4. Otevřít v prohlížeči na http://localhost:5050 

Teď používáte Learner konverzace a můžete vytvořit a představuje model Learner konverzace.  

> [!NOTE]
> Při spuštění je dostupná pro pozvané Learner konverzace projektu.  Pokud http://localhost:5050 ukazuje HTTP `403` chyba, to znamená, že váš účet nebyl pozvaný.  Prosím [požádat o pozvání](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Kurzy, ukázky a přepínání mezi robotů

Výše uvedených pokynů spustit obecný prázdný robota.  Spustit kurz nebo místo toho ukázka bot:

1. Pokud máte webové konverzace Learner otevřete uživatelské rozhraní, vraťte do seznamu modely na úrovni http://localhost:5050/home.
    
2. Pokud je spuštěn jiný bot (jako je `npm start` nebo `npm run demo-pizza`), zastavte ji.  Není potřeba zastavit proces uživatelského rozhraní, nebo zavřete webový prohlížeč.

3. Spusťte ukázku bot z příkazového řádku (kroku 2 výše).  Ukázky patří:

  ```bash
  npm run tutorial-general
  npm run tutorial-entity-detection
  npm run tutorial-session-callbacks
  npm run tutorial-api-calls
  npm run tutorial-hybrid
  npm run demo-password
  npm run demo-pizza
  npm run demo-storage
  npm run demo-vrapp
  ```

4. Pokud ještě nejste, přepněte do konverzace Learner webového uživatelského rozhraní v prohlížeči Chrome načtením http://localhost:5050/home. 

5. Klikněte na "Import kurzy" (pouze je nutné provést jednou).  To bude trvat přibližně minutu a zkopíruje konverzace Learner modely pro všechny kurzy ke svému účtu Learner konverzace.

6. Klikněte na ukázky modelu v uživatelském rozhraní Learner konverzace, který odpovídá ukázku, kterou jste spustili.

Zdrojové soubory pro ukázkách, které jsou v `cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Vytváření robotů, která zahrnuje kód back endu

1. Pokud máte webové konverzace Learner otevřete uživatelské rozhraní, vraťte do seznamu modely na úrovni http://localhost:5050/home.
    
2. Pokud je spuštěn robota (jako je `npm run demo-pizza`), zastavte ji.  Není potřeba zastavit proces uživatelského rozhraní, nebo zavřete webový prohlížeč.

3. V případě potřeby upravit kód v `cl-bot-01/src/app.ts`.

4. Znovu sestavte a znovu spusťte bot:

    ```bash    
    npm run build
    npm start
    ```

5. Pokud ještě nejste, přepněte do konverzace Learner webového uživatelského rozhraní v prohlížeči Chrome načtením http://localhost:5050/home. 

6. Vytvořit nový model Learner konverzace v uživatelském rozhraní a spusťte výuky.

7. Provádět změny kódu v `cl-bot-01/src/app.ts`, opakujte postup uvedený výš, od kroku 2.

## <a name="vscode"></a>VSCode

Ve VSCode, existuje jsou spuštěny konfigurace pro každý ukázku a pro "prázdný robota" `cl-bot-01/src/app.ts`.  Otevřít `cl-bot-01` složky ve VSCode.

## <a name="advanced-configuration"></a>Pokročilá konfigurace

Zde je šablona `.env.example` soubor ukazuje, jaké prostředí proměnné můžete nastavit ke konfiguraci ukázky.

Můžete upravit tyto porty, aby nedocházelo ke konfliktům mezi další služby spuštěné na počítači tak, že přidáte `.env` souboru do kořenového adresáře projektu:

```bash
cp .env.example .env
```

Tato funkce využívá standardní konfiguraci, která vám umožní místní spuštění vašeho robota a začít používat Learner konverzace.  (Dále, k nasazení svého robota rozhraní Bot Framework, některé úpravy pro tento soubor bude potřeba.)

## <a name="support"></a>Podpora

- Označte své otázky [Stack Overflow](https://stackoverflow.com) s "microsoft cognitive".
- Žádost o funkci na naše [User Voice stránky](https://aka.ms/conversation-learner-uservoice)
- Otevřete problém na našem [úložiště GitHub](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Přispívání

Tento projekt přijal [pravidla chování pro Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Další informace najdete v [nejčastějších dotazech k pravidlům chování](https://opensource.microsoft.com/codeofconduct/faq/). S případnými dalšími dotazy nebo připomínkami se obraťte na adresu [opencode@microsoft.com](mailto:opencode@microsoft.com).

## <a name="source-repositories"></a>Zdrojové úložiště

- [conversationlearner – ukázky](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner modelů](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner uživatelského rozhraní](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner Chat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ahoj světe](./tutorials/1-hello-world.md)
