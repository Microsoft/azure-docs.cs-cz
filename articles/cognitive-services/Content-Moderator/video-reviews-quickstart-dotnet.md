---
title: Azure obsahu moderátora - vytvořit video recenze pomocí rozhraní .NET | Microsoft Docs
description: Postup vytvoření video zkontroluje pomocí sady Azure obsahu moderátora SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/18/2018
ms.author: sajagtap
ms.openlocfilehash: cb487314b8695f3676fdb22a9d7e3ec5ca3ed9f2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342529"
---
# <a name="create-video-reviews-using-net"></a>Vytvořit video recenze pomocí rozhraní .NET

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat obsahu moderátora SDK pomocí C# do:

- Vytvoření kontrolu videa pro lidské moderátorů
- Přidat snímky kontrolu
- Získat snímky pro kontrola 
- Získat stav a podrobnosti o kontrola
- Publikování kontrola

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte [moderovaná videa (viz rychlý start)](video-moderation-api.md) a mít data odpovědi. Ho potřebovat pro vytvoření recenze na základě rámce pro lidské moderátorů.

Tento článek také předpokládá, že jste již obeznámeni s Visual Studio a C#.

### <a name="sign-up-for-content-moderator-services"></a>Zaregistrujte si obsahu moderátora služby

Před použitím služby obsahu moderátora přes rozhraní REST API nebo sady SDK, je nutné klíč předplatného.

Na řídicím panelu obsahu moderátora můžete najít svůj klíč předplatného v **nastavení** > **pověření** > **rozhraní API**  >  **Zkušební Ocp-Apim-Subscription-Key**. Další informace najdete v tématu [přehled](overview.md).

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Příprava videa a snímky videa ke kontrole

Snímky video a ukázku videa ke kontrole musí online publikovat, protože je nutné jejich adresy URL.

