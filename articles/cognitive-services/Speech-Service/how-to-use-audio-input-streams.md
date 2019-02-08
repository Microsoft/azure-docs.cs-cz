---
title: Koncepce zvukový vstupní datový proud SDK řeči
titleSuffix: Azure Cognitive Services
description: Přehled možností SDK řeči zvukový vstupní datový proud rozhraní API.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: f34504dddaa21cc162476d1fdb462e7be5c48629
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877299"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>O sadou SDK pro řeč zvukový vstup datového proudu rozhraní API

Sada SDK řeči **zvuk vstupní Stream** rozhraní API poskytuje možnost streamování audiostreamy do nástroje pro rozpoznávání namísto použití mikrofonu nebo vstupní soubor rozhraní API.

Následující kroky jsou potřeba při používání zvuk vstupní datové proudy:

- Určete formát zvukový datový proud. Formát musí být podporována sadou SDK pro řeč a řeči služby. V současné době se podporuje pouze následující konfiguraci:

  Zvukové vzorky ve formátu PCM, jeden kanál, 16000 vzorků za sekundu, délku 32 000 bajtů za sekundu, dva bloku zarovnání (16 bitů včetně odsazení ukázku), 16 bitů na vzorku.

  Odpovídající kód v sadě SDK vytvořte zvukový formát vypadá například takto:

  ```
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Ujistěte se, že váš kód může poskytnout NEZPRACOVANÁ zvukových dat podle těchto specifikací. Pokud data zdroje zvuku neodpovídá podporované formáty, musí být zvuk převedou do požadovaného formátu.

- Vytvoření vlastních zvukových vstupního datového proudu třídu odvozenou z `PullAudioInputStreamCallback`. Implementujte členy `Read()` a `Close()`. Signatura funkce stejné je závislá na jazyku, ale kód bude vypadat podobně jako tato ukázka kódu:

  ```
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

- Vytvořit zvuku konfiguraci podle vaší Zvuk formátu a vstupní datový proud. Předáno při vytváření rozlišovači konfigurace pravidelných rozpoznávání řeči a zvuku vstupní konfiguraci. Příklad:

  ```
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
