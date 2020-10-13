---
title: Vytváření recenzí přepisů videa pomocí .NET-Content Moderator
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak vytvořit recenze přepisu videa pomocí sady Azure Cognitive Services Content Moderator SDK pro .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: f2d0ddae8a9bd8054c740402b8beb3bb0bccfa9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88919212"
---
# <a name="create-video-transcript-reviews-using-net"></a>Vytváření recenzí přepisů videa pomocí .NET

Tento článek poskytuje informace a ukázky kódu, které vám pomůžou rychle začít používat [sadu Content moderator SDK s jazykem C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) k těmto akcím:

- Vytvoření recenze videa pro moderátory lidí
- Přidání moderovaného přepisu do Revize
- Publikování Revize

## <a name="prerequisites"></a>Požadavky

- Pokud jste to ještě neudělali, přihlaste se nebo vytvořte účet na webu [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) Content moderator.
- V tomto článku se předpokládá, že jste rozvedli [střední video](video-moderation-api.md) a [vytvořili jste kontrolu videa](video-reviews-quickstart-dotnet.md) v nástroji pro revize pro rozhodování o lidském rozhodování. Nyní chcete přidat do nástroje pro revize moderované přepisy videa.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Ujistěte se, že klíč rozhraní API může volat rozhraní API pro revize (vytváření úloh).

Pokud jste začínali na portálu Azure Portal, můžete po dokončení předchozích kroků získat dva klíče Content Moderatoru.

Pokud plánujete ve své ukázkové sadě SDK používat klíč rozhraní API poskytnutý službou Azure, postupujte podle pokynů v části o [použití klíče Azure s rozhraním API pro kontroly](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis), aby aplikace mohla volat rozhraní API pro kontroly a vytvářet kontroly.

Pokud použijete klíč bezplatné zkušební verze vygenerovaný nástrojem pro kontrolu, váš účet nástroje pro kontrolu už klíč zná, proto není nutné dělat nic dalšího.

## <a name="prepare-your-video-for-review"></a>Příprava videa na kontrolu

Přidejte přepis do recenze videa. Video se musí publikovat online. Potřebujete svůj koncový bod streamování. Koncový bod streamování umožňuje přehrání videa v přehrávači videa nástroje pro kontrolu.

![Miniatura ukázky videa](images/ams-video-demo-view.PNG)

- Zkopírujte **adresu URL** na této [Azure Media Services ukázkové](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) stránce pro adresu URL manifestu.

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio

1. Přidejte do svého řešení nový projekt **Konzolová aplikace (.NET Framework)**.

1. Pojmenujte projekt **VideoTranscriptReviews**.

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

Přidejte následující soukromé vlastnosti do oboru názvů **VideoTranscriptReviews**, **program**třídy. Aktualizujte `AzureEndpoint` `CMSubscriptionKey` pole a hodnotami adresy URL koncového bodu a klíče předplatného. Můžete je najít na kartě **rychlý Start** prostředku v Azure Portal.

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

Přidejte následující definici metody do oboru názvů VideoTranscriptReviews, program třídy.

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
1. Objekt **IList \<CreateVideoReviewsBodyItem> ** . Každý objekt **CreateVideoReviewsBodyItem** představuje kontrolu videa. Tento rychlý Start vytváří vždy jednu kontrolu.

**CreateVideoReviewsBodyItem** má několik vlastností. Minimálně nastavte následující vlastnosti:
- **Obsah**. Adresa URL videa, která se má zkontrolovat
- ID **obsahu.** ID, které se má přiřadit k recenzi videa
- **Stav**. Nastavte hodnotu na Nepublikováno. Pokud ho nenastavíte, použije se výchozí nastavení "čeká", což znamená, že se recenze videa publikuje a čeká na revizi pro lidskou kontrolu. Po publikování recenze videa už nemůžete do ní přidávat snímky videa, přepisy ani jejich nemoderování.

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
> Klíč služby Content Moderator má omezenou rychlost v jednotkách RPS (žádosti za sekundu). Když tento limit překročíte, sada SDK vyvolá výjimku s kódem chyby 429.
>
> Klíč úrovně Free má limit nastavený na 1 RPS za sekundu.

