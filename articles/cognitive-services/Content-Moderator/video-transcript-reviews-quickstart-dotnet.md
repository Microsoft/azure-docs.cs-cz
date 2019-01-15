---
title: Vytvoření pomocí rozhraní .NET – Content Moderator revize přepis videa
titlesuffix: Azure Cognitive Services
description: Vytvoření kontroly přepis videa pomocí Content Moderator SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 672d801b9a6c45a8ab3eb9964b6e061eeee11f59
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266820"
---
# <a name="create-video-transcript-reviews-using-net"></a>Vytvoření kontroly přepis videa pomocí .NET

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat [Content Moderator SDK pomocí jazyka C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) na:

- Vytváření video kontroly pro lidských moderátorů
- Přidat moderované přepisu k revizi
- Publikování revize

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte [který video](video-moderation-api.md) a [vytvořili videa revize](video-reviews-quickstart-dotnet.md) v nástroj pro recenze lidské rozhodování. Chcete přidat nástroj pro recenze moderování videa záznamy o studiu.

Tento článek také předpokládá, že jste již obeznámeni s Visual Studio a C#.

## <a name="sign-up-for-content-moderator"></a>Registrace do Content Moderatoru

Než začnete služby Content Moderatoru prostřednictvím rozhraní REST API nebo sady SDK používat, budete potřebovat klíč předplatného. Podle pokynů v tématu [Vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) se přihlaste k odběru Content Moderatoru a získejte svůj klíč.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>Registrace účtu nástroje pro kontrolu, pokud jste se nezaregistrovali v předchozím kroku

Pokud jste Content Moderator získali na portálu Azure Portal, [zaregistrujte si i účet nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) a vytvořte posuzovací tým. Abyste mohli zavolat rozhraní API pro kontroly a tím zahájit úlohu a zobrazit kontroly v nástroji pro kontrolu, budete potřebovat ID týmu a nástroj pro kontrolu.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Ujistěte se, že svůj klíč rozhraní API můžete volat rozhraní API pro kontrolu (vytvoření úlohy)

Pokud jste začínali na portálu Azure Portal, můžete po dokončení předchozích kroků získat dva klíče Content Moderatoru. 

Pokud plánujete ve své ukázkové sadě SDK používat klíč rozhraní API poskytnutý službou Azure, postupujte podle pokynů v části o [použití klíče Azure s rozhraním API pro kontroly](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api), aby aplikace mohla volat rozhraní API pro kontroly a vytvářet kontroly.

Pokud použijete klíč bezplatné zkušební verze vygenerovaný nástrojem pro kontrolu, váš účet nástroje pro kontrolu už klíč zná, proto není nutné dělat nic dalšího.

## <a name="prepare-your-video-for-review"></a>Příprava vašeho videa k revizi

Přidáte přepisu videa shrnutí. Video musí publikovaná online. Potřebujete její koncový bod streamování. Koncový bod streamování umožňuje přehrávač videa nástroj pro revize k přehrání videa.

![Miniatura videa demo](images/ams-video-demo-view.PNG)

- Kopírování **URL** na tomto [Azure Media Services vyzkoušejte](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) stránky pro adresu URL manifestu.

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu Visual Studio

1. Přidejte do svého řešení nový projekt **Konzolová aplikace (.NET Framework)**.

1. Pojmenujte projekt **VideoTranscriptReviews**.

1. Projekt vyberte jako jediný spouštěný projekt řešení.

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Nainstalujte následující balíčky NuGet pro projekt TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizace příkazů using programu

Upravit program společnosti příkazy následujícím způsobem.

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;
    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;


### <a name="add-private-properties"></a>Přidání soukromých vlastností

Přidejte následující soukromé vlastnosti do oboru názvů VideoTranscriptReviews, třídu programu.

Uvedeno, nahraďte vzorové hodnoty těchto vlastností.


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
            /// the Content Moderator web site. Your team name is the Id associated 
            /// with your subscription.</remarks>
            private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

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


### <a name="create-content-moderator-client-object"></a>Vytvoření objektu klienta Content Moderatoru

Přidejte následující definici metody do oboru názvů VideoTranscriptReviews, třídu programu.

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
            Endpoint = AzureBaseURL
        };
    }

## <a name="create-a-video-review"></a>Vytváření video kontroly

