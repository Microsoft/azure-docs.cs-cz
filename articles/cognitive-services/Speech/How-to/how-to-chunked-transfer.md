---
title: Postup zvukový datový proud bloku přenos | Microsoft Docs
description: Postup použití bloku trasfer pro rozpoznávání řeči službě odesílat datový proud zvuku
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 7d02340932dfc547893c4c40cbe08978b7b93756
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342627"
---
# <a name="chunked-transfer-encoding"></a>Blokové kódování přenosu

K transcribe řeči na text, rozpoznávání řeči Microsoft rozhraní API můžete pro odesílání zvukovém souboru jako jeden celý bloku nebo rozdělení na malé bloky zvukovém souboru. Pro efektivní zvuk streamování a sníží se latence přepis, je doporučeno používat [blokové kódování přenosu](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) k vysílání datového proudu zvuk ke službě. Jiných implementacích může vést k vyšší latence pohledu uživatele. Další informace najdete v tématu [datové proudy zvuk](../concepts.md#audio-streams) stránky.

> [!NOTE]
> Nemusí nahrát více než 10 sekund zvuk v jakékoli jeden požadavek a doba trvání celkový požadavek nesmí překročit 14 sekund.
> [!NOTE]
> Je třeba zadat bloku přenos kódování, pouze pokud používáte [rozhraní REST API](../GetStarted/GetStartedREST.md) volat službu řeči. Aplikace, které používají [klientské knihovny](../GetStarted/GetStartedClientLibraries.md) není potřeba konfigurovat blokové kódování přenosu.

Následující kód ukazuje, jak nastavit blokové kódování přenosu a k odeslání zvukový soubor se blokové do bloků, 1024 bajtů.

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
