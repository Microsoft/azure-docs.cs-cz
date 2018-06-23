---
title: Rychlý start C# pro Azure kognitivní služby Microsoft překladač řeči rozhraní API | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít používat rozhraní API pro rozpoznávání řeči překladač Microsoft v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: 6489a0be72cedffdfa4f7021f889b2d39c1c358f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342636"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-c"></a>Rychlý start pro Microsoft překladač řeči rozhraní API pomocí jazyka C# 
<a name="HOLTop"></a>

Tento článek ukazuje, jak používat rozhraní API pro rozpoznávání řeči překladač Microsoft překládat slova používaný v souboru ve formátu WAV.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat [Visual Studio 2017](https://www.visualstudio.com/downloads/) pro spuštění tohoto kódu v systému Windows. (Bezplatná edice Community bude fungovat.)

Budete potřebovat soubor ve formátu WAV s názvem "speak.wav" ve stejné složce jako spustitelný soubor, které zkompilujete z následující kód. Tento soubor ve formátu WAV musí být ve standardní PCM, 16 bitů, 16kHz mono formátu. Můžete získat takové .wav soubor z [překladač Text řeči API](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Speak).

Musíte mít [kognitivní rozhraní API služby účet](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraní API služby Microsoft překladač řeči**. Budete potřebovat předplatné klíč z vaší [řídicí panel Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Převede řeči

Následující kód překládá řeči z jednoho jazyka do druhého.

1. Vytvořte nový projekt C# v vaše oblíbená rozhraní IDE.
2. Přidejte kód níže uvedenou.
3. Nahraďte `key` hodnotu s přístupový klíč platný pro vaše předplatné.
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

**Převede řeči odpovědi**

Úspěšné výsledkem je vytvoření souboru s názvem "speak2.wav". Tento soubor obsahuje překlad slova používaný v "speak.wav".

[Zpět na začátek](#HOLTop)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Překladač řeči kurzu](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled řeči překladač](../overview.md)
[referenční dokumentace rozhraní API](http://docs.microsofttranslator.com/speech-translate.html)
