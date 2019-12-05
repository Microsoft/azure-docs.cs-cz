---
title: Vývoj aplikací pomocí sady Speech SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Naučte se, jak nasadit aplikaci, která používá sadu Speech SDK na podporovaných platformách.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: f4200a0a43507a335f0e4e59e5a794d2bc4d6142
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816553"
---
# <a name="ship-an-application"></a>Dodání aplikace

Podívejte se [sadou SDK pro řeč licence](https://aka.ms/csspeech/license201809), stejně jako [oznámení k softwaru třetích stran](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) při distribuci Azure Cognitive Services sadou SDK pro řeč. Projděte si také, [prohlášení o ochraně osobních údajů Microsoft](https://aka.ms/csspeech/privacy).

V závislosti na platformě existují různých závislostí ke spuštění vaší aplikace.

## <a name="windows"></a>Windows

Cognitive Services SDK řeči je testován na Windows 10 a Windows serveru 2016.

Sada Cognitive Services Speech SDK vyžaduje sadu [Microsoft Visual C++ Redistributable pro Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) v systému. Instalační programy pro nejnovější verzi si můžete stáhnout `Microsoft Visual C++ Redistributable for Visual Studio 2019` tady:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Pokud vaše aplikace používá spravovaného kódu `.NET Framework 4.6.1` nebo novější je nutné na cílovém počítači.

U mikrofon vstup je potřeba nainstalovat knihovny Media Foundation. Tyto knihovny jsou součástí sady Windows 10 a Windows serveru 2016. Mikrofon se nepoužívá jako vstupní zvuková zařízení, je možné použít sadu SDK pro řeč bez těchto knihoven.

Požadované sadou SDK pro řeč soubory je možné nasadit ve stejném adresáři jako vaši aplikaci. Tímto způsobem vaší aplikace můžete přistupovat přímo na knihovny. Ujistěte se, zda že jste vybrali správnou verzi (Win32/x64), která odpovídá vaší aplikace.

| Name (Název) | Funkce |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK pro nativní a spravovaná nasazení |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | vyžaduje se pro spravované nasazení                      |

> [!NOTE]
> Počínaje verzí 1.3.0 soubor `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (expedováno v předchozích verzích) už není potřeba. Tato funkce je teď integrovaná v základní sadě SDK.

> [!NOTE]
> Pro projekt aplikace model Windows Forms App (.NET Framework C# ) se ujistěte, že jsou knihovny zahrnuté v nastavení nasazení vašeho projektu. Tuto možnost můžete zaškrtnout v části `Properties -> Publish Section`. Klikněte na tlačítko `Application Files` a v rozevíracím seznamu vyhledejte odpovídající knihovny. Ujistěte se, že je hodnota nastavená na `Included`. Visual Studio bude soubor zahrnovat při publikování nebo nasazení projektu.

## <a name="linux"></a>Linux

Sada Speech SDK aktuálně podporuje distribuce Ubuntu 16,04, Ubuntu 18,04 a Debian 9.
Pro nativní aplikaci, je třeba dodávat knihovně sadou SDK pro řeč `libMicrosoft.CognitiveServices.Speech.core.so`.
Ujistěte se, zda že jste vybrali verzi (x86, x64), která odpovídá vaší aplikace. V závislosti na verzi systému Linux můžete také potřebovat zahrnout tyto závislosti:

- Sdílené knihovny knihovna GNU C (včetně knihovny POSIX vlákna programování `libpthreads`)
- Knihovna OpenSSL (`libssl.so.1.0.0` nebo `libssl.so.1.0.2`)
- Sdílená knihovna pro aplikace ALSA (`libasound.so.2`)

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

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Zjistěte, jak rozpoznávat řeč v jazyce C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
