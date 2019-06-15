---
title: Zvuk kodek komprimovaný Stream se sadou SDK pro řeč – hlasové služby
titleSuffix: Azure Cognitive Services
description: Naučíte se Streamovat komprimované audio hlasové služby Azure se sadou SDK pro řeč. K dispozici pro C++, C#a v Javě pro Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: amishu
ms.openlocfilehash: 8717dad27786450e4275c9fd91523c0e9379f375
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073058"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Pomocí kodek komprimované zvukového vstupu se sadou SDK pro řeč

Sada SDK řeči **komprimované zvuk vstupní Stream** rozhraní API poskytuje způsob, jak datový proud stream komprimované audio Speech Service s využitím PullStream nebo PushStream.

> [!IMPORTANT]
> Vysílání datového proudu komprimované zvuku se podporuje jenom pro C++, C#a v Javě v Linuxu (Ubuntu 16.04, Ubuntu 18.04, Debian 9).
> Rozpoznávání řeči SDK verze 1.4.0 nebo novější je povinný.

Wav/PCM najdete v dokumentaci hlavní linie řeči.  Mimo wav/PCM jsou podporovány následující kodek komprimované vstupní formáty:

- MP3
- DÍLE/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>Požadavky na použití kodek komprimované zvukového vstupu

Instalace těchto doplňkových závislostí pro použití komprimované zvukového vstupu se sadou SDK pro řeč pro Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Příklad kódu pomocí kodek komprimované zvukového vstupu

Abyste mohli Streamovat v komprimované zvukový formát, který se hlasové služby, vytvořte `PullAudioInputStream` nebo `PushAudioInputStream`. Potom vytvořte `AudioConfig` z instance třídy datového proudu, určující formát komprese datového proudu.

Předpokládejme, že máte vstupní datový proud třídu s názvem `myPushStream` a používáte DÍLE/OGG. Váš kód může vypadat takto:

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
