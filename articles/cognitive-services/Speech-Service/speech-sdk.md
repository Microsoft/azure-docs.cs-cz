---
title: Informace o službách Cognitive Services řeči SDK
description: Přehled sady SDK k dispozici pro službu rozpoznávání řeči.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jerkin
ms.openlocfilehash: 20fe0c3501e562584cd7762555479457a34a9297
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281319"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Informace o službách Cognitive Services řeči SDK

Cognitive Services řeči Software Development Kit (SDK) poskytuje vaše aplikace nativní přístup k funkcím služby řeči, což usnadňuje vývoj softwaru. V současné době sada SDK poskytuje přístup k **převod řeči na Text**, **překlad řeči**, a **rozpoznávání záměru**.

[!include[Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-windows-sdk"></a>Získat Windows SDK

Verze Windows sadou SDK pro řeč zahrnuje 32bitové a 64bitové klientské knihovny jazyka C/C++ a spravované knihovny (.NET) pro použití s C#. Sada SDK lze nainstalovat v sadě Visual Studio pomocí nástroje NuGet; jednoduše vyhledejte `Microsoft.CognitiveServices.Speech`.

## <a name="get-the-linux-sdk"></a>Získání sady SDK pro Linux

Ujistěte se, že máte požadované kompilátor a knihovny spuštěním následujících příkazů prostředí:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> Tyto pokyny předpokládají, že používáte Ubuntu 16.04 na počítači (x86 nebo x64). Na různé verze Ubuntu nebo jiné distribuce Linuxu přizpůsobit tyto kroky pro vaše prostředí.

Potom [stáhnout sadu SDK](https://aka.ms/csspeech/linuxbinary) a rozbalte soubory do adresáře podle vašeho výběru. Tato tabulka zobrazuje strukturu složek sady SDK.

|Cesta|Popis|
|-|-|
|`license.md`|Licence|
|`third-party-notices.md`|Oznámení třetích stran|
|`include`|Soubory hlaviček pro jazyky C a C++|
|`lib/x64`|Nativní x64 knihovna pro propojení s vaší aplikací|
|`lib/x86`|Nativní x86 knihovna pro propojení s vaší aplikací|

K vytvoření aplikace, zkopírovat nebo přesunout požadované binární soubory (a knihovny) do svého vývojového prostředí a zahrnout je podle potřeby do procesu sestavení.

## <a name="get-the-java-sdk"></a>Získání sady Java SDK

Sady Java SDK pro Android je zabalena jako [AAR (knihovna pro Android)](https://developer.android.com/studio/projects/android-library), který obsahuje potřebné knihovny, stejně jako požadovaná Android oprávnění k jeho používání.
Je hostován v úložiště Maven v `https://csspeechstorage.blob.core.windows.net/maven/` jako balíček `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`.
Spotřebě balíček z vašeho projektu Android Studio proveďte následující změny:

* Na úrovni projektu `build.gradle` přidejte následující kód do `repository` části:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* V úrovni modulu `build.gradle` přidejte následující kód do `dependencies` části:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.5.0'
  ```

Sada Java SDK je také součástí [sadou SDK pro řeč zařízení](speech-devices-sdk.md).

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
