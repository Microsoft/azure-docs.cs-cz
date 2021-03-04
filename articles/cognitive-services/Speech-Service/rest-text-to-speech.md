---
title: Referenční informace k rozhraní API pro převod textu na řeč (REST) – služba Speech
titleSuffix: Azure Cognitive Services
description: Naučte se používat REST API převodu textu na řeč. V tomto článku se seznámíte s možnostmi autorizace, možnostmi dotazů, postupy strukturování požadavků a přijetím odpovědi.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: e5872b6ab97a47c09081cf9c26fb7f140b110c3f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733444"
---
# <a name="text-to-speech-rest-api"></a>Rozhraní REST API pro převod textu na řeč

Služba rozpoznávání řeči umožňuje [převést text na syntetizované rozpoznávání řeči](#convert-text-to-speech) a [získat seznam podporovaných hlasů](#get-a-list-of-voices) pro oblast s použitím sady rozhraní REST API. Každý dostupný koncový bod je přidružen k oblasti. Vyžaduje se klíč předplatného pro koncový bod nebo oblast, kterou plánujete použít.

REST API převodu textu na řeč podporuje neuronové a standardní hlasy převodu textu na řeč, z nichž každý podporuje konkrétní jazyk a dialekt identifikovaný národním prostředím.

* Úplný seznam hlasů najdete v tématu [Podpora jazyků](language-support.md#text-to-speech).
* Informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#text-to-speech).

> [!IMPORTANT]
> Náklady se liší pro standardní, vlastní a neuronové hlasy. Další informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Než začnete používat toto rozhraní API, pochopte:

* REST API převodu textu na řeč vyžaduje autorizační hlavičku. To znamená, že při přístupu ke službě musíte dokončit výměnu tokenů. Další informace najdete v tématu [Ověřování](#authentication).

> [!TIP]
> V [tomto článku](sovereign-clouds.md) najdete Azure Government a koncových bodů Azure Čína.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Získat seznam hlasů

`voices/list`Koncový bod umožňuje získat úplný seznam hlasů pro konkrétní oblast nebo koncový bod.

### <a name="regions-and-endpoints"></a>Oblasti a koncové body

| Oblast | Koncový bod |
|--------|----------|
| Austrálie – východ | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brazílie – jih | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Střední Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA – střed | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Východní Asie | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA – východ 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Francie – střed | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Indie – střed | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japonsko – východ | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Jižní Korea – střed | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA – středosever | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Severní Evropa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Jižní Afrika – sever | `https://southafricanorth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Středojižní USA | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Southeast Asia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Spojené království – jih | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA – středozápad | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| West Europe | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA – západ | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Západní USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

> [!TIP]
> [Hlasy ve verzi Preview](language-support.md#neural-voices-in-preview) jsou dostupné jenom v těchto třech oblastech: Východní USA, západní Evropa a jihovýchodní Asie.

### <a name="request-headers"></a>Hlavičky požadavku

Tato tabulka obsahuje seznam požadovaných a volitelných hlaviček pro požadavky na převod textu na řeč.

| Hlavička | Popis | Požadováno/volitelné |
|--------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Klíč předplatného služby Speech. | Buď toto záhlaví, nebo `Authorization` je povinné. |
| `Authorization` | Autorizační token předchází slovu `Bearer` . Další informace najdete v tématu [Ověřování](#authentication). | Buď toto záhlaví, nebo `Ocp-Apim-Subscription-Key` je povinné. |



### <a name="request-body"></a>Text požadavku

Pro `GET` požadavky na tento koncový bod není vyžadován text.

### <a name="sample-request"></a>Ukázková žádost

Tato žádost vyžaduje pouze hlavičku autorizace.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
```

### <a name="sample-response"></a>Ukázková odpověď

Tato odpověď byla zkrácena, aby ukázala strukturu odpovědi.

> [!NOTE]
> Dostupnost hlasu se liší podle oblasti nebo koncového bodu.

```json
[
  {
    "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
    "DisplayName": "Hoda",
    "LocalName": "هدى",
    "ShortName": "ar-EG-Hoda",
    "Gender": "Female",
    "Locale": "ar-EG",
    "SampleRateHertz": "16000",
    "VoiceType": "Standard",
    "Status": "GA"
  },

...
      
    {
    "Name": "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)",
    "DisplayName": "Aria",
    "LocalName": "Aria",
    "ShortName": "en-US-AriaNeural",
    "Gender": "Female",
    "Locale": "en-US",
    "StyleList": [
      "chat",
      "customerservice",
      "newscast-casual",
      "newscast-formal",
      "cheerful",
      "empathetic"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "GA"
  },
  
  ...
    
     {
    "Name": "Microsoft Server Speech Text to Speech Voice (ga-IE, OrlaNeural)",
    "DisplayName": "Orla",
    "LocalName": "Orla",
    "ShortName": "ga-IE-OrlaNeural",
    "Gender": "Female",
    "Locale": "ga-IE",
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },
  
  ...
    
   {
    "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, YunxiNeural)",
    "DisplayName": "Yunxi",
    "LocalName": "云希",
    "ShortName": "zh-CN-YunxiNeural",
    "Gender": "Male",
    "Locale": "zh-CN",
    "StyleList": [
      "Calm",
      "Fearful",
      "Cheerful",
      "Disgruntled",
      "Serious",
      "Angry",
      "Sad",
      "Depressed",
      "Embarrassed"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

    ...
]
```

### <a name="http-status-codes"></a>Stavové kódy HTTP

Stavový kód HTTP pro každou odpověď indikuje úspěch nebo běžné chyby.

| Stavový kód HTTP | Popis | Možný důvod |
|------------------|-------------|-----------------|
| 200 | OK | Požadavek byl úspěšný. |
| 400 | Chybný požadavek | Povinný parametr chybí, je prázdný nebo má hodnotu null. Nebo hodnota předaná buď požadovanému, nebo volitelnému parametru není platná. Běžným problémem je záhlaví, které je příliš dlouhé. |
| 401 | Neautorizováno | Požadavek není autorizovaný. Zkontrolujte, jestli je klíč předplatného nebo token platný a ve správné oblasti. |
| 429 | Příliš mnoho žádostí | Překročili jste kvótu nebo míru požadavků povolených pro vaše předplatné. |
| 502 | Chybná brána    | Problém v síti nebo na straně serveru. Může také označovat neplatné hlavičky. |


## <a name="convert-text-to-speech"></a>Převod textu na řeč

`v1`Koncový bod umožňuje převod textu na řeč pomocí [jazyka SSML (Speech Shrnutí Markup Language)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Oblasti a koncové body

Tyto oblasti jsou podporované pro převod textu na řeč pomocí REST API. Ujistěte se, že jste vybrali koncový bod, který odpovídá vaší oblasti předplatného.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Hlavičky požadavku

Tato tabulka obsahuje seznam požadovaných a volitelných hlaviček pro požadavky na převod textu na řeč.

| Hlavička | Popis | Požadováno/volitelné |
|--------|-------------|---------------------|
| `Authorization` | Autorizační token předchází slovu `Bearer` . Další informace najdete v tématu [Ověřování](#authentication). | Vyžadováno |
| `Content-Type` | Určuje typ obsahu pro zadaný text. Přijatá hodnota: `application/ssml+xml` . | Vyžadováno |
| `X-Microsoft-OutputFormat` | Určuje formát výstup zvuku. Úplný seznam přijatých hodnot najdete v tématu [zvukové výstupy](#audio-outputs). | Vyžadováno |
| `User-Agent` | Název aplikace. Zadaná hodnota musí být kratší než 255 znaků. | Vyžadováno |

### <a name="audio-outputs"></a>Zvukové výstupy

Toto je seznam podporovaných formátů zvuku, které se odesílají v každé žádosti jako `X-Microsoft-OutputFormat` záhlaví. Každá z nich zahrnuje přenosovou rychlost a typ kódování. Služba Speech podporuje zvukové výstupy 24 kHz, 16 kHz a 8 kHz.

```output
raw-16khz-16bit-mono-pcm            raw-8khz-8bit-mono-mulaw
riff-8khz-8bit-mono-alaw            riff-8khz-8bit-mono-mulaw
riff-16khz-16bit-mono-pcm           audio-16khz-128kbitrate-mono-mp3
audio-16khz-64kbitrate-mono-mp3     audio-16khz-32kbitrate-mono-mp3
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
audio-24khz-160kbitrate-mono-mp3    audio-24khz-96kbitrate-mono-mp3
audio-24khz-48kbitrate-mono-mp3     ogg-24khz-16bit-mono-opus
```

> [!NOTE]
> Pokud váš vybraný hlasový a výstupní formát má jiné přenosové rychlosti, zvuk se v případě potřeby převzorkuje. OGG-24khz-16bitový-mono-Opus se dá dekódovat pomocí [kodeku Opus](https://opus-codec.org/downloads/)

### <a name="request-body"></a>Text požadavku

Tělo každé `POST` žádosti se odešle jako [jazyk SSML (Speech syntézy)](speech-synthesis-markup.md). SSML umožňuje zvolit hlas a jazyk syntetizované řeči, kterou vrátí služba pro převod textu na mluvené slovo. Úplný seznam podporovaných hlasů najdete v tématu [Podpora jazyků](language-support.md#text-to-speech).

> [!NOTE]
> Pokud používáte vlastní hlas, text požadavku se dá poslat jako prostý text (ASCII nebo UTF-8).

### <a name="sample-request"></a>Ukázková žádost

Tento požadavek HTTP používá SSML k určení hlasu a jazyka. Pokud je délka těla dlouhá a výsledný zvuk překračuje 10 minut, zkrátí se na 10 minut. Jinými slovy, délka zvuku nesmí překročit 10 minut.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaNeural'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>Stavové kódy HTTP

Stavový kód HTTP pro každou odpověď indikuje úspěch nebo běžné chyby.

| Stavový kód HTTP | Popis | Možný důvod |
|------------------|-------------|-----------------|
| 200 | OK | Požadavek byl úspěšný. tělo odpovědi je zvukový soubor. |
| 400 | Chybný požadavek | Povinný parametr chybí, je prázdný nebo má hodnotu null. Nebo hodnota předaná buď požadovanému, nebo volitelnému parametru není platná. Běžným problémem je záhlaví, které je příliš dlouhé. |
| 401 | Neautorizováno | Požadavek není autorizovaný. Zkontrolujte, jestli je klíč předplatného nebo token platný a ve správné oblasti. |
| 415 | Nepodporovaný typ média | Je možné, že `Content-Type` bylo zadáno chybné. `Content-Type` měla by být nastavena na `application/ssml+xml` . |
| 429 | Příliš mnoho žádostí | Překročili jste kvótu nebo míru požadavků povolených pro vaše předplatné. |
| 502 | Chybná brána    | Problém v síti nebo na straně serveru. Může také označovat neplatné hlavičky. |

Pokud je stav HTTP `200 OK` , tělo odpovědi obsahuje zvukový soubor v požadovaném formátu. Tento soubor se dá přehrát jako přenesený, uložený do vyrovnávací paměti nebo uložený do souboru.

## <a name="next-steps"></a>Další kroky

- [Vytvoření bezplatného účtu Azure](https://azure.microsoft.com/free/cognitive-services/)
- [Asynchronní syntéza pro dlouhý formát zvuku](./long-audio-api.md)
- [Začínáme se službou Custom Voice](how-to-custom-voice.md)