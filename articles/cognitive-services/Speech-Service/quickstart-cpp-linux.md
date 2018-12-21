---
title: 'Rychlý start: Rozpoznávání řeči, jazyka C++ (Linux) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v C++ v Linuxu s využitím sady Speech SDK.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: wolfma
ms.openlocfilehash: d741550fc64ef7544e33304f8608ad6e6ee3426e
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723637"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-by-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v jazyce C++ v Linuxu pomocí sadou SDK pro řeč

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku vytvoříte pro Ubuntu Linux 16.04 nebo 18.04 konzolové aplikace jazyka C++. Použijete sadu Cognitive Services [Speech SDK](speech-sdk.md) k přepisu řeči na text v reálném čase z mikrofonu počítače. Aplikace je sestavená se [sadou Speech SDK pro Linux](https://aka.ms/csspeech/linuxbinary) a kompilátorem C++ vaší Linuxové distribuce (například `g++`).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete klíč předplatného služby Speech. Můžete ho získat zdarma. Podrobnosti najdete v článku [Vyzkoušejte si službu Speech zdarma](get-started.md).

## <a name="install-speech-sdk"></a>Instalace sady Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady Cognitive Services Speech SDK je `1.2.0`.

Sadu Speech SDK pro Linux můžete použít k sestavení 64bitových i 32bitových aplikací. Požadované knihovny a soubory hlaviček si můžete stáhnout jako soubor tar z https://aka.ms/csspeech/linuxbinary.

Sadu SDK si stáhněte a nainstalujte následujícím způsobem:

1. Ujistěte se, že jsou závislosti sady SDK nainstalované.

   ```sh
   sudo apt-get update
   sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
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

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. V tomto novém souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného služby Speech.

1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

## <a name="build-the-app"></a>Sestavení aplikace

> [!NOTE]
> Nezapomeňte zadat níže uvedené příkazy jako _jeden příkazový řádek_. Nejjednodušším způsobem, jak to udělat, je zkopírovat příkaz pomocí tlačítka **Kopírovat** vedle každého příkazu a vložit ho do příkazového řádku vašeho prostředí.

* V systému **x64** (64bitovém systému) spusťte následující příkaz, kterým vytvoříte aplikaci.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* V systému **x86** (32bitovém systému) spusťte následující příkaz, kterým vytvoříte aplikaci.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
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

1.  V okně konzoly se zobrazí výzva požadující, abyste něco řekli. Vyslovte anglickou frázi nebo větu. Vaše řeč se přenese do služby Speech a přepíše na text, který se zobrazí ve stejném okně.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte C++ – ukázky na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
