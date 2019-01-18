---
title: 'Rychlý start: Rozpoznávání řeči, Node.js – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Tento průvodce vám vytvořit speech to text konzolové aplikace pomocí sadou SDK pro řeč pro Node.js. Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: fmegen
ms.openlocfilehash: e0ae916687ca32835dd8daf6e5059b8f6eea0ff6
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382170"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>Rychlý start: Rozpoznávání řeči se sadou SDK pro řeč pro Node.js

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit projekt Node.js pomocí JavaScriptu vazba Cognitive Services SDK řeči přepisy převod řeči na text.
Aplikace je založena na webu společnosti Microsoft [sadou SDK pro řeč Cognitive Services](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).
* Aktuální verze [Node.js](https://nodejs.org).

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte novou složku a inicializace projektu.

```sh
npm init -f
```

Tímto krokem se init soubory package.json s výchozími hodnotami. Pravděpodobně bude chtít tento soubor upravit později.

## <a name="install-the-speech-sdk"></a>Instalace sady SDK pro řeč

Přidejte do projektu Node.js SDK řeči.

```
npm install microsoft-cognitiveservices-speech-sdk
```

Tím se stáhne a nainstaluje nejnovější verzi sadou SDK pro řeč a všechny požadované součásti z npmjs. Sada SDK se nainstalují v `node_modules` adresáře uvnitř složky vašeho projektu.

## <a name="use-the-speech-sdk"></a>Umožňuje využít řeč SDK

Vytvořte ve složce nový soubor `index.js` a otevřete ho v textovém editoru.

> [!NOTE]
> Všimněte si, že v Node.js sadou SDK pro řeč nepodporuje datový typ souboru nebo mikrofon. Obě jsou podporovány pouze v prohlížečích. Místo toho použijte rozhraní Stream sadou SDK pro řeč, buď prostřednictvím `AudioInputStream.createPushStream()` nebo `AudioInputStream.createPullStream()`.

V tomto příkladu budeme používat `PushAudioInputStream` rozhraní.

Přidejte následující kód jazyka JavaScript:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>Spuštění ukázky

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

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalace a používání sadou SDK pro řeč s Visual Studio Code

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte Node.js ukázky na Githubu](https://aka.ms/csspeech/samples)
