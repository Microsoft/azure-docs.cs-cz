---
title: Postup přenosu zvukového datového proudu do bloků dat | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Použití bloku trasfer k odesílání zvukového datového proudu do služby Zpracování řeči Bingu
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 60428a7a5e28b7c9d6cd76f31374f24df48423a6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966488"
---
# <a name="chunked-transfer-encoding"></a>Kódování blokového přenosu

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

K přepisovat řeči na text vám rozhraní API pro rozpoznávání řeči od Microsoftu umožňuje odeslat zvuk jako jeden celý blok nebo CHOP zvuk do malých bloků dat. Pro efektivní streamování zvuku a snížení latence přepisu se doporučuje použít [kódování blokového přenosu](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) ke streamování zvuku do služby. Další implementace mohou vést k vyšší latenci uživateli. Další informace najdete na stránce [zvukové streamy](../concepts.md#audio-streams) .

> [!NOTE]
> Nesmíte nahrávat více než 10 sekund zvukového záznamu v jednom požadavku a celková doba trvání žádosti nesmí překročit 14 sekund.
> [!NOTE]
> Kódování v bloku dat je nutné zadat pouze v případě, že používáte [rozhraní REST API](../GetStarted/GetStartedREST.md) pro volání služby řeči. Aplikace, které používají [klientské knihovny](../GetStarted/GetStartedClientLibraries.md) , nemusí konfigurovat kódování přenosů v bloku.

Následující kód ukazuje, jak nastavit kódování blokového přenosu a odeslat zvukový soubor do bloku na 1024 bajtů.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```
