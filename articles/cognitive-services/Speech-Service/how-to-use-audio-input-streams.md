---
title: Koncepty AudioInputStream | Dokumentace Microsoftu
description: Přehled možností AudioInputStream rozhraní API.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
manager: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: fmegen
ms.openlocfilehash: 0eafa7e88df5d00a67646ca7f82ca027602a40b3
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071442"
---
# <a name="about-the-audio-input-stream-api"></a>O zvukový vstup datového proudu rozhraní API

**Zvuk vstupní Stream** rozhraní API poskytuje možnost streamování audiostreamy do nástroje pro rozpoznávání namísto použití mikrofonu nebo vstupní soubor rozhraní API.

## <a name="api-overview"></a>Přehled rozhraní API

Rozhraní API používá dvě komponenty, `AudioInputStream` (nezpracovaná zvukových dat) a `AudioInputStreamFormat`.

`AudioInputStreamFormat` Definuje formát zvuková data. Je možné porovnat s standardní `WAVEFORMAT` struktury souborů wave na Windows.

  - `FormatTag`

    Formát zvuku. Sadou SDK pro řeč aktuálně podporuje pouze `format 1` (PCM - little endian).

  - `Channels`

    Počet kanálů. Aktuální služba speech podporuje pouze jeden kanál (mono) zvuku materiálu.

  - `SamplesPerSec`

    Vzorkovací frekvence. Nahrávání mikrofonu typické má 16000 vzorků za sekundu.

  - `AvgBytesPerSec`

    Průměrný počet bajtů za sekundu, vypočítá jako `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`. Průměrný počet bajtů za sekundu se může lišit pro zvukové datové proudy, které používají proměnné přenosových rychlostí.

  - `BlockAlign`

    Vypočítá velikost jeden snímek jako `Channels * ceil(wBitsPerSample, 8)`. Z důvodu odsazení, může být vyšší než tato hodnota skutečnou hodnotu.

  - `BitsPerSample`

    Bity na vzorku. Typické zvukový datový proud používá 16 bitů na vzorku (Kvalita CD).

`AudioInputStream` Základní třídu, budou přepsaná identifikátorem adaptér vašeho vlastního datového proudu. Tento adaptér má k implementaci těchto funkcí:

   - `GetFormat()`

     Tato funkce je volána k získání formátu zvukový datový proud. Získá ukazatel do vyrovnávací paměti AudioInputStreamFormat.

   - `Read()`

     Tato funkce je volána k získání dat z zvukový datový proud. Jeden parametr má ukazatel na kopírování zvuková data do vyrovnávací paměti. Druhý parametr je velikost vyrovnávací paměti. Funkce vrátí počet bajtů, které jsou zkopírovány do vyrovnávací paměti. Vrácená hodnota `0` označuje konec datového proudu.

   - `Close()`

     Tato funkce je volána zavřete zvukový datový proud.

## <a name="usage-examples"></a>Příklady použití

Následující kroky se podílejí obecně platí, při použití zvukové streamy ve vstupu:

  - Určete formát zvukový datový proud. Formát musí být podporován sady SDK a službu rozpoznávání řeči. Aktuálně se podporuje následující konfigurace:

    Jeden zvukový formát značky (PCM), jeden kanál, 16000 vzorků za sekundu, délku 32 000 bajtů za sekundu, zarovnání dvou bloku (16 bitů včetně odsazení ukázku), 16 bitů na ukázky

  - Ujistěte se, že váš kód může poskytnout NEZPRACOVANÁ zvukových dat jde o specifikace určené výše. Pokud data zdroje zvuku neodpovídá podporované formáty, musí být zvuk převedou do požadovaného formátu.

  - Odvodit třídu vaše vlastní zvukový vstupní datový proud z `AudioInputStream`. Implementace `GetFormat()`, `Read()`, a `Close()` operace. Signatura funkce stejné je závislá na jazyku, ale kód bude vypadat podobně jako tento vzorový kód::

    ```
     public class ContosoAudioStream : AudioInputStream {
        ContosoConfig config;

        public ContosoAudioStream(const ContosoConfig& config) {
            this.config = config;
        }

        public void GetFormat(AudioInputStreamFormat& format) {
            // returns format data to the caller.
            // e.g. format.FormatTag = config.XXX;
            // ...
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

  - Použijte zvukový vstupního datového proudu:

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    var result = await recognizer.RecognizeAsync();

    var text = result.GetText();

    // In some languages you need to delete the stream explicitly.
    // delete contosoStream;
    ```

  - V některých jazycích `contosoStream` musí být explicitně odstraněny po dokončení uznání. AudioStream nelze uvolnit předtím, než je kompletní vstup přečtený. Ve scénáři pomocí `StopContinuousRecognitionAsync` a `StopContinuousRecognitionAsync` vyžaduje koncept znázorněné v tomto příkladu:

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    await recognizer.StartContinuousRecognitionAsync();

    // ERROR: do not delete the contosoStream before ending recognition!
    // delete contosoStream;

    await recognizer.StopContinuousRecognitionAsync();

    // OK: Safe to delete the contosoStream.
    // delete contosoStream;
    ```

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