Vytvořit kontrolu videa s **ContentModeratorClient.Reviews.CreateVideoReviews**. Další informace najdete v [referenčních informacích k rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** má následující povinné parametry:
1. Řetězec obsahující typ MIME, které by se měly "application/json". 
1. Název týmu Content Moderatoru.
1. **IList<CreateVideoReviewsBodyItem>**  objektu. Každý **CreateVideoReviewsBodyItem** objekt představuje přezkoumání videa. Tento rychlý start vytvoří jeden revize najednou.

**CreateVideoReviewsBodyItem** má několik vlastností. Minimálně nastavte následující vlastnosti:
- **Obsahu**. Adresa URL videa musí zkontrolovat.
- **ContentId**. ID přiřazení k revizi videa.
- **Stav**. Nastavte hodnotu na "Nepublikované." Pokud ho nenastavíte, použije se výchozí "Čeká na vyřízení", což znamená, že je publikovaná videa revize a recenze prováděné lidmi ve stavu čekání. Po publikování videa revize můžete už přidat snímky videí, přepis nebo výsledek moderování přepisu k němu.

> [!NOTE]
> **CreateVideoReviews** vrátí objekt IList<string>. Každý z těchto řetězců obsahuje ID videa revize. Tyto identifikátory jsou identifikátory GUID a nejsou stejná jako hodnota **ContentId** vlastnost. 

Přidejte následující definici metody do oboru názvů VideoReviews, třídu programu.

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
> Klíč služby Content Moderator má omezený počet požadavků za sekundu (RPS). Když tento limit překročíte, sada SDK vyvolá výjimku s kódem chyby 429. 
>
> Klíč úrovně Free má limit nastavený na 1 RPS.

## <a name="add-transcript-to-video-review"></a>Přidat přepisu videa revize

Přidat přepis videa shrnutí s **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** má následující povinné parametry:
1. ID vašeho týmu Content Moderatoru.
1. ID videa kontroly vrácený **CreateVideoReviews**.
1. A **Stream** objekt, který obsahuje přepisu.

Zápis musí být ve formátu WebVTT. Další informace najdete v tématu [WebVTT: Text videa na webu sleduje formátu](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Program používá ukázkový přepisu ve formátu VTT. Skutečná řešení, použijte službu Azure Media Indexer [generovat řádné záznamy o studiu](https://docs.microsoft.com/azure/media-services/media-services-index-content) z videa.

Přidejte následující definici metody do oboru názvů VideotranscriptReviews, třídu programu.

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

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Přidat výsledek moderování přepisu videa revize

Kromě přidání přepis videa shrnutí, je také přidat výsledek moderování tento přepis. Uděláte s **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Další informace najdete v [referenčních informacích k rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** má následující povinné parametry:
1. Řetězec obsahující typ MIME, které by se měly "application/json". 
1. Název týmu Content Moderatoru.
1. ID videa kontroly vrácený **CreateVideoReviews**.
1. Objekt IList<TranscriptModerationBodyItem>. A **TranscriptModerationBodyItem** má následující vlastnosti:
- **Podmínky**. Objekt IList<TranscriptModerationBodyItemTermsItem>. A **TranscriptModerationBodyItemTermsItem** má následující vlastnosti:
- **Index**. Index založený na nule termín.
- **Termín**. Řetězec, který obsahuje výraz.
- **Časové razítko**. Řetězec, který obsahuje během několika sekund, čas v přepisu, ve kterém se nacházejí podmínky.

Zápis musí být ve formátu WebVTT. Další informace najdete v tématu [WebVTT: Text videa na webu sleduje formátu](https://www.w3.org/TR/webvtt1/).

Přidejte následující definici metody do oboru názvů VideoTranscriptReviews, třídu programu. Tato metoda odešle přepis můžete **ContentModeratorClient.TextModeration.ScreenText** metody. Také převádí výsledek na objekt IList<TranscriptModerationBodyItem>a odešle **AddVideoTranscriptModerationResult**.

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


## <a name="publish-video-review"></a>Publikovat video revize

Publikovat video recenzi s **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** má následující povinné parametry:
1. Název týmu Content Moderatoru.
1. ID videa kontroly vrácený **CreateVideoReviews**.

Přidejte následující definici metody do oboru názvů VideoReviews, třídu programu.

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

## <a name="putting-it-all-together"></a>Spojení všech součástí dohromady

Přidat **hlavní** definici metody do oboru názvů VideoTranscriptReviews, třídu programu. A konečně zavřete třídu Program a VideoTranscriptReviews oboru názvů.

> [!NOTE]
> Program používá ukázkový přepisu ve formátu VTT. Skutečná řešení, použijte službu Azure Media Indexer [generovat řádné záznamy o studiu](https://docs.microsoft.com/azure/media-services/media-services-index-content) z videa. 

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
            Console.ReadKey();
        }
    }

## <a name="run-the-program-and-review-the-output"></a>Spuštění programu a kontrola výstupu

Při spuštění aplikace, zobrazí se výstup na následující řádky:

    Creating a video review.
    Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="navigate-to-your-video-transcript-review"></a>Přejděte na kontrolu přepis videa

Přejděte k revizi přepis videa v nástroji pro kontrolu Content Moderatoru **zkontrolujte**>**videa**>**přepisu** obrazovky.

Zobrazí se následující funkce:
- Dva řádky přepisu, kterou jste přidali
- Termín vulgárních výrazů nalezen a zvýrazněnou službou moderování textu
- Výběr textu určené k transkripci začíná video od tohoto časového razítka

![Revize přepis videa pro lidských moderátorů](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Další postup

Získejte [Content Moderator sady .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a [řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tuto a další rychlé starty Content Moderator pro .NET.

Zjistěte, jak generovat [video kontroly](video-reviews-quickstart-dotnet.md) v nástroj pro recenze.

Podívejte se na podrobný kurz o tom, jak vyvíjet [dokončení moderování videa řešení](video-transcript-moderation-review-tutorial-dotnet.md).
