---
title: Jak používat jiné služby Batch – hlasové služby
titlesuffix: Azure Cognitive Services
description: Přepis batch je ideální, pokud chcete přepisy velké množství zvuk v úložišti, jako jsou objekty BLOB Azure. Pomocí vyhrazené rozhraní REST API můžete odkazovat na zvukové soubory pomocí sdíleného přístupového podpisu (SAS) identifikátor URI a asynchronně přijímat přepisů.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: bf89180ea98473d2da3495286396a12c6f25288f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228657"
---
# <a name="why-use-batch-transcription"></a>Proč používat službu Batch určené k transkripci?

Přepis batch je ideální, pokud chcete přepisy velké množství zvuk v úložišti, jako jsou objekty BLOB Azure. Pomocí vyhrazené rozhraní REST API můžete odkazovat na zvukové soubory pomocí sdíleného přístupového podpisu (SAS) identifikátor URI a asynchronně přijímat přepisů.

>[!NOTE]
> Standardní předplatné (S0) pro hlasové služby je potřeba pomocí služby batch určené k transkripci. Bezplatné předplatné klíče (F0) nebudou fungovat. Další informace najdete v tématu [ceny a omezení](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/).

## <a name="the-batch-transcription-api"></a>Přepis rozhraní API služby Batch

Rozhraní API služby Batch určené k transkripci nabízí asynchronní přepis řeči na text, společně s další funkce. Je rozhraní REST API, který poskytuje metody pro:

1. Vytváření žádostí o zpracování služby batch
1. Stav dotazu
1. Přepisy stahování

> [!NOTE]
> Rozhraní API služby Batch určené k transkripci je ideální pro volání Center, která obvykle accumulate tisíce hodin zvukového záznamu. Rozhraní API se řídí filozofií "vypal a zapomeň", což usnadňuje přepisy velké objemy zvukových nahrávek.

### <a name="supported-formats"></a>Podporované formáty

Rozhraní API služby Batch určené k transkripci podporuje následující formáty:

| Formát | Kodek | S přenosovou rychlostí | Vzorkovací frekvence |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bitů | 8 nebo 16 mono, stereo kHz, |
| MP3 | PCM | 16 bitů | 8 nebo 16 mono, stereo kHz, |
| OGG | DÍLE | 16 bitů | 8 nebo 16 mono, stereo kHz, |

> [!NOTE]
> Rozhraní API služby Batch určené k transkripci vyžaduje klíč rozhraní S0 (platit úroveň). Nefunguje s klíčem free (f0).

Přepis rozhraní API služby Batch pro stereo zvukové datové proudy, rozdělí levého a pravého kanálu během přepis. Každé dva soubory JSON s výsledkem jsou vytvořeny z jednoho kanálu. Časová razítka na utterance umožňují vývojářům vytvořit seřazený konečné přepisu. Podle následující ukázky JSON ukazuje výstup kanál, includuing vlastností pro nastavení filtr vulgárních výrazů a interpunkční znaménka modelu.

```json
{
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "models": [],
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "description": "An optional description of the transcription.",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic"
  },
```

> [!NOTE]
> Rozhraní API určené k transkripci služby Batch používá službu REST pro požadování přepisů, jejich stav a přidružené výsledky. Můžete použít rozhraní API z jakéhokoli jazyka. Další část popisuje, jak se používá rozhraní API.

### <a name="query-parameters"></a>Parametry dotazu

Tyto parametry mohou být zahrnuty v řetězci dotazu požadavku REST.

| Parametr | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | Určuje způsob zpracování vulgárních výrazů v výsledky rozpoznávání. Platné hodnoty jsou `none` který zakáže filtrování vulgárních výrazů `masked` hvězdičky, která nahradí vulgárních výrazů `removed` výsledek, který zruší všechny vulgárních výrazů nebo `tags` které přidá značky "vulgárních výrazů". Ve výchozím nastavení je `masked`. | Nepovinné |
| `PunctuationMode` | Určuje způsob zpracování interpunkce v výsledky rozpoznávání. Platné hodnoty jsou `none` který zakáže interpunkční znaménka, `dictated` což naznačuje explicitní interpunkce, `automatic` které umožní dekodér řešit interpunkční znaménka, nebo `dictatedandautomatic` což naznačuje nařízeny interpunkční znaménka nebo automaticky. | Nepovinné |


## <a name="authorization-token"></a>Autorizační token

