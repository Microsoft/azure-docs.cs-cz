---
title: Použít převod textu na řeč pomocí hlasové služby
description: Další informace o použití převod textu na řeč služby řeči.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: d19b71542f032111bbd3d9c7f3fe246110377b5d
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127582"
---
# <a name="use-text-to-speech-in-speech-service"></a>Použití "Převod textu na řeč" v Speech service

Speech service poskytuje funkce pro převod textu na řeč prostřednictvím jednoduché požadavku HTTP. Publikovat text, který se budou na příslušný koncový bod, a tato služba vrátí zvukový soubor (`.wav`) obsahující syntetizovat řeči. Aplikace může potom použít zvuková jako vlastní stavový objekt.

Text příspěvku požadavku pro převod textu na řeč, může být prostý text (ASCII nebo UTF8) nebo [SSML](speech-synthesis-markup.md) dokumentu. Prostého textu žádosti se používá s hlasem výchozí. Ve většině případů budete chtít používat SSML text. Musí zahrnovat požadavek HTTP [autorizace](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#authentication) token. 

Koncové body regionální převod textu na řeč se tady zobrazí. Použijte mechanismem vhodným pro vaše předplatné.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>Zadejte hlasový vstup

Pokud chcete nastavit hlas, použijte `<voice>` [SSML](speech-synthesis-markup.md) značky. Příklad:

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

Zobrazit [převod textu na řeč hlasy](supported-languages.md#text-to-speech) seznam dostupných hlasů a jejich názvy.

## <a name="make-a-request"></a>Vytvořit žádost

Žádost o převod textu na řeč HTTP se provádí v režimu POST s textem, budou v textu požadavku. Maximální délka obsahu žádosti HTTP je 1024 znaků. Žádost musí mít následující hlavičky: 

Záhlaví|Hodnoty|Komentáře
-|-|-
|`Content-Type` | `application/ssml+xml` | Formát vstupního textu.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`audio-16khz-16kbps-mono-siren`<br>`riff-16khz-16kbps-mono-siren`<br>`riff-16khz-16bit-mono-pcm`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | Zvukový formát výstupu.
|`User-Agent`   |Název aplikace | Název aplikace je povinné a musí být kratší než 255 znaků.
| `Authorization`   | Autorizační token získaný tím, že předloží váš klíč předplatného pro službu tokenů. Každý token je platný 10 minut. Zobrazit [rozhraní REST API: ověřování](rest-apis.md#authentication).

> [!NOTE]
> Pokud vybraný hlasový a výstupní formát různé přenosové rychlosti, zvuku poklesu podle potřeby. hlasy 24khz nepodporují `audio-16khz-16kbps-mono-siren` a `riff-16khz-16kbps-mono-siren` formáty výstupu. 

Ukázka požadavku je uveden níže.

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

Text se stavem odpovědi 200 zvukový v zadané výstupní formát.

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

Pokud dojde k chybě, se používají stavové kódy níže. Text odpovědi chybu obsahuje také popis problému.

|Kód|Popis|Problém|
|-|-|-|
400 |Chybný požadavek |Povinný parametr nebyl nalezen, prázdný nebo null. Nebo hodnota předaná buď povinný nebo volitelný parametr není platný. Běžné potíže se hlavičku, která je příliš dlouhý.
401|Neautorizováno |Požadavek není autorizovaný. Ověřte váš klíč předplatného nebo je token platný.
413|Příliš velká entita požadavku|Vstup SSML je delší než 1024 znaků.
|502|Chybná brána    | Problém sítě nebo na straně serveru. Může také znamenat neplatné záhlaví.

Další informace o textu k rozhraní REST API pro rozpoznávání řeči, naleznete v tématu [rozhraní REST API](rest-apis.md#text-to-speech).

## <a name="next-steps"></a>Další postup

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznávání řeči v jazyce C++](quickstart-cpp-windows.md)
- [Rozpoznávání řeči v jazyce C#](quickstart-csharp-dotnet-windows.md)
- [Rozpoznávání řeči v jazyce Java](quickstart-java-android.md)
