---
title: Azure Batch určené k transkripci rozhraní API
description: Ukázky
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 5af829ca076b39758973c28a44d918b9ba5782b1
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42351246"
---
# <a name="batch-transcription"></a>Dávkový přepis

Přepis batch je ideální, pokud máte velké množství zvuk. Můžete odkazovat na zvukové soubory a získat zpět přepisů v asynchronním režimu.

## <a name="batch-transcription-api"></a>Přepis rozhraní API služby batch

Přepis Batch API nabízí asynchronní převod řeči na text přepisu, společně s další funkce.

> [!NOTE]
> Rozhraní API služby Batch určené k transkripci je ideální pro volání Center, která obvykle accumulate tisíce hodin zvukového záznamu. Rozhraní API se řídí filozofií "vypal a zapomeň", což usnadňuje přepisy velkého objemu zvukové záznamy.

### <a name="supported-formats"></a>Podporované formáty

Přepis rozhraní API služby Batch podporuje následující formáty:

Název| Kanál  |
----|----------|
MP3 |   Mono   |   
MP3 |  Stereo  | 
WAV |   Mono   |
WAV |  Stereo  |

Pro stereo zvukové datové proudy určené k transkripci Batch rozdělí levého a pravého kanálu během přepis. Každé dva soubory JSON s výsledkem jsou vytvořeny z jednoho kanálu. Časová razítka na utterance umožňují vývojářům vytvořit seřazený konečné přepisu. Podle následující ukázky JSON ukazuje výstupní kanál.

```json
       {
        "recordingsUrl": "https://mystorage.blob.core.windows.net/cris-e2e-datasets/TranscriptionsDataset/small_sentence.wav?st=2018-04-19T15:56:00Z&se=2040-04-21T15:56:00Z&sp=rl&sv=2017-04-17&sr=b&sig=DtvXbMYquDWQ2OkhAenGuyZI%2BYgaa3cyvdQoHKIBGdQ%3D",
        "resultsUrls": {
            "channel_0": "https://mystorage.blob.core.windows.net/bestor-87a0286f-304c-4636-b6bd-b3a96166df28/TranscriptionData/24265e4c-e459-4384-b572-5e3e7795221f?sv=2017-04-17&sr=b&sig=IY2qd%2Fkgtz2PwRe2C88BphH4Hv%2F1VCb1UVJ33xsw%2BEY%3D&se=2018-04-23T14:48:24Z&sp=r"
        },
        "statusMessage": "None.",
        "id": "0bb95356-ff06-469d-acc7-81f9144a269a",
        "createdDateTime": "2018-04-20T14:11:57.167",
        "lastActionDateTime": "2018-04-20T14:12:54.643",
        "status": "Succeeded",
        "locale": "en-US"
    },
```

> [!NOTE]
> Přepis rozhraní API služby Batch používá službu REST pro požadování přepisů, jejich stav a přidružené výsledky. Můžete použít rozhraní API z jakéhokoli jazyka. Další část popisuje, jak se používají.

## <a name="authorization-token"></a>Autorizační token

