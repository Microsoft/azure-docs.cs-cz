---
title: Rychlý start SDK řeči pro C++ a Linux | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Get informace a ukázky kódu můžete rychle začít používat sadu SDK řeči se systémy Linux a C++ v kognitivní služby.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: e5ea74f92eb91ff89f013a4ee9ef7cbe0f001db0
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111146"
---
# <a name="quickstart-for-c-and-linux"></a>Rychlý start pro C++ a Linux

Aktuální verze sady SDK kognitivní řeči služby je `0.4.0`.

Kognitivní řeči Services SDK pro Linux je k dispozici pro vytvoření 64bitové a 32bitové aplikace. Požadované soubory si můžete stáhnout jako soubor vkládání z https://aka.ms/csspeech/linuxbinary.

> [!NOTE]
> Pokud hledáte rychlý start pro C++ a Windows, přejděte [zde](quickstart-cpp-windows.md).
> Pokud hledáte rychlý start pro C# a systému Windows, přejděte [zde](quickstart-csharp-windows.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Tyto pokyny předpokládají, že používáte na 16.04 Ubuntu na počítači (x86 nebo x64).
> Různé verze Ubuntu nebo jiný distribuční Linux je nutné přizpůsobit požadované kroky.

## <a name="prerequisites"></a>Požadavky

[!include[Ubuntu Prerequisites](includes/ubuntu1604-prerequisites.md)]

## <a name="getting-the-binary-package"></a>Získávání binární balíčku

[!include[License Notice](includes/license-notice.md)]

1. Vyberte adresář (absolutní cesta), kam chcete umístit řeči SDK binární soubory a hlaviček.
   Například vyberte cestu `speechsdk` pod domovského adresáře:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Pokud ještě neexistuje, vytvořte adresář:

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Stažení a extrakci `.tar.gz` archivu řeči SDK binární soubory služby:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Ověřte obsah nejvyšší úrovně adresáře extrahované balíčku:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Měl by se zobrazit upozornění třetích stran a souborů s licencí, a také `include` adresář pro hlavičky a `lib` adresář pro knihovny.

   [!include[Linux Binary Archive Content](includes/linuxbinary-content.md)]

## <a name="sample-code"></a>Ukázka kódu

Následující kód rozpozná anglické řeči z mikrofonu.
Umístěte jej do souboru s názvem `quickstart-linux.cpp`:

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Linux/quickstart-linux/quickstart-linux.cpp#code)]

> [!IMPORTANT]
> Nahraďte klíč předplatného ten, který jste získali. <br>
> Nahraďte [oblast](regions.md) s přidruženou k odběru kategorií, například nahraďte `westus` pro bezplatné předplatné zkušební verze.

## <a name="building"></a>Sestavení

> [!NOTE]
> Nezapomeňte zkopírovat a vložit příkazy sestavení níže jako _jeden řádek_.

* Spusťte následující příkaz k vytvoření aplikace na x64 počítače:

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* Spusťte následující příkaz k vytvoření aplikace na x86 počítače:

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="running"></a>Spuštěno

Ke spuštění aplikace, musíte nakonfigurovat cestu knihovny pro zavaděč tak, aby odkazovaly do knihovny řeči SDK.

* Na x64 počítače, spusťte:

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
  ```

* Na x86 počítače, spusťte:

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
  ```

Spusťte aplikaci takto:

```sh
./quickstart-linux
```

Pokud všechno proběhne správně, měli byste vidět výstup podobný tomuto:

```text
Say something...
We recognized: What's the weather
```

## <a name="downloading-the-sample"></a>Stažení ukázky

Nejnovější sadu vzorků, najdete v článku [úložiště GitHub SDK ukázka kognitivní služby řeči](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Další postup

* Přejděte [ukázky stránky](samples.md) Další ukázky.
