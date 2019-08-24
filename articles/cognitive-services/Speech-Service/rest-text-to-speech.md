---
title: Referenční informace k rozhraní API pro převod textu na řeč (REST) – služba Speech
titleSuffix: Azure Cognitive Services
description: Naučte se používat REST API převodu textu na řeč. V tomto článku se dozvíte o autorizaci, možnosti dotazu, jak strukturovat žádost a přijetí odpovědi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b0a0d788c9fadd13b9a37f541a81945c86b37c29
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559183"
---
# <a name="text-to-speech-rest-api"></a>REST API převodu textu na řeč

Služba Speech Services umožňuje [převést text na syntetizované rozpoznávání řeči](#convert-text-to-speech) a [získat seznam podporovaných hlasů](#get-a-list-of-voices) pro oblast s použitím sady rozhraní REST API. Každý dostupný koncový bod je přidružen k oblasti. Vyžaduje se klíč předplatného pro koncový bod nebo oblast, kterou plánujete použít.

REST API převodu textu na řeč podporuje neuronové a standardní hlasy převodu textu na řeč, z nichž každý podporuje konkrétní jazyk a dialekt identifikovaný národním prostředím.

* Úplný seznam hlasů najdete v tématu [Podpora jazyků](language-support.md#text-to-speech).
* Informace o regionální dostupnosti najdete v tématu [oblasti](regions.md#text-to-speech).

> [!IMPORTANT]
> Náklady se liší pro standardní, vlastní a neuronové hlasy. Další informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Než začnete používat toto rozhraní API, pochopte:

* Převod textu na řeč rozhraní REST API vyžaduje také hlavičku ověřování. To znamená, že je potřeba dokončit výměnu tokenů pro přístup ke službě. Další informace najdete v tématu [Ověřování](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Získat seznam hlasů

`voices/list` Koncový bod umožňuje získat úplný seznam hlasů pro konkrétní oblast nebo koncový bod.

### <a name="regions-and-endpoints"></a>Oblasti a koncových bodů

| Oblast | Koncový bod |
|--------|----------|
| Austrálie – východ | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brazílie – jih | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kanada – střed | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Střední USA | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Východní Asie | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Východní USA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Francie – střed | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Indie – střed | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japonsko – východ | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Jižní Korea – střed | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Středoseverní USA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Severní Evropa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Středojižní USA | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Jihovýchodní Asie | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Velká Británie – jih | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Západní Evropa | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Západní USA | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA – západ 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Hlavičky požadavku

Tato tabulka obsahuje seznam požadovaných a volitelných hlaviček pro požadavky na převod textu na řeč.

| Záhlaví | Popis | Povinné / volitelné |
|--------|-------------|---------------------|
| `Authorization` | Autorizační token předcházet slovo `Bearer`. Další informace najdete v tématu [Ověřování](#authentication). | Požadováno |

### <a name="request-body"></a>Text požadavku

Pro `GET` požadavky na tento koncový bod není vyžadován text.

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
| 400 | Chybný požadavek | Povinný parametr nebyl nalezen, prázdný nebo null. Nebo hodnota předaná buď povinný nebo volitelný parametr není platný. Běžné potíže se hlavičku, která je příliš dlouhý. |
| 401 | Neautorizováno | Požadavek není autorizovaný. Ověřte váš klíč předplatného nebo token je platný a v oblasti správné. |
| 429 | Příliš mnoho žádostí | Překročili jste kvótu nebo počet požadavků pro vaše předplatné povolená. |
| 502 | Chybná brána | Problém sítě nebo na straně serveru. Může také znamenat neplatné záhlaví. |


## <a name="convert-text-to-speech"></a>Převod textu na řeč

Koncový bod umožňuje převod textu na řeč pomocí [jazyka SSML (Speech Shrnutí Markup Language).](speech-synthesis-markup.md) `v1`

### <a name="regions-and-endpoints"></a>Oblasti a koncových bodů

Tyto oblasti jsou podporovány pro převod textu na řeč pomocí rozhraní REST API. Ujistěte se, že vyberete koncového bodu, který odpovídá oblasti vašeho předplatného.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Hlavičky požadavku

Tato tabulka obsahuje seznam požadovaných a volitelných hlaviček pro požadavky na převod textu na řeč.

| Záhlaví | Popis | Povinné / volitelné |
|--------|-------------|---------------------|
| `Authorization` | Autorizační token předcházet slovo `Bearer`. Další informace najdete v tématu [Ověřování](#authentication). | Požadováno |
| `Content-Type` | Určuje typ obsahu pro zadaný text. Přijata hodnota: `application/ssml+xml`. | Požaduje se |
| `X-Microsoft-OutputFormat` | Určuje formát zvukového výstupu. Úplný seznam platných hodnot najdete v tématu [zvuku výstupy](#audio-outputs). | Požaduje se |
| `User-Agent` | Název aplikace Zadaná hodnota musí být kratší než 255 znaků. | Požadováno |

### <a name="audio-outputs"></a>Zvukový výstupy

Toto je seznam podporovaných formátů zvuku, které se odesílají v každé žádosti o jako `X-Microsoft-OutputFormat` záhlaví. Každý zahrnuje s přenosovou rychlostí a typ kódování. Služba Speech Services podporuje zvukové výstupy 24 kHz, 16 kHz a 8 kHz.

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
> Pokud vybraný hlasový a výstupní formát různé přenosové rychlosti, zvuku poklesu podle potřeby. Avšak 24 kHz hlasy nepodporuje `audio-16khz-16kbps-mono-siren` a `riff-16khz-16kbps-mono-siren` výstupní formáty.

### <a name="request-body"></a>Text požadavku

Tělo každé `POST` žádosti se odešle jako [jazyk SSML (Speech syntézy)](speech-synthesis-markup.md). SSML umožňuje zvolit hlas a jazyk syntetizované řeči, kterou vrátí služba pro převod textu na mluvené slovo. Úplný seznam podporovaných hlasů najdete v tématu [Podpora jazyků](language-support.md#text-to-speech).

> [!NOTE]
> Pokud používáte vlastní hlas, text požadavku se dá poslat jako prostý text (ASCII nebo UTF-8).

### <a name="sample-request"></a>Ukázková žádost

Tento požadavek HTTP používá k určení hlasu SSML. Text může mít maximálně 1 000 znaků.

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

* [.NET Core,C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>Stavové kódy HTTP

Stavový kód HTTP pro každou odpověď indikuje úspěch nebo běžné chyby.

| Stavový kód HTTP | Popis | Možný důvod |
|------------------|-------------|-----------------|
| 200 | OK | Žádost byla úspěšná. text odpovědi je zvukový soubor. |
| 400 | Chybný požadavek | Povinný parametr nebyl nalezen, prázdný nebo null. Nebo hodnota předaná buď povinný nebo volitelný parametr není platný. Běžné potíže se hlavičku, která je příliš dlouhý. |
| 401 | Neautorizováno | Požadavek není autorizovaný. Ověřte váš klíč předplatného nebo token je platný a v oblasti správné. |
| 413 | Příliš velká entita požadavku | Vstup SSML je delší než 1024 znaků. |
| 415 | Nepodporovaný typ média | Je možné, že bylo zadáno `Content-Type` chybné. `Content-Type`měla by být nastavena `application/ssml+xml`na. |
| 429 | Příliš mnoho žádostí | Překročili jste kvótu nebo počet požadavků pro vaše předplatné povolená. |
| 502 | Chybná brána | Problém sítě nebo na straně serveru. Může také znamenat neplatné záhlaví. |

Pokud je stav protokolu HTTP `200 OK`, tělo odpovědi obsahuje zvukový soubor v požadovanému formátu. Tento soubor můžete přehrát má přenést, uložit do vyrovnávací paměti, nebo uložit do souboru.

## <a name="next-steps"></a>Další postup

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
