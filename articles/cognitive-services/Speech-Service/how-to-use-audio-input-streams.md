---
title: Koncepty zvukového vstupního datového proudu sady Speech SDK
titleSuffix: Azure Cognitive Services
description: Přehled možností rozhraní API zvukového vstupního datového proudu sady Speech SDK.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 3039276a49e7bb41660d114e78ca047a3f77f279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74109936"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>O rozhraní API zvukového vstupního datového proudu sady Speech SDK

Rozhraní API **pro vstupní vstupní datový proud** zvuku sady Speech SDK poskytuje způsob streamování zvuku do rozhodců namísto použití rozhraní API pro mikrofon nebo vstupní soubor.

Při použití vstupních datových proudů zvuku jsou vyžadovány následující kroky:

- Identifikujte formát zvukového proudu. Formát musí být podporován sadou Speech SDK a službou Řeč. V současné době je podporována pouze následující konfigurace:

  Zvukové ukázky ve formátu PCM, jeden kanál, 16000 vzorků za sekundu, 32000 bajtů za sekundu, dva bloky zarovnání (16 bitů včetně odsazení pro vzorek), 16 bitů na vzorek.

  Odpovídající kód v sadě SDK pro vytvoření zvukového formátu vypadá takto:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Ujistěte se, že váš kód může poskytnout zvuková data RAW podle těchto specifikací. Pokud zdrojová data zvuku neodpovídají podporovaným formátům, musí být zvuk překódován do požadovaného formátu.

- Vytvořte si vlastní třídu `PullAudioInputStreamCallback`vstupního datového proudu zvuku odvozenou z aplikace . Implementujte členy `Read()` a `Close()`. Přesný podpis funkce je závislý na jazyku, ale kód bude vypadat podobně jako tato ukázka kódu:

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Vytvořte konfiguraci zvuku na základě zvukového formátu a vstupního datového proudu. Při vytváření nástroje pro rozpoznávání předejte pravidelnou konfiguraci řeči i konfiguraci zvukového vstupu. Například:

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Podívejte se, jak rozpoznat řeč v C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
