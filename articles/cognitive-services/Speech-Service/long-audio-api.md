---
title: Dlouhé zvukové rozhraní API (Preview) – služba Speech
titleSuffix: Azure Cognitive Services
description: Naučte se, jak dlouhé zvukové rozhraní API je navržené pro asynchronní syntézu dlouhého textu na řeč.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: b7cca314ec59e46cf17751b1aec28b5c3ea029ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81401058"
---
# <a name="long-audio-api-preview"></a>Dlouhé zvukové rozhraní API (Preview)

Rozhraní API pro dlouhé zvukové služby je navržené pro asynchronní syntézu dlouhého textu na řeč (například: zvukové knihy). Toto rozhraní API nevrátí syntetizované zvuky v reálném čase, místo toho, abyste se mohli dotazovat na odpovědi a využívat výstupy, když jsou zpřístupněné ze služby. Na rozdíl od rozhraní API pro rozpoznávání řeči používaného sadou Speech SDK může dlouhé zvukové rozhraní API vytvářet syntetizované zvuky delší než 10 minut, což je ideální pro vydavatele a platformy zvukového obsahu.

Další výhody pro dlouhé zvukové rozhraní API:

* Syntetizované rozpoznávání řeči vrácené službou používá hlasy neuronové, které zajišťují zvukové výstupy s vysokou přesností.
* Vzhledem k tomu, že se odpovědi v reálném čase nepodporují, není nutné nasazovat hlasový koncový bod.

> [!NOTE]
> Rozhraní API pro dlouhé zvukové rozhraní teď podporuje jenom [vlastní neuronové hlas](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Pracovní postup

Při použití rozhraní API dlouhého zvuku se obvykle odesílá textový soubor nebo soubory, které se mají syntetizovat, dotaz na jeho stav, a pokud je stav úspěšný, můžete si stáhnout zvukový výstup.

Tento diagram poskytuje přehled o pracovním postupu na nejvyšší úrovni.

![Diagram pracovního postupu dlouhého zvukového rozhraní API](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Příprava obsahu pro syntézu

Při přípravě textového souboru se ujistěte, že:

* Je buď prostý text (. txt), nebo SSML text (. txt)
* Je kódovaný jako [UTF-8 s označením pořadí bajtů (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) .
* Je jeden soubor, ne zip.
* Obsahuje více než 400 znaků pro prostý text nebo 400 [fakturovatelných znaků](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) pro text SSML a méně než 10 000 odstavců.
  * U prostého textu je každý odstavec oddělený příkladem **zadání a návratového** [vstupu v prostém textu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) .
  * V případě textu SSML se každý SSMLový kus považuje za odstavec. SSML části musí být oddělené různými odstavci – [Příklad textového vstupu SSML textu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> Pro čínštinu (kontinentální), čínština (Hongkong), čínština (Tchaj-wan), japonština a korejština, se jedno slovo bude počítat jako dva znaky. 

## <a name="submit-synthesis-requests"></a>Odeslání žádostí o Shrnutí

Po přípravě vstupního obsahu postupujte podle pokynů k [rychlému startu pro přenos zvukové syntézy](https://aka.ms/long-audio-python) a odešlete žádost. Pokud máte více než jeden vstupní soubor, budete muset odeslat více požadavků. Je třeba mít na paměti některá omezení: 
* Klient může pro každý účet předplatného Azure odeslat až 5 požadavků na server za sekundu. Pokud překročí omezení, klient obdrží kód chyby 429 (příliš mnoho požadavků). Snižte prosím částku žádosti za sekundu.
* Server může spouštět a zařadit do fronty až 120 požadavků pro každý účet předplatného Azure. Pokud se překročí omezení, server vrátí kód chyby 429 (příliš mnoho požadavků). Počkejte prosím a zabraňte odeslání nové žádosti, dokud nebudou dokončeny některé žádosti.
* Server bude pro každý účet předplatného Azure uchovávat až 20 000 požadavků. Pokud překročí omezení, před odesláním nových požadavků prosím odstraňte nějaké žádosti.

## <a name="audio-output-formats"></a>Formáty zvukového výstupu

Podporujeme flexibilní formáty zvukového výstupu. Můžete generovat zvukové výstupy na jeden odstavec nebo zřetězit zvuky do jednoho výstupu nastavením parametru ' concatenateResult '. Rozhraní API pro dlouhé zvukové rozhraní podporuje následující formáty zvukového výstupu:

> [!NOTE]
> Výchozí formát zvuku je RIFF-16khz-16bitový-mono-PCM.

* RIFF-8KHz-16bitový-mono-PCM
* RIFF-16khz-16bitový-mono-PCM
* RIFF-24khz-16bitový-mono-PCM
* RIFF-48kHz-16bitový-mono-PCM
* Audio-16khz-32kbitrate-mono-MP3
* Audio-16khz-64kbitrate-mono-MP3
* Audio-16khz-128kbitrate-mono-MP3
* Audio-24khz-48kbitrate-mono-MP3
* Audio-24khz-96kbitrate-mono-MP3
* Audio-24khz-160kbitrate-mono-MP3

## <a name="quickstarts"></a>Rychlé starty

Nabízíme rychlé starty, které vám pomůžou úspěšně spouštět rozhraní API pro dlouhé zvukové zařízení. Tato tabulka obsahuje seznam dlouhých rychlých startů rozhraní API pro přenos v jazyce.

* [Rychlý Start: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Ukázka kódu
Vzorový kód pro dlouhé zvukové rozhraní API je k dispozici na GitHubu.

* [Vzorový kód: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Vzorový kód: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Vzorový kód: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
