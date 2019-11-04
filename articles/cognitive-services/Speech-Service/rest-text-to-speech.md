---
title: Referenční informace k rozhraní API pro převod textu na řeč (REST) – služba Speech
titleSuffix: Azure Cognitive Services
description: Naučte se používat REST API převodu textu na řeč. V tomto článku se seznámíte s možnostmi autorizace, možnostmi dotazů, postupy strukturování požadavků a přijetím odpovědi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9fa0157bd458d2de028cab8ff9c836761e99562f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481201"
---
# <a name="text-to-speech-rest-api"></a>REST API převodu textu na řeč

Služba Speech Services umožňuje [převést text na syntetizované rozpoznávání řeči](#convert-text-to-speech) a [získat seznam podporovaných hlasů](#get-a-list-of-voices) pro oblast s použitím sady rozhraní REST API. Každý dostupný koncový bod je přidružen k oblasti. Vyžaduje se klíč předplatného pro koncový bod nebo oblast, kterou plánujete použít.

REST API převodu textu na řeč podporuje neuronové a standardní hlasy převodu textu na řeč, z nichž každý podporuje konkrétní jazyk a dialekt identifikovaný národním prostředím.

* Úplný seznam hlasů najdete v tématu [Podpora jazyků](language-support.md#text-to-speech).
* Informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#text-to-speech).

> [!IMPORTANT]
> Náklady se liší pro standardní, vlastní a neuronové hlasy. Další informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Než začnete používat toto rozhraní API, pochopte:

* REST API převodu textu na řeč vyžaduje autorizační hlavičku. To znamená, že při přístupu ke službě musíte dokončit výměnu tokenů. Další informace najdete v tématu [Ověřování](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Získat seznam hlasů

Koncový bod `voices/list` umožňuje získat úplný seznam hlasů pro konkrétní oblast nebo koncový bod.

### <a name="regions-and-endpoints"></a>Oblasti a koncové body

| Oblast | Koncový bod |
|--------|----------|
| Austrálie – východ | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brazílie – jih | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kanada – střed | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Střední USA | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Východní Asie | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA – východ | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Východ USA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Francie – střed | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Indie – střed | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japonsko – východ | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Korea – střed | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Středoseverní USA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Severní Evropa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Středojižní USA | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Jihovýchodní Asie | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Velká Británie – jih | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Západní Evropa | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Západní USA | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Západní USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Hlavičky požadavku

Tato tabulka obsahuje seznam požadovaných a volitelných hlaviček pro požadavky na převod textu na řeč.

| Hlavička | Popis | Požadováno/volitelné |
|--------|-------------|---------------------|
| `Authorization` | Autorizační token předchází `Bearer`m slovem. Další informace najdete v tématu [Ověřování](#authentication). | Požaduje se |

### <a name="request-body"></a>Text požadavku

Pro `GET` požadavků na tento koncový bod není tělo vyžadováno.

### <a name="sample-request"></a>Ukázková žádost

Tato žádost vyžaduje pouze hlavičku autorizace.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Ukázková odpověď

Tato odpověď byla zkrácena, aby ukázala strukturu odpovědi.

> [!NOTE]
> Dostupnost hlasu se liší podle oblasti nebo koncového bodu.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)",
        "ShortName": "cs-CZ-Jakub",
        "Gender": "Male",
        "Locale": "cs-CZ"
    },

    ...

]
```

### <a name="http-status-codes"></a>Stavové kódy HTTP

Stavový kód HTTP pro každou odpověď indikuje úspěch nebo běžné chyby.

| Stavový kód HTTP | Popis | Možný důvod |
|------------------|-------------|-----------------|
| 200 | OK | Požadavek byl úspěšný. |
| 400 | Nesprávná žádost | Povinný parametr chybí, je prázdný nebo má hodnotu null. Nebo hodnota předaná buď požadovanému, nebo volitelnému parametru není platná. Běžným problémem je záhlaví, které je příliš dlouhé. |
| 401 | Neautorizováno | Požadavek není autorizovaný. Zkontrolujte, jestli je klíč předplatného nebo token platný a ve správné oblasti. |
| 429 | Příliš mnoho žádostí | Překročili jste kvótu nebo míru požadavků povolených pro vaše předplatné. |
| 502 | Chybná brána | Problém v síti nebo na straně serveru. Může také označovat neplatné hlavičky. |


## <a name="convert-text-to-speech"></a>Převod textu na řeč

`v1` koncový bod umožňuje převod textu na řeč pomocí [jazyka SSML (Speech Shrnutí značek)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Oblasti a koncové body

Tyto oblasti jsou podporované pro převod textu na řeč pomocí REST API. Ujistěte se, že jste vybrali koncový bod, který odpovídá vaší oblasti předplatného.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Hlavičky požadavku

Tato tabulka obsahuje seznam požadovaných a volitelných hlaviček pro požadavky na převod textu na řeč.

| Hlavička | Popis | Požadováno/volitelné |
|--------|-------------|---------------------|
| `Authorization` | Autorizační token předchází `Bearer`m slovem. Další informace najdete v tématu [Ověřování](#authentication). | Požaduje se |
| `Content-Type` | Určuje typ obsahu pro zadaný text. Přijatá hodnota: `application/ssml+xml`. | Požaduje se |
| `X-Microsoft-OutputFormat` | Určuje formát výstup zvuku. Úplný seznam přijatých hodnot najdete v tématu [zvukové výstupy](#audio-outputs). | Požaduje se |
| `User-Agent` | Název aplikace Zadaná hodnota musí být kratší než 255 znaků. | Požaduje se |

### <a name="audio-outputs"></a>Zvukové výstupy

Toto je seznam podporovaných formátů zvuku, které se odesílají v jednotlivých žádostech jako `X-Microsoft-OutputFormat` záhlaví. Každá z nich zahrnuje přenosovou rychlost a typ kódování. Služba Speech Services podporuje zvukové výstupy 24 kHz, 16 kHz a 8 kHz.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Pokud váš vybraný hlasový a výstupní formát má jiné přenosové rychlosti, zvuk se v případě potřeby převzorkuje. Nicméně na 24 kHz hlasy nepodporují `audio-16khz-16kbps-mono-siren` a `riff-16khz-16kbps-mono-siren` výstupní formáty.

### <a name="request-body"></a>Text požadavku

Tělo každé žádosti o `POST` se odešle jako [jazyk SSML (Speech syntézy)](speech-synthesis-markup.md). SSML umožňuje zvolit hlas a jazyk syntetizované řeči, kterou vrátí služba pro převod textu na mluvené slovo. Úplný seznam podporovaných hlasů najdete v tématu [Podpora jazyků](language-support.md#text-to-speech).

> [!NOTE]
> Pokud používáte vlastní hlas, text požadavku se dá poslat jako prostý text (ASCII nebo UTF-8).

### <a name="sample-request"></a>Ukázková žádost

Tento požadavek HTTP používá SSML k určení hlasu a jazyka. Tělo nemůže být delší než 1 000 znaků.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-JessaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Příklady konkrétního jazyka najdete v našich rychlých startech:

* [.NET Core,C#](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>Stavové kódy HTTP

Stavový kód HTTP pro každou odpověď indikuje úspěch nebo běžné chyby.

| Stavový kód HTTP | Popis | Možný důvod |
|------------------|-------------|-----------------|
| 200 | OK | Požadavek byl úspěšný. tělo odpovědi je zvukový soubor. |
| 400 | Nesprávná žádost | Povinný parametr chybí, je prázdný nebo má hodnotu null. Nebo hodnota předaná buď požadovanému, nebo volitelnému parametru není platná. Běžným problémem je záhlaví, které je příliš dlouhé. |
| 401 | Neautorizováno | Požadavek není autorizovaný. Zkontrolujte, jestli je klíč předplatného nebo token platný a ve správné oblasti. |
| 413 | Entita požadavku je příliš velká. | Vstup SSML je delší než 1024 znaků. |
| 415 | Nepodporovaný typ média | Je možné, že byl zadán nesprávný `Content-Type`. `Content-Type` by měly být nastavené na `application/ssml+xml`. |
| 429 | Příliš mnoho žádostí | Překročili jste kvótu nebo míru požadavků povolených pro vaše předplatné. |
| 502 | Chybná brána | Problém v síti nebo na straně serveru. Může také označovat neplatné hlavičky. |

Pokud je stav HTTP `200 OK`, text odpovědi obsahuje zvukový soubor v požadovaném formátu. Tento soubor se dá přehrát jako přenesený, uložený do vyrovnávací paměti nebo uložený do souboru.

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
