---
title: Vytváření recenzí videí pomocí Content Moderator .NET
titleSuffix: Azure Cognitive Services
description: Tento článek poskytuje informace a ukázky kódu, které vám pomůžou rychle začít používat sadu Content Moderator SDK s jazykem C# k vytváření recenzí videí.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 55802b04e10f06c7e362033f97d24c4e20c12c62
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566485"
---
# <a name="create-video-reviews-using-net"></a>Vytváření recenzí videí pomocí .NET

Tento článek poskytuje informace a ukázky kódu, které vám pomůžou rychle začít používat [sadu Content moderator SDK s jazykem C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) k těmto akcím:

- Vytvoření recenze videa pro moderátory lidí
- Přidat snímky do recenze
- Získat snímky pro kontrolu
- Získat stav a podrobnosti kontroly
- Publikování Revize

## <a name="prerequisites"></a>Požadavky

- Přihlaste se nebo vytvořte účet na webu [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) Content moderator.
- V tomto článku se předpokládá, že jste toto video rozstavili [(viz rychlý Start)](video-moderation-api.md) a máte data odpovědi. Budete ho potřebovat pro vytváření kontrol na základě snímků pro lidské moderování.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Kontrola, že klíč rozhraní API může volat rozhraní API pro kontroly, aby se mohla vytvořit kontrola

Pokud jste začínali na portálu Azure Portal, můžete po dokončení předchozích kroků získat dva klíče Content Moderatoru.

Pokud plánujete ve své ukázkové sadě SDK používat klíč rozhraní API poskytnutý službou Azure, postupujte podle pokynů v části o [použití klíče Azure s rozhraním API pro kontroly](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis), aby aplikace mohla volat rozhraní API pro kontroly a vytvářet kontroly.

Pokud použijete klíč bezplatné zkušební verze vygenerovaný nástrojem pro kontrolu, váš účet nástroje pro kontrolu už klíč zná, proto není nutné dělat nic dalšího.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Příprava videa a snímků videa na kontrolu

Video a ukázkové snímky videa, které se mají zkontrolovat, je nutné publikovat online, protože potřebujete jejich adresy URL.

