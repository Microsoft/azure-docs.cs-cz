---
title: O řeči SDK - řeč služby
titleSuffix: Azure Cognitive Services
description: Sada SDK (Speech Software Development Kit) poskytuje vašim aplikacím nativní přístup k funkcím služby Speech, což usnadňuje vývoj softwaru. Tento článek obsahuje další podrobnosti o sdk pro Windows, Linux a Android.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 984d2dfe07faa22756b4be167aa86a69806b1a84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331089"
---
# <a name="about-the-speech-sdk"></a>Informace o sadě Speech SDK

Sada SDK (Speech Software Development Kit) poskytuje vašim aplikacím přístup k funkcím služby Speech, což usnadňuje vývoj softwaru s podporou řeči. V současné době sady SDK poskytují přístup k **převodu řeči na text**, **převodu textu na řeč**, **překladu řeči**, rozpoznávání **záměru**a **kanálu přímé řeči rozhraní Bot Framework**.

Pomocí sady Speech SDK můžete snadno zachytit zvuk z mikrofonu, číst z datového proudu nebo přistupovat ke zvukovým souborům z úložiště. Sada Speech SDK podporuje 16bitový, 16kHz/8 kHz wav/PCM pro rozpoznávání řeči. Další zvukové formáty jsou podporovány pomocí [koncového bodu REST pro převod řeči na text](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) nebo [služby dávkového přepisu](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

Obecný přehled o možnostech a podporovaných platformách naleznete na [stránce zadání](https://aka.ms/csspeech)dokumentace .

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Získání sady SDK

# <a name="windows"></a>[Windows](#tab/windows)

> [!WARNING]
> Sada Speech SDK podporuje verze systému Windows 10 nebo novější. Starší verze systému Windows **nejsou podporovány**.

Pro Windows podporujeme následující jazyky:

* C# (UPW a .NET), C++: Můžete odkazovat a používat nejnovější verzi našeho balíčku Speech SDK NuGet. Balíček obsahuje 32bitové a 64bitové klientské knihovny a spravované knihovny (.NET). Sadu SDK lze nainstalovat v sadě Visual Studio pomocí nuget, [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech).

* Java: Můžete odkazovat a používat nejnovější verzi našeho balíčku Speech SDK Maven, který podporuje pouze Windows x64. V projektu Maven `https://csspeechstorage.blob.core.windows.net/maven/` přidejte jako další `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` úložiště a odkaz jako závislost.

# <a name="linux"></a>[Linux](#tab/linux)

> [!NOTE]
> V současné době podporujeme pouze Ubuntu 16.04, Ubuntu 18.04, Debian 9, Red Hat Enterprise Linux (RHEL) 8 a CentOS 8 na následujících cílových architekturách:
> - x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) a ARM64 (Debian/Ubuntu) pro vývoj C++
> - x64, ARM32 (Debian/Ubuntu) a ARM64 (Debian/Ubuntu) pro Javu
> - x64 pro jádro rozhraní .NET a Python

Ujistěte se, že máte nainstalované požadované knihovny spuštěním následujících příkazů prostředí:

Na Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Na Debianu 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

Na rhel/centos 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> Na RHEL/CentOS 8 postupujte podle [pokynů, jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

* C#: Můžete odkazovat a používat nejnovější verzi našeho balíčku Speech SDK NuGet. Chcete-li odkazovat na sadu SDK, přidejte do projektu následující odkaz na balíček:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: Můžete odkazovat a používat nejnovější verzi našeho balíčku Speech SDK Maven. V projektu Maven `https://csspeechstorage.blob.core.windows.net/maven/` přidejte jako další `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` úložiště a odkaz jako závislost.

* C++: Stáhněte sadu SDK jako [balíček .tar](https://aka.ms/csspeech/linuxbinary) a rozbalte soubory v adresáři podle vašeho výběru. V následující tabulce je uvedena struktura složek sady SDK:

  |Cesta|Popis|
  |-|-|
  |`license.md`|Licence|
  |`ThirdPartyNotices.md`|Oznámení třetích stran|
  |`include`|Soubory hlaviček pro C a C++|
  |`lib/x64`|Nativní knihovna x64 pro propojení s vaší aplikací|
  |`lib/x86`|Nativní knihovna x86 pro propojení s vaší aplikací|

  Chcete-li vytvořit aplikaci, zkopírujte nebo přesuňte požadované binární soubory (a knihovny) do vývojového prostředí. Zahrňte je podle potřeby v procesu sestavení.

# <a name="android"></a>[Android](#tab/android)

Java SDK pro Android je zabalen jako [AAR (Android Library)](https://developer.android.com/studio/projects/android-library), který obsahuje potřebné knihovny a požadovaná oprávnění Android. Je hostován v úložišti Maven `https://csspeechstorage.blob.core.windows.net/maven/` `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`jako balíček .

Chcete-li balíček využívat z projektu Android Studio, proveďte následující změny:

* V souboru build.gradle na úrovni projektu `repository` přidejte do oddílu následující:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* V souboru build.gradle na úrovni modulu `dependencies` přidejte do oddílu následující:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Sada Java SDK je také součástí [sady SDK pro řečová zařízení](speech-devices-sdk.md).

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Další kroky

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Podívejte se, jak rozpoznat řeč v C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
