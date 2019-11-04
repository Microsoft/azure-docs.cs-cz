---
title: Vývoj aplikací pomocí sady Speech SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Naučte se vytvářet aplikace pomocí sady Speech SDK.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 166ae00085f07ef24d746b60947a31e7680a0f00
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490996"
---
# <a name="ship-an-application"></a>Dodání aplikace

Podívejte se na [licenci sady Speech SDK](https://aka.ms/csspeech/license201809)a na [software třetí strany oznámení](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) při distribuci sady Azure Cognitive Services Speech SDK. Přečtěte si také [prohlášení o zásadách ochrany osobních údajů společnosti Microsoft](https://aka.ms/csspeech/privacy).

V závislosti na platformě existují různé závislosti pro spuštění vaší aplikace.

## <a name="windows"></a>Windows

Sada Cognitive Services Speech SDK je testována ve Windows 10 a v systému Windows Server 2016.

Sada Cognitive Services Speech SDK vyžaduje sadu [Microsoft Visual C++ Redistributable pro Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) v systému. Instalační programy pro nejnovější verzi `Microsoft Visual C++ Redistributable for Visual Studio 2019` můžete stáhnout zde:

- [Chyb](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [platformě](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Pokud vaše aplikace používá spravovaný kód, na cílovém počítači se vyžaduje `.NET Framework 4.6.1` nebo novější.

V případě vstupu přes mikrofon musí být nainstalovány knihovny Media Foundation. Tyto knihovny jsou součástí systémů Windows 10 a Windows Server 2016. Je možné použít sadu Speech SDK bez těchto knihoven, pokud se jako vstupní zvukové zařízení nepoužívá mikrofon.

Požadované soubory sady Speech SDK lze nasadit do stejného adresáře jako vaše aplikace. Tímto způsobem může aplikace získat přímý přístup ke knihovnám. Ujistěte se, že jste vybrali správnou verzi (Win32/x64), která odpovídá vaší aplikaci.

| Name (Název) | Funkce
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Základní sada SDK požadovaná pro nativní a spravované nasazení
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Požadováno pro spravované nasazení

>[!NOTE]
> Počínaje verzí 1.3.0 soubor `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (expedováno v předchozích verzích) už není potřeba. Tato funkce je teď integrovaná v základní sadě SDK.

>[!NOTE]
> Pro projekt aplikace model Windows Forms App (.NET Framework C# ) se ujistěte, že jsou knihovny zahrnuté v nastavení nasazení vašeho projektu. Tuto možnost můžete zaškrtnout v části `Properties -> Publish Section`. Klikněte na tlačítko `Application Files` a v rozevíracím seznamu vyhledejte odpovídající knihovny. Ujistěte se, že je hodnota nastavená na `Included`. Visual Studio bude soubor zahrnovat při publikování nebo nasazení projektu.

## <a name="linux"></a>Linux

Sada Speech SDK aktuálně podporuje distribuce Ubuntu 16,04, Ubuntu 18,04 a Debian 9.
Pro nativní aplikaci je nutné dodávat knihovnu sady Speech SDK `libMicrosoft.CognitiveServices.Speech.core.so`.
Ujistěte se, že jste vybrali verzi (x86, x64), která odpovídá vaší aplikaci. V závislosti na verzi pro Linux budete taky muset zahrnout tyto závislosti:

* Sdílené knihovny knihovny GNU C (včetně knihovny programování vláken POSIX, `libpthreads`)
* Knihovna OpenSSL (`libssl.so.1.0.0` nebo `libssl.so.1.0.2`)
* Sdílená knihovna pro aplikace ALSA (`libasound.so.2`)

V Ubuntu by ve výchozím nastavení měly být knihovny GNU C nainstalované. Poslední tři lze nainstalovat pomocí těchto příkazů:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

V Debian 9 nainstalujte tyto balíčky:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>Další kroky

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Podívejte se, jak rozpoznávat řeč vC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
