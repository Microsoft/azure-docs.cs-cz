---
title: 'Rychlý start: Rozhraní Translator Speech API v jazyce C#'
titlesuffix: Azure Cognitive Services
description: Získejte informace a ukázky kódu, které vám pomůžou rychle začít používat rozhraní Translator Speech API.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: quickstart
ms.date: 3/5/2018
ms.author: v-jaswel
ROBOTS: NOINDEX
ms.openlocfilehash: 7d1f5968fe6ddffce3194f070b6a17aca4d3d9ab
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995036"
---
# <a name="quickstart-translator-speech-api-with-c"></a>Rychlý start: Rozhraní Translator Speech API v jazyce C# 
<a name="HOLTop"></a>

V tomto článku se dozvíte, jak použít rozhraní Translator Speech API k překladu vyslovených slov do souboru .wav.

## <a name="prerequisites"></a>Požadavky

Ke spuštění tohoto kódu ve Windows budete potřebovat [Visual Studio 2017](https://www.visualstudio.com/downloads/). (Bude stačit bezplatná verze Community Edition.)

Budete potřebovat soubor .wav s názvem „speak.wav“ ve stejné složce jako spustitelný soubor, který zkompilujete z níže uvedeného kódu. Tento soubor .wav musí být ve standardním 16bitovém, 16kHz monofonním formátu PCM.

Musíte mít [účet rozhraní Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s rozhraním **Microsoft Translator Speech API**. Budete potřebovat placené klíče předplatného ze svého [řídicího panelu Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Překlad řeči

Následující kód překládá řeč z jednoho jazyka do druhého.

1. Ve svém oblíbeném prostředí IDE vytvořte nový projekt C#.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```csharp
using System;
using System.IO;
using System.Net.WebSockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace TranslateSpeechQuickStart
{
    class Program
    {
        static string host = "wss://dev.microsofttranslator.com";
        static string path = "/speech/translate";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        async static Task Send (ClientWebSocket client, string input_path)
        {
            var audio = File.ReadAllBytes(input_path);
            var audio_out_buffer = new ArraySegment<byte>(audio);
            Console.WriteLine("Sending audio.");
            await client.SendAsync(audio_out_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            /* Make sure the audio file is followed by silence.
             * This lets the service know that the audio input is finished. */
            var silence = new byte[3200000];
            var silence_buffer = new ArraySegment<byte>(silence);
            await client.SendAsync(silence_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            Console.WriteLine("Done sending.");
            await client.CloseAsync(WebSocketCloseStatus.NormalClosure, "", CancellationToken.None);
        }

        async static Task Receive(ClientWebSocket client, string output_path)
        {
            var inbuf = new byte[102400];
            var segment = new ArraySegment<byte>(inbuf);
            var stream = new FileStream(output_path, FileMode.Create);

            Console.WriteLine("Awaiting response.");
            while (client.State == WebSocketState.Open)
            {
                var result = await client.ReceiveAsync(segment, CancellationToken.None);
                switch (result.MessageType)
                {
                    case WebSocketMessageType.Close:
                        Console.WriteLine("Received close message. Status: " + result.CloseStatus + ". Description: " + result.CloseStatusDescription);
                        await client.CloseAsync(WebSocketCloseStatus.NormalClosure, string.Empty, CancellationToken.None);
                        break;
                    case WebSocketMessageType.Text:
                        Console.WriteLine("Received text.");
                        Console.WriteLine(Encoding.UTF8.GetString(inbuf).TrimEnd('\0'));
                        break;
                    case WebSocketMessageType.Binary:
                        Console.WriteLine("Received binary data: " + result.Count + " bytes.");
                        stream.Write(inbuf, 0, result.Count);
                        break;
                }
            }

            stream.Close();
            stream.Dispose();
        }

        async static void TranslateSpeech()
        {
            var client = new ClientWebSocket();
            client.Options.SetRequestHeader ("Ocp-Apim-Subscription-Key", key);

            string from = "en-US";
            string to = "it-IT";
            string features = "texttospeech";
            string voice = "it-IT-Elsa";
            string api = "1.0";

            string input_path = "speak.wav";
            string output_path = "speak2.wav";

            string uri = host + path +
                "?from=" + from +
                "&to=" + to +
                "&api-version=" + api +
                "&features=" + features +
                "&voice=" + voice;

            Console.WriteLine("uri: " + uri);
            Console.WriteLine("Opening connection.");
            await client.ConnectAsync(new Uri(uri), CancellationToken.None);
            Console.WriteLine("Connection open.");
            Task.WhenAll(Send(client, input_path), Receive(client, output_path)).Wait();
        }

        static void Main()
        {
            TranslateSpeech();
            Console.ReadLine();
        }

    }
}
```

**Odpověď s přeloženou řečí**

Úspěšným výsledkem je vytvoření souboru s názvem „speak2.wav“. Tento soubor obsahuje překlad vyslovených slov ze souboru „speak.wav“.

[Zpět na začátek](#HOLTop)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz ke službě Translator Speech](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Viz také 

[Přehled služby Translator Speech](../overview.md)
[Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
