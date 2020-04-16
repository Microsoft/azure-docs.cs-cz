---
title: Dlouhé zvukové rozhraní API (preview) – služba řeči
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak je rozhraní Long Audio API navrženo pro asynchronní syntézu dlouho formátovaného textu na řeč.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: b7cca314ec59e46cf17751b1aec28b5c3ea029ed
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401058"
---
# <a name="long-audio-api-preview"></a>Dlouhé zvukové rozhraní API (náhled)

Rozhraní Long Audio API je určeno pro asynchronní syntézu dlouho formátovaného textu na řeč (například audio knihy). Toto rozhraní API nevrací syntetizovaný zvuk v reálném čase, místo toho se očekává, že budete dotazování na odpovědi a spotřebovávají výstupy, jak jsou k dispozici ze služby. Na rozdíl od rozhraní API pro převod textu na řeč, které používá sada Speech SDK, může rozhraní LONG Audio API vytvářet syntetizovaný zvuk delší než 10 minut, takže je ideální pro vydavatele a platformy zvukového obsahu.

Další výhody rozhraní Long Audio API:

* Syntetizovaná řeč vrácená službou používá neurální hlasy, které zajišťují vysoce věrné zvukové výstupy.
* Vzhledem k tomu, že odpovědi v reálném čase nejsou podporovány, není nutné nasadit koncový bod hlasu.

> [!NOTE]
> Dlouhé zvukové rozhraní API nyní podporuje pouze [vlastní neural hlas](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Pracovní postup

Obvykle, když používáte dlouhé zvukové rozhraní API, odešlete textový soubor nebo soubory, které mají být syntetizovány, dotazování na stav, pak pokud je stav úspěšný, můžete stáhnout zvukový výstup.

Tento diagram poskytuje přehled na vysoké úrovni pracovního postupu.

![Dlouhý diagram pracovního postupu zvukového rozhraní API](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Příprava obsahu pro syntézu

Při přípravě textového souboru se ujistěte, že:

* Je buď prostý text (.txt) nebo SSML text (.txt)
* Je kódován jako [UTF-8 s označením objednávky bajtů (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Je jeden soubor, ne zip
* Obsahuje více než 400 znaků pro prostý text nebo 400 [fakturovatelných znaků](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) pro text SSML a méně než 10 000 odstavců.
  * U prostého textu je každý odstavec oddělen stisknutím **klávesy Enter/Return** - Zobrazit [příklad zadávání ve formátu prostého textu.](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Pro text SSML je každý kus SSML považován za odstavec. Kusy SSML musí být odděleny různými odstavci - Zobrazit [příklad vstupu textu SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> Pro čínštinu (pevninskou), čínštinu (Hongkong), čínštinu (Tchaj-wan), japonštinu a korejštinu se jedno slovo započítá jako dva znaky. 

## <a name="submit-synthesis-requests"></a>Odeslat žádosti o syntézu

Po přípravě vstupního obsahu postupujte podle [rychlého spuštění aplikace long-form audio synthesis](https://aka.ms/long-audio-python) a odešlete požadavek. Pokud máte více než jeden vstupní soubor, budete muset odeslat více požadavků. Existují určitá omezení, která je třeba znát: 
* Klient může odesílat až 5 požadavků na server za sekundu pro každý účet předplatného Azure. Pokud překročí omezení, klient získá kód chyby 429 (příliš mnoho požadavků). Prosím, snižte částku žádosti za sekundu
* Server může spouštět a zařadit do fronty až 120 požadavků pro každý účet předplatného Azure. Pokud překročí omezení, server vrátí kód chyby 429 (příliš mnoho požadavků). Počkejte a vyhněte se odesílání nové žádosti, dokud nebudou některé požadavky dokončeny.
* Server bude mít až 20 000 požadavků pro každý účet předplatného Azure. Pokud toto omezení překročí, odstraňte prosím některé požadavky před odesláním nových

## <a name="audio-output-formats"></a>Formáty zvukového výstupu

Podporujeme flexibilní formáty audio výstupu. Můžete generovat zvukové výstupy na odstavec nebo zřetězit zvuk do jednoho výstupu nastavením parametru 'concatenateResult'. Rozhraní Long Audio API podporuje následující formáty zvukového výstupu:

> [!NOTE]
> Výchozí zvukový formát je riff-16khz-16bit-mono-pcm.

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* audio-16khz-32kbitrate-mono-mp3
* audio-16khz-64kbitrate-mono-mp3
* audio-16khz-128kbitrate-mono-mp3
* audio-24khz-48kbitrate-mono-mp3
* audio-24khz-96kbitrate-mono-mp3
* audio-24khz-160kbitrate-mono-mp3

## <a name="quickstarts"></a>Rychlé starty

Nabízíme rychlé starty navržené tak, aby vám pomohly úspěšně spustit rozhraní Long Audio API. Tato tabulka obsahuje seznam rychlých startů rozhraní Long Audio API uspořádaných podle jazyka.

* [Úvodní příručka: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Ukázka kódu
Ukázkový kód pro rozhraní Long Audio API je k dispozici na GitHubu.

* [Ukázkový kód: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Ukázkový kód: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Ukázkový kód: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
