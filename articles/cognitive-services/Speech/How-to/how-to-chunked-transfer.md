---
title: Způsob přenosu bloků zvukový Stream | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Použití bloku trasfer odesílat zvukový datový proud do služby pro zpracování řeči Bingu
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 82c07332af7d4a5df4a6af15ef65abcd8a00f603
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216876"
---
# <a name="chunked-transfer-encoding"></a>Blokové kódování přenosu

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Pro přepisy převod řeči na text, rozhraní API pro rozpoznávání řeči Microsoft vám umožní odeslat zvuk jako jeden celý blok dat nebo jej zvuku do malých bloků. Pro efektivní streamování zvuku a snížení latence přepis, se doporučuje použít [blokového kódování přenosu](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) ke streamování zvuku a služby. V jiných implementacích může vést k vyšší latencí vnímané uživatele. Další informace najdete v tématu [zvukové datové proudy](../concepts.md#audio-streams) stránky.

> [!NOTE]
> Nemusí nahrávat zvuk v jedné žádosti více než 10 sekund a doba trvání celkový požadavek nemůže být delší než 14 sekund.
> [!NOTE]
> Je třeba zadat blokového kódování pouze v případě, že používáte přenosu [rozhraní REST API](../GetStarted/GetStartedREST.md) volat službu rozpoznávání řeči. Aplikace, které používají [klientské knihovny](../GetStarted/GetStartedClientLibraries.md) není potřeba konfigurovat blokového kódování přenosu.

Následující kód ukazuje, jak nastavit blokového kódování přenosu a posílat zvukový soubor se blokové do 1024 bajtů bloků.

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
