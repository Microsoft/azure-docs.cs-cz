---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 989862c533487f1b8e5a79a691e2afd039dd59ff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400554"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Vytvoření prostředku řeči Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=macos&pivots=programming-language-cpp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Vytvořte zdrojový soubor jazyka C++ s názvem `helloworld.cpp` a vložte do něj následující kód.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. V tomto novém souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného služby Speech.

1. Nahraďte `YourServiceRegion` řetězec **identifikátorem oblasti** z [oblasti](https://aka.ms/speech/sdkregion) přidružené `westus` k vašemu předplatnému (například pro bezplatné zkušební předplatné).

1. Nahraďte `whatstheweatherlike.wav` řetězec vlastním názvem souboru.

> [!NOTE]
> Sada Speech SDK bude ve výchozím nastavení rozpoznána použití jazyka en-us, viz [Určení zdrojového jazyka pro řeč na text,](../../../../how-to-specify-source-language.md) kde naleznete informace o výběru zdrojového jazyka.

## <a name="build-the-app"></a>Sestavení aplikace

> [!NOTE]
> Nezapomeňte zadat níže uvedené příkazy jako _jeden příkazový řádek_. Nejjednodušším způsobem, jak to udělat, je zkopírovat příkaz pomocí tlačítka **Kopírovat** vedle každého příkazu a vložit ho do příkazového řádku vašeho prostředí.

* Chcete-li vytvořit aplikaci, spusťte následující příkaz.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Spuštění aplikace

1. Nakonfigurujte cestu ke knihovně zavaděče, aby odkazovala na knihovnu sady Speech SDK.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Spusťte aplikaci.

   ```sh
   ./helloworld
   ```

1. Zvukový soubor je přenášen do služby Rozpoznávání řeči a první utterance v souboru je přepsán a text, který se zobrazí ve stejném okně.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
