---
title: 'Rychlý start: Rozpoznávání řeči v jazyce JavaScript v Node.js pomocí sady SDK služby řeči'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v jazyce JavaScript v Node.js pomocí sady SDK služby řeči
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: fmegen
ms.openlocfilehash: 6dc482f8629bf8ca75af5f1643999ae896fa2435
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53609400"
---
# <a name="quickstart-recognize-speech-in-javascript-in-nodejs-using-the-speech-service-sdk"></a>Rychlý start: Rozpoznávání řeči v jazyce JavaScript v Node.js pomocí sady SDK služby řeči

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit projekt Node.js pomocí JavaScriptu vazba Cognitive Services SDK řeči přepisy převod řeči na text.
Aplikace je založena na webu společnosti Microsoft [sadou SDK pro řeč Cognitive Services](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu Speech. Viz [Vyzkoušejte si službu Speech zdarma](get-started.md).
* Aktuální verze [Node.js](https://nodejs.org).

## <a name="create-a-new-project-folder"></a>Vytvořte novou složku projektu

Vytvořte novou prázdnou složku a inicializujte ji jako nový projekt jazyka JavaScript a Node.js.

```sh
npm init -f
```

Tímto krokem se init soubory package.json s výchozími hodnotami. Pravděpodobně bude chtít tento soubor upravit později.

## <a name="install-the-speech-sdk-for-javascript-into-that-folder"></a>Instalace sadou SDK pro řeč pro JavaScript do této složky

Přidat sadou SDK pro řeč prostřednictvím `npm install microsoft-cognitiveservices-speech-sdk` do projektu Node.js.

Toto stáhne a nainstaluje nejnovější verzi sadou SDK pro řeč a všechny nezbytné součásti z npmjs. Sada SDK se nainstalují v `node_modules` adresáře uvnitř složky vašeho projektu.

## <a name="using-the-speech-sdk"></a>Pomocí sady SDK pro řeč

Vytvořte ve složce nový soubor `index.js` a otevřete ho v textovém editoru.

> [!NOTE]
> Všimněte si, že v Node.js sadou SDK pro řeč nepodporuje datový typ souboru nebo mikrofon. Obě jsou podporovány pouze v prohlížečích. Místo toho použijte rozhraní Stream sadou SDK pro řeč, buď prostřednictvím `AudioInputStream.createPushStream()` nebo `AudioInputStream.createPullStream()`.

V tomto příkladu budeme používat `PushAudioInputStream` rozhraní.

Přidejte následující kód jazyka JavaScript:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="running-the-sample-from-command-line"></a>Spuštění ukázky z příkazového řádku

Spusťte aplikaci, přizpůsobit `YourSubscriptionKey`, `YourServiceRegion`, a `YourAudioFile.wav` do vaší konfigurace. Potom můžete provést zavoláním následujícího příkazu:

```sh
node index.js
```

Bude aktivovat rozpoznávání pomocí zadaného názvu souboru a prezentujte výstup na konzole.

Tady je ukázkový výstup spuštění `index.js` po aktualizaci klíč předplatného a pomocí souboru `whatstheweatherlike.wav`.

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="running-the-sample-from-visual-studio-code"></a>Spuštění ukázky z Visual Studio Code

Ukázku můžete spustit i z Visual Studio Code. Postupujte podle těchto kroků nainstalujte, otevřete a spusťte tohoto rychlého startu:

1. Spusťte Visual Studio Code a klikněte na "Otevřít složku" a pak přejděte do složky, rychlý start

   ![Snímek obrazovky otevřít složku](media/sdk/qs-js-node-01-open_project.png)

1. Otevřete terminál ve Visual Studio Code

   ![Snímek obrazovky okna terminálu](media/sdk/qs-js-node-02_open_terminal.png)

1. Spuštěním npm nainstalujte závislosti

   ![Snímek obrazovky instalace npm](media/sdk/qs-js-node-03-npm_install.png)

1. Teď jste připravení otevřete `index.js`a nastavte zarážku

   ![Snímek obrazovky s zarážku na řádku 16 index.js](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Chcete-li spustit ladění, stiskněte F5 nebo vyberte v nabídce ladění/spuštění ladění

   ![Snímek obrazovky nabídky ladění](media/sdk/qs-js-node-05-start_debugging.png)

1. Při dosažení zarážky, si můžete prohlédnout v zásobníku volání a proměnné

   ![Snímek obrazovky ladicího programu](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. Žádný výstup zobrazí v okně konzoly ladění

   ![Snímek obrazovky s konzolou pro ladění](media/sdk/qs-js-node-07-debug_output.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Vyhledejte tuto ukázku ve složce `quickstart/js-node`.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získejte naše ukázky](speech-sdk.md#get-the-samples)