Jak se všemi funkcemi Unified Speech Service, vytvořte klíč předplatného z [webu Azure portal](https://portal.azure.com). Kromě toho získání klíče rozhraní API pro rozpoznávání řeči portálu: 

1. Přihlaste se k [Custom Speech](https://customspeech.ai).

2. Vyberte **Předplatná**.

3. Vyberte **vygenerovat klíč rozhraní API**.

    ![Snímek obrazovky předplatná řeči vlastní stránky](media/stt/Subscriptions.jpg)

4. Zkopírujte a vložte tento klíč v klientském kódu v následujícím příkladu.

> [!NOTE]
> Pokud budete chtít použít vlastní model, budete potřebovat ID tohoto modelu příliš. Všimněte si, že to není ID nasazení nebo koncový bod, najdete v zobrazení Podrobnosti o koncovém bodu. To je ID modelu, který můžete načíst při výběru podrobnosti tohoto modelu.

## <a name="sample-code"></a>Ukázka kódu

Upravte následující vzorový kód s klíč předplatného a klíč rozhraní API. To umožňuje získat nosný token.

```cs
    public static async Task<CrisClient> CreateApiV1ClientAsync(string username, string key, string hostName, int port)
        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;

            var tokenProviderPath = "/oauth/ctoken";
            var clientToken = await CreateClientTokenAsync(client, hostName, port, tokenProviderPath, username, key).ConfigureAwait(false);
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", clientToken.AccessToken);

            return new CrisClient(client);
        }
```

Po obdržení tokenu, je nutné zadat identifikátor URI SAS odkazující na zvukový soubor, které vyžadují určené k transkripci. Zbytek kódu prochází stav a zobrazí výsledky.

```cs
   static async Task TranscribeAsync()
        { 
            // Creating a Batch transcription API Client
            var client = 
                await CrisClient.CreateApiV1ClientAsync(
                    "<your msa>", // MSA email
                    "<your api key>", // API key
                    "stt.speech.microsoft.com",
                    443).ConfigureAwait(false);
            
            var newLocation = 
                await client.PostTranscriptionAsync(
                    "<selected locale i.e. en-us>", // Locale 
                    "<your subscription key>", // Subscription Key
                    new Uri("<SAS URI to your file>")).ConfigureAwait(false);

            var transcription = await client.GetTranscriptionAsync(newLocation).ConfigureAwait(false);

            while (true)
            {
                transcription = await client.GetTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                if (transcription.Status == "Failed" || transcription.Status == "Succeeded")
                {
                    Console.WriteLine("Transcription complete!");

                    if (transcription.Status == "Succeeded")
                    {
                        var resultsUri = transcription.ResultsUrls["channel_0"];

                        WebClient webClient = new WebClient();

                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(resultsUri, filename);

                        var results = File.ReadAllText(filename);
                        Console.WriteLine(results);
                    }

                    await client.DeleteTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                    break;
                }
                else
                {
                    Console.WriteLine("Transcription status: " + transcription.Status);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.ReadLine();
        }
```

> [!NOTE]
> V předchozím kódu je klíč předplatného z Speech(Preview) prostředku, kterou vytvoříte na webu Azure portal. Klíče získané z prostředku služby Custom Speech Service nefungují.

Všimněte si, že asynchronní instalační program pro zvuk odesílání a příjem určené k transkripci stav. Vytvoření klienta je klienta .NET protokolu Http. Je `PostTranscriptions` metodu pro odesílání podrobnosti zvukový soubor a `GetTranscriptions` metoda na příjem výsledků. `PostTranscriptions` Vrátí popisovač, a `GetTranscriptions` tento popisovač používá k vytvoření popisovače k získání stavu určené k transkripci.

Aktuální vzorový kód neurčuje žádné vlastní modely. Služba používá základní modely pro přepisování na soubor nebo soubory. K určení vzorů, můžete na stejné metodě předat ID modelu akustických a jazykový model. 

Pokud nechcete použít směrný plán, musíte předat ID modelu akustických a jazykových modelů.

> [!NOTE]
> Pro přepis směrný plán není nutné deklarovat koncovým bodům modelů směrného plánu. Pokud chcete použít vlastní modely, zadáte své ID koncové body, jako [ukázka](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Pokud chcete použít akustický směrného plánu se jazykový model směrného plánu, musíte pouze deklarovat ID vlastního modelu koncový bod. Microsoft zjistí model partnera standardní hodnoty (se ho akustický nebo jazyk) a který používá ke splnění žádosti určené k transkripci.

### <a name="supported-storage"></a>Podporované úložiště

Úložiště objektů Blob v Azure je aktuálně pouze úložiště podporované.

## <a name="downloading-the-sample"></a>Stažení ukázky

Ukázka je vidět tady je na [Githubu](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Obvykle přepisování zvukového záznamu vyžaduje časový rozsah, který se rovná době trvání zvukový soubor a režijní náklady na 2 až 3 minuty.

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
