---
title: Kodek Stream komprimovaný zvuk se sadou Speech SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Naučte se streamovat komprimovaný zvuk do služby Azure Speech Services pomocí sady Speech SDK. K C++dispozici C#pro systémy, a Java pro Linux, Java v Androidu a objektivní-C v iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 668964c597b8d748220cbeec68e0ba68300cb406
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464359"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Použití komprimovaného zvukového vstupu pomocí kodeku se sadou Speech SDK

Rozhraní API pro **komprimovaný zvuk vstupního streamu** sady Speech SDK poskytuje způsob, jak streamovat komprimovaný zvuk do služby pro rozpoznávání řeči pomocí PullStream nebo PushStream.

> [!IMPORTANT]
> V současné době se pro C++jazyky, C#a Java v systému Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) aktuálně podporují streamování komprimovaných zvuků. Podporuje se taky pro [Java v Androidu](how-to-use-codec-compressed-audio-input-streams-android.md) a [v cíli C na platformě iOS](how-to-use-codec-compressed-audio-input-streams-ios.md) .
> Je vyžadována sada Speech SDK verze 1.7.0 nebo vyšší.

Informace o WAV/PCM najdete v dokumentaci k hlavní Speech.  Mimo zvuk WAV/PCM jsou podporovány následující formáty komprimované vstupní hodnoty kodeku:

- MP3
- OPUS/OGG
- FLAC
- ALAW v kontejneru WAV
- MULAW v kontejneru WAV

## <a name="prerequisites"></a>Předpoklady

Zpracování komprimovaného zvuku je implementováno pomocí [GStreamer](https://gstreamer.freedesktop.org). V případě důvodů licencování nejsou binární soubory GStreamer kompilovány a propojeny se sadou Speech SDK. Takže vývojář aplikace musí nainstalovat následující hodnoty na 18,04, 16,04 a Debian 9 pro použití komprimovaného vstupního zvuku.

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Příklad kódu pomocí komprimovaného zvukového vstupu kodeku

Pokud chcete streamovat v komprimovaném zvukovém formátu ke službám Speech, vytvořte `PullAudioInputStream` nebo `PushAudioInputStream`. Pak vytvořte `AudioConfig` z instance vaší třídy streamu a určete formát komprese datového proudu.

Řekněme, že máte vstupní třídu streamu nazvanou `myPushStream` a používáte OPUS/OGG. Váš kód může vypadat takto:

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

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Viz rozpoznávání řeči v jazyce Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
