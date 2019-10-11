---
title: Vytváření revizí pomocí Content Moderator .NET
titleSuffix: Azure Cognitive Services
description: Jak vytvořit recenze pomocí Azure Content Moderator SDK pro .NET.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: sajagtap
ms.openlocfilehash: e3b36eae4f6dc4343828a38fa4ffe3920a83f589
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242874"
---
# <a name="create-human-reviews-net"></a>Vytvořit lidské recenze (.NET)

Kontroluje ukládání a zobrazování obsahu pro Moderátoři pro lidské moderování k vyhodnocení. Když uživatel dokončí revizi, výsledky se odešlou do zadaného koncového bodu zpětného volání. Tato příručka poskytuje informace a ukázky kódu, které vám pomohou začít používat [sadu Content moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) k těmto akcím:

- Vytvoření sady revizí pro lidské moderování
- Získat stav stávajících revizí pro lidské moderování

## <a name="prerequisites"></a>Požadované součásti

- Přihlaste se nebo vytvořte účet na webu [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) Content moderator.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Zajistěte, aby váš klíč rozhraní API mohl volat kontrolu rozhraní API pro vytvoření revize.

Po dokončení předchozích kroků může dokončit dva Content Moderator klíče, pokud jste začali z Azure Portal.

Pokud máte v ukázce sady SDK používat klíč rozhraní API pro Azure, postupujte podle kroků uvedených v části [použití klíče Azure spolu s bodem recenze rozhraní API](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) , pokud chcete, aby aplikace volala rozhraní API pro revize a vytvořila recenze.

Pokud použijete bezplatný zkušební klíč vygenerovaný nástrojem pro kontrolu, váš účet nástroje pro kontrolu už o klíči ví, a proto nejsou potřeba žádné další kroky.

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. Přidejte do svého řešení nový projekt **konzolové aplikace (.NET Framework)** .

   V ukázkovém kódu pojmenujte projekt **CreateReviews**.

1. Vyberte tento projekt jako jeden spouštěný projekt pro řešení.

### <a name="install-required-packages"></a>Nainstalovat požadované balíčky

Nainstalujte následující balíčky NuGet:

- Microsoft. Azure. Cognitiveservices Account. ContentModerator
- Microsoft. REST. ClientRuntime
- Newtonsoft. JSON

### <a name="update-the-programs-using-statements"></a>Aktualizovat příkazy using programu

Upravte příkazy using programu.


```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Vytvoření klienta Content Moderator

Přidejte následující kód, který vytvoří klienta Content Moderator pro vaše předplatné.

> [!IMPORTANT]
> Aktualizujte pole **a** a **CMSubscriptionKey** hodnotami identifikátoru vaší oblasti a klíče předplatného.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The region/location for your Content Moderator account,
    /// for example, westus.
    /// </summary>
    private static readonly string AzureRegion = "YOUR API REGION";

    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureBaseURL =
        $"https://{AzureRegion}.api.cognitive.microsoft.com";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Vytvoření třídy pro přidružení informací o interním obsahu k ID Revize

Do třídy **program** přidejte následující třídu. Tato třída slouží k přidružení ID revize k vašemu internímu ID obsahu položky.

```csharp
/// <summary>
    /// Associates the review ID (assigned by the service) to the internal
    /// content ID of the item.
    /// </summary>
    public class ReviewItem
    {
        /// <summary>
        /// The media type for the item to review.
        /// </summary>
        public string Type;

        /// <summary>
        /// The URL of the item to review.
        /// </summary>
        public string Url;

        /// <summary>
        /// The internal content ID for the item to review.
        /// </summary>
        public string ContentId;

        /// <summary>
        /// The ID that the service assigned to the review.
        /// </summary>
        public string ReviewId;
    }
```

### <a name="initialize-application-specific-settings"></a>Inicializovat nastavení specifické pro aplikaci

> [!NOTE]
> Váš klíč služby Content Moderator má omezení četnosti požadavků za sekundu (RPS). Pokud tento limit překročíte, sada SDK vyvolá výjimku s kódem chyby 429.
>
> Klíč bezplatné úrovně má jeden limit RPS frekvence.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Do třídy **program** v program.cs přidejte následující konstanty

```csharp
/// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Image List API.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const double latencyDelay = 45;

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";
```

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Do třídy **program** v program.cs přidejte následující konstanty a statická pole.

Aktualizujte tyto hodnoty tak, aby obsahovaly informace specifické pro vaše předplatné a tým.

> [!NOTE]
> Nastavte konstantu týmem na název, který jste použili při vytváření předplatného [nástroje Content moderator recenze](https://contentmoderator.cognitive.microsoft.com/) . Tým načtěte z oddílu **přihlašovací údaje** v nabídce **Nastavení** (ozubené kolo).
>
> Název týmu je hodnota pole **ID** v oddílu **rozhraní API** .

```csharp
/// <summary>
/// The name of the team to assign the review to.
/// </summary>
/// <remarks>This must be the team name you used to create your
/// Content Moderator account. You can retrieve your team name from
/// the Content Moderator web site. Your team name is the Id associated
/// with your subscription.</remarks>
private const string TeamName = "YOUR REVIEW TEAM ID";

