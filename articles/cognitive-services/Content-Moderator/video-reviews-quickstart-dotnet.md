---
title: Vytváření videorecenzí pomocí rozhraní .NET – Moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat sadu Content Moderator SDK s c# k vytvoření recenzí videí.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 7130ed43183d64b00f8f5ef1697b9a3b456ad396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72931674"
---
# <a name="create-video-reviews-using-net"></a>Vytváření recenzí videí pomocí rozhraní .NET

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat [sadu Content Moderator SDK s c#:](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

- Vytvoření videorecenze pro lidské moderátory
- Přidání rámců do recenze
- Získejte rámce pro kontrolu
- Získání stavu a podrobností recenze
- Publikování recenze

## <a name="prerequisites"></a>Požadavky

- Přihlaste se nebo si vytvořte účet na webu [nástroje Kontrola](https://contentmoderator.cognitive.microsoft.com/) moderátora obsahu.
- Tento článek předpokládá, že jste [moderovali video (viz rychlý start)](video-moderation-api.md) a máte data odpovědi. Potřebujete ji pro vytváření recenzí založených na rámecech pro lidské moderátory.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Kontrola, že klíč rozhraní API může volat rozhraní API pro kontroly, aby se mohla vytvořit kontrola

Pokud jste začínali na portálu Azure Portal, můžete po dokončení předchozích kroků získat dva klíče Content Moderatoru.

Pokud plánujete ve své ukázkové sadě SDK používat klíč rozhraní API poskytnutý službou Azure, postupujte podle pokynů v části o [použití klíče Azure s rozhraním API pro kontroly](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis), aby aplikace mohla volat rozhraní API pro kontroly a vytvářet kontroly.

Pokud použijete klíč bezplatné zkušební verze vygenerovaný nástrojem pro kontrolu, váš účet nástroje pro kontrolu už klíč zná, proto není nutné dělat nic dalšího.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Příprava videa a snímků videa ke kontrole

Video a ukázkové snímky videa ke kontrole musí být publikovány online, protože potřebujete jejich adresy URL.

> [!NOTE]
> Program používá ručně uložené screenshoty z videa s náhodným dospělým / pikantní skóre pro ilustraci použití recenze API. V reálné situaci použijete [výstup moderování videa](video-moderation-api.md#run-the-program-and-review-the-output) k vytvoření obrázků a přiřazení skóre. 

Pro video potřebujete koncový bod streamování, aby nástroj pro kontrolu přehrál video v zobrazení přehrávače.

![Miniatura ukázky videa](images/ams-video-demo-view.PNG)

- Zkopírujte **adresu URL** na této [ukázkové](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) stránce Azure Media Services pro adresu URL manifestu.

Pro snímky videa (obrázky) použijte následující obrázky:

![Miniatura snímku videa 1](images/ams-video-frame-thumbnails-1.PNG) | ![Miniatura snímku videa 2](images/ams-video-frame-thumbnails-2.PNG) | ![Miniatura snímku videa 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Snímek 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Rám 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Rám 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio

1. Přidejte do svého řešení nový projekt **Konzolová aplikace (.NET Framework)**.

1. Název projektu **VideoReviews**.

1. Projekt vyberte jako jeden spouštěný projekt řešení.

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Nainstalujte následující balíčky NuGet pro projekt TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizace příkazů using programu

Upravte pomocí příkazů programu následujícím způsobem.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Přidání soukromých vlastností

Přidejte do oboru názvů **VideoReviews**, **Program**třídy přidejte následující soukromé vlastnosti. Aktualizujte `AzureEndpoint` `CMSubscriptionKey` pole a hodnotami adresy URL koncového bodu a klíče předplatného. Najdete je na kartě **Rychlý start** vašeho prostředku na webu Azure Portal.


```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
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
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Vytvořit objekt klienta moderátora obsahu

Přidejte následující definici metody do oboru názvů **VideoReviews**, **program**třídy .

```csharp
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
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>Vytvoření recenze videa

Vytvořte video recenzi s **ContentModeratorClient.Reviews.CreateVideoReviews**. Další informace najdete v [referenčních informacích k rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** má následující požadované parametry:
1. Řetězec, který obsahuje typ MIME, který by měl být "application/json." 
1. Název týmu moderátora obsahu.
1. **Objekt>\<IList CreateVideoReviewsBodyItem.** Každý **objekt CreateVideoReviewsBodyItem** představuje recenzi videa. Tento rychlý start vytvoří jednu recenzi najednou.

**CreateVideoReviewsBodyItem** má několik vlastností. Minimálně nastavíte následující vlastnosti:
- **Obsah**. Adresa URL videa, které má být zkontrolováno.
- **ContentId**. ID, které chcete přiřadit k recenzi videa.
- **Stav**. Nastavte hodnotu na "Nepublikováno". Pokud ji nenastavíte, bude výchozí hodnota "Čeká na vyřízení", což znamená, že recenze videa je publikována a čeká na lidskou recenzi. Po publikování recenze videa již nelze do ní přidávat snímky videa, přepis nebo výsledek moderování přepisu.

> [!NOTE]
> **CreateVideoReviews** vrátí řetězec\<IList>. Každý z těchto řetězců obsahuje ID pro video recenzi. Tato ID jsou identifikátory GUID a nejsou stejné jako hodnota **ContentId** vlastnost. 

Přidejte následující definici metody do oboru názvů VideoReviews, program třídy.

```csharp
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
```

> [!NOTE]
> Klíč služby Content Moderator má limit četnosti žádostí za sekundu (RPS), a pokud ho překročíte, sada SDK zobrazí výjimku s chybovým kódem 429.
>
> Klíč úrovně Free má limit nastavený na 1 RPS za sekundu.

## <a name="add-video-frames-to-the-video-review"></a>Přidání snímků videa do recenze videa

Snímky videa přidáte do recenze videa pomocí **contentmoderatorclient.reviews.AddVideoFrameUrl** (pokud jsou snímky videa hostovány online) nebo **ContentModeratorClient.Reviews.AddVideoFrameStream** (pokud jsou snímky videa hostovány místně). Tento rychlý start předpokládá, že snímky videa jsou hostovány online, a proto používá **AddVideoFrameUrl**. Další informace najdete v [referenčních informacích k rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** má následující požadované parametry:
1. Řetězec, který obsahuje typ MIME, který by měl být "application/json."
1. Název týmu moderátora obsahu.
1. Video recenzi ID vrátil **CreateVideoReviews**.
1. Objekt **>\<položky IList VideoFrameBodyItem.** Každý objekt **VideoFrameBodyItem** představuje snímek videa.

**VideoFrameBodyItem** má následující vlastnosti:
- **Časové razítko**. Řetězec, který obsahuje v sekundách čas ve videu, ze kterého byl pořízen snímek videa.
- **FrameImage**. Adresa URL snímku videa.
- **Metadata**. > položku IList\<VideoFrameBodyItemMetadataItem. **VideoFrameBodyItemMetadataItem** je jednoduše pár klíč/hodnota. Mezi platné klíče patří:
- **recenzeDoporučeno**. True, pokud je doporučena lidská recenze snímku videa.
- **adultScore**. Hodnota od 0 do 1, která hodnotí závažnost obsahu pro dospělé v rámci videa.
- **a**. True, pokud video obsahuje obsah pro dospělé.
- **racyScore**. Hodnota od 0 do 1, která hodnotí závažnost pikantního obsahu v snímku videa.
- **r**. True, pokud video snímek obsahuje pikantní obsah.
- **RecenzentResultTags**. IList\<VideoFrameBodyItemReviewerResultTagsItem>. **VideoFrameBodyItemReviewerResultTagsItem** je jednoduše pár klíč/hodnota. Aplikace může tyto značky použít k uspořádání snímků videa.

> [!NOTE]
> Tento rychlý start generuje náhodné hodnoty pro vlastnosti **adultScore** a **racyScore.** V produkční aplikaci byste tyto hodnoty získali ze [služby moderování videa](video-moderation-api.md)nasazené jako mediální služba Azure.

Přidejte následující definice metod do oboru názvů VideoReviews, program třídy.

```csharp
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
```

```csharp
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
```

## <a name="get-video-frames-for-video-review"></a>Získání snímků videa pro kontrolu videa

Můžete získat video snímky pro video recenzi s **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** má následující požadované parametry:
1. Název týmu moderátora obsahu.
1. Video recenzi ID vrátil **CreateVideoReviews**.
1. Nulový index prvního snímku videa získat.
1. Počet snímků videa získat.

Přidejte následující definici metody do oboru názvů VideoReviews, program třídy.

```csharp
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
```

## <a name="get-video-review-information"></a>Získání informací o recenzi videa

Získáte informace o video recenzi s **ContentModeratorClient.Reviews.GetReview**. **GetReview** má následující požadované parametry:
1. Název týmu moderátora obsahu.
1. Video recenzi ID vrátil **CreateVideoReviews**.

Přidejte následující definici metody do oboru názvů VideoReviews, program třídy.

```csharp
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
```

## <a name="publish-video-review"></a>Publikovat video recenzi

Můžete publikovat video recenzi s **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** má následující požadované parametry:
1. Název týmu moderátora obsahu.
1. Video recenzi ID vrátil **CreateVideoReviews**.

Přidejte následující definici metody do oboru názvů VideoReviews, program třídy.

```csharp
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
```

## <a name="putting-it-all-together"></a>Spojení všech součástí dohromady

Přidejte definici **hlavní** metody do oboru názvů VideoReviews, program třídy. Nakonec zavřete program třídy a VideoReviews oboru názvů.

```csharp
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
```

## <a name="run-the-program-and-review-the-output"></a>Spuštění programu a kontrola výstupu
Při spuštění aplikace se zobrazí výstup na následujících řádcích:

```json
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
```

## <a name="check-out-your-video-review"></a>Podívejte se na video recenzi

Nakonec se recenze videa zobrazí v účtu nástroje pro kontrolu moderátora obsahu na obrazovce **Zkontrolovat**>**video.**

![Video recenze pro lidské moderátory](images/ams-video-review.PNG)

## <a name="next-steps"></a>Další kroky

Získejte [sada Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a řešení sady Visual [Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tento a další rychlé starty moderátora obsahu pro rozhraní .NET.

Přečtěte si, jak do recenze videa přidat [moderování přepisu.](video-transcript-moderation-review-tutorial-dotnet.md) 

Podívejte se na podrobný návod, jak vyvinout [kompletní video moderování řešení](video-transcript-moderation-review-tutorial-dotnet.md).
