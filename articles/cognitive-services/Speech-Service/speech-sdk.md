---
title: O Speech Service SDK – hlasové služby
titleSuffix: Azure Cognitive Services
description: Speech Service Software Development Kit (SDK) poskytuje vaše aplikace nativní přístup k funkce Speech service, což usnadňuje vývoj softwaru. Tento článek obsahuje další podrobnosti o sadě SDK pro Windows, Linux a Android.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 1c29ed47e499ee23fab9f3b34e3974f479e3dbb7
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53604267"
---
# <a name="about-the-speech-service-sdk"></a>O sady SDK služby pro zpracování řeči

Speech Service Software Development Kit (SDK) poskytuje vaše aplikace nativní přístup k funkce Speech service, což usnadňuje vývoj softwaru. V současné době sada SDK poskytuje přístup k **převod řeči na Text**, **překlad řeči**, a **rozpoznávání záměru**.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Získání sady SDK

### <a name="windows"></a>Windows

Pro Windows Podporujeme následující jazyky:

* C#Jazyk C++ (UPW a .NET): Můžete odkazovat a využívat nejnovější verzi naší balíček NuGet sady SDK pro řeč. Balíček obsahuje 32bitové a 64bitové klientské knihovny a spravované knihovny (.NET). Sada SDK lze nainstalovat v sadě Visual Studio pomocí NuGet. Vyhledejte **Microsoft.CognitiveServices.Speech**.

* Java: Můžete odkazovat a využívat nejnovější verzi naší řeči SDK Maven balíček, který podporuje pouze Windows x64. V projektu Maven, přidejte `https://csspeechstorage.blob.core.windows.net/maven/` jako další úložiště a odkaz `com.microsoft.cognitiveservices.speech:client-sdk:1.2.0` jako závislost.

### <a name="linux"></a>Linux

> [!NOTE]
> V současné době podporujeme pouze Ubuntu 16.04 a 18.04 na počítači (x86 nebo x64 pro vývoj v jazyce C++ a x64 pro jazyk Java a .NET Core).

Ujistěte se, že máte požadované kompilátor a knihovny instalaci spuštěním následujících příkazů prostředí:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#: Můžete odkazovat a využívat nejnovější verzi naší balíček NuGet sady SDK pro řeč. K odkazování sadu SDK, přidejte následující odkaz na balíček do projektu:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.2.0" />
  ```

* Java: Můžete odkazovat a využívat nejnovější verzi naší řeči SDK Maven balíček. V projektu Maven, přidejte `https://csspeechstorage.blob.core.windows.net/maven/` jako další úložiště a odkaz `com.microsoft.cognitiveservices.speech:client-sdk:1.2.0` jako závislost.

* JAZYK C++: Stáhnout sadu SDK jako [instalačního balíčku .tar](https://aka.ms/csspeech/linuxbinary) a rozbalte soubory v adresáři podle vašeho výběru. V následující tabulce jsou uvedeny strukturu složek sady SDK:

  |Cesta|Popis|
  |-|-|
  |`license.md`|Licence|
  |`ThirdPartyNotices.md`|Oznámení třetích stran|
  |`include`|Soubory hlaviček pro jazyky C a C++|
  |`lib/x64`|Nativní x64 knihovna pro propojení s vaší aplikací|
  |`lib/x86`|Nativní x86 knihovna pro propojení s vaší aplikací|

  Vytvoření aplikace, zkopírovat nebo přesunout do požadované binární soubory (a knihoven) do svého vývojového prostředí. Zahrnutí podle potřeby v procesu sestavení.

### <a name="android"></a>Android

Sady Java SDK pro Android je zabalena jako [AAR (knihovna pro Android)](https://developer.android.com/studio/projects/android-library), který obsahuje potřebné knihovny a požadovaná oprávnění pro Android. Je hostován v úložiště Maven v `https://csspeechstorage.blob.core.windows.net/maven/` jako balíček `com.microsoft.cognitiveservices.speech:client-sdk:1.2.0`.

Chcete-li využívají balíček z vašeho projektu Android Studio, proveďte následující změny:

* V souboru build.gradle na úrovni projektu přidejte následující text do `repository` části:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* V souboru build.gradle úrovni modulu, přidejte následující text do `dependencies` části:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.2.0'
  ```

Sada Java SDK je také součástí [sadou SDK pro řeč zařízení](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