> [!NOTE]
> Program používá ručně uložené snímky obrazovky z videa s náhodných dospělý/zájem skóre pro ilustraci použití rozhraní API revize. V situaci, skutečné, můžete použít [výstupu videa přerušování](video-moderation-api.md#run-the-program-and-review-the-output) vytvářet bitové kopie a přiřadit skóre. 

Videa budete potřebovat koncový bod streamování, aby nástroj zkontrolujte přehrávání videa na zobrazení přehrávač.

![Ukázkové video miniaturu](images/ams-video-demo-view.PNG)

- Kopírování **URL** v tomto [ukázku Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) stránky pro adresu URL manifestu.

Pro snímky videa (imagí) použijte následující bitové kopie:

![Snímek videa miniaturu 1](images/ams-video-frame-thumbnails-1.PNG) | ![Snímek videa miniaturu 2](images/ams-video-frame-thumbnails-2.PNG) | ![Snímek videa miniaturu 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Rámce 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Rámce 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Rámce 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. Přidejte nový **konzolovou aplikaci (rozhraní .NET Framework)** projekt pro vaše řešení.

1. Název projektu **VideoReviews**.

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

Přidejte následující soukromé vlastnosti do oboru názvů VideoReviews, třídy Program.

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

Přidejte následující definici metody do oboru názvů VideoReviews, třídy Program.

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
> Klíč obsahu moderátora služby má požadavky na druhý omezení četnosti (RPS) a pokud limit překročíte, vyvolá výjimku s kódem 429 chyby, sady SDK. 
>
> Úroveň free klíč může mít jeden RPS rychlost.

## <a name="add-video-frames-to-the-video-review"></a>Přidat snímky videa ke kontrole video

Přidat snímky videa pro kontrolu video s **ContentModeratorClient.Reviews.AddVideoFrameUrl** (Pokud se vaše snímky videa hostují online) nebo **ContentModeratorClient.Reviews.AddVideoFrameStream** () Pokud vaše snímky videa jsou hostována místně). Tento rychlý start předpokládá video rámců hostované online a proto používá **AddVideoFrameUrl**. Další informace najdete v tématu [referenční dokumentace rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** má následující požadované parametry:
1. Řetězec, který obsahuje typ MIME, které by se měly "application/json."
1. Název vašeho obsahu moderátora týmu.
1. ID videa zkontrolujte vrácený **CreateVideoReviews**.
1. **IList<VideoFrameBodyItem>**  objektu. Každý **VideoFrameBodyItem** objekt představuje snímek videa.

**VideoFrameBodyItem** má následující vlastnosti:
- **Časové razítko**. Řetězec, který obsahuje, v sekundách, čas v video, ze kterého pořízení snímku videa.
- **FrameImage**. Adresa URL snímku videa.
- **Metadata**. Objekt IList<VideoFrameBodyItemMetadataItem>. **VideoFrameBodyItemMetadataItem** je jednoduše dvojici klíč/hodnota. Platné klíče zahrnují:
- **reviewRecommended**. True, pokud se doporučuje kontrolu lidského snímku videa.
- **adultScore**. Hodnotu od 0 do 1, která vyhodnotí závažnost obsah pro dospělé v rámci videa.
- **A** Hodnota TRUE, pokud na video obsahuje obsah pro dospělé.
- **racyScore**. Hodnotu od 0 do 1, která vyhodnotí závažnost zájem obsah do snímku videa.
- **r**. Hodnota TRUE, pokud snímek videa obsahuje zájem obsah.
- **ReviewerResultTags**. Objekt IList<VideoFrameBodyItemReviewerResultTagsItem>. **VideoFrameBodyItemReviewerResultTagsItem** je jednoduše dvojici klíč/hodnota. Aplikace můžete použít tyto značek k uspořádání snímky videa.

> [!NOTE]
> Tento rychlý start generuje náhodné hodnoty **adultScore** a **racyScore** vlastnosti. V případě produkční aplikace lze získat tyto hodnoty z [video přerušování služby](video-moderation-api.md), nasazené jako služba Azure média.

Přidejte následující definice metod do oboru názvů VideoReviews, třídy Program.

    <summary>
    /// Create a video frame to add to a video review after the video review is created.
    /// </summary>
    /// <param name="url">The URL of the video frame image.</param>
    /// <returns>The video frame.</returns>
    private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
    {
        // We generate random "adult" and "racy" scores for the video frame.
        Random rand = new Random();

        var frame = new VideoFrameBodyItem
        {
            // The timestamp is measured in milliseconds. Convert from seconds.
            Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
            FrameImage = url,

            Metadata = new List<VideoFrameBodyItemMetadataItem>
            {
                new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
                new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
                new VideoFrameBodyItemMetadataItem("a", "false"),
                new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
                new VideoFrameBodyItemMetadataItem("r", "false")
            },

            ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
            {
                new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
            }
        };

        return frame;
    }

    /// <summary>
    /// Add a video frame to the indicated video review. For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="url">The URL of the video frame image.</param>
    static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
    {
        Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

        var frames = new List<VideoFrameBodyItem>()
        {
            CreateFrameToAddToReview(url, timestamp_seconds)
        };
            
        client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

        Thread.Sleep(throttleRate);
    

## <a name="get-video-frames-for-video-review"></a>Získat snímky videa ke kontrole video

Můžete získat snímky videa pro kontrolu video s **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** má následující požadované parametry:
1. Název vašeho obsahu moderátora týmu.
1. ID videa zkontrolujte vrácený **CreateVideoReviews**.
1. Index založený na nule první snímku videa získat.
1. Počet snímků videa získat.

Přidejte následující definici metody do oboru názvů VideoReviews, třídy Program.

    /// <summary>
    /// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    static void GetFrames(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

        Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0, Int32.MaxValue);
        Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

        Thread.Sleep(throttleRate);
    }

## <a name="get-video-review-information"></a>Získání informací o video revize

Získání informací pro kontrolu video s **ContentModeratorClient.Reviews.GetReview**. **GetReview** má následující požadované parametry:
1. Název vašeho obsahu moderátora týmu.
1. ID videa zkontrolujte vrácený **CreateVideoReviews**.

Přidejte následující definici metody do oboru názvů VideoReviews, třídy Program.

    /// <summary>
    /// Get the information for the indicated video review. For more information, see the reference API:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    private static void GetReview(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

        var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
        Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

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

Přidat **hlavní** definici metody do oboru názvů VideoReviews, třídy Program. Nakonec zavřete třídy Program a VideoReviews obor názvů.

    static void Main(string[] args)
    {
        using (ContentModeratorClient client = NewClient())
        {
            // Create a review with the content pointing to a streaming endpoint (manifest)
            var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
            string review_id = CreateReview(client, "review1", streamingcontent);

            var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
            var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
            var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

            // Add the frames from 17, 64, and 144 seconds.
            AddFrame(client, review_id, frame1_url, "17");
            AddFrame(client, review_id, frame2_url, "64");
            AddFrame(client, review_id, frame3_url, "144");

            // Get frames information and show
            GetFrames(client, review_id);
            GetReview(client, review_id);

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
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    {
        "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
        "VideoFrames": [
        {
            "Timestamp": "17000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.808312381528463"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.846378884206702"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
        ]
        },
        {
            "Timestamp": "64000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.576078300166912"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.244768953064815"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
        ]
        },
        {
            "Timestamp": "144000",
            "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
            "Metadata": [
            {
                "Key": "reviewRecommended",
                "Value": "true"
            },
            {
                "Key": "adultScore",
                "Value": "0.664480847150311"
            },
            {
                "Key": "a",
                "Value": "false"
            },
            {
                "Key": "racyScore",
                "Value": "0.933817870418456"
            },
            {
                "Key": "r",
                "Value": "false"
            }
            ],
            "ReviewerResultTags": [
            {
                "Key": "tag1",
                "Value": "value1"
            }
            ]
        }
        ]
    }
    
    Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    {
        "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
        "SubTeam": "public",
        "Status": "UnPublished",
        "ReviewerResultTags": [],
        "CreatedBy": "testreview6",
        "Metadata": [
        {
            "Key": "FrameCount",
            "Value": "3"
        }
        ],
        "Type": "Video",
        "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        "ContentId": "review1",
        "CallbackEndpoint": null
    }

    Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="check-out-your-video-review"></a>Podívejte se na video zkontrolovali

Nakonec uvidíte video kontrola v vašeho obsahu moderátora Zkontrolujte nástroj účet na **zkontrolujte**>**Video** obrazovky.

![Video revize pro lidské moderátorů](images/ams-video-review.PNG)

## <a name="next-steps"></a>Další postup

Informace o postupu přidání [přepis přerušování](video-transcript-moderation-review-tutorial-dotnet.md) ke kontrole videa. 

Podívejte se na podrobný kurz o tom, jak vyvíjet [dokončení video přerušování řešení](video-transcript-moderation-review-tutorial-dotnet.md).

[Stáhněte si řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) v tomto a dalších – elementy QuickStart obsahu moderátora pro .NET.
