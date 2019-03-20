---
title: Převod textu na řeč reference k rozhraní API (REST) – hlasové služby
titleSuffix: Azure Cognitive Services
description: Další informace o použití rozhraní API REST pro převod textu na řeč. V tomto článku se dozvíte o autorizaci, možnosti dotazu, jak strukturovat žádost a přijetí odpovědi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: d67d8462c177d19dfa3cebbd0b4b000fbe3f41b8
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57894966"
---
# <a name="text-to-speech-rest-api"></a>Převod textu na řeč REST API

Hlasové služby umožňují převést převod textu na řeč pomocí rozhraní REST API. Každý dostupný koncový bod je přidružen k oblasti. Vaše aplikace vyžaduje klíč předplatného pro koncový bod, který chcete použít. Převod textu na řeč rozhraní REST API podporuje neuronových sítí a standardní převod textu na řeč hlasů, z nichž každý podporuje konkrétní jazyku a dialektu, identifikovat podle národního prostředí.

* Úplný seznam hlasů, naleznete v tématu [jazykovou podporu](language-support.md#text-to-speech).
* Informace o dostupnosti v jednotlivých oblastech najdete v tématu [oblastech](regions.md#text-to-speech).

> [!IMPORTANT]
> Náklady se liší pro standardní, vlastní a neuronových sítí hlasy. Další informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Před použitím tohoto rozhraní API, porozumět:

* Převod textu na řeč rozhraní REST API vyžaduje také hlavičku ověřování. To znamená, že je potřeba dokončit výměnu tokenů pro přístup ke službě. Další informace najdete v tématu [Ověřování](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="request-headers"></a>Hlavičky požadavku

Tato tabulka obsahuje povinné a nepovinné hlavičky pro žádosti o převod řeči na text.

| Hlavička | Popis | Povinné / volitelné |
|--------|-------------|---------------------|
| `Authorization` | Autorizační token předcházet slovo `Bearer`. Další informace najdete v tématu [Ověřování](#authentication). | Požaduje se |
| `Content-Type` | Určuje typ obsahu pro zadaný text. Přijata hodnota: `application/ssml+xml`. | Požaduje se |
| `X-Microsoft-OutputFormat` | Určuje formát zvukového výstupu. Úplný seznam platných hodnot najdete v tématu [zvuku výstupy](#audio-outputs). | Požaduje se |
| `User-Agent` | Název aplikace Zadaná hodnota musí být kratší než 255 znaků. | Požaduje se |

## <a name="audio-outputs"></a>Zvukový výstupy

Toto je seznam podporovaných formátů zvuku, které se odesílají v každé žádosti o jako `X-Microsoft-OutputFormat` záhlaví. Každý zahrnuje s přenosovou rychlostí a typ kódování. 24 KHz, 16 KHz, podporuje hlasových služeb a vypíše 8 KHz zvuk.

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
> Pokud vybraný hlasový a výstupní formát různé přenosové rychlosti, zvuku poklesu podle potřeby. Ale 24khz hlasy nepodporují `audio-16khz-16kbps-mono-siren` a `riff-16khz-16kbps-mono-siren` formáty výstupu.

## <a name="request-body"></a>Text požadavku

Text jednotlivých `POST` žádosti se odešle jako [řeči syntézu Markup Language (SSML)](speech-synthesis-markup.md). SSML můžete rozhodnout voice a jazyk řečového vrácené službou převod textu na řeč. Úplný seznam podporovaných hlasů, naleznete v tématu [jazykovou podporu](language-support.md#text-to-speech).

> [!NOTE]
> Pokud používáte vlastní hlasové, tělo požadavku nelze odesílat jako prostý text (ASCII nebo UTF-8).

## <a name="sample-request"></a>Ukázková žádost

Tento požadavek HTTP používá k určení hlasu SSML. Text může mít maximálně 1 000 znaků.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Najdete v našich rychlých startů pro konkrétní jazyk příklady:

* [.NET Core, C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.js](quickstart-nodejs-text-to-speech.md)

## <a name="http-status-codes"></a>Stavové kódy HTTP

Stavový kód HTTP pro každou odpověď indikuje úspěch nebo běžné chyby.

| Stavový kód HTTP | Popis | Možný důvod |
|------------------|-------------|-----------------|
| 200 | OK | Žádost byla úspěšná. text odpovědi je zvukový soubor. |
| 400 | Chybný požadavek | Povinný parametr nebyl nalezen, prázdný nebo null. Nebo hodnota předaná buď povinný nebo volitelný parametr není platný. Běžné potíže se hlavičku, která je příliš dlouhý. |
| 401 | Neautorizováno | Požadavek není autorizovaný. Ověřte váš klíč předplatného nebo token je platný a v oblasti správné. |
| 413 | Příliš velká entita požadavku | Vstup SSML je delší než 1024 znaků. |
| 429 | Příliš mnoho žádostí | Překročili jste kvótu nebo počet požadavků pro vaše předplatné povolená. |
| 502 | Chybná brána | Problém sítě nebo na straně serveru. Může také znamenat neplatné záhlaví. |

Pokud je stav protokolu HTTP `200 OK`, tělo odpovědi obsahuje zvukový soubor v požadovanému formátu. Tento soubor můžete přehrát má přenést, uložit do vyrovnávací paměti, nebo uložit do souboru.

## <a name="next-steps"></a>Další postup

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
