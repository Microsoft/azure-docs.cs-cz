---
title: Koncepty AudioInputStream | Microsoft Docs
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
ms.openlocfilehash: 528356473c4221a815fa68cbec3426866c4cbd23
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "35343971"
---
# <a name="about-the-audio-input-stream-api"></a>O zvuk vstupní stream rozhraní API

**Zvuk vstupní datový proud** rozhraní API poskytuje způsob, jak stream zvukových datových proudů do nástroje pro rozpoznávání místo mikrofon nebo vstupní soubor rozhraní API.

## <a name="api-overview"></a>Přehled rozhraní API

Rozhraní API používá dvě komponenty, `AudioInputStream` (nezpracovaná data zvuk) a `AudioInputStreamFormat`.

`AudioInputStreamFormat` Definuje formát zvuková data. Může ve srovnání s standardní `WAVEFORMAT` struktury wave souborů v systému Windows.

  - `FormatTag`

    Formát zvukovém souboru. Sada SDK řeči aktuálně podporuje jenom `format 1` (PCM - little endian).

  - `Channels`

    Počet kanálů. Aktuální řeči služba podporuje pouze jeden kanál (mono) zvuk materiálů.

  - `SamplesPerSec`

    Vzorkovací frekvence. Typické mikrofon záznam má 16000 ukázky za sekundu.

  - `AvgBytesPerSec`

    Průměrný počet bajtů za sekundu, vypočítá se jako `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`. Průměrný počet bajtů za sekundu může být různé pro zvukové datové proudy, které používají proměnné přenosových rychlostí.

  - `BlockAlign`

    Velikost jeden snímek, vypočítá se jako `Channels * ceil(wBitsPerSample, 8)`. Z důvodu odsazení, může být vyšší než tato hodnota se skutečnou hodnotou.

  - `BitsPerSample`

    Bitů na vzorek. Typické zvukový datový proud používá 16 bitů na vzorek (CD kvality).

`AudioInputStream` Základní třída přepíše váš vlastní datový proud adaptér. Tento adaptér je třeba implementovat tyto funkce:

   - `GetFormat()`

     Tato funkce je volána potřebujete formát zvuk datového proudu. Získá ukazatel do vyrovnávací paměti AudioInputStreamFormat.

   - `Read()`

     Tato funkce je volána k získání dat z zvukový stream. Jeden parametr. je zde ukazatel do ke zkopírování zvuk dat do vyrovnávací paměti. Druhý parametr je velikost vyrovnávací paměti. Vrátí počet bajtů, které jsou zkopírovány do vyrovnávací paměti. Vrácená hodnota `0` označuje konec datového proudu.

   - `Close()`

     Tato funkce je volána k zavřít zvukový stream.

## <a name="usage-examples"></a>Příklady použití

Obecně platí následující kroky se podílejí při použití zvuk vstupní datové proudy:

  - Určete formát zvuk datového proudu. Formát musí podporovat sady SDK a službu řeči. Aktuálně se podporuje následující konfigurace:

    Značka jednoho Zvuk formátu (PCM), jeden kanál, 16000 ukázky za sekundu, délku 32 000 bajtů za sekundu, dvě bloku align (16 bitů včetně odsazení pro ukázku), 16 bitů na vzorek

  - Zajistěte, aby že váš kód můžete zadat NEZPRACOVANÁ data zvuk, specifikace identifikovat výše. Pokud zvuk zdrojová data neodpovídá podporované formáty, musí být zvukovému záznamu převodu do požadovaný formát.

  - Odvození třídy vaše vlastní zvuk vstupního datového proudu z `AudioInputStream`. Implementace `GetFormat()`, `Read()`, a `Close()` operaci. Podpis funkce stejné je závislá, ale kód bude vypadat podobně jako tento ukázkový kód::

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

  - Použijte zvuk vstupního datového proudu:

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

  - V některých jazycích `contosoStream` musí být explicitně odstranit po dokončení rozpoznávání. Nelze uvolnit AudioStream, před dokončení vstup je pro čtení. Scénář pomocí `StopContinuousRecognitionAsync` a `StopContinuousRecognitionAsync` vyžaduje koncept zobrazené v této ukázce:

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

* [Získat zkušební verze předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
* [Informace o tom, rozpoznávat řeč v jazyce C#](quickstart-csharp-windows.md)
