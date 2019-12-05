---
title: O sadě Speech SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Sada Speech Software Development Kit (SDK) poskytuje vašim aplikacím nativní přístup k funkcím služby Speech, což usnadňuje vývoj softwaru. Tento článek obsahuje další podrobnosti o sadě SDK pro Windows, Linux a Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 58738c9180fcc45a6958ea61b26d898caf4f3061
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819115"
---
# <a name="about-the-speech-sdk"></a>Informace o sadě Speech SDK

Rozpoznávání řeči Software Development Kit (SDK) poskytuje vaše aplikace přístup k funkce Speech service usnadňují vývoj softwaru podporou řeči. Sady SDK v současné době poskytují přístup k převodu **řeči na text**, převod **textu na řeč**, **překladu řeči**, **rozpoznávání záměrů**a **přímém line kanálu pro rozhraní bot**. Obecný přehled možností a podporovaných platforem najdete na [stránce](https://aka.ms/csspeech)dokumentace.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Získání sady SDK

### <a name="windows"></a>Windows

Pro Windows Podporujeme následující jazyky:

* C# jazyka C++ (UPW a .NET): lze odkazovat a využívat nejnovější verzi naší balíček NuGet sady SDK pro řeč. Balíček obsahuje 32bitové a 64bitové klientské knihovny a spravované knihovny (.NET). Sada SDK lze nainstalovat v sadě Visual Studio pomocí NuGet. Vyhledejte **Microsoft.CognitiveServices.Speech**.

* Java: Můžete odkazovat a využívat nejnovější verzi naší řeči SDK Maven balíček, který podporuje pouze Windows x64. V projektu Maven, přidejte `https://csspeechstorage.blob.core.windows.net/maven/` jako další úložiště a odkaz `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` jako závislost.

### <a name="linux"></a>Linux

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
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.7.0" />
  ```

* Java: Můžete odkazovat a využívat nejnovější verzi naší řeči SDK Maven balíček. V projektu Maven, přidejte `https://csspeechstorage.blob.core.windows.net/maven/` jako další úložiště a odkaz `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` jako závislost.

* Jazyk C++: Stáhněte si sadu SDK jako [instalačního balíčku .tar](https://aka.ms/csspeech/linuxbinary) a rozbalte soubory v adresáři podle vašeho výběru. V následující tabulce jsou uvedeny strukturu složek sady SDK:

  |Cesta|Popis|
  |-|-|
  |`license.md`|Licence|
  |`ThirdPartyNotices.md`|Oznámení třetích stran|
  |`include`|Soubory hlaviček pro jazyky C a C++|
  |`lib/x64`|Nativní x64 knihovna pro propojení s vaší aplikací|
  |`lib/x86`|Nativní x86 knihovna pro propojení s vaší aplikací|

  Vytvoření aplikace, zkopírovat nebo přesunout do požadované binární soubory (a knihoven) do svého vývojového prostředí. Zahrnutí podle potřeby v procesu sestavení.

### <a name="android"></a>Android

Java SDK pro Android je zabalená jako [Knihovna AAR (Android Library)](https://developer.android.com/studio/projects/android-library), která zahrnuje nezbytné knihovny a požadovaná oprávnění pro Android. Je hostován v úložiště Maven v `https://csspeechstorage.blob.core.windows.net/maven/` jako balíček `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Chcete-li využívají balíček z vašeho projektu Android Studio, proveďte následující změny:

* V souboru build.gradle na úrovni projektu přidejte následující text do `repository` části:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* V souboru build.gradle úrovni modulu, přidejte následující text do `dependencies` části:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Sada Java SDK je také součástí [sadou SDK pro řeč zařízení](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Další kroky

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
