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
ms.openlocfilehash: c5e12812a4305493be2cdc234946796b21dd26d8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558946"
---
# <a name="about-the-speech-sdk"></a>Sada Speech SDK

Sada Speech Software Development Kit (SDK) poskytuje vašim aplikacím přístup k funkcím služby Speech, což usnadňuje vývoj softwaru s podporou řeči. Sady SDK v současné době poskytují přístup k převodu **řeči na text**, převod **textu na řeč**, **překladu řeči**, **rozpoznávání záměrů**a přímém **line kanálu pro rozhraní bot**. Obecný přehled možností a podporovaných platforem najdete na [stránce](https://aka.ms/csspeech)dokumentace.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Získání sady SDK

### <a name="windows"></a>Windows

Pro Windows Podporujeme následující jazyky:

* C#(UWP a .NET) C++: Můžete odkazovat a používat nejnovější verzi našeho balíčku NuGet sady Speech SDK. Balíček obsahuje 32bitové a 64bitové klientské knihovny a spravované knihovny (.NET). Sada SDK lze nainstalovat v sadě Visual Studio pomocí NuGet. Vyhledejte **Microsoft.CognitiveServices.Speech**.

* Java: Můžete odkazovat a používat nejnovější verzi našeho balíčku Maven sady Speech SDK, který podporuje pouze systém Windows x64. V projektu Maven, přidejte `https://csspeechstorage.blob.core.windows.net/maven/` jako další úložiště a odkaz `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0` jako závislost.

### <a name="linux"></a>Linux

> [!NOTE]
> V současné době podporujeme jenom Ubuntu 16,04, Ubuntu 18,04 a Debian 9 na počítači (x86 nebo x64 pro C++ vývoj a x64 pro .NET Core, Java a Python).

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

* C#: Můžete odkazovat a používat nejnovější verzi našeho balíčku NuGet sady Speech SDK. K odkazování sadu SDK, přidejte následující odkaz na balíček do projektu:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.6.0" />
  ```

* Java: Můžete odkazovat a používat nejnovější verzi našeho balíčku Maven sady Speech SDK. V projektu Maven, přidejte `https://csspeechstorage.blob.core.windows.net/maven/` jako další úložiště a odkaz `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0` jako závislost.

* C++: Stáhněte si sadu SDK jako [balíček. tar](https://aka.ms/csspeech/linuxbinary) a rozbalte soubory v adresáři dle vašeho výběru. V následující tabulce jsou uvedeny strukturu složek sady SDK:

  |Cesta|Popis|
  |-|-|
  |`license.md`|Licence|
  |`ThirdPartyNotices.md`|Oznámení třetích stran|
  |`include`|Soubory hlaviček pro jazyky C a C++|
  |`lib/x64`|Nativní x64 knihovna pro propojení s vaší aplikací|
  |`lib/x86`|Nativní x86 knihovna pro propojení s vaší aplikací|

  Vytvoření aplikace, zkopírovat nebo přesunout do požadované binární soubory (a knihoven) do svého vývojového prostředí. Zahrnutí podle potřeby v procesu sestavení.

### <a name="android"></a>Android

Java SDK pro Android je zabalená jako [Knihovna AAR (Android Library)](https://developer.android.com/studio/projects/android-library), která zahrnuje nezbytné knihovny a požadovaná oprávnění pro Android. Je hostován v úložiště Maven v `https://csspeechstorage.blob.core.windows.net/maven/` jako balíček `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`.

Chcete-li využívají balíček z vašeho projektu Android Studio, proveďte následující změny:

* V souboru build.gradle na úrovni projektu přidejte následující text do `repository` části:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* V souboru build.gradle úrovni modulu, přidejte následující text do `dependencies` části:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.6.0'
  ```

Sada Java SDK je také součástí [sadou SDK pro řeč zařízení](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
