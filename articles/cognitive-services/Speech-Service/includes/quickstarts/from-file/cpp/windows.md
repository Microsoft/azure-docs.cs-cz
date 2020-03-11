---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 2f029725ff53c6bf01481f7a188e070cf84550c9
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "79081885"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=windows)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor **helloworld.cpp**.

1. Nahraďte celý kód následujícím fragmentem kódu:
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Nahraďte řetězec `YourServiceRegion` **identifikátorem oblasti** z [oblasti](https://aka.ms/speech/sdkregion) přidružené k vašemu předplatnému (například `westus` pro bezplatné zkušební předplatné).

1. Nahraďte řetězec `whatstheweatherlike.wav` vlastním názvem souboru.

1. V řádku nabídek vyberte možnost **soubor** > **Uložit vše**.

> [!NOTE]
> Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. V řádku nabídek vyberte **sestavit** > **Sestavit řešení** a sestavte aplikaci. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **ladění** > **Spustit ladění** (nebo stiskněte klávesu **F5**) a spusťte aplikaci **HelloWorld** .

1. Váš zvukový soubor se přenáší do služby pro rozpoznávání řeči a první utterance v souboru je přepisu na text, který se zobrazí ve stejném okně.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]