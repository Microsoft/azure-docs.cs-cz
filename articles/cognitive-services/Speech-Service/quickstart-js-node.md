---
title: 'Rychlý start: Rozpoznávání řeči, Node.js – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Použijte tuto příručku k vytvoření speech to text konzolové aplikace pomocí sadou SDK pro řeč pro Node.js. Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: fmegen
ms.openlocfilehash: 99938f8b64339556c0ee4b9caa9124c609159af6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747267"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>Rychlý start: Rozpoznávání řeči se sadou SDK pro řeč pro Node.js

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit projekt Node.js pomocí JavaScriptu vazba sadou SDK pro řeč pro Azure Cognitive Services pro přepisy převod řeči na text.
Aplikace je založena na [sadou SDK pro řeč pro jazyk JavaScript](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).
* Aktuální verze [Node.js](https://nodejs.org).

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte novou složku a inicializace projektu:

```sh
npm init -f
```

Tento příkaz inicializuje **package.json** souborů s výchozími hodnotami. Pravděpodobně budete chtít tento soubor upravit později.

## <a name="install-the-speech-sdk"></a>Instalace sady SDK pro řeč

Přidejte do projektu Node.js sadou SDK pro řeč:

```
npm install microsoft-cognitiveservices-speech-sdk
```

Tento příkaz stáhne a nainstaluje nejnovější verzi sadou SDK pro řeč a všechny požadované součásti z **npmjs**. Sada SDK nainstaluje `node_modules` adresáře uvnitř složky vašeho projektu.

## <a name="use-the-speech-sdk"></a>Umožňuje využít řeč SDK

Vytvořte nový soubor ve složce s názvem `index.js`a tento soubor otevřít v textovém editoru.

> [!NOTE]
> V Node.js, sadou SDK pro řeč nepodporuje mikrofon nebo **souboru** datového typu. Obě jsou podporovány pouze v prohlížečích. Místo toho použijte **Stream** rozhraní sadou SDK pro řeč, buď prostřednictvím `AudioInputStream.createPushStream()` nebo `AudioInputStream.createPullStream()`.

V tomto příkladu používáme `PushAudioInputStream` rozhraní.

Přidejte tento kód jazyka JavaScript:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>Spuštění ukázky

Otevřete aplikaci, přizpůsobit `YourSubscriptionKey`, `YourServiceRegion`, a `YourAudioFile.wav` do vaší konfigurace. Po zavolání tohoto příkazu ho spusťte:

```sh
node index.js
```

Aktivuje rozlišení pomocí zadaného názvu souboru. A představuje výstup na konzole.

Tato ukázka je výstup při spuštění `index.js` po aktualizaci klíče předplatného a použít soubor `whatstheweatherlike.wav`:

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

Ukázku můžete také spustit z aplikace Visual Studio Code. Postupujte podle následujících kroků nainstalujte, otevřete a spuštění tohoto rychlého startu:

1. Spusťte Visual Studio Code. Vyberte **otevřete složku**. Pak přejděte do složky, rychlý start.

   ![Otevřít složku](media/sdk/qs-js-node-01-open_project.png)

1. Otevřete terminál ve Visual Studio Code.

   ![V okně terminálu](media/sdk/qs-js-node-02_open_terminal.png)

1. Spustit `npm` instalace závislostí.

   ![instalace npm](media/sdk/qs-js-node-03-npm_install.png)

1. Nyní jste připraveni k otevření `index.js`a nastavte zarážku.

   ![index.js s zarážku na řádku 16](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Ladění spustíte stisknutím klávesy F5 nebo vyberte **ladění/spuštění ladění** z nabídky.

   ![V nabídce ladění](media/sdk/qs-js-node-05-start_debugging.png)

1. Při dosažení zarážky, si můžete prohlédnout zásobník volání a proměnné.

   ![Ladicí program](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. Žádný výstup zobrazuje v okně konzoly ladění.

   ![Konzole ladění](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte Node.js ukázky na Githubu](https://aka.ms/csspeech/samples)