/// <summary>
/// The optional name of the subteam to assign the review to.
/// </summary>
private const string Subteam = null;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team. 
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "YOUR API ENDPOINT";

/// <summary>
/// The media type for the item to review.
/// </summary>
/// <remarks>Valid values are "image", "text", and "video".</remarks>
private const string MediaType = "image";

/// <summary>
/// The URLs of the images to create review jobs for.
/// </summary>
private static readonly string[] ImageUrls = new string[] {
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg"
    // add more if you want
};

/// <summary>
/// The metadata key to initially add to each review item.
/// </summary>
private const string MetadataKey = "sc";

/// <summary>
/// The metadata value to initially add to each review item.
/// </summary>
private const string MetadataValue = "true";
```

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Do třídy **program** v program.cs přidejte následující statická pole.

Pomocí těchto polí můžete sledovat stav aplikace.

```csharp
/// <summary>
/// A static reference to the text writer to use for logging.
/// </summary>
private static TextWriter writer;

/// <summary>
/// The cached review information, associating a local content ID
/// to the created review ID for each item.
/// </summary>
private static List<ReviewItem> reviewItems =
    new List<ReviewItem>();
```

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>Vytvoření metody pro zápis zpráv do souboru protokolu

Do třídy **program** přidejte následující metodu.

```csharp
/// <summary>
/// Writes a message to the log file, and optionally to the console.
/// </summary>
/// <param name="message">The message.</param>
/// <param name="echo">if set to <c>true</c>, write the message to the console.</param>
private static void WriteLine(string message = null, bool echo = false)
{
    writer.WriteLine(message ?? String.Empty);

    if (echo)
    {
        Console.WriteLine(message ?? String.Empty);
    }
}
```

## <a name="create-a-method-to-create-a-set-of-reviews"></a>Vytvoření metody pro vytvoření sady revizí

Normálně máte nějakou obchodní logiku pro identifikaci příchozích imagí, textu nebo videa, která je potřeba zkontrolovat. Tady ale stačí použít pevný seznam imagí.

Do třídy **program** přidejte následující metodu.

```csharp
/// <summary>
/// Create the reviews using the fixed list of images.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
private static void CreateReviews(ContentModeratorClient client)
{
    WriteLine(null, true);
    WriteLine("Creating reviews for the following images:", true);

    // Create the structure to hold the request body information.
    List<CreateReviewBodyItem> requestInfo =
        new List<CreateReviewBodyItem>();

    // Create some standard metadata to add to each item.
    List<CreateReviewBodyItemMetadataItem> metadata =
        new List<CreateReviewBodyItemMetadataItem>(
        new CreateReviewBodyItemMetadataItem[] {
            new CreateReviewBodyItemMetadataItem(
                MetadataKey, MetadataValue)
        });

    // Populate the request body information and the initial cached review information.
    for (int i = 0; i < ImageUrls.Length; i++)
    {
        // Cache the local information with which to create the review.
        var itemInfo = new ReviewItem()
        {
            Type = MediaType,
            ContentId = i.ToString(),
            Url = ImageUrls[i],
            ReviewId = null
        };

        WriteLine($" - {itemInfo.Url}; with id = {itemInfo.ContentId}.", true);

        // Add the item informaton to the request information.
        requestInfo.Add(new CreateReviewBodyItem(
            itemInfo.Type, itemInfo.Url, itemInfo.ContentId,
            CallbackEndpoint, metadata));

        // Cache the review creation information.
        reviewItems.Add(itemInfo);
    }

    var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync(
        "application/json", TeamName, requestInfo);

    // Update the local cache to associate the created review IDs with
    // the associated content.
    var reviewIds = reviewResponse.Result.Body;
    for (int i = 0; i < reviewIds.Count; i++)
    {
        Program.reviewItems[i].ReviewId = reviewIds[i];
    }

    WriteLine(JsonConvert.SerializeObject(
    reviewIds, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>Vytvoření metody pro získání stavu stávajících revizí

Do třídy **program** přidejte následující metodu.

> [!Note]
> V praxi byste nastavili adresu URL zpětného volání `CallbackEndpoint` na adresu URL, která by obdržela výsledky ruční kontroly (prostřednictvím požadavku HTTP POST). Tuto metodu můžete změnit, aby kontrolovala stav probíhajících revizí.

```csharp
/// <summary>
    /// Gets the review details from the server.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void GetReviewDetails(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Getting review details:", true);
        foreach (var item in reviewItems)
        {
            var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(
                TeamName, item.ReviewId);

            WriteLine(
                $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                $"{reviewDetail.Result.Body.Status}.", true);
            WriteLine(JsonConvert.SerializeObject(
                reviewDetail.Result.Body, Formatting.Indented));

            Thread.Sleep(throttleRate);
        }
    }
```

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Přidejte kód pro vytvoření sady revizí a kontrolu jejího stavu

Do metody **Main** přidejte následující kód.

Tento kód simuluje mnohé z operací, které provádíte při definování a správě seznamu, a také použití seznamu na obrázky obrazovky. Funkce protokolování umožňují zobrazit objekty odpovědí generované sadou SDK volání služby Content mModerator.

```csharp
using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
{
    writer = outputWriter;
    using (var client = Clients.NewClient())
    {
        CreateReviews(client);
        GetReviewDetails(client);

        Console.WriteLine();
        Console.WriteLine("Perform manual reviews on the Content Moderator site.");
        Console.WriteLine("Then, press any key to continue.");
        Console.ReadKey();

        Console.WriteLine();
        Console.WriteLine($"Waiting {latencyDelay} seconds for results to propigate.");
        Thread.Sleep(latencyDelay * 1000);

        GetReviewDetails(client);
    }

    writer = null;
    outputWriter.Flush();
    outputWriter.Close();
}

Console.WriteLine();
Console.WriteLine("Press any key to exit...");
Console.ReadKey();
```

## <a name="run-the-program-and-review-the-output"></a>Spusťte program a zkontrolujte výstup.

Zobrazí se následující vzorový výstup:

```console
Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
```

Přihlaste se k nástroji Content Moderator recenze, aby se zobrazila nevyřízená recenze obrázku s popiskem **SC** nastaveným na **hodnotu true**. Zobrazí se **také výchozí značky a** a **r** a všechny vlastní značky, které jste mohli v rámci nástroje pro revize definovat.

K odeslání použijte tlačítko **Další** .

![Recenze obrázků pro moderátory lidí](images/moderation-reviews-quickstart-dotnet.PNG)

Pak pokračujte stisknutím libovolné klávesy.

```console
Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...
```

## <a name="check-out-the-following-output-in-the-log-file"></a>Podívejte se na následující výstup v souboru protokolu.

> [!NOTE]
> Ve výstupním souboru řetězce "\{teamname}" a "\{callbackUrl}" odrážejí hodnoty pro pole `TeamName` a `CallbackEndpoint` v uvedeném pořadí.

ID revize a adresy URL obsahu obrázků se liší při každém spuštění aplikace a po dokončení kontroly pole `reviewerResultTags` odráží, jak kontrolor označil položku.

```json
Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.
    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Pending",
        "reviewerResultTags": [],
        "createdBy": "{teamname}",
        "metadata": [
        {
              "key": "sc",
              "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
              "key": "a",
              "value": "False"
        },
        {
              "key": "r",
              "value": "True"
        },
        {
              "key": "sc",
              "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
              "key": "sc",
              "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>Adresa URL vašeho zpětného volání, pokud je zadaná, obdrží tuto odpověď.

Zobrazí se odpověď podobná následujícímu příkladu:

```json
{
    "ReviewId": "201801i48a2937e679a41c7966e838c92f5e649",
    "ModifiedOn": "2018-01-06T05:04:40.5525865Z",
    "ModifiedBy": "yourusername",
    "CallBackType": "Review",
    "ContentId": "0",
    "ContentType": "Image",
    "Metadata": {
        "sc": "true"
        },
    "ReviewerResultTags": {
        "a": "False",
        "r": "False",
    }
}
```

## <a name="next-steps"></a>Další kroky

Získejte [sadu Content moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a Stáhněte si [řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tuto a další Content moderator rychlé starty pro .NET a začněte s integrací.