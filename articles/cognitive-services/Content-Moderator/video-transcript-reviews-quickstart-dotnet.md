---
title: Azure obsahu moderátora - vytvořit video přepis recenze pomocí rozhraní .NET | Microsoft Docs
description: Postup vytvoření video přepis zkontroluje pomocí sady Azure obsahu moderátora SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/19/2018
ms.author: sajagtap
ms.openlocfilehash: 3286da6e38f0fba02386d877a835fb694ed0fdec
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342720"
---
# <a name="create-video-transcript-reviews-using-net"></a>Vytvořit video přepis recenze pomocí rozhraní .NET

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat obsahu moderátora SDK pomocí C# do:

- Vytvoření kontrolu videa pro lidské moderátorů
- Přidání moderované přepis do kontrola
- Publikování kontrola

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte [moderovaná video](video-moderation-api.md) a [vytvořit video zkontrolujte](video-reviews-quickstart-dotnet.md) v kontrolní nástroj pro lidské rozhodování. Chcete přidat moderované video přepisy v nástroji revize.

Tento článek také předpokládá, že jste již obeznámeni s Visual Studio a C#.

### <a name="sign-up-for-content-moderator-services"></a>Zaregistrujte si obsahu moderátora služby

Před použitím služby obsahu moderátora přes rozhraní REST API nebo sady SDK, je nutné klíč předplatného.

Na řídicím panelu obsahu moderátora najít svůj klíč předplatného v **nastavení** > **pověření** > **rozhraní API**  >   **Zkušební verze Ocp-Apim-Subscription-Key**. Další informace najdete v tématu [přehled](overview.md).

### <a name="prepare-your-video-for-review"></a>Příprava videa ke kontrole

Přidejte zápis do kontrolu videa. Video musí být publikován online. Je nutné jeho koncového bodu streamování. Koncový bod streamování umožňuje přehrávání videa nástroj revize pro přehrávání videa.

![Ukázkové video miniaturu](images/ams-video-demo-view.PNG)

- Kopírování **URL** v tomto [ukázku Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) stránky pro adresu URL manifestu.

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. Přidejte nový **konzolovou aplikaci (rozhraní .NET Framework)** projekt pro vaše řešení.

1. Název projektu **VideoTranscriptReviews**.

1. Vyberte tento projekt jako jeden počáteční projekt pro řešení.

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Nainstalujte následující balíčky NuGet pro projekt TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizace programu je pomocí příkazů

Upravit program je pomocí příkazů následujícím způsobem.

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;
    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;


### <a name="add-private-properties"></a>Přidáním vlastních vlastností

Přidejte následující soukromé vlastnosti do oboru názvů VideoTranscriptReviews, třídy Program.

Pokud vyznačeno, nahraďte ukázkové hodnoty těchto vlastností.


    namespace VideoReviews
    {
        class Program
        {
            // NOTE: Replace this example location with the location for your Content Moderator account.
            /// <summary>
            /// The region/location for your Content Moderator account, 
            /// for example, westus.
            /// </summary>
            private static readonly string AzureRegion = "YOUR CONTENT MODERATOR REGION";

            // NOTE: Replace this example key with a valid subscription key.
            /// <summary>
            /// Your Content Moderator subscription key.
            /// </summary>
            private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

            // NOTE: Replace this example team name with your Content Moderator team name.
            /// <summary>
            /// The name of the team to assign the job to.
            /// </summary>
            /// <remarks>This must be the team name you used to create your 
            /// Content Moderator account. You can retrieve your team name from
            /// the Conent Moderator web site. Your team name is the Id associated 
            /// with your subscription.</remarks>
            public static readonly string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

            /// <summary>
            /// The base URL fragment for Content Moderator calls.
            /// </summary>
            private static readonly string AzureBaseURL =
                $"{AzureRegion}.api.cognitive.microsoft.com";

            /// <summary>
            /// The minimum amount of time, in milliseconds, to wait between calls
            /// to the Content Moderator APIs.
            /// </summary>
            private const int throttleRate = 2000;


### <a name="create-content-moderator-client-object"></a>Vytvoření obsahu moderátora klientského objektu

Přidejte následující definici metody do oboru názvů VideoTranscriptReviews, třídy Program.

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
        {
            BaseUrl = AzureBaseURL
        };
    }

## <a name="create-a-video-review"></a>Vytvoření kontrolu video

Vytvoření kontrolu video s **ContentModeratorClient.Reviews.CreateVideoReviews**. Další informace najdete v tématu [referenční dokumentace rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** má následující požadované parametry:
1. Řetězec, který obsahuje typ MIME, které by se měly "application/json." 
1. Název vašeho obsahu moderátora týmu.
1. **IList<CreateVideoReviewsBodyItem>**  objektu. Každý **CreateVideoReviewsBodyItem** objekt představuje kontrolu videa. Tento rychlý start vytvoří jeden zkontrolujte najednou.

**CreateVideoReviewsBodyItem** má několik vlastností. Minimálně nastavte následující vlastnosti:
- **Obsahu**. Adresa URL videa mají být zkontrolovány.
- **ContentId**. ID přiřazení ke kontrole video.
- **Stav**. Nastavit hodnotu "Unpublished." Pokud ho nenastavíte, bude výchozí na "Čekající na vyřízení", což znamená, že je publikován video přehled a čeká se na lidské revize. Po publikování videa kontrolu je již snímky videa, přepis nebo výsledku přerušování přepis do něj může přidat.

> [!NOTE]
> **CreateVideoReviews** vrátí objekt IList<string>. Každý z těchto řetězců obsahuje ID pro kontrolu videa. Tyto identifikátory jsou identifikátory GUID a nejsou stejná jako hodnota **ContentId** vlastnost. 

Přidejte následující definici metody do oboru názvů VideoReviews, třídy Program.

    /// <summary>
    /// Create a video review. For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="id">The ID to assign to the video review.</param>
    /// <param name="content">The URL of the video to review.</param>
    /// <returns>The ID of the video review.</returns>
    private static string CreateReview(ContentModeratorClient client, string id, string content)
    {
        Console.WriteLine("Creating a video review.");

        List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
            new CreateVideoReviewsBodyItem
            {
                Content = content,
                ContentId = id,
                /* Note: to create a published review, set the Status to "Pending".
                However, you cannot add video frames or a transcript to a published review. */
                Status = "Unpublished",
            }
        };

        var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

        Thread.Sleep(throttleRate);

        // We created only one review.
        return result[0];
    }

> [!NOTE]
> Klíč obsahu moderátora služby má požadavky na druhý omezení četnosti (RPS). Pokud limit překročíte, sadu SDK vyvolá výjimku s kódem 429 chyby. 
>
> Úroveň free klíč může mít jeden RPS rychlost.

## <a name="add-transcript-to-video-review"></a>Přidat přepis ke kontrole video

Přidání přepis do kontrolu video s **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** má následující požadované parametry:
1. ID obsahu moderátora týmu.
1. ID videa zkontrolujte vrácený **CreateVideoReviews**.
1. A **datového proudu** objekt, který obsahuje zápis.

Zápis musí být ve formátu WebVTT. Další informace najdete v tématu [WebVTT: Web Video textové stopy Format](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Program používá ukázka přepis ve formátu VTT. V řešení reálných, používáte službu Azure Media Indexer tak, aby [generovat přepis](https://docs.microsoft.com/azure/media-services/media-services-index-content) z video.

Přidejte následující definici metody do oboru názvů VideotranscriptReviews, třídy Program.

    /// <summary>
    /// Add a transcript to the indicated video review.
    /// The transcript must be in the WebVTT format.
    /// For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="transcript">The video transcript.</param>
    static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
    {
        Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
        client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
        Thread.Sleep(throttleRate);
    }

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Přidání výsledku přerušování přepis ke kontrole video

Kromě přidání přepis pro kontrolu videa, je také přidat výsledek moderování této přepis. Uděláte s **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Další informace najdete v tématu [referenční dokumentace rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** má následující požadované parametry:
1. Řetězec, který obsahuje typ MIME, které by se měly "application/json." 
1. Název vašeho obsahu moderátora týmu.
1. ID videa zkontrolujte vrácený **CreateVideoReviews**.
1. Objekt IList<TranscriptModerationBodyItem>. A **TranscriptModerationBodyItem** má následující vlastnosti:
- **Podmínky**. Objekt IList<TranscriptModerationBodyItemTermsItem>. A **TranscriptModerationBodyItemTermsItem** má následující vlastnosti:
- **Index**. Index založený na nule podmínek.
- **Termín**. Řetězec, který obsahuje výraz.
- **Časové razítko**. Řetězec, který obsahuje, v sekundách, čas v přepisu, ve kterém se nacházejí podmínky.

Zápis musí být ve formátu WebVTT. Další informace najdete v tématu [WebVTT: Web Video textové stopy Format](https://www.w3.org/TR/webvtt1/).

Přidejte následující definici metody do oboru názvů VideoTranscriptReviews, třídy Program. Tato metoda odešle přepis k **ContentModeratorClient.TextModeration.ScreenText** metoda. Také převádí výsledek na objekt IList<TranscriptModerationBodyItem>a odešle na **AddVideoTranscriptModerationResult**.

    /// <summary>
    /// Add the results of moderating a video transcript to the indicated video review.
    /// For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="transcript">The video transcript.</param>
    static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
    {
        Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

        // Screen the transcript using the Text Moderation API. For more information, see:
        // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
        Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

        // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
        List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
        if (null != screen.Terms)
        {
            foreach (var term in screen.Terms)
            {
                if (term.Index.HasValue)
                {
                    terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
                }
            }
        }

        List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
        {
            new TranscriptModerationBodyItem()
            {
                Timestamp = "0",
                Terms = terms
            }
        };

        client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

        Thread.Sleep(throttleRate);
    }


## <a name="publish-video-review"></a>Publikování videa revize

Publikování kontrolu video s **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** má následující požadované parametry:
1. Název vašeho obsahu moderátora týmu.
1. ID videa zkontrolujte vrácený **CreateVideoReviews**.

Přidejte následující definici metody do oboru názvů VideoReviews, třídy Program.

    /// <summary>
    /// Publish the indicated video review. For more information, see the reference API:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    private static void PublishReview(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Publishing the review with ID {0}.", review_id);
        client.Reviews.PublishVideoReview(TeamName, review_id);
        Thread.Sleep(throttleRate);
    }

## <a name="putting-it-all-together"></a>Třeba umisťovat všechny společně

Přidat **hlavní** definici metody do oboru názvů VideoTranscriptReviews, třídy Program. Nakonec zavřete třídy Program a VideoTranscriptReviews obor názvů.

> [!NOTE]
> Program používá ukázka přepis ve formátu VTT. V řešení reálných, používáte službu Azure Media Indexer tak, aby [generovat přepis](https://docs.microsoft.com/azure/media-services/media-services-index-content) z video. 

    static void Main(string[] args)
    {
        using (ContentModeratorClient client = NewClient())
        {
            // Create a review with the content pointing to a streaming endpoint (manifest)
            var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
            string review_id = CreateReview(client, "review1", streamingcontent);

            var transcript = @"WEBVTT

            01:01.000 --> 02:02.000
            First line with a crap word in a transcript.

            02:03.000 --> 02:25.000
            This is another line in the transcript.
            ";

            AddTranscript(client, review_id, transcript);

            AddTranscriptModerationResult(client, review_id, transcript);

            // Publish the review
            PublishReview(client, review_id);

            Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
            Console.WriteLine("Press any key to close the application.");
            Console.Read();
        }
    }

## <a name="run-the-program-and-review-the-output"></a>Spusťte program a prohlédněte si výstup

Při spuštění aplikace zobrazí výstup na následující řádky:

    Creating a video review.
    Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="navigate-to-your-video-transcript-review"></a>Přejděte na video přepis zkontrolovali

Přejít na video přepis zkontrolujte v nástroji zkontrolujte vašeho obsahu moderátora na **zkontrolujte**>**Video**>**přepis** obrazovky.

Zobrazí následující funkce:
- Dva řádky přepis, které jste přidali
- Termín vulgárnost najít a zvýrazněnou službou přerušování textu
- Výběr textu přepis začne videa z tohoto časového razítka

![Zkontrolujte video přepis pro lidské moderátorů](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Další postup

Zjistěte, jak vygenerovat [video zkontroluje](video-reviews-quickstart-dotnet.md) v nástroji revize.

Podívejte se na podrobný kurz o tom, jak vyvíjet [dokončení video přerušování řešení](video-transcript-moderation-review-tutorial-dotnet.md).

[Stáhněte si řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) v tomto a dalších – elementy QuickStart obsahu moderátora pro .NET.
