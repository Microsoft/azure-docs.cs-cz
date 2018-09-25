---
title: Koncepce zvukový vstupní datový proud SDK řeči
description: Přehled možností SDK řeči zvukový vstupní datový proud rozhraní API.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: 6c2d7c5787305f60b73ab83ea17367b04e03ac12
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985176"
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

- Vytvoření vlastních zvukových vstupního datového proudu třídu odvozenou z `PullAudioInputStreamCallback`. Implementace `Read()` a `Close()` členy. Signatura funkce stejné je závislá na jazyku, ale kód bude vypadat podobně jako tato ukázka kódu:

  ```
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public size_t Read(byte *buffer, size_t size) {
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
