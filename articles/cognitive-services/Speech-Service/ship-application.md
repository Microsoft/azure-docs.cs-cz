---
title: Referenční dokumentace rozhraní API pro rozpoznávání řeči rozhraní API sady SDK dokumentace k – kurzy, služby Azure kognitivní služby kognitivní | Microsoft Docs
description: Naučte se vytvářet a vyvíjet aplikace s kognitivní řeči SDK služby
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: d410dda09fdd30181b633c454b1d44610b10c472
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "35343957"
---
# <a name="shipping-an-application"></a>Přesouvání aplikace

Sledovat [řeči SDK licence](license.md), a taky [oznámení softwaru třetích stran](third-party-notices.md) při distribuci kognitivní řeči Services SDK. Projděte si také téma [prohlášení o ochraně osobních údajů Microsoft](https://aka.ms/csspeech/privacy).

V závislosti na platformě existují různé závislosti spuštění vaší aplikace.

## <a name="windows"></a>Windows

Kognitivní Services řeči SDK je testován na Windows 10 a Windows Server 2016.

Kognitivní řeči Services SDK vyžaduje [Microsoft Visual C++ Redistributable pro Visual Studio 2017](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) v systému. Instalační programy pro nejnovější verzi si můžete stáhnout `Microsoft Visual C++ Redistributable for Visual Studio 2017` tady:

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

Pokud vaše aplikace používá spravovaného kódu `.Net Framework 4.6.1` nebo novější je nutné na cílovém počítači.

Pro vstup mikrofon musíte nainstalovat v knihovnách Media Foundation. Tyto knihovny jsou součástí Windows 10 a Windows Server 2016. Mikrofon není používána jako vstupní zvuková zařízení, je možné použít sadu SDK řeči bez tyto knihovny.

Požadované soubory řeči SDK se dá nasadit ve stejném adresáři jako aplikace. Tímto způsobem vaše aplikace může přistupovat přímo na knihovny. Ujistěte se, zda že jste vybrali správnou verzi (Win32/x64) odpovídající vaší aplikace.

| Název | Funkce
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | základní sady SDK, vyžaduje se pro nativní a spravovaná nasazení
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | vyžaduje se pro spravované nasazení
| `Microsoft.CognitiveServices.Speech.csharp.dll` | vyžaduje se pro spravované nasazení

## <a name="linux"></a>Linux

Pro nativní aplikace musíte dodávat knihovny sady SDK řeči `libMicrosoft.CognitiveServices.Speech.core.so`.
Zkontrolujte, zda jste vybrali verze (x86, x64) odpovídající vaší aplikace. V závislosti na verzi systému Linux může také musíte zahrnout tyto závislosti:

* Sdílené knihovny GNU knihovny jazyka C (včetně knihovně POSIX vláken programování `libpthreads`)
* Knihovny OpenSSL (`libssl.so.1.0.0`)
* Knihovna cURL (`libcurl.so.4`)
* Sdílenou knihovnu pro aplikace ALSA (`libasound.so.2`)

Na Ubuntu 16.04 například knihovny GNU C musí již být nainstalován ve výchozím nastavení. Poslední tři můžete nainstalovat pomocí těchto příkazů:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>Další postup

* [Získat zkušební verze předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
* [Informace o tom, rozpoznávat řeč v jazyce C#](quickstart-csharp-windows.md)
