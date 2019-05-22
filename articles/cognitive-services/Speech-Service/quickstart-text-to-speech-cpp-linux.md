---
title: 'Rychlý start: Syntetizace řeči, jazyka C++ (Linux) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak tak, aby odpovídaly řeči v jazyce C++ v Linuxu pomocí sadou SDK pro řeč
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: yinhew
ms.openlocfilehash: b5b768e52a40652840e9e3d3069df8a9c895f77b
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001620"
---
# <a name="quickstart-synthesize-speech-in-c-on-linux-by-using-the-speech-sdk"></a>Rychlý start: Syntetizace řeči v jazyce C++ v Linuxu pomocí sadou SDK pro řeč

Rychlí průvodci jsou také k dispozici pro [rozpoznávání řeči](quickstart-cpp-linux.md).

V tomto článku vytvoříte C++ konzolové aplikace pro Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9). Použití služeb Cognitive Services [sadou SDK pro řeč](speech-sdk.md) syntetizace řeč z textu v reálném čase a přehrát řeč na váš počítač mluvčího. Aplikace je sestavená se [sadou Speech SDK pro Linux](https://aka.ms/csspeech/linuxbinary) a kompilátorem C++ vaší Linuxové distribuce (například `g++`).

## <a name="prerequisites"></a>Požadavky

Klíč předplatného hlasové služby k dokončení tohoto rychlého startu potřebujete. Můžete ho získat zdarma. Zobrazit [hlasové služby si můžete vyzkoušet zdarma](get-started.md) podrobnosti.

## <a name="install-speech-sdk"></a>Instalace sady Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady Cognitive Services Speech SDK je `1.5.1`.

Sadu Speech SDK pro Linux můžete použít k sestavení 64bitových i 32bitových aplikací. Požadované knihovny a soubory hlaviček si můžete stáhnout jako soubor tar z https://aka.ms/csspeech/linuxbinary.

Sadu SDK si stáhněte a nainstalujte následujícím způsobem:

1. Ujistěte se, že jsou závislosti sady SDK nainstalované.

   * On Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Na Debian 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

1. Vyberte adresář, do kterého se mají soubory sady Speech SDK extrahovat, a proměnnou prostředí `SPEECHSDK_ROOT` nastavte tak, aby odkazovala na tento adresář. Tato proměnná umožňuje snadno odkazovat na adresář v budoucích příkazech. Pokud třeba chcete používat adresář `speechsdk` ve svém domovském adresáři, použijte příkaz podobný tomuto:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Pokud adresář neexistuje, vytvořte ho.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Stáhněte a extrahujte archiv `.tar.gz` obsahující binární soubory sady Speech SDK:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Ověřte obsah adresáře nejvyšší úrovně extrahovaného balíčku:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Výpis adresáře by měl obsahovat oznámení třetích stran a soubory s licencí a také adresář `include` obsahující soubory hlaviček (`.h`) a adresář `lib` obsahující knihovny.

   [!INCLUDE [Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Vytvořte zdrojový soubor jazyka C++ s názvem `helloworld.cpp` a vložte do něj následující kód.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-linux/helloworld.cpp#code)]

1. V tento nový soubor, nahraďte řetězec `YourSubscriptionKey` s klíči předplatného hlasové služby.

1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

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

## <a name="run-the-app"></a>Spuštění aplikace

1. Nakonfigurujte cestu ke knihovně zavaděče, aby odkazovala na knihovnu sady Speech SDK.

   * V systému **x64** (64bitovém systému) zadejte následující příkaz.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * V systému **x86** (32bitovém systému) zadejte tento příkaz.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. Spusťte aplikaci.

   ```sh
   ./helloworld
   ```

1. V okně konzoly výzva zobrazí, vás vyzve k zadání nějaký text. Zadejte pár slov nebo věty. Text, který jste zadali se přenášejí do služeb řeči a syntetizovat do mluvené řeči, který přehraje na reproduktoru.

   ```text
   Type some text that you want to speak...
   > hello
   Speech synthesized to speaker for text [hello]
   Press enter to exit...
   ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte C++ – ukázky na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení hlasová písma](how-to-customize-voice-font.md)
- [Záznam hlasu ukázky](record-custom-voice-samples.md)
