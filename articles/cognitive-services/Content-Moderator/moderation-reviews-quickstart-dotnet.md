---
title: Azure Content Moderator – vytvoření recenze pomocí .NET | Dokumentace Microsoftu
description: Vytvoření recenze, pomocí Azure Content Moderator SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/10/2018
ms.author: sajagtap
ms.openlocfilehash: c5f301e7ed15100c39f0af77942147275b966ed9
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47180769"
---
# <a name="create-reviews-using-net"></a>Vytvoření kontroly pomocí .NET

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou začít používat [Content Moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) na:
 
- Vytvořit sadu revizí pro lidských moderátorů
- Získat stav kontrol existující lidských moderátorů

Obecně platí se vloží obsah přes nějaké automatizované moderování před naplánované pro recenze prováděné lidmi. Tento článek se zabývá jenom vytvořit kontrolu pro lidské moderování. Kompletní scénář, najdete v článku [moderování obsahu Facebook](facebook-post-moderation.md) a [moderování katalogů elektronického obchodování](ecommerce-retail-catalog-moderation.md) kurzy.

Tento článek předpokládá, že jste již obeznámeni s Visual Studio a C#.

## <a name="sign-up-for-content-moderator"></a>Zaregistrujte si Content Moderatoru

Než budete moct použít služby Content Moderator přes rozhraní REST API nebo sady SDK, je nutné klíč předplatného.
Odkazovat [rychlý Start](quick-start.md) se dozvíte, jak můžete získat klíč.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>Zaregistrovat účet nástroj pro kontrolu, pokud nebyla dokončena v předchozím kroku

