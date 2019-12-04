---
title: 'Rychlý Start: rozpoznávání řeči z mikrofonu C++ , (Linux) – služba Speech'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč v C++ systému Linux pomocí sady Speech SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: eda87a8146c936287ce82966602d162f047de7c8
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796132"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md?tabs=linux)
> * [Vytvořit prázdný ukázkový projekt](../../../../quickstarts/create-project.md?tabs=linux)
> * Ujistěte se, že máte přístup k mikrofonu pro záznam zvuku

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Vytvořte zdrojový soubor jazyka C++ s názvem `helloworld.cpp` a vložte do něj následující kód.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/from-microphone/helloworld.cpp#code)]

1. V tomto novém souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného služby Speech Services.

1. Řetězec `YourServiceRegion` nahraďte [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

> [!NOTE]
> Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .

## <a name="build-the-app"></a>Sestavení aplikace

> [!NOTE]
> Nezapomeňte zadat níže uvedené příkazy jako _jeden příkazový řádek_. Nejjednodušším způsobem, jak to udělat, je zkopírovat příkaz pomocí tlačítka **Kopírovat** vedle každého příkazu a vložit ho do příkazového řádku vašeho prostředí.

* V systému **x64** (64bitovém systému) spusťte následující příkaz, kterým vytvoříte aplikaci.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* V systému **x86** (32bitovém systému) spusťte následující příkaz, kterým vytvoříte aplikaci.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* V systému **ARM64** (64) spusťte následující příkaz, který sestaví aplikaci.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>Spusťte aplikaci

1. Nakonfigurujte cestu ke knihovně zavaděče, aby odkazovala na knihovnu sady Speech SDK.

   * V systému **x64** (64bitovém systému) zadejte následující příkaz.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * V systému **x86** (32bitovém systému) zadejte tento příkaz.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * V systému **ARM64** (64 bitů) zadejte následující příkaz.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. Spusťte aplikaci.

   ```sh
   ./helloworld
   ```

1. V okně konzoly se zobrazí výzva požadující, abyste něco řekli. Vyslovte anglickou frázi nebo větu. Váš hlas se přenáší do služby Speech Services a přepisu na text, který se zobrazí ve stejném okně.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
