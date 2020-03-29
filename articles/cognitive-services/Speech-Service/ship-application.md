---
title: Vývoj aplikací pomocí sady Speech SDK – služba Řeč
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak nasadit aplikaci, která používá sadku Speech SDK na podporovaných platformách.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330800"
---
# <a name="ship-an-application"></a>Dodání aplikace

Dodržujte [licenci sady Speech SDK](https://aka.ms/csspeech/license201809)a [také oznámení softwaru jiných výrobců](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) při distribuci sady Azure Cognitive Services Speech SDK. Přečtěte si také [Prohlášení společnosti Microsoft o zásadách ochrany osobních údajů](https://aka.ms/csspeech/privacy).

V závislosti na platformě existují různé závislosti pro spuštění aplikace.

## <a name="windows"></a>Windows

Sada SDK pro rozpoznávání řeči služeb Cognitive Services je testována ve Windows 10 a Windows Server 2016.

Sada SDK pro rozpoznávání řeči služeb Cognitive Services vyžaduje sadu [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) v systému. Instalační programy si můžete stáhnout pro `Microsoft Visual C++ Redistributable for Visual Studio 2019` nejnovější verzi zde:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Pokud vaše aplikace používá `.NET Framework 4.6.1` spravovaný kód, nebo později je požadováno v cílovém počítači.

Pro vstup mikrofonu musí být nainstalovány knihovny Media Foundation. Tyto knihovny jsou součástí Windows 10 a Windows Server 2016. Je možné použít sadu Speech SDK bez těchto knihoven, pokud se jako vstupní zvukové zařízení nepoužívá mikrofon.

Požadované soubory sady Speech SDK lze nasadit ve stejném adresáři jako aplikace. Tímto způsobem může vaše aplikace přímo přistupovat ke knihovnám. Ujistěte se, že jste vybrali správnou verzi (Win32/x64), která odpovídá vaší aplikaci.

| Name (Název) | Funkce |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | Základní sada SDK, požadovaná pro nativní a spravované nasazení |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Požadováno pro spravované nasazení                      |

> [!NOTE]
> Počínaje verzí 1.3.0 soubor `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dodáván v předchozích verzích) již není potřeba. Funkce je nyní integrována do základní sady SDK.

> [!NOTE]
> Pro projekt C# aplikace Windows Forms (.NET Framework) zkontrolujte, zda jsou knihovny zahrnuty v nastavení nasazení projektu. Můžete zkontrolovat v `Properties -> Publish Section`části . Klikněte `Application Files` na tlačítko a vyhledejte odpovídající knihovny ze seznamu posunout dolů. Zkontrolujte, zda je `Included`hodnota nastavena na hodnotu . Visual Studio bude obsahovat soubor při publikování nebo nasazení projektu.

## <a name="linux"></a>Linux

Speech SDK v současné době podporuje distribuce Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8.
Pro nativní aplikaci je třeba dobýt knihovnu sady Speech SDK . `libMicrosoft.CognitiveServices.Speech.core.so`
Ujistěte se, že jste vybrali verzi (x86, x64), která odpovídá vaší aplikaci. V závislosti na verzi Linuxu může být také nutné zahrnout následující závislosti:

- Sdílené knihovny knihovny GNU C (včetně knihovny POSIX Threads Programming library), `libpthreads`
- Knihovna OpenSSL`libssl.so.1.0.0` `libssl.so.1.0.2`( nebo )
- Sdílená knihovna pro aplikace`libasound.so.2`ALSA ( )

Na Ubuntu by měly být knihovny GNU C již ve výchozím nastavení nainstalovány. Poslední tři lze nainstalovat pomocí těchto příkazů:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Na Debianu 9 nainstalujte tyto balíčky:

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

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Podívejte se, jak rozpoznat řeč v C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
