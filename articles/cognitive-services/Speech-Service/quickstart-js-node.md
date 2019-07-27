---
title: 'Rychlý start: Rozpoznávání řeči, Node. js – Speech Service'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete vytvořit konzolovou aplikaci pro převod řeči na text pomocí sady Speech SDK pro Node. js. Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 1a3d1bc62e995aff43d9538f49b436a7de16f1e9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554079"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>Rychlý start: Rozpoznávání řeči pomocí sady Speech SDK pro Node. js

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit projekt Node. js pomocí vazby JavaScriptu sady Speech SDK pro Azure Cognitive Services přepisovat řeči na text.
Aplikace je založená na sadě [Speech SDK pro JavaScript](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného Azure pro službu Speech Service. [Získejte je zdarma](get-started.md).
* Aktuální verze [Node. js](https://nodejs.org).

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte novou složku a inicializujte projekt:

```sh
npm init -f
```

Tento příkaz inicializuje soubory **Package. JSON** s výchozími hodnotami. Tento soubor budete pravděpodobně chtít později upravit.

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Přidejte sadu Speech SDK do projektu Node. js:

```
npm install microsoft-cognitiveservices-speech-sdk
```

Tento příkaz stáhne a nainstaluje nejnovější verzi sady Speech SDK a všechny požadované požadavky z **npmjs**. Sada SDK se nainstaluje v `node_modules` adresáři do složky projektu.

## <a name="use-the-speech-sdk"></a>Použití sady Speech SDK

Vytvořte nový soubor ve složce s názvem `index.js`a otevřete tento soubor pomocí textového editoru.

> [!NOTE]
> V Node. js sada Speech SDK nepodporuje datový typ mikrofonu nebo **souboru** . Obě jsou podporované jenom v prohlížečích. Místo toho použijte rozhraní **Stream** pro sadu Speech SDK, a to buď `AudioInputStream.createPushStream()` prostřednictvím `AudioInputStream.createPullStream()`nebo.

V tomto příkladu používáme `PushAudioInputStream` rozhraní.

Přidat tento kód JavaScriptu:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>Spuštění ukázky

Pro otevření aplikace, přizpůsobení `YourSubscriptionKey`, `YourServiceRegion`a `YourAudioFile.wav` ke konfiguraci. Pak ji spusťte voláním tohoto příkazu:

```sh
node index.js
```

Aktivuje rozpoznávání pomocí zadaného názvu souboru. A prezentuje výstup na konzole.

Tato ukázka je výstupem při spuštění `index.js` nástroje po aktualizaci klíče předplatného a použití souboru: `whatstheweatherlike.wav`

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

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalace a použití sady Speech SDK s Visual Studio Code

Ukázku můžete také spustit z Visual Studio Code. Pomocí těchto kroků můžete nainstalovat, otevřít a spustit rychlý Start:

1. Spusťte Visual Studio Code. Vyberte možnost **Otevřít složku**. Pak přejděte do složky pro rychlé zprovoznění.

   ![Otevřít složku](media/sdk/qs-js-node-01-open_project.png)

1. Otevřete terminál v Visual Studio Code.

   ![Okno terminálu](media/sdk/qs-js-node-02_open_terminal.png)

1. Spusťte `npm` pro instalaci závislostí.

   ![instalace npm](media/sdk/qs-js-node-03-npm_install.png)

1. Teď jste připraveni otevřít `index.js`a nastavit zarážku.

   ![index. js se zarážkou na řádku 16](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Chcete-li spustit ladění, vyberte možnost F5 nebo vyberte ladění **/Spustit ladění** z nabídky.

   ![Nabídka ladění](media/sdk/qs-js-node-05-start_debugging.png)

1. Když je dosaženo zarážky, můžete zkontrolovat zásobník volání a proměnné.

   ![Ladicí program](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. Libovolný výstup se zobrazí v okně konzoly ladění.

   ![Ladit konzolu](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky pro Node. js na GitHubu](https://aka.ms/csspeech/samples)