> [!NOTE]
> Program používá ručně uložené snímky obrazovky z videa s náhodnými výsledky pro dospělé/pikantní k ilustraci použití rozhraní API pro revize. V reálném čase můžete k vytváření obrázků a přiřazování skóre použít [výstup moderování videa](video-moderation-api.md#run-the-program-and-review-the-output) . 

Pro video potřebujete koncový bod streamování, aby nástroj pro revize přehrál video v zobrazení přehrávače.

![Miniatura ukázky videa](images/ams-video-demo-view.PNG)

- Zkopírujte **adresu URL** na této [Azure Media Services ukázkové](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) stránce pro adresu URL manifestu.

Pro snímky videa (obrázky) použijte následující Image:

![Miniatura snímku videa 1](images/ams-video-frame-thumbnails-1.PNG) | ![Miniatura snímku videa 2](images/ams-video-frame-thumbnails-2.PNG) | ![Miniatura snímku videa 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
Snímek 1 | Snímek 2 | Snímek 3 |

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio

1. Přidejte do svého řešení nový projekt **Konzolová aplikace (.NET Framework)**.

1. Pojmenujte projekt **VideoReviews**.

1. Projekt vyberte jako jeden spouštěný projekt řešení.

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Pro projekt TermLists nainstalujte následující balíčky NuGet.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizace příkazů using programu

Pomocí příkazů programu upravte následující příkazy.

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

Přidejte následující soukromé vlastnosti do oboru názvů **VideoReviews**, **program** třídy. Aktualizujte `AzureEndpoint` `CMSubscriptionKey` pole a hodnotami adresy URL koncového bodu a klíče předplatného. Můžete je najít na kartě **rychlý Start** prostředku v Azure Portal.


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

### <a name="create-content-moderator-client-object"></a>Vytvořit objekt Content Moderator klienta

Přidejte následující definici metody do oboru názvů **VideoReviews**, **program** třídy.

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

## <a name="create-a-video-review"></a>Vytvořit recenzi videa

Vytvoří recenzi videa pomocí **ContentModeratorClient. Reviews. CreateVideoReviews**. Další informace najdete v [referenčních informacích k rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** má následující požadované parametry:
1. Řetězec obsahující typ MIME, který by měl být "Application/JSON" 
1. Váš Content Moderator název týmu.
1. Objekt **IList \<CreateVideoReviewsBodyItem>** . Každý objekt **CreateVideoReviewsBodyItem** představuje kontrolu videa. Tento rychlý Start vytváří vždy jednu kontrolu.

**CreateVideoReviewsBodyItem** má několik vlastností. Minimálně nastavte následující vlastnosti:
- **Obsah**. Adresa URL videa, která se má zkontrolovat
- ID **obsahu.** ID, které se má přiřadit k recenzi videa
- **Stav:** Nastavte hodnotu na Nepublikováno. Pokud ho nenastavíte, použije se výchozí nastavení "čeká", což znamená, že se recenze videa publikuje a čeká na revizi pro lidskou kontrolu. Po publikování recenze videa už nemůžete do ní přidávat snímky videa, přepisy ani jejich nemoderování.

> [!NOTE]
> **CreateVideoReviews** vrací objekt IList \<string> . Každý z těchto řetězců obsahuje ID pro kontrolu videa. Tato ID jsou identifikátory GUID a nejsou stejná jako **hodnota vlastnosti ID** obsahu. 

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

## <a name="add-video-frames-to-the-video-review"></a>Přidat snímky videa do recenze videa

Snímky videa se přidávají do recenze videa pomocí **ContentModeratorClient. Reviews. AddVideoFrameUrl** (Pokud se vaše video snímky hostují online) nebo **ContentModeratorClient. Reviews. AddVideoFrameStream** (Pokud se vaše snímky videa hostují místně). Tento rychlý Start předpokládá, že vaše snímky videa jsou hostované online, a proto používá **AddVideoFrameUrl**. Další informace najdete v [referenčních informacích k rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** má následující požadované parametry:
1. Řetězec obsahující typ MIME, který by měl být "Application/JSON"
1. Váš Content Moderator název týmu.
1. ID kontroly videa vrácené funkcí **CreateVideoReviews**.
1. Objekt **IList \<VideoFrameBodyItem>** . Každý objekt **VideoFrameBodyItem** představuje snímek videa.

**VideoFrameBodyItem** má následující vlastnosti:
- **Časové razítko**. Řetězec, který obsahuje v sekundách čas ve videu, ze kterého byl snímek videa proveden.
- **FrameImage**. Adresa URL rámce videa
- **Metadata**. Objekt IList \<VideoFrameBodyItemMetadataItem> . **VideoFrameBodyItemMetadataItem** je pouze pár klíč/hodnota. Mezi platné klíče patří:
- **reviewRecommended**. True, pokud se doporučuje lidská recenze snímku videa.
- **adultScore**. Hodnota od 0 do 1, která bude vyhodnotit závažnost obsahu pro dospělé v rámci videa.
- **a**. True, pokud video obsahuje obsah pro dospělé.
- **racyScore**. Hodnota od 0 do 1, která bude vyhodnotit závažnost pikantní obsahu ve snímku videa.
- **r**. True, pokud snímek videa obsahuje pikantní obsah.
- **ReviewerResultTags**. Objekt IList \<VideoFrameBodyItemReviewerResultTagsItem> . **VideoFrameBodyItemReviewerResultTagsItem** je pouze pár klíč/hodnota. Aplikace může tyto značky použít k uspořádání snímků videa.

> [!NOTE]
> V tomto rychlém startu se generují náhodné hodnoty pro vlastnosti **adultScore** a **racyScore** . V produkční aplikaci byste tyto hodnoty získali ze [služby pro moderování videa](video-moderation-api.md), která je nasazena jako Azure Media Service.

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

## <a name="get-video-frames-for-video-review"></a>Získat snímky videa pro kontrolu videa

Snímky videa můžete získat pro kontrolu videa pomocí **ContentModeratorClient. Reviews. GetVideoFrames**. **GetVideoFrames** má následující požadované parametry:
1. Váš Content Moderator název týmu.
1. ID kontroly videa vrácené funkcí **CreateVideoReviews**.
1. Index prvního snímku videa, který je vypočítán od nuly.
1. Počet video snímků, které se mají získat.

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

## <a name="get-video-review-information"></a>Získat informace o kontrole videa

Získáte informace o kontrole videa pomocí **ContentModeratorClient. Reviews. GetView**. **Prověřit** má následující požadované parametry:
1. Váš Content Moderator název týmu.
1. ID kontroly videa vrácené funkcí **CreateVideoReviews**.

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

## <a name="publish-video-review"></a>Publikování recenze videa

Můžete publikovat kontrolu videa pomocí **ContentModeratorClient. Reviews. PublishVideoReview**. **PublishVideoReview** má následující požadované parametry:
1. Váš Content Moderator název týmu.
1. ID kontroly videa vrácené funkcí **CreateVideoReviews**.

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

Přidejte definici **Main** metody do oboru názvů VideoReviews, program třídy. Nakonec zavřete třídu program a obor názvů VideoReviews.

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

## <a name="check-out-your-video-review"></a>Zkontrolovat video

Nakonec se na obrazovce **zkontrolovat** video zobrazí Kontrola videa v účtu nástroje Content moderator revize >  .

![Kontrola videa pro moderátory lidí](images/ams-video-review.PNG)

## <a name="next-steps"></a>Další kroky

Získejte [Content moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a řešení sady [Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tuto a další Content moderator rychlý Start pro .NET.
