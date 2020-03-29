---
title: Vytváření recenzí přepisů videí pomocí rozhraní .NET – Moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak vytvořit recenze přepisu videa pomocí sady SDK moderátora obsahu služby Azure Cognitive Services pro rozhraní .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: b2d763454b86570b57a16fb9ae2107a2a2bcd23d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73744389"
---
# <a name="create-video-transcript-reviews-using-net"></a>Vytváření recenzí přepisů videí pomocí rozhraní .NET

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat [sadu Content Moderator SDK s c#:](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

- Vytvoření videorecenze pro lidské moderátory
- Přidání moderovaného přepisu do recenze
- Publikování recenze

## <a name="prerequisites"></a>Požadavky

- Pokud jste tak ještě neučinili, přihlaste se nebo si vytvořte účet na webu [nástroje Kontrola](https://contentmoderator.cognitive.microsoft.com/) moderátora obsahu.
- Tento článek předpokládá, že jste [moderovali video](video-moderation-api.md) a [vytvořili recenzi videa](video-reviews-quickstart-dotnet.md) v nástroji pro kontrolu lidského rozhodování. Nyní chcete přidat moderované přepisy videa do nástroje pro kontrolu.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Ujistěte se, že klíč rozhraní API může volat rozhraní API pro kontrolu (vytváření úloh)

Pokud jste začínali na portálu Azure Portal, můžete po dokončení předchozích kroků získat dva klíče Content Moderatoru.

Pokud plánujete ve své ukázkové sadě SDK používat klíč rozhraní API poskytnutý službou Azure, postupujte podle pokynů v části o [použití klíče Azure s rozhraním API pro kontroly](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis), aby aplikace mohla volat rozhraní API pro kontroly a vytvářet kontroly.

Pokud použijete klíč bezplatné zkušební verze vygenerovaný nástrojem pro kontrolu, váš účet nástroje pro kontrolu už klíč zná, proto není nutné dělat nic dalšího.

## <a name="prepare-your-video-for-review"></a>Příprava videa ke kontrole

Přidejte přepis do recenze videa. Video musí být zveřejněno online. Potřebujete jeho koncový bod streamování. Koncový bod streamování umožňuje přehrávači videa nástroje pro kontrolu přehrávání videa.

![Miniatura ukázky videa](images/ams-video-demo-view.PNG)

- Zkopírujte **adresu URL** na této [ukázkové](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) stránce Azure Media Services pro adresu URL manifestu.

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio

1. Přidejte do svého řešení nový projekt **Konzolová aplikace (.NET Framework)**.

1. Název projektu **VideoTranscriptReviews**.

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

Přidejte následující soukromé vlastnosti do oboru názvů **VideoTranscriptReviews**, **třída Program**. Aktualizujte `AzureEndpoint` `CMSubscriptionKey` pole a hodnotami adresy URL koncového bodu a klíče předplatného. Najdete je na kartě **Rychlý start** vašeho prostředku na webu Azure Portal.

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

Přidejte následující definici metody do oboru názvů VideoTranscriptReviews, třída Program.

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
> Klíč služby Content Moderator má omezenou rychlost v jednotkách RPS (žádosti za sekundu). Když tento limit překročíte, sada SDK vyvolá výjimku s kódem chyby 429.
>
> Klíč úrovně Free má limit nastavený na 1 RPS za sekundu.

## <a name="add-transcript-to-video-review"></a>Přidání přepisu do recenze videa

Přepis přidáte do recenze videa pomocí **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** má následující požadované parametry:
1. ID týmu moderátora obsahu.
1. Video recenzi ID vrátil **CreateVideoReviews**.
1. Stream **Stream** objekt, který obsahuje přepis.

Přepis musí být ve formátu WebVTT. Další informace naleznete v tématu [WebVTT: Web Video Text Tracks Format](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Program používá ukázkový přepis ve formátu VTT. V reálném řešení pomocí služby Azure Media Indexer ke [generování přepisu](https://docs.microsoft.com/azure/media-services/media-services-index-content) z videa.

Přidejte následující definici metody do oboru názvů VideotranscriptReviews, třída Program.

```csharp
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
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Přidání výsledku moderování přepisu do recenze videa

Kromě přidání přepisu do recenze videa přidáte také výsledek moderování tohoto přepisu. Uděláte to s **ContentModeratorClient.Reviews.AddVideoTranscriptModeonResult**. Další informace najdete v [referenčních informacích k rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModeonResult** má následující požadované parametry:
1. Řetězec, který obsahuje typ MIME, který by měl být "application/json." 
1. Název týmu moderátora obsahu.
1. Video recenzi ID vrátil **CreateVideoReviews**.
1. IList\<PřepisModerováníBodyItem>. A **TranscriptModerationBodyItem** má následující vlastnosti:
1. **Podmínky**. IList\<PřepisModerováníBodyItemPoložky>. A **TranscriptModerationBodyItemItem** má následující vlastnosti:
1. **Index**. Nula index termínu.
1. **Termín**. Řetězec, který obsahuje termín.
1. **Časové razítko**. Řetězec, který obsahuje v sekundách čas v přepisu, ve kterém jsou nalezeny podmínky.

Přepis musí být ve formátu WebVTT. Další informace naleznete v tématu [WebVTT: Web Video Text Tracks Format](https://www.w3.org/TR/webvtt1/).

Přidejte následující definici metody do oboru názvů VideoTranscriptReviews, třída Program. Tato metoda odešle přepis do **ContentModeratorClient.TextModeon.ScreenText** metoda. To také překládá výsledek do\<IList TranscriptModerationBodyItem> a odešle **do AddVideoTranscriptModerationResult**.

```csharp
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

Přidejte definici **hlavní** metody do oboru názvů VideoTranscriptReviews, program třídy. Nakonec zavřete program třídy a VideoTranscriptReviews obor názvů.

> [!NOTE]
> Program používá ukázkový přepis ve formátu VTT. V reálném řešení pomocí služby Azure Media Indexer ke [generování přepisu](https://docs.microsoft.com/azure/media-services/media-services-index-content) z videa.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

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
```

## <a name="run-the-program-and-review-the-output"></a>Spuštění programu a kontrola výstupu

Při spuštění aplikace se zobrazí výstup na následujících řádcích:

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Přechod na recenzi přepisu videa

Přejděte na recenzi přepisu videa v nástroji pro kontrolu moderátora obsahu na obrazovce **Zkontrolovat**>přepis**videa.**>**Transcript**

Zobrazí se následující funkce:
- Dva řádky přepisu, které jste přidali
- Vulgární výrazy nalezené a zvýrazněné službou moderování textu
- Výběr textu přepisu spustí video z tohoto časového razítka.

![Video přepis recenzi pro lidské moderátory](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Další kroky

Získejte [sada Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a řešení sady Visual [Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tento a další rychlé starty moderátora obsahu pro rozhraní .NET.

Přečtěte si, jak generovat [recenze videí](video-reviews-quickstart-dotnet.md) v nástroji pro recenze.

Podívejte se na podrobný návod, jak vyvinout [kompletní video moderování řešení](video-transcript-moderation-review-tutorial-dotnet.md).
