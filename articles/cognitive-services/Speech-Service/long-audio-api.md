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
ms.openlocfilehash: 550579b40470d7a1ad02031b8140e7d0a7164f46
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310593"
---
# <a name="long-audio-api-preview"></a>Dlouhé zvukové rozhraní API (Preview)

Rozhraní API pro dlouhé zvukové soubory je navržené pro asynchronní syntézu dlouhého textu na řeč (například zvukové knihy, články s novinkami a dokumenty). Toto rozhraní API nevrátí syntetizované zvuky v reálném čase, místo toho, abyste se mohli dotazovat na odpovědi a využívat výstupy, když jsou zpřístupněné ze služby. Na rozdíl od rozhraní API pro rozpoznávání řeči používaného sadou Speech SDK může dlouhé zvukové rozhraní API vytvářet syntetizované zvuky delší než 10 minut, což je ideální pro vydavatele a platformy zvukového obsahu.

Další výhody pro dlouhé zvukové rozhraní API:

* Syntetizované rozpoznávání řeči vrácené službou používá nejlepší hlasy neuronové.
* Není potřeba nasazovat hlasový koncový bod, protože v něm nejsou žádné hlasy v režimu dávek v reálném čase.

> [!NOTE]
> Rozhraní API pro dlouhé zvukové rozhraní teď podporuje [neuronové hlasy](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) a [vlastní neuronové hlasy](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

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
> Pro čínštinu (kontinentální), čínština (Hongkong – zvláštní administrativní oblast), čínština (Tchaj-wan), japonština a korejština, se jedno slovo bude počítat jako dva znaky. 

## <a name="submit-synthesis-requests"></a>Odeslání žádostí o Shrnutí

Po přípravě vstupního obsahu postupujte podle pokynů k [rychlému startu pro přenos zvukové syntézy](https://aka.ms/long-audio-python) a odešlete žádost. Pokud máte více než jeden vstupní soubor, budete muset odeslat více požadavků. 

**Stavové kódy http** označují běžné chyby.

| Rozhraní API | Stavový kód HTTP | Popis | Proposal |
|-----|------------------|-------------|----------|
| Vytvořit | 400 | Funkce Voice syntézy není v této oblasti povolena. | Změňte klíč předplatného řeči s podporovanou oblastí. |
|        | 400 | Platný je jenom **standardní** odběr řeči pro tuto oblast. | Změňte klíč předplatného řeči na cenovou úroveň Standard. |
|        | 400 | Překročil limit počtu požadavků 20 000 pro účet Azure. Před odesláním nových žádostí prosím odeberte nějaké žádosti. | Server bude pro každý účet Azure uchovávat až 20 000 požadavků. Před odesláním nových požadavků odstraňte nějaké žádosti. |
|        | 400 | Tento model se nedá použít ve shrnutí hlasu: {modelID}. | Ujistěte se, že stav {modelID} je správný. |
|        | 400 | Oblast pro požadavek se neshoduje s oblastí pro model: {modelID}. | Ujistěte se, že se oblast {modelID} shoduje s oblastí žádosti. |
|        | 400 | Funkce Voice syntézy podporuje pouze textový soubor v kódování UTF-8 se značkou pořadí bajtů. | Ujistěte se, že vstupní soubory jsou v kódování UTF-8 se značkou pořadí bajtů. |
|        | 400 | V požadavku na Shrnutí hlasu jsou povolené jenom platné vstupy SSML. | Ujistěte se, že vstupní výrazy SSML jsou správné. |
|        | 400 | Hlasový název {Voice} nebyl ve vstupním souboru nalezen. | Vstupní název vstupu SSML není zarovnán s identifikátorem ID modelu. |
|        | 400 | Velikost odstavce ve vstupním souboru by měla být menší než 10 000. | Ujistěte se, že je odstavec v souboru menší než 10 000. |
|        | 400 | Vstupní soubor by měl být delší než 400 znaků. | Ujistěte se, že vstupní soubor překračuje 400 znaků. |
|        | 404 | Model deklarovaný v definici hlasové syntézy nejde najít: {modelID}. | Ujistěte se, že je {modelID} správné. |
|        | 429 | Překročil aktivní limit pro syntézu hlasu. Počkejte prosím, než se dokončí některé požadavky. | Server může spouštět a zařadit do fronty až 120 požadavků pro každý účet Azure. Počkejte prosím a vyhněte se odesílání nových požadavků do doby, než se dokončí některé žádosti. |
| Vše       | 429 | Existuje příliš mnoho požadavků. | Klient může pro každý účet Azure odeslat až 5 požadavků na server za sekundu. Snižte prosím částku žádosti za sekundu. |
| Odstranit    | 400 | Úkol syntézy hlasu se pořád používá. | Je možné odstranit pouze **dokončené** nebo **neúspěšné**požadavky. |
| GetByID   | 404 | Zadanou entitu nelze nalézt. | Ujistěte se, že je ID syntézy správné. |

## <a name="regions-and-endpoints"></a>Oblasti a koncové body

Rozhraní API pro dlouhé zvukové rozhraní je k dispozici ve více oblastech s jedinečnými koncovými body.

| Oblast | Koncový bod |
|--------|----------|
| Austrálie – východ | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| Střední Kanada | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| USA – východ | `https://eastus.customvoice.api.speech.microsoft.com` |
| Indie – střed | `https://centralindia.customvoice.api.speech.microsoft.com` |
| USA – středojih | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| Jihovýchodní Asie | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Spojené království – jih | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Západní Evropa | `https://westeurope.customvoice.api.speech.microsoft.com` |
| USA – západ 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Formáty zvukového výstupu

Podporujeme flexibilní formáty zvukového výstupu. Nastavením parametru ' concatenateResult ' můžete vygenerovat zvukové výstupy na jeden odstavec nebo zřetězit výstup zvuku do jednoho výstupu. Rozhraní API pro dlouhé zvukové rozhraní podporuje následující formáty zvukového výstupu:

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