Se všemi funkcemi služby řeči, při vytváření odběru klíč z [webu Azure portal](https://portal.azure.com) podle našich [Příručka Začínáme](get-started.md). Pokud budete chtít získat přepisů z našich základní modely, vytváří se klíč je všechno, co musíte udělat.

Pokud budete chtít přizpůsobit a použití vlastního modelu, přidáte klíč předplatného portál custom speech následujícím způsobem:

1. Přihlaste se k [Custom Speech](https://customspeech.ai).

2. V pravém horním rohu, vyberte **předplatná**.

3. Vyberte **připojit stávající předplatné**.

4. V místním okně přidejte alias a klíč předplatného.

    ![V okně Přidat předplatné](media/stt/Subscriptions.jpg)

5. Zkopírujte a vložte tento klíč v klientském kódu v následujícím příkladu.

> [!NOTE]
> Pokud budete chtít použít vlastní model, budete potřebovat ID tohoto modelu příliš. Toto ID není ID koncového bodu, které se nachází v zobrazení Podrobnosti o koncovém bodu. To je ID modelu, který můžete načíst při výběru podrobnosti tohoto modelu.

## <a name="sample-code"></a>Ukázka kódu

Upravte následující vzorový kód s klíč předplatného a klíč rozhraní API. Tato akce umožňuje získat nosný token.

```cs
     public static CrisClient CreateApiV2Client(string key, string hostName, int port)

        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

            return new CrisClient(client);
        }
```

Po získání tokenu, zadejte identifikátor URI SAS, který odkazuje na zvukový soubor, který vyžaduje přepis. Zbytek kódu prochází stav a zobrazí výsledky. Zpočátku je nastavit klíč, oblast, modely a SA, jak je znázorněno v následujícím fragmentu kódu. V dalším kroku vytvoříte instanci klienta a požadavek POST.

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;

            // SAS URI
            private const string RecordingsBlobUri = "SAS URI pointing to the file in Azure Blob Storage";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("guid of the acoustic adaptation model");
            private static Guid AdaptedLanguageId = new Guid("guid of the language model");

            // Creating a Batch Transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);

            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

Teď, když jste provedli požadavek, můžete vyhledat a stáhnout výsledky určené k transkripci, jak je znázorněno v následujícím fragmentu kódu:

```cs

            // get all transcriptions for the user
            transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            // for each transcription in the list we check the status
            foreach (var transcription in transcriptions)
            {
                switch(transcription.Status)
                {
                    case "Failed":
                    case "Succeeded":

                            // we check to see if it was one of the transcriptions we created from this client.
                        if (!createdTranscriptions.Contains(transcription.Id))
                        {
                            // not created from here, continue
                            continue;
                        }

                        completed++;

                        // if the transcription was successful, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succeeded. Results: ");
                            Console.WriteLine(results);
                        }

                    break;
                    case "Running":
                    running++;
                     break;
                    case "NotStarted":
                    notStarted++;
                    break;

                    }
                }
            }
        }
```

Úplné podrobnosti o předchozí volání, najdete v našich [dokument swagger](https://westus.cris.ai/swagger/ui/index). Úplnou ukázku je znázorněno zde, v části [Githubu](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> V předchozím kódu klíč předplatného se z prostředku řeči, kterou vytvoříte na webu Azure Portal. Klíče, které jste získali z prostředku služby Custom Speech Service nefungují.

Poznamenejte si nastavení asynchronní pro zvuk odesílání a příjem určené k transkripci stav. Klient, který vytvoříte je klienta .NET protokolu HTTP. Je `PostTranscriptions` metodu pro odesílání podrobnosti zvukový soubor a `GetTranscriptions` metodu pro příjem výsledků. `PostTranscriptions` Vrátí popisovač, a `GetTranscriptions` používá k vytvoření popisovače se získat stav určené k transkripci.

Aktuální vzorový kód neurčuje vlastního modelu. Služba používá základní modely pro přepisování na soubor nebo soubory. K určení vzorů, můžete předat na stejné metodě jako ID modelu akustických a jazykový model.

Pokud nechcete použít směrný plán, předejte ID modelu akustických a jazykových modelů.

> [!NOTE]
> Pro směrný plán přepisů není nutné deklarovat koncovým bodům modelů směrného plánu. Pokud chcete použít vlastní modely, zadáte své ID koncové body, jako [ukázka](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Pokud chcete použít akustický směrného plánu se jazykový model směrný plán, je nutné deklarovat pouze ID vlastního modelu koncový bod. Microsoft zjistí model směrného plánu partnera&mdash;zda akustických a v jakémkoli jazyce&mdash;a použije ho ke splnění žádosti určené k transkripci.

### <a name="supported-storage"></a>Podporované úložiště

Úložiště objektů Blob v Azure je v současné době pouze úložiště podporované.

## <a name="download-the-sample"></a>Stažení ukázky

Ukázka v tomto článku můžete najít na [Githubu](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Přepisování zvukového záznamu obvykle vyžaduje časový rozsah, který je rovna hodnotě doba trvání zvukový soubor a dvěma na tři minuty režii.

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
