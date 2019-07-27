---
title: Kodek Stream komprimovaný zvuk se sadou Speech SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Naučte se streamovat komprimovaný zvuk do služby Azure Speech Services pomocí sady Speech SDK. K C++dispozici C#pro systémy, a Java pro Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: b29b42dea9522526d49c1bda017a522855946def
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559551"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Použití komprimovaného zvukového vstupu pomocí kodeku se sadou Speech SDK

Rozhraní API pro **komprimovaný zvuk vstupního streamu** sady Speech SDK poskytuje způsob, jak streamovat komprimovaný zvuk do služby pro rozpoznávání řeči pomocí PullStream nebo PushStream.

> [!IMPORTANT]
> Komprimovaný zvuk streamování je podporovaný jenom C++pro C#systémy, a Java v systému Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9).
> Je vyžadována sada Speech SDK verze 1.4.0 nebo vyšší.

Informace o WAV/PCM najdete v dokumentaci k hlavní Speech.  Mimo zvuk WAV/PCM jsou podporovány následující formáty komprimované vstupní hodnoty kodeku:

- MP3
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>Požadavky na použití komprimovaného zvukového vstupu kodeku

Nainstalujte tyto další závislosti pro použití komprimovaného zvukového vstupu se sadou Speech SDK pro Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Příklad kódu pomocí komprimovaného zvukového vstupu kodeku

Pokud chcete streamovat v komprimovaném zvukovém formátu ke službám Speech `PullAudioInputStream` , `PushAudioInputStream`vytvořte nebo. Pak vytvořte `AudioConfig` z instance vaší třídy streamu a určete formát komprese datového proudu.

Pojďme předpokládat, že máte třídu vstupního datového proudu nazvanou `myPushStream` a používáte Opus/OGG. Váš kód může vypadat takto:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>Další postup

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
