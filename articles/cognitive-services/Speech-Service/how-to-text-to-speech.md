---
title: Použití textu na řeč pomocí služby řeči | Microsoft Docs
description: Další informace o použití použijte Text řeči ve službě řeči.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 6c358b5a40b1d8e91c2e1af5eb493b13604cf82e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045054"
---
# <a name="use-text-to-speech-in-speech-service"></a>Použití "Převod textu na řeč" v řeči služby

Služba rozpoznávání řeči poskytuje funkce pro převod textu na řeč prostřednictvím přehledné požadavek HTTP. POST text, který má přečteno příslušný koncový bod služby a služby vrátí zvukový soubor (`.wav`) obsahující syntetizované řeči. Aplikace pak můžete použít tento zvuk, jak ho líbí.

Text v příspěvku žádostí pro převod textu na řeč, může být prostý text (ASCII nebo UTF8) nebo [SSML](speech-synthesis-markup.md) dokumentu. Prostého textu žádosti jsou oznamována s výchozí hlasu. Ve většině případů budete chtít použít SSML textu. Požadavek HTTP musí obsahovat autorizační token. 

Zde se zobrazují regionální převod textu na řeč koncových bodů. Použijte jeden vhodné pro vaše předplatné.

[!include[](includes/endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>Zadejte hlasu

Pokud chcete zadat hlasu, použijte `<voice>` [SSML](speech-synthesis-markup.md) značky. Příklad:

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

V tématu [převod textu na řeč hlasy](supported-languages.md#text-to-speech) seznam dostupných hlasů a jejich názvy.

## <a name="make-a-request"></a>Vytvoření žádosti o

Žádost o převod textu na řeč HTTP se provádí v režimu POST s textem přečteno v textu požadavku. Maximální délka obsahu žádosti HTTP je 1024 znaků. Žádost musí mít následující hlavičky: 

Záhlaví|Hodnoty|Komentáře
-|-|-
|`Content-Type` | `application/ssml+xml` | Formát vstupního textu.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`audio-16khz-16kbps-mono-siren`<br>`riff-16khz-16kbps-mono-siren`<br>`riff-16khz-16bit-mono-pcm`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | Zvuk formát výstupu.
|`User-Agent`   |Název aplikace | Název aplikace je vyžadován a musí být kratší než 255 znaků.
| `Authorization`   | Autorizační token získat prezentací svůj klíč předplatného na službu tokenů. Každý token je platný 10 minut. V tématu [rozhraní REST API: ověřování](rest-apis.md#authentication).

> [!NOTE]
> Pokud vybraný hlas a výstupní formát různé přenosové rychlosti, podle potřeby poklesu zvukovém souboru. hlasy 24khz nepodporují `audio-16khz-16kbps-mono-siren` a `riff-16khz-16kbps-mono-siren` výstup formáty. 

Ukázková žádost je uveden níže.

```xml
POST /cognitiveservices/v1
HTTP/1.1
Host: westus.tts.speech.microsoft.com
X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
User-Agent: Test TTS application
Authorization: (authorization token)

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
</voice> </speak>
```

Text odpovědi se stavem 200 obsahuje zvuk ve formátu zadaným výstupem.

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

Pokud dojde k chybě, se používají následující stavové kódy. Text odpovědi chyby taky obsahuje popis problému.

|Kód|Popis|Problém|
|-|-|-|
400 |Chybná žádost |Požadovaný parametr nebyl nalezen, prázdný nebo null. Nebo hodnota předaná buď požadované nebo volitelné parametr je neplatný. Běžné problémy je hlavičku, která je příliš dlouhý.
401|Neautorizováno |Požadavek není ověřen. Ověřte, že svůj klíč předplatného nebo token je platný.
413|Entita požadavku je příliš velký|Vstup SSML je delší než 1024 znaků.
|502|Chybná brána    | Problém sítě nebo na straně serveru. Může taky znamenat neplatné záhlaví.

Další informace o Text, který se rozhraní REST API pro rozpoznávání řeči, najdete v části [rozhraní REST API](rest-apis.md#text-to-speech).

## <a name="next-steps"></a>Další postup

- [Získat zkušební verze předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznávat řeč v jazyce C#](quickstart-csharp-windows.md)
