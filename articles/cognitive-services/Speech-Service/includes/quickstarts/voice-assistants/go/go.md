---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 8f5a8aa2b949ddd07208cac054147596512f783f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213628"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md)
> * Vytvoření robota připojeného k [kanálu pro přímý vstup řeči](/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Ujistěte se, že máte přístup k mikrofonu pro záznam zvuku
>
  > [!NOTE]
  > Přečtěte si [seznam podporovaných oblastí pro hlasové asistenty](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) a zajistěte, aby byly prostředky nasazeny v jedné z těchto oblastí.

## <a name="setup-your-environment"></a>Nastavení vašeho prostředí

Aktualizujte soubor. mod nejnovější verzí sady SDK přidáním tohoto řádku.
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.15.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem
Obsah zdrojového souboru (např.) nahraďte `quickstart.go` níže, což zahrnuje:

- definice balíčku Main
- Import nezbytných modulů ze sady Speech SDK
- proměnné pro ukládání informací bot, které budou nahrazeny později v tomto rychlém startu
- Jednoduchá implementace pomocí mikrofonu pro vstup zvuku
- obslužné rutiny událostí pro různé události, ke kterým dochází během interakce s rozpoznáváním řeči

```sh
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/dialog"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_BOT_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := dialog.NewBotFrameworkConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    connector, err := dialog.NewDialogServiceConnectorFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer connector.Close()
    activityReceivedHandler := func(event dialog.ActivityReceivedEventArgs) {
        defer event.Close()
        fmt.Println("Received an activity.")
    }
    connector.ActivityReceived(activityReceivedHandler)
    recognizedHandle := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognized ", event.Result.Text)
    }
    connector.Recognized(recognizedHandle)
    recognizingHandler := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognizing ", event.Result.Text)
    }
    connector.Recognizing(recognizingHandler)
    connector.ListenOnceAsync()
    <-time.After(10 * time.Second)
}
```

`YOUR_SUBSCRIPTION_KEY`Hodnoty a nahraďte `YOUR_BOT_REGION` skutečnými hodnotami z prostředku řeči.

- Přejděte do Azure Portal a otevřete prostředek řeči.
- V části **klíče a koncový bod** vlevo jsou k dispozici dva dostupné klíče předplatného.
    - Použijte buď jednu jako `YOUR_SUBSCRIPTION_KEY` náhradu hodnoty.
- V **přehledu** vlevo si všimněte oblasti a namapujte ji na identifikátor oblasti.
    - Jako náhradu hodnoty použijte identifikátor oblasti `YOUR_BOT_REGION` , například: `"westus"` pro **západní USA**

   > [!NOTE]
   > Přečtěte si [seznam podporovaných oblastí pro hlasové asistenty](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) a zajistěte, aby byly prostředky nasazeny v jedné z těchto oblastí.

   > [!NOTE]
   > Informace o konfiguraci robota najdete v dokumentaci k rozhraní bot pro [kanál Direct line Speech](/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="code-explanation"></a>Vysvětlení kódu
Klíč předplatného řeči a oblast jsou nutné k vytvoření objektu konfigurace řeči. Objekt konfigurace je nutný k vytvoření instance objektu pro rozpoznávání řeči.

Instance nástroje pro rozpoznávání nabízí více způsobů rozpoznávání řeči. V tomto příkladu je rozpoznávání řeči nepřetržitě rozpoznáno. Tato funkce umožňuje službě rozpoznávání řeči zjistit, že posíláte mnoho frází pro rozpoznávání, a když se program ukončí a přestane rozpoznávat řeč. V případě, že se výsledky vrátí, kód je zapíše do konzoly.

## <a name="build-and-run"></a>Sestavení a spuštění
Nyní jste nastavili sestavení projektu a otestujete svého vlastního hlasového asistenta pomocí služby Speech.
1. Sestavte projekt, např. **"jít Build"** .
2. Spusťte modul a vymluvte frázi nebo větu do mikrofonu zařízení. Váš hlas se přenáší do kanálu pro přímý přenos řeči a přepisu na text, který se zobrazí jako výstup.


> [!NOTE]
> Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]