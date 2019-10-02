---
title: O sadě Speech SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Sada Speech Software Development Kit (SDK) poskytuje vašim aplikacím nativní přístup k funkcím služby Speech, což usnadňuje vývoj softwaru. V tomto článku najdete další podrobnosti o sadě SDK pro Windows, Linux a Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: ee505cc78c16d85640c7e13541272f1bafe30061
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71800004"
---
# <a name="about-the-speech-sdk"></a>Sada Speech SDK

Sada Speech Software Development Kit (SDK) poskytuje vašim aplikacím přístup k funkcím služby Speech, což usnadňuje vývoj softwaru s podporou řeči. Sady SDK v současné době poskytují přístup k převodu **řeči na text**, převod **textu na řeč**, **překladu řeči**, **rozpoznávání záměrů**a **přímém line kanálu pro rozhraní bot**. Obecný přehled možností a podporovaných platforem najdete na [stránce](https://aka.ms/csspeech)dokumentace.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Získat sadu SDK

### <a name="windows"></a>Windows

V případě systému Windows podporujeme následující jazyky:

* C#(UWP a .NET), C++: můžete odkazovat a používat nejnovější verzi našeho balíčku NuGet sady Speech SDK. Balíček zahrnuje 32 a 64 klientských knihoven a spravovaných knihoven (.NET). Sadu SDK můžete nainstalovat do sady Visual Studio pomocí NuGet. Vyhledejte **Microsoft. cognitiveservices Account. Speech**.

* Java: můžete odkazovat a používat nejnovější verzi našeho balíčku Maven sady Speech SDK, který podporuje jenom Windows x64. V projektu Maven přidejte `https://csspeechstorage.blob.core.windows.net/maven/` jako další úložiště a odkaz `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` jako závislost.

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

* C#: Můžete odkazovat a používat nejnovější verzi našeho balíčku NuGet sady Speech SDK. Chcete-li odkazovat na sadu SDK, přidejte do projektu následující odkaz na balíček:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.7.0" />
  ```

* Java: můžete odkazovat a používat nejnovější verzi našeho balíčku Maven sady Speech SDK. V projektu Maven přidejte `https://csspeechstorage.blob.core.windows.net/maven/` jako další úložiště a odkaz `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` jako závislost.

* C++: Stáhněte si sadu SDK jako [balíček. tar](https://aka.ms/csspeech/linuxbinary) a rozbalte soubory v adresáři dle vašeho výběru. Struktura složek sady SDK je znázorněna v následující tabulce:

  |Cesta|Popis|
  |-|-|
  |`license.md`|Licence|
  |`ThirdPartyNotices.md`|Oznámení třetích stran|
  |`include`|Hlavičkové soubory pro C aC++|
  |`lib/x64`|Nativní knihovna x64 pro propojení s vaší aplikací|
  |`lib/x86`|Nativní knihovna x86 pro propojení s vaší aplikací|

  Chcete-li vytvořit aplikaci, zkopírujte nebo přesuňte požadované binární soubory (a knihovny) do vývojového prostředí. Zahrňte je podle požadavků v procesu sestavení.

### <a name="android"></a>Android

Java SDK pro Android je zabalená jako [Knihovna AAR (Android Library)](https://developer.android.com/studio/projects/android-library), která zahrnuje nezbytné knihovny a požadovaná oprávnění pro Android. Hostuje se v úložišti Maven na `https://csspeechstorage.blob.core.windows.net/maven/` jako balíček `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Chcete-li balíček využívat z Android Studio projektu, proveďte následující změny:

* V souboru Build. Gradle na úrovni projektu přidejte následující část do oddílu `repository`:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* V souboru Build. Gradle na úrovni modulu přidejte do části `dependencies` následující:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Sada Java SDK je také součástí [sady Speech SDK pro zařízení](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Další kroky

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Podívejte se, jak rozpoznávat řeč vC#](quickstart-csharp-dotnet-windows.md)
