---
title: Azure Batch přepis rozhraní API | Azure Microsoft Docs
description: Ukázky
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 01bbf4ca19b0fb702aa76d5149fb0e38389fe455
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054819"
---
# <a name="batch-transcription"></a>Přepis batch

Přepis batch je ideální pro případy použití s velkým množstvím zvukovém souboru. Umožňuje vývojáři přejděte zvukových souborů a získat zpátky transcriptions v asynchronním režimu.

## <a name="batch-transcription-api"></a>Přepis batch rozhraní API

Přepis Batch API umožňuje výše uvedené scénáře. Nabízí asynchronní převod řeči na text přepis spolu s další funkce.

> [!NOTE]
> Přepis Batch rozhraní API je ideální pro centrech volání, které obvykle hromadit tisíce hodin zvukovém souboru. Ještě efektivněji & zapomenout filosofie rozhraní API usnadňuje transcribe velkého objemu zvukových záznamů.

### <a name="supported-formats"></a>Podporované formáty

Přepis Batch API cílem je stane de facto pro scénáře související s center všechny offline volání a podporu pro všechny související formáty. Aktuálně podporované formáty:

Název| Kanál  |
----|----------|
MP3 |   Mono   |   
MP3 |  Stereofonním systémem  | 
WAV |   Mono   |
WAV |  Stereofonním systémem  |

Pro stereo zvukové datové proudy rozdělí Batch přepis během přepis levého a pravého kanálu. Každé dva soubory JSON s výsledkem jsou vytvořené z jeden kanál. Časová razítka na utterance povolit vývojáře k vytvoření seřazené konečné přepis. Následující ukázka JSON zobrazí výstup kanál.

    ```
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
> Přepis Batch API používá služby REST pro požadování transcriptions, jejich stav a přidružené výsledky. Ho je založena na rozhraní .NET a nemá žádné externí závislosti. Další část popisuje, jak se používají.

## <a name="authorization-token"></a>Autorizační token

Jak se všemi funkcemi služby Unified rozpoznávání řeči, uživatel musí vytvořit klíč předplatného z [portál Azure](https://portal.azure.com). Kromě toho musí být získali z portálu řeči klíč rozhraní API. Kroky pro vygenerování klíč rozhraní API:

1. Přihlaste se k https://customspeech.ai.

2. Klikněte na předplatná.

3. Klikněte na možnost `Generate API Key`.

    ![Nahrávání zobrazení](media/stt/Subscriptions.jpg)

4. Zkopírujte a vložte klíči v klientském kódu v následující ukázce.

> [!NOTE]
> Pokud máte v úmyslu použít vlastní model budete potřebovat Identifikátor tohoto modelu příliš. Všimněte si, že se nejedná nasazení nebo ID koncového bodu, které se nachází v zobrazení podrobností koncový bod, ale ID modelu, který může načíst po kliknutí na položku Podrobnosti tohoto modelu

## <a name="sample-code"></a>Ukázka kódu

Využitím rozhraní API je docela rovnou dál. Následující ukázkový kód je potřeba lze přizpůsobit pomocí klíč předplatného a klíč rozhraní API.

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
                    "<your subscripition key>", // Subscription Key
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
> Klíč předplatného zmiňována ve výše uvedeném fragmentu kódu je klíč z Speech(Preview) prostředku, kterou vytvoříte na portálu Azure. Klíče získané z prostředku vlastní řeči služby nebude fungovat.


Všimněte si asynchronní nastavení pro publikování zvuk a příjem přepis stavu. Vytvoření klienta je NET Http klienta. Je `PostTranscriptions` metodu pro odesílání podrobnosti zvukový soubor a `GetTranscriptions` metodu výsledky. `PostTranscriptions` Vrátí popisovač, a `GetTranscriptions` metoda používá k vytvoření popisovače získat stav přepis tento popisovač.

Aktuální ukázkový kód neurčuje žádné vlastní modely. Služba bude používat modely směrný plán pro přepisování soubory. Pokud uživatel chce zadejte modely, jeden předat na stejnou metodu modelIDs acoustic a model jazyk. 

Pokud jeden nechce používat směrného plánu, jeden musí projít ID modelu pro modely acoustic a jazyk.

> [!NOTE]
> Pro směrný plán přepis uživatel nemá k deklaraci koncových bodů modelů směrného plánu. Pokud chce uživatel použít vlastní modely má zajistit jejich ID koncové body, jako [ukázka](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Pokud chce uživatel použít akustickými směrný plán s modelem jazyk směrného plánu pak pouze má deklarovat ID vlastní modelu koncový bod. Interně bude našem systému zjistěte modelu směrného plánu partnera (být ho akustickými nebo language) a použijte ho k fullfill přepis žádosti.

### <a name="supported-storage"></a>Podporované úložiště

Se v současné době podporováno pouze úložiště objektů blob v Azure.

## <a name="downloading-the-sample"></a>Stažení ukázky

Ukázka zobrazí tady je na [Githubu](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Obvykle vyžaduje zvuk přepis rovna trvání zvukový soubor plus režijní náklady na 2 až 3 minuty časové období.

## <a name="next-steps"></a>Další postup

* [Získat zkušební verze předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
