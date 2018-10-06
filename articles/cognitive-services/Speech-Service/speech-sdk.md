---
title: Informace o službách Cognitive Services řeči SDK
description: Přehled sady SDK k dispozici pro službu rozpoznávání řeči.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 09/24/2018
ms.author: v-jerkin
ms.openlocfilehash: c300c97691cbdd30a58342eafaf6b559a298c1db
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815203"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Informace o službách Cognitive Services řeči SDK

Cognitive Services řeči Software Development Kit (SDK) poskytuje vaše aplikace nativní přístup k funkce Speech service, což usnadňuje vývoj softwaru. V současné době sada SDK poskytuje přístup k **převod řeči na Text**, **překlad řeči**, a **rozpoznávání záměru**.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Získání sady SDK

### <a name="windows"></a>Windows

Pro Windows Podporujeme následující jazyky:

* C# jazyka C++ (UPW a .NET): lze odkazovat a využívat nejnovější verzi naší balíček NuGet sady SDK pro řeč. Balíček obsahuje 32bitové a 64bitové klientské knihovny a spravované knihovny (.NET). Sada SDK lze nainstalovat v sadě Visual Studio pomocí NuGet. Vyhledejte **Microsoft.CognitiveServices.Speech**.

* Java: Můžete odkazovat a využívat nejnovější verzi naší řeči SDK Maven balíček, který podporuje pouze Windows x64. V projektu Maven, přidejte `https://csspeechstorage.blob.core.windows.net/maven/` jako další úložiště a odkaz `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` jako závislost. 

### <a name="linux"></a>Linux

> [!NOTE]
> V současné době podporujeme pouze Ubuntu 16.04 na počítači (x86 nebo x64 pro vývoj v jazyce C++ a x64 pro jazyk Java a .NET Core).

Ujistěte se, že máte požadované kompilátor a knihovny instalaci spuštěním následujících příkazů prostředí:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#: Můžete odkazovat a používat nejnovější verzi naší balíček NuGet sady SDK pro řeč. K odkazování sadu SDK, přidejte následující odkaz na balíček do projektu:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.0.0" />
  ```

* Java: Můžete odkazovat a využívat nejnovější verzi naší řeči SDK Maven balíček. V projektu Maven, přidejte `https://csspeechstorage.blob.core.windows.net/maven/` jako další úložiště a odkaz `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` jako závislost. 

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

Sady Java SDK pro Android je zabalena jako [AAR (knihovna pro Android)](https://developer.android.com/studio/projects/android-library), který obsahuje potřebné knihovny, stejně jako požadovaná Android oprávnění k jeho používání. Je hostován v úložiště Maven v `https://csspeechstorage.blob.core.windows.net/maven/` jako balíček `com.microsoft.cognitiveservices.speech:client-sdk:1.0.0`.

Chcete-li využívají balíček z vašeho projektu Android Studio, proveďte následující změny:

* V souboru build.gradle na úrovni projektu přidejte následující text do `repository` části:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* V souboru build.gradle úrovni modulu, přidejte následující text do `dependencies` části:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.0.0'
  ```

Sada Java SDK je také součástí [sadou SDK pro řeč zařízení](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
