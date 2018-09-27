---
title: Vytvoření pomocí rozhraní .NET – Content Moderator videa revize
titlesuffix: Azure Cognitive Services
description: Jak vytvořit videa kontroly pomocí Content Moderator SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/18/2018
ms.author: sajagtap
ms.openlocfilehash: 284ee24bbb0a15d107acf85e2d58072a0ecbbc6e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219036"
---
# <a name="create-video-reviews-using-net"></a>Vytvoření videa kontroly pomocí .NET

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat [Content Moderator SDK pomocí jazyka C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) na:

- Vytváření video kontroly pro lidských moderátorů
- Přidat snímky shrnutí
- Získat snímky pro revizi 
- Získat stav a podrobnosti o kontroly
- Publikování revize

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte [který video (viz rychlý start)](video-moderation-api.md) a mít data odpovědi. Ho potřebujete k vytváření recenzí založených na snímcích pro lidské moderátory.

Tento článek také předpokládá, že jste již obeznámeni s Visual Studio a C#.

## <a name="sign-up-for-content-moderator"></a>Zaregistrujte si Content Moderatoru

Než budete moct použít služby Content Moderator přes rozhraní REST API nebo sady SDK, je nutné klíč předplatného.
Odkazovat [rychlý Start](quick-start.md) se dozvíte, jak můžete získat klíč.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>Zaregistrovat účet nástroj pro kontrolu, pokud nebyla dokončena v předchozím kroku

