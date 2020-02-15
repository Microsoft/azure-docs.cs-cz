---
title: O sadě Speech SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Sada Speech Software Development Kit (SDK) poskytuje vašim aplikacím nativní přístup k funkcím služby Speech, což usnadňuje vývoj softwaru. Tento článek obsahuje další podrobnosti o sadě SDK pro Windows, Linux a Android.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 04546883e6742086fa05fc450920b1026702bd08
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212525"
---
# <a name="about-the-speech-sdk"></a>Informace o sadě Speech SDK

Rozpoznávání řeči Software Development Kit (SDK) poskytuje vaše aplikace přístup k funkce Speech service usnadňují vývoj softwaru podporou řeči. Sady SDK v současné době poskytují přístup k převodu **řeči na text**, převod **textu na řeč**, **překladu řeči**, **rozpoznávání záměrů**a **přímém line kanálu pro rozhraní bot**.

Zvuk můžete snadno zachytit z mikrofonu, číst z datového proudu nebo přistupovat ke zvukovým souborům z úložiště pomocí sady Speech SDK. Sada Speech SDK podporuje pro rozpoznávání řeči 16bitový zvuk WAV/PCM 16 bitů, 16 kHz/8 kHz pro rozpoznávání řeči v jednom kanálu. Další zvukové formáty jsou podporovány pomocí [koncového bodu REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) nebo [služby Batch přepisu](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)řeči pro text.

Obecný přehled možností a podporovaných platforem najdete na [stránce](https://aka.ms/csspeech)dokumentace.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Získání sady SDK

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

> [!WARNING]
> Sada Speech SDK podporuje Windows 10 nebo novější verze. Starší verze systému Windows nejsou **podporovány**.

Pro Windows Podporujeme následující jazyky:

* C# jazyka C++ (UPW a .NET): lze odkazovat a využívat nejnovější verzi naší balíček NuGet sady SDK pro řeč. Balíček obsahuje 32bitové a 64bitové klientské knihovny a spravované knihovny (.NET). Sadu SDK můžete nainstalovat do sady Visual Studio pomocí NuGet, [Microsoft. cognitiveservices Account. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech).

* Java: Můžete odkazovat a využívat nejnovější verzi naší řeči SDK Maven balíček, který podporuje pouze Windows x64. V projektu Maven přidejte `https://csspeechstorage.blob.core.windows.net/maven/` jako další úložiště a `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` na odkaz jako závislost.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

> [!NOTE]
> V současné době podporujeme jenom Ubuntu 16,04, Ubuntu 18,04 a Debian 9 u těchto cílových architektur:
> - x86, x64 a ARM64 pro C++ vývoj
> - x64 a ARM64 pro jazyk Java
> - x64 pro .NET Core a Python

Ujistěte se, že máte nainstalované požadované knihovny spuštěním následujících příkazů prostředí:

V Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

V Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

* C#: Můžete odkazovat a používat nejnovější verzi naší balíček NuGet sady SDK pro řeč. K odkazování sadu SDK, přidejte následující odkaz na balíček do projektu:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: Můžete odkazovat a využívat nejnovější verzi naší řeči SDK Maven balíček. V projektu Maven přidejte `https://csspeechstorage.blob.core.windows.net/maven/` jako další úložiště a `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` na odkaz jako závislost.

* C++: Stáhněte si sadu SDK jako [balíček. tar](https://aka.ms/csspeech/linuxbinary) a rozbalte soubory v adresáři dle vašeho výběru. V následující tabulce jsou uvedeny strukturu složek sady SDK:

  |Cesta|Popis|
  |-|-|
  |`license.md`|Licence|
  |`ThirdPartyNotices.md`|Oznámení třetích stran|
  |`include`|Soubory hlaviček pro jazyky C a C++|
  |`lib/x64`|Nativní x64 knihovna pro propojení s vaší aplikací|
  |`lib/x86`|Nativní x86 knihovna pro propojení s vaší aplikací|

  Vytvoření aplikace, zkopírovat nebo přesunout do požadované binární soubory (a knihoven) do svého vývojového prostředí. Zahrnutí podle potřeby v procesu sestavení.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Java SDK pro Android je zabalená jako [Knihovna AAR (Android Library)](https://developer.android.com/studio/projects/android-library), která zahrnuje nezbytné knihovny a požadovaná oprávnění pro Android. Hostuje se v úložišti Maven ve `https://csspeechstorage.blob.core.windows.net/maven/` jako `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`balíčku.

Chcete-li využívají balíček z vašeho projektu Android Studio, proveďte následující změny:

* V souboru Build. Gradle na úrovni projektu přidejte do části `repository` následující:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* V souboru Build. Gradle na úrovni modulu přidejte do části `dependencies` následující:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Sada Java SDK je také součástí [sady Speech SDK pro zařízení](speech-devices-sdk.md).

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Další kroky

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Podívejte se, jak rozpoznávat řeč vC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
