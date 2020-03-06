---
title: Vývoj aplikací pomocí sady Speech SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Naučte se, jak nasadit aplikaci, která používá sadu Speech SDK na podporovaných platformách.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 9507428e63b337b3d8419a833d03d081d494c522
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330800"
---
# <a name="ship-an-application"></a>Dodání aplikace

Podívejte se na [licenci sady Speech SDK](https://aka.ms/csspeech/license201809)a na [software třetí strany oznámení](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) při distribuci sady Azure Cognitive Services Speech SDK. Přečtěte si také [prohlášení o zásadách ochrany osobních údajů společnosti Microsoft](https://aka.ms/csspeech/privacy).

V závislosti na platformě existují různých závislostí ke spuštění vaší aplikace.

## <a name="windows"></a>Windows

Cognitive Services SDK řeči je testován na Windows 10 a Windows serveru 2016.

Sada Cognitive Services Speech SDK vyžaduje sadu [Microsoft Visual C++ Redistributable pro Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) v systému. Instalační programy pro nejnovější verzi `Microsoft Visual C++ Redistributable for Visual Studio 2019` můžete stáhnout zde:

- [Chyb](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [platformě](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Pokud vaše aplikace používá spravovaný kód, na cílovém počítači se vyžaduje `.NET Framework 4.6.1` nebo novější.

U mikrofon vstup je potřeba nainstalovat knihovny Media Foundation. Tyto knihovny jsou součástí sady Windows 10 a Windows serveru 2016. Mikrofon se nepoužívá jako vstupní zvuková zařízení, je možné použít sadu SDK pro řeč bez těchto knihoven.

Požadované sadou SDK pro řeč soubory je možné nasadit ve stejném adresáři jako vaši aplikaci. Tímto způsobem vaší aplikace můžete přistupovat přímo na knihovny. Ujistěte se, zda že jste vybrali správnou verzi (Win32/x64), která odpovídá vaší aplikace.

| Název | Funkce |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK pro nativní a spravovaná nasazení |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | vyžaduje se pro spravované nasazení                      |

> [!NOTE]
> Počínaje verzí 1.3.0 soubor `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (expedováno v předchozích verzích) už není potřeba. Tato funkce je teď integrovaná v základní sadě SDK.

> [!NOTE]
> Pro projekt aplikace model Windows Forms App (.NET Framework C# ) se ujistěte, že jsou knihovny zahrnuté v nastavení nasazení vašeho projektu. Tuto možnost můžete zaškrtnout v části `Properties -> Publish Section`. Klikněte na tlačítko `Application Files` a v rozevíracím seznamu vyhledejte odpovídající knihovny. Ujistěte se, že je hodnota nastavená na `Included`. Visual Studio bude soubor zahrnovat při publikování nebo nasazení projektu.

## <a name="linux"></a>Linux

Sada Speech SDK aktuálně podporuje distribuce Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8.
Pro nativní aplikaci je nutné dodávat knihovnu sady Speech SDK `libMicrosoft.CognitiveServices.Speech.core.so`.
Ujistěte se, zda že jste vybrali verzi (x86, x64), která odpovídá vaší aplikace. V závislosti na verzi systému Linux můžete také potřebovat zahrnout tyto závislosti:

- Sdílené knihovny knihovny GNU C (včetně knihovny programování vláken POSIX, `libpthreads`)
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

V RHEL/CentOS 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> V RHEL/CentOS 8 postupujte podle pokynů, [jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Podívejte se, jak rozpoznávat řeč vC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