## <a name="add-transcript-to-video-review"></a>Přidat přepis do recenze videa

Do recenze videa přidáte přepis pomocí **ContentModeratorClient. Reviews. AddVideoTranscript**. **AddVideoTranscript** má následující požadované parametry:
1. Vaše Content Moderator ID týmu.
1. ID kontroly videa vrácené funkcí **CreateVideoReviews**.
1. Objekt **datového proudu** , který obsahuje přepis.

Přepis musí být ve formátu WebVTT. Další informace naleznete v tématu [WebVTT: text webového videa sleduje formát](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Program používá ukázkový přepis ve formátu VTT. V reálném řešení použijete službu Azure Media Indexer k [Vytvoření přepisu](https://docs.microsoft.com/azure/media-services/media-services-index-content) z videa.

Přidejte následující definici metody do oboru názvů VideotranscriptReviews, program třídy.

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

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Přidání výsledku moderování přepisu do kontroly videa

Kromě přidání přepisu do recenze videa přidáte také výsledek moderování tohoto přepisu. Provedete to pomocí **ContentModeratorClient. Reviews. AddVideoTranscriptModerationResult**. Další informace najdete v [referenčních informacích k rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** má následující požadované parametry:
1. Řetězec obsahující typ MIME, který by měl být "Application/JSON" 
1. Váš Content Moderator název týmu.
1. ID kontroly videa vrácené funkcí **CreateVideoReviews**.
1. Objekt IList \<TranscriptModerationBodyItem> . **TranscriptModerationBodyItem** má následující vlastnosti:
1. **Podmínek**. Objekt IList \<TranscriptModerationBodyItemTermsItem> . **TranscriptModerationBodyItemTermsItem** má následující vlastnosti:
1. **Index**. Index výrazu založený na nule.
1. **Termín**. Řetězec, který obsahuje termín.
1. **Časové razítko**. Řetězec, který obsahuje v sekundách čas v přepisu, při kterém jsou výrazy nalezeny.

Přepis musí být ve formátu WebVTT. Další informace naleznete v tématu [WebVTT: text webového videa sleduje formát](https://www.w3.org/TR/webvtt1/).

Přidejte následující definici metody do oboru názvů VideoTranscriptReviews, program třídy. Tato metoda odesílá přepis do metody **ContentModeratorClient. TextModeration. ScreenText** . Také přeloží výsledek na IList \<TranscriptModerationBodyItem> a odešle do **AddVideoTranscriptModerationResult**.

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

Přidejte definici **Main** metody do oboru názvů VideoTranscriptReviews, program třídy. Nakonec zavřete třídu program a obor názvů VideoTranscriptReviews.

> [!NOTE]
> Program používá ukázkový přepis ve formátu VTT. V reálném řešení použijete službu Azure Media Indexer k [Vytvoření přepisu](https://docs.microsoft.com/azure/media-services/media-services-index-content) z videa.

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

## <a name="navigate-to-your-video-transcript-review"></a>Přejít na kontrolu přepisu videa

Přejít na kontrolu přepisu videa v nástroji pro kontrolu Content moderator na obrazovce **Kontrola** > **Video** > **přepisu** videa.

Zobrazí se následující funkce:
- Dva řádky přepisu, které jste přidali
- Výraz vulgárního výrazu byl nalezen a zvýrazněný službou pro moderování textu
- Výběr přepisu textu spustí video z tohoto časového razítka.

![Kontrola přepisu videa pro Moderátoři pro člověka](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Další kroky

Získejte [Content moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a řešení sady [Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tuto a další Content moderator rychlý Start pro .NET.

Naučte se generovat [recenze videí](video-reviews-quickstart-dotnet.md) v nástroji pro revize.

Podrobné informace o tom, jak vyvíjet [kompletní řešení pro moderování videa](video-transcript-moderation-review-tutorial-dotnet.md), najdete v podrobném kurzu.
