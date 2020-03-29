---
title: Odkaz na rozhraní API pro převod textu na řeč (REST) – služba řeči
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak používat rozhraní REST API pro převod textu na řeč. V tomto článku se dozvíte o možnostech autorizace, možnostech dotazu, způsobu strukturování požadavku a přijetí odpovědi.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 17b5e21291078f424ee775f21add181859dbbed5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131636"
---
# <a name="text-to-speech-rest-api"></a>Rozhraní REST API pro převod textu na řeč

Služba Řeč umožňuje [převést text na syntetizované řeči](#convert-text-to-speech) a [získat seznam podporovaných hlasů](#get-a-list-of-voices) pro oblast pomocí sady rest API. Každý dostupný koncový bod je přidružen k oblasti. Je vyžadován klíč předplatného pro koncový bod nebo oblast, kterou chcete použít.

Rozhraní REST API pro převod textu na řeč podporuje neurální a standardní hlasy převodu textu na řeč, z nichž každý podporuje určitý jazyk a dialekt, identifikovaný národním prostředím.

* Úplný seznam hlasů naleznete v tématu [podpora jazyků](language-support.md#text-to-speech).
* Informace o místní dostupnosti naleznete v [tématu Oblasti](regions.md#text-to-speech).

> [!IMPORTANT]
> Náklady se liší pro standardní, vlastní a neurální hlasy. Další informace naleznete v [tématu Ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Před použitím tohoto rozhraní API, pochopit:

* Rozhraní REST API pro převod textu na řeč vyžaduje hlavičku autorizace. To znamená, že je třeba dokončit výměnu tokenů pro přístup ke službě. Další informace najdete v tématu [Ověřování](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Získejte seznam hlasů

Koncový `voices/list` bod umožňuje získat úplný seznam hlasů pro konkrétní oblast/koncový bod.

### <a name="regions-and-endpoints"></a>Oblasti a koncové body

| Region (Oblast) | Koncový bod |
|--------|----------|
| Austrálie – východ | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brazílie – jih | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Střední Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA – střed | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Východní Asie | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA – východ | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA – východ 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Francie – střed | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Indie – střed | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japonsko – východ | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Jižní Korea – střed | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA – středosever | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Severní Evropa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA – středojih | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Jihovýchodní Asie | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Spojené království – jih | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Západní Evropa | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA – západ | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA – západ 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Hlavičky požadavku

V této tabulce jsou uvedena požadovaná a volitelná záhlaví pro žádosti o převod textu na řeč.

| Hlavička | Popis | Povinné / Nepovinné |
|--------|-------------|---------------------|
| `Authorization` | Autorizační token, kterému `Bearer`předchází slovo . Další informace najdete v tématu [Ověřování](#authentication). | Požaduje se |

### <a name="request-body"></a>Text požadavku

Tělo není vyžadováno `GET` pro požadavky na tento koncový bod.

### <a name="sample-request"></a>Požadavek na ukázku

Tento požadavek vyžaduje pouze hlavičku autorizace.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Ukázková odpověď

Tato odpověď byla zkrácena pro ilustraci struktury odpovědi.

> [!NOTE]
> Dostupnost hlasu se liší podle oblasti/koncového bodu.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)",
        "ShortName": "zh-CN-XiaoxiaoNeural",
        "Gender": "Female",
        "Locale": "zh-CN",
        "SampleRateHertz": "24000",
        "VoiceType": "Neural"
    },

    ...
]
```

### <a name="http-status-codes"></a>Stavové kódy HTTP

Stavový kód HTTP pro každou odpověď označuje úspěch nebo běžné chyby.

| Stavový kód HTTP | Popis | Možný důvod |
|------------------|-------------|-----------------|
| 200 | OK | Požadavek byl úspěšný. |
| 400 | Chybný požadavek | Požadovaný parametr chybí, je prázdný nebo null. Nebo hodnota předaná požadovanému nebo volitelnému parametru je neplatná. Běžným problémem je záhlaví, které je příliš dlouhé. |
| 401 | Neautorizováno | Požadavek není autorizován. Zkontrolujte, zda je klíč nebo token předplatného platný a ve správné oblasti. |
| 429 | Příliš mnoho požadavků | Překročili jste kvótu nebo míru požadavků povolených pro vaše předplatné. |
| 502 | Špatná brána    | Problém na straně sítě nebo serveru. Může také znamenat neplatné záhlaví. |


## <a name="convert-text-to-speech"></a>Převod textu na řeč

Koncový `v1` bod umožňuje převést převod textu na řeč pomocí [jazyka značek syntézy řeči (SSML).](speech-synthesis-markup.md)

### <a name="regions-and-endpoints"></a>Oblasti a koncové body

Tyto oblasti jsou podporovány pro převod textu na řeč pomocí rozhraní REST API. Ujistěte se, že vyberete koncový bod, který odpovídá oblasti předplatného.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Hlavičky požadavku

V této tabulce jsou uvedena požadovaná a volitelná záhlaví pro žádosti o převod textu na řeč.

| Hlavička | Popis | Povinné / Nepovinné |
|--------|-------------|---------------------|
| `Authorization` | Autorizační token, kterému `Bearer`předchází slovo . Další informace najdete v tématu [Ověřování](#authentication). | Požaduje se |
| `Content-Type` | Určuje typ obsahu pro daný text. Přijatá `application/ssml+xml`hodnota: . | Požaduje se |
| `X-Microsoft-OutputFormat` | Určuje formát zvukového výstupu. Úplný seznam přijatých hodnot naleznete v [části zvukové výstupy](#audio-outputs). | Požaduje se |
| `User-Agent` | Název aplikace. Zadaný hodnota musí být menší než 255 znaků. | Požaduje se |

### <a name="audio-outputs"></a>Zvukové výstupy

Toto je seznam podporovaných zvukových formátů, `X-Microsoft-OutputFormat` které jsou odesílány v každém požadavku jako záhlaví. Každý obsahuje přenosový tok a typ kódování. Služba Řeč podporuje zvukové výstupy 24 kHz, 16 kHz a 8 kHz.

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
> Pokud vybraný formát hlasu a výstupu má různé přenosové rychlosti, zvuk se podle potřeby převzorkuje. Nicméně, 24 kHz hlasy nepodporují `audio-16khz-16kbps-mono-siren` a `riff-16khz-16kbps-mono-siren` výstupní formáty.

### <a name="request-body"></a>Text požadavku

Tělo každého `POST` požadavku je odesláno jako [jazyk značek syntézy řeči (SSML).](speech-synthesis-markup.md) SSML umožňuje zvolit hlas a jazyk syntetizované řeči vrácené službou převodu textu na řeč. Úplný seznam podporovaných hlasů naleznete v [tématu jazyková podpora](language-support.md#text-to-speech).

> [!NOTE]
> Pokud používáte vlastní hlas, tělo požadavku lze odeslat jako prostý text (ASCII nebo UTF-8).

### <a name="sample-request"></a>Požadavek na ukázku

Tento požadavek HTTP používá SSML k určení hlasu a jazyka. Pokud je délka těla dlouhá a výsledný zvuk přesahuje 10 minut - je zkrácen na 10 minut. Jinými slovy, délka zvuku nesmí přesáhnout 10 minut.

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

Podívejte se na naše rychlé starty pro konkrétní jazyk příklady:

* [.NET Jádro, C #](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>Stavové kódy HTTP

Stavový kód HTTP pro každou odpověď označuje úspěch nebo běžné chyby.

| Stavový kód HTTP | Popis | Možný důvod |
|------------------|-------------|-----------------|
| 200 | OK | Požadavek byl úspěšný. tělo odezvy je zvukový soubor. |
| 400 | Chybný požadavek | Požadovaný parametr chybí, je prázdný nebo null. Nebo hodnota předaná požadovanému nebo volitelnému parametru je neplatná. Běžným problémem je záhlaví, které je příliš dlouhé. |
| 401 | Neautorizováno | Požadavek není autorizován. Zkontrolujte, zda je klíč nebo token předplatného platný a ve správné oblasti. |
| 413 | Entita požadavku je příliš velká. | Vstup SSML je delší než 1024 znaků. |
| 415 | Nepodporovaný typ média | Je možné, že `Content-Type` bylo poskytnuto něco špatného. `Content-Type`by měla `application/ssml+xml`být nastavena na . |
| 429 | Příliš mnoho požadavků | Překročili jste kvótu nebo míru požadavků povolených pro vaše předplatné. |
| 502 | Špatná brána    | Problém na straně sítě nebo serveru. Může také znamenat neplatné záhlaví. |

Pokud je `200 OK`stav HTTP , tělo odpovědi obsahuje zvukový soubor v požadovaném formátu. Tento soubor lze přehrát při přenosu, uložení do vyrovnávací paměti nebo uložení do souboru.

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services)
- [Asynchronní syntéza pro dlouhotrvající zvuk](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)
- [Začínáme se službou Custom Voice](how-to-custom-voice.md)
