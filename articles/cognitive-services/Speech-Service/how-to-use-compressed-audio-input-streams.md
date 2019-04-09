---
title: Komprimovaný Stream zvuk se sadou SDK pro řeč – hlasové služby
titleSuffix: Azure Cognitive Services
description: Naučíte se Streamovat komprimované audio hlasové služby Azure se sadou SDK pro řeč. K dispozici pro C++, C#a v Javě pro Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 2066dc3e20ab9fc92b23fd071728ea6a920d3324
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012419"
---
# <a name="stream-compressed-audio-with-the-speech-sdk"></a>Komprimovaný Stream zvuk se sadou SDK pro řeč

Sada SDK řeči **komprimované zvuk vstupní Stream** rozhraní API poskytuje způsob, jak datový proud stream komprimované audio Speech Service s využitím PullStream nebo PushStream.

> [!IMPORTANT]
> Vysílání datového proudu komprimované zvuku se podporuje jenom pro C++, C#a v Javě v Linuxu (Ubuntu 16.04 nebo Ubuntu 18.04).
> Podpora je omezena na MP3 a DÍLE/OGG.

## <a name="prerequisites"></a>Požadavky

Je třeba nainstalovat tyto závislosti pomocí komprimované zvukového vstupu sadou SDK pro řeč pro Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="streaming-compressed-audio"></a>Komprimovaný datový proud zvuku

Abyste mohli Streamovat v komprimované zvukový formát, který se hlasové služby, vytvořte `PullAudioInputStream` nebo `PushAudioInputStream`. Potom vytvořte `AudioConfig` z instance třídy datového proudu, určující formát komprese datového proudu.

Předpokládejme, že máte vstupní datový proud třídu s názvem `myPushStream` a používáte DÍLE/OGG. Je to, jak může vypadat kód: 

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

* [Získejte zkušební verzi předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