Pokud jste získali Content Moderator z webu Azure portal, také [zaregistrujte si účet nástroj pro revize](https://contentmoderator.cognitive.microsoft.com/) a vytvořte tým kontroly. Budete potřebovat Id týmu a nástroje pro recenze pro volání rozhraní API přezkoumání a spuštění úlohy a zobrazíte recenzí v nástroj pro recenze.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Ujistěte se, že svůj klíč rozhraní API můžete volat rozhraní API pro kontrolu pro vytvoření revize

Po dokončení předchozích kroků, můžete uvíznout dva klíče Content Moderator Pokud jste spustili z webu Azure portal. 

Pokud máte v plánu používat klíč rozhraní API poskytuje Azure ve vaší ukázce sady SDK, postupujte podle pokynů uvedených v [klíče služby Azure pomocí rozhraní API pro kontrolu](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api) oddílu, aby vaše aplikace volat rozhraní API pro kontrolu a vytvořit revize.

Pokud používáte bezplatné zkušební verze klíč vygenerovaný pomocí nástroje pro recenze, váš účet nástroj pro revize již ví o klíči a proto nejsou požadované žádné další kroky.

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. Přidat nový **Konzolová aplikace (.NET Framework)** do svého řešení projekt.

   Ve vzorovém kódu, pojmenujte projekt **CreateReviews**.

1. Vyberte tento projekt jako jeden spouštěný projekt pro řešení.

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Nainstalujte následující balíčky NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizace programu v nástrojích příkazy

Upravit program v nástrojích příkazy.

    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="create-the-content-moderator-client"></a>Vytvoření klienta služby Content Moderator

Přidejte následující kód k vytvoření klienta Content Moderator pro vaše předplatné.

> [!IMPORTANT]
> Aktualizace **Oblast_azure** a **CMSubscriptionKey** pole s hodnotami vaší oblasti identifikátor a klíč předplatného.


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

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Vytvoření třídy přidružení interní informace o obsahu k ID revize

Přidejte následující třídy, která se **Program** třídy.
Tato třída slouží k přidružení ID revize interní ID obsahu pro položku.

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

### <a name="initialize-application-specific-settings"></a>Inicializace nastavení specifické pro aplikaci

> [!NOTE]
> Klíč služby Content Moderator má požadavků za druhé omezení četnosti (předávajících stran) a při překročení limitu, vyvolá výjimku s kódem chyby 429, sady SDK. 
>
> Klíč úroveň free má omezení četnosti jeden RPS.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Přidejte následující konstanty **Program** třída v souboru Program.cs.
    
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

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Přidejte následující konstanty a statická pole na **Program** třída v souboru Program.cs.

Aktualizujte tyto hodnoty tak, aby obsahovala informace specifické pro vaše předplatné a týmu.

> [!NOTE]
> Konstanta TeamName nastavíte na název, který jste použili při vytváření vašeho [nástroj pro kontrolu Content Moderatoru](https://contentmoderator.cognitive.microsoft.com/) předplatného. Načíst TeamName z **pověření** tématu **nastavení** nabídky (ozubené kolo).
>
> Hodnota je název vašeho týmu **Id** pole **rozhraní API** oddílu.

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

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Přidejte následující statické pole na **Program** třída v souboru Program.cs.

Pomocí těchto polí můžete sledovat stav aplikace.

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

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>Vytvořte metodu k zápisu zprávy do souboru protokolu

Do třídy **Program** přidejte následující metodu. 

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

## <a name="create-a-method-to-create-a-set-of-reviews"></a>Vytvořit metodu pro vytvoření sady revize

Za normálních okolností máte nějakou obchodní logiku pro identifikaci příchozí obrázky, text, nebo videa, která je třeba. Však stačí používáme pevně daný seznam imagí.

Do třídy **Program** přidejte následující metodu.

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

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>Vytvořte metodu k získání stavu existující revize

Do třídy **Program** přidejte následující metodu. 

> [!Note]
> V praxi, nastavte adresu URL zpětného volání `CallbackEndpoint` na adresu URL, která by zobrazí výsledky ruční kontrolu (prostřednictvím požadavku HTTP POST).
> Můžete změnit tak tuto metodu ke kontrole stavu čekající revize.

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

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Přidejte kód, který vytvoříte sadu revizí a zkontrolujte stav

Přidejte následující kód, který **hlavní** metody.

Tento kód simuluje mnoho operací, které provedete v definování a správa seznamu, jakož i pomocí seznamu do bitové kopie obrazovky. Funkce protokolování umožňují zobrazit objekty odpovědi generovaných volání sady SDK ke službě Content Moderatoru.

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

## <a name="run-the-program-and-review-the-output"></a>Spusťte program a prohlédněte si výstup

Uvidíte následující ukázkový výstup:

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.

Přihlaste se k Content Moderator Zkontrolujte nástroj zobrazte čekající revize s image **sc** popisek nastavený **true**. Zobrazí také výchozí **a** a **r** značky a žádné vlastní značky, které byly definovány v rámci nástroje pro recenze. 

Použití **Další** tlačítko Odeslat.

![Obrázek kontroly pro lidských moderátorů](images/moderation-reviews-quickstart-dotnet.PNG)

Stisknutím jakékoli klávesy, abyste mohli pokračovat.

    Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...

## <a name="check-out-the-following-output-in-the-log-file"></a>Projděte si následující výstup do souboru protokolu.

> [!NOTE]
> Ve výstupním souboru, řetězce "\{teamname}" a "\{callbackUrl}" odrážejí hodnoty `TeamName` a `CallbackEndpoint` pole, v uvedeném pořadí.

Zkontrolujte ID a bitové kopie obsahu, adresy URL se liší pokaždé, když spustíte aplikaci a provedení, když je kontrola, `reviewerResultTags` pole odráží, jak kontrolor označené položky.

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

## <a name="your-callback-url-if-provided-receives-this-response"></a>Vaše adresa Url zpětného volání-li zadán, obdrží tuto odpověď

Zobrazí se odpověď jako v následujícím příkladu:

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


## <a name="next-steps"></a>Další postup

Získejte [Content Moderator sady .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a [řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tuto a další rychlé starty Content Moderator pro platformu .NET a začít používat svoji integraci.
