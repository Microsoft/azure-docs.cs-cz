---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: a18ce7b7c28b99967668bc33c5a94cbb58bfbc34
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377251"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md)
> * Ujistěte se, že máte přístup k mikrofonu pro záznam zvuku

## <a name="setup-your-environment"></a>Nastavení vašeho prostředí

Aktualizujte soubor. mod nejnovější verzí sady SDK přidáním tohoto řádku.
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.13.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem
1. Obsah zdrojového souboru (např.) nahraďte `sr-quickstart.go` níže, což zahrnuje:

- definice balíčku Main
- Import nezbytných modulů ze sady Speech SDK
- proměnné pro ukládání informací o předplatném, které se nahradí později v tomto rychlém startu
- Jednoduchá implementace pomocí mikrofonu pro vstup zvuku
- obslužné rutiny událostí pro různé události, ke kterým dochází při rozpoznávání řeči

```sh
package recognizer

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

`YOUR_SUBSCRIPTION_KEY`Hodnoty a nahraďte `YOUR_SUBSCRIPTIONKEY_REGION` skutečnými hodnotami z prostředku řeči.

- Přejděte do Azure Portal a otevřete prostředek řeči.
- Pod **klíčem** nalevo jsou k dispozici dva dostupné klíče předplatného.
    - Použijte buď jednu jako `YOUR_SUBSCRIPTION_KEY` náhradu hodnoty.
- V **přehledu** vlevo si všimněte oblasti a namapujte ji na identifikátor oblasti.
- Jako náhradu hodnoty použijte identifikátor oblasti `YOUR_SUBSCRIPTIONKEY_REGION` , například: `"westus"` pro **západní USA**

## <a name="code-explanation"></a>Vysvětlení kódu
Klíč předplatného řeči a oblast jsou nutné k vytvoření objektu konfigurace řeči. Objekt konfigurace je nutný k vytvoření instance objektu pro rozpoznávání řeči.

Instance nástroje pro rozpoznávání nabízí více způsobů rozpoznávání řeči. V tomto příkladu je rozpoznávání řeči nepřetržitě rozpoznáno. Tato funkce umožňuje službě rozpoznávání řeči zjistit, že posíláte mnoho frází pro rozpoznávání, a když se program ukončí a přestane rozpoznávat řeč. V případě, že se výsledky vrátí, kód je zapíše do konzoly.

## <a name="build-and-run"></a>Sestavení a spuštění
Nyní jste nastavili sestavení projektu a testování rozpoznávání řeči pomocí služby Speech.
1. Sestavte projekt, např. **"jít Build"** .
2. Spusťte modul a vymluvte frázi nebo větu do mikrofonu zařízení. Váš hlas se přenáší do služby Speech a přepisu na text, který se zobrazí ve výstupu.


> [!NOTE]
> Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .


## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
