---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 4a8b91fbcecbc4f763c9a24ef8c2bcedc377e3b0
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80659172"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Vytvoření prostředku řeči Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=macos)
> * Ujistěte se, že máte přístup k mikrofonu pro snímání zvuku

## <a name="source-code"></a>Zdrojový kód

Vytvořte zdrojový soubor Jazyka C++ s názvem *helloworld.cpp*a vložte do něj následující kód.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/macos/from-microphone/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Vysvětlení kódu

[!INCLUDE [code explanation](../code-explanation.md)]

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

1. V okně konzoly se zobrazí výzva požadující, abyste něco řekli. Vyslovte anglickou frázi nebo větu. Vaše řeč se přenese do služby Speech a přepíše na text, který se zobrazí ve stejném okně.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](../footer.md)]