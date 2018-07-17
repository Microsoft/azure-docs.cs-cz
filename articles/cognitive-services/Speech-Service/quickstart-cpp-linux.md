---
title: 'Rychlý start: Rozpoznávat řeč v jazyce C++ v Linuxu pomocí Cognitive Services SDK řeči | Dokumentace Microsoftu'
titleSuffix: Microsoft Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v jazyce C++ v Linuxu pomocí Cognitive Services SDK řeči
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 2c919040233226818505dbafc260d56d4d0e3c9e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070728"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-using-the-speech-sdk"></a>Rychlý start: Rozpoznávat řeč v jazyce C++ v Linuxu pomocí sadou SDK pro řeč

V tomto článku se dozvíte, jak k vytvoření konzolové aplikace jazyka C++ v systému Linux (Ubuntu 16.04) přepisy převod řeči na text pomocí Cognitive Services SDK řeči.

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu rozpoznávání řeči. Zobrazit [službu řeči si můžete vyzkoušet zdarma](get-started.md).
* Počítač Ubuntu 16.04 s mikrofon pracovní.
* Chcete-li instalovat balíčky potřebné k sestavení a spuštění tohoto vzorku, spusťte následující příkaz:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="get-the-speech-sdk"></a>Získání sady SDK pro řeč

[!include[License Notice](includes/license-notice.md)]

Aktuální verze sady SDK pro řeč Cognitive Services je `0.5.0`.

Cognitive Services sadou SDK pro řeč pro Linux je k dispozici pro vytváření 64bitové a 32bitové aplikace.
Požadované soubory můžete stáhnout jako soubor tar z https://aka.ms/csspeech/linuxbinary.
Stáhněte a nainstalujte sadu SDK následujícím způsobem:

1. Výběr adresáře (absolutní cesta), kam chcete umístit binární soubory sadou SDK pro řeč a záhlaví.
   Například vyberte cestu `speechsdk` v domovském adresáři:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Pokud ještě neexistuje, vytvořte adresář:

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Stažení a extrakci `.tar.gz` archiv se sadou SDK pro řeč binárních souborů:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Ověření obsahu adresář nejvyšší úrovně extrahované balíčku:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Měl by se zobrazit oznámení třetích stran a soubory s licencí, ale i `include` adresáře pro záhlaví a `lib` adresáře pro knihovny.

   [!include[Linux Binary Archive Content](includes/linuxbinary-content.md)]

## <a name="add-the-sample-code"></a>Přidejte ukázkový kód

1. Přidejte následující kód do souboru s názvem `helloworld.cpp`:

  [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. Nahraďte řetězec `YourSubscriptionKey` s klíči předplatného.

1. Nahraďte řetězec `YourServiceRegion` s [oblasti](regions.md) přidružených k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

## <a name="building"></a>Sestavení

> [!NOTE]
> Nezapomeňte zkopírovat a vložit příkazy sestavení níže jako _jeden řádek_.

* Na **x64** počítače, spusťte následující příkaz k sestavení aplikace:

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* Na **x86** počítače, spusťte následující příkaz k sestavení aplikace:

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="run-the-sample"></a>Spuštění ukázky

1. Nakonfigurujte cestu ke knihovně zavaděče tak, aby odkazoval na knihovny sadou SDK pro řeč konfigurací.

   * Na **x64** počítače, spusťte:

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * Na **x86** počítače, spusťte:

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. Spusťte aplikaci následujícím způsobem:

   ```sh
   ./helloworld
   ```

1. Byste měli vidět výstup podobný tomuto:

   ```text
   Say something...
   We recognized: What's the weather
   ```

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Hledat v této ukázce `quickstart/cpp-linux` složky.

## <a name="next-steps"></a>Další postup

* Přejděte [stránku s ukázkami](samples.md) Další ukázky.
