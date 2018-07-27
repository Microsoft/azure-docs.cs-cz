---
title: Azure Cognitive Services, Cognitive Services – Reference k rozhraní API dokumentace k – kurzy, sada SDK API pro zpracování řeči
description: Další informace o vytváření a vývoj aplikací s využitím Cognitive Services SDK řeči
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: df6224208e93f4a332247440933b3a4d11c1bb43
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283027"
---
# <a name="shipping-an-application"></a>Přesouvání aplikace

Podívejte se [sadou SDK pro řeč licence](license.md), stejně jako [oznámení softwaru třetích stran](third-party-notices.md) při distribuci Cognitive Services SDK řeči. Projděte si také, [prohlášení o ochraně osobních údajů Microsoft](https://aka.ms/csspeech/privacy).

V závislosti na platformě existují různých závislostí ke spuštění vaší aplikace.

## <a name="windows"></a>Windows

Cognitive Services SDK řeči je testován na Windows 10 a Windows serveru 2016.

Cognitive Services řeči SDK vyžaduje [Microsoft Visual C++ Redistributable pro Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) v systému. Instalační programy pro nejnovější verzi si můžete stáhnout `Microsoft Visual C++ Redistributable for Visual Studio 2017` tady:

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

Pokud vaše aplikace používá spravovaného kódu `.Net Framework 4.6.1` nebo novější je nutné na cílovém počítači.

Knihovny Media Foundation pro vstup mikrofon, potřeba nainstalovat. Tyto knihovny jsou součástí sady Windows 10 a Windows serveru 2016. Mikrofon se nepoužívá jako vstupní zvuková zařízení, je možné použít sadu SDK pro řeč bez těchto knihoven.

Požadované sadou SDK pro řeč soubory je možné nasadit ve stejném adresáři jako vaši aplikaci. Tímto způsobem vaší aplikace můžete přistupovat přímo na knihovny. Ujistěte se, zda že jste vybrali správnou verzi (Win32/x64) odpovídající vaší aplikace.

| Název | Funkce
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK pro nativní a spravovaná nasazení
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | vyžaduje se pro spravované nasazení
| `Microsoft.CognitiveServices.Speech.csharp.dll` | vyžaduje se pro spravované nasazení

## <a name="linux"></a>Linux

Pro nativní aplikace, budete potřebovat k odeslání knihovně sadou SDK pro řeč `libMicrosoft.CognitiveServices.Speech.core.so`.
Ujistěte se, že při výběru verze (x86, x64) odpovídající vaší aplikace. V závislosti na verzi systému Linux budete také muset zahrnovat následující závislosti:

* Sdílené knihovny knihovna GNU C (včetně knihovny POSIX vlákna programování `libpthreads`)
* Knihovny OpenSSL (`libssl.so.1.0.0`)
* Knihovna cURL (`libcurl.so.4`)
* Sdílená knihovna pro aplikace ALSA (`libasound.so.2`)

Na Ubuntu 16.04 například knihovny GNU C musí již být nainstalováno ve výchozím nastavení. Poslední tři můžete nainstalovat pomocí těchto příkazů:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