Pokud jste získali Content Moderator z webu Azure portal, také [zaregistrujte si účet nástroj pro revize](https://contentmoderator.cognitive.microsoft.com/) a vytvořte tým kontroly. Budete potřebovat Id týmu a nástroje pro recenze pro volání rozhraní API přezkoumání a spuštění úlohy a zobrazíte recenzí v nástroj pro recenze.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Ujistěte se, že svůj klíč rozhraní API můžete volat rozhraní API pro kontrolu pro vytvoření revize

Po dokončení předchozích kroků, můžete uvíznout dva klíče Content Moderator Pokud jste spustili z webu Azure portal. 

Pokud máte v plánu používat klíč rozhraní API poskytuje Azure ve vaší ukázce sady SDK, postupujte podle pokynů uvedených v [klíče služby Azure pomocí rozhraní API pro kontrolu](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api) oddílu, aby vaše aplikace volat rozhraní API pro kontrolu a vytvořit revize.

Pokud používáte bezplatné zkušební verze klíč vygenerovaný pomocí nástroje pro recenze, váš účet nástroj pro revize již ví o klíči a proto nejsou požadované žádné další kroky.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Připravit vaše video a snímky videí k revizi

Videa a ukázkové snímky videí a projděte si musí online publikovat, protože je nutné jejich adresy URL.

> [!NOTE]
> Program používá ručně uložené snímky z videa s náhodné pro dospělé/pikantní skóre pro ilustraci použití rozhraní API pro kontrolu. V reálné situaci, můžete použít [moderování videa výstup](video-moderation-api.md#run-the-program-and-review-the-output) vytváření imagí a přiřadit skóre. 

Videa je třeba koncový bod streamování, tak, aby nástroj pro recenze přehrávání videa v zobrazení přehrávače.

![Miniatura videa demo](images/ams-video-demo-view.PNG)

- Kopírování **URL** na tomto [Azure Media Services vyzkoušejte](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) stránky pro adresu URL manifestu.

Pro snímky videa (imagí) použijte následující Image:

![Miniatura videa rámce 1](images/ams-video-frame-thumbnails-1.PNG) | ![Miniatura videa rámce 2](images/ams-video-frame-thumbnails-2.PNG) | ![Miniatura videa rámec 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Orámování 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Orámování 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Rámec 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. Přidat nový **Konzolová aplikace (.NET Framework)** do svého řešení projekt.

1. Pojmenujte projekt **VideoReviews**.

1. Vyberte tento projekt jako jeden spouštěný projekt pro řešení.

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Nainstalujte následující balíčky NuGet pro projekt TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizace programu v nástrojích příkazy

Upravit program společnosti příkazy následujícím způsobem.

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;
    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;


### <a name="add-private-properties"></a>Přidáním vlastních vlastností

Přidejte následující soukromé vlastnosti do oboru názvů VideoReviews, třídu programu.

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

Přidejte následující definici metody do oboru názvů VideoReviews, třídu programu.

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

Vytvořit kontrolu videa s **ContentModeratorClient.Reviews.CreateVideoReviews**. Další informace najdete v tématu [reference k rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

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
> Klíč služby Content Moderator má požadavků za druhé omezení četnosti (předávajících stran) a při překročení limitu, vyvolá výjimku s kódem chyby 429, sady SDK. 
>
> Klíč úroveň free má omezení četnosti jeden RPS.

## <a name="add-video-frames-to-the-video-review"></a>Přidat snímky videí do video revize

Přidat snímky videí do video recenzi s **ContentModeratorClient.Reviews.AddVideoFrameUrl** (Pokud je vaše video snímků jsou hostované online) nebo **ContentModeratorClient.Reviews.AddVideoFrameStream** () Pokud vaše video snímků jsou hostované místně). Tento rychlý start předpokládá videa snímků hostují online a proto používá **AddVideoFrameUrl**. Další informace najdete v tématu [reference k rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** má následující povinné parametry:
1. Řetězec obsahující typ MIME, které by se měly "application/json".
1. Název týmu Content Moderatoru.
1. ID videa kontroly vrácený **CreateVideoReviews**.
1. **IList<VideoFrameBodyItem>**  objektu. Každý **VideoFrameBodyItem** objekt představuje snímek videa.

**VideoFrameBodyItem** má následující vlastnosti:
- **Časové razítko**. Řetězec, který obsahuje během několika sekund, čas ve videu, ze kterého pořízení snímku videa.
- **FrameImage**. Adresa URL videa rámce.
- **Metadata**. Objekt IList<VideoFrameBodyItemMetadataItem>. **VideoFrameBodyItemMetadataItem** je jednoduše dvojice klíč/hodnota. Platný klíčů patří:
- **reviewRecommended**. True, pokud se doporučuje recenze videa rámce.
- **adultScore**. Hodnotu od 0 do 1, které hodnotí závažnost obsah pro dospělé v rámci video.
- **A** Hodnota TRUE, pokud video obsahuje obsah pro dospělé.
- **racyScore**. Hodnotu od 0 do 1, které hodnotí závažnost pikantní obsah v rámci video.
- **r**. True, pokud rámec video obsahuje pikantní obsah.
- **ReviewerResultTags**. Objekt IList<VideoFrameBodyItemReviewerResultTagsItem>. **VideoFrameBodyItemReviewerResultTagsItem** je jednoduše dvojice klíč/hodnota. Aplikace může používání těchto značek k uspořádání snímky videí.

> [!NOTE]
> Generuje náhodné hodnoty pro tento rychlý Start **adultScore** a **racyScore** vlastnosti. V produkční aplikace, lze získat z těchto hodnot [služba moderování videa](video-moderation-api.md)budou nasazené jako službu Azure Media.

Přidejte následující definice metod do oboru názvů VideoReviews, třídu programu.

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
    

## <a name="get-video-frames-for-video-review"></a>Získat snímky videa pro videa revize

Můžete získat snímky videí pro přezkoumání videa s **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** má následující povinné parametry:
1. Název týmu Content Moderatoru.
1. ID videa kontroly vrácený **CreateVideoReviews**.
1. Z nuly vycházející index prvního rámce video na získání.
1. Počet video rámečků k získání.

Přidejte následující definici metody do oboru názvů VideoReviews, třídu programu.

    /// <summary>
    /// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    static void GetFrames(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

        Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
        Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

        Thread.Sleep(throttleRate);
    }

## <a name="get-video-review-information"></a>Získejte videa kontrola informací o

Získat informace o přezkoumání videa s **ContentModeratorClient.Reviews.GetReview**. **GetReview** má následující povinné parametry:
1. Název týmu Content Moderatoru.
1. ID videa kontroly vrácený **CreateVideoReviews**.

Přidejte následující definici metody do oboru názvů VideoReviews, třídu programu.

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

## <a name="putting-it-all-together"></a>Vložení všechno dohromady

Přidat **hlavní** definici metody do oboru názvů VideoReviews, třídu programu. A konečně zavřete třídu Program a VideoReviews oboru názvů.

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
            Console.ReadKey();
        }
    }

## <a name="run-the-program-and-review-the-output"></a>Spusťte program a prohlédněte si výstup
Při spuštění aplikace, zobrazí se výstup na následující řádky:

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

## <a name="check-out-your-video-review"></a>Podívejte se na video revize

A konečně uvidíte videa kontrolu v Content Moderatoru Zkontrolujte nástroj účet na **zkontrolujte**>**videa** obrazovky.

![Video revize pro lidských moderátorů](images/ams-video-review.PNG)

## <a name="next-steps"></a>Další postup

Získejte [Content Moderator sady .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a [řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tuto a další rychlé starty Content Moderator pro .NET.

Zjistěte, jak přidat [přepisu moderování](video-transcript-moderation-review-tutorial-dotnet.md) videa kontrolou. 

Podívejte se na podrobný kurz o tom, jak vyvíjet [dokončení moderování videa řešení](video-transcript-moderation-review-tutorial-dotnet.md).
