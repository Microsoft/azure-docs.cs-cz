---
title: 'Rychlý start: Vytvoření pomocí rozhraní .NET – Content Moderator revize'
titlesuffix: Azure Cognitive Services
description: Způsob vytvoření revizí pomocí sady Azure Content Moderator SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 8a5c11df1b6353a100dd5f1cf388308b9c048932
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258860"
---
# <a name="quickstart-create-reviews-using-net"></a>Rychlý start: Vytvoření kontroly pomocí .NET

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou začít používat [sadu Content Moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) k následujícím účelům:
 
- Vytvoření sady revizí pro lidské moderátory
- Získání stavu existujících revizí pro lidské moderátory

Většinou obsah projde nějakým automatizovaným moderováním, než se naplánuje lidská revize. Tento článek se zabývá pouze způsobem vytvoření revize pro lidské moderování. Kompletní scénář, najdete v článku [moderování obsahu Facebook](facebook-post-moderation.md) a [moderování obrázků produktů elektronického obchodování](ecommerce-retail-catalog-moderation.md) kurzy.

Tento článek předpokládá, že už máte zkušenosti se sadou Visual Studio a jazykem C#.

## <a name="sign-up-for-content-moderator"></a>Registrace do Content Moderatoru

Než začnete služby Content Moderatoru prostřednictvím rozhraní REST API nebo sady SDK používat, budete potřebovat klíč předplatného. Podle pokynů v tématu [Vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) se přihlaste k odběru Content Moderatoru a získejte svůj klíč.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>Registrace účtu nástroje pro kontrolu, pokud jste se nezaregistrovali v předchozím kroku

Pokud jste Content Moderator získali na portálu Azure Portal, [zaregistrujte si i účet nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) a vytvořte posuzovací tým. Abyste mohli zavolat rozhraní API pro kontroly a tím zahájit úlohu a zobrazit kontroly v nástroji pro kontrolu, budete potřebovat ID týmu a nástroj pro kontrolu.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Kontrola, že klíč rozhraní API může volat rozhraní API pro kontroly, aby se mohla vytvořit kontrola

Pokud jste začínali na portálu Azure Portal, můžete po dokončení předchozích kroků získat dva klíče Content Moderatoru. 

Pokud plánujete ve své ukázkové sadě SDK používat klíč rozhraní API poskytnutý službou Azure, postupujte podle pokynů v části o [použití klíče Azure s rozhraním API pro kontroly](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api), aby aplikace mohla volat rozhraní API pro kontroly a vytvářet kontroly.

Pokud budete používat klíč bezplatné zkušební verze vygenerovaný nástrojem pro revidování, pak váš účet nástroje pro revidování už klíč zná a není potřeba už žádné další kroky provádět.

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio

1. Přidejte do svého řešení nový projekt **Konzolová aplikace (.NET Framework)**.

   Ve vzorovém kódu pojmenujte tento projekt **CreateReviews**.

1. Projekt vyberte jako jediný spouštěný projekt řešení.

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Nainstalujte následující balíčky NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizace příkazů using programu

Upravte příkazy using programu.

    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="create-the-content-moderator-client"></a>Vytvoření klienta Content Moderatoru

Přidejte následující kód, abyste pro své předplatné vytvořili klienta Content Moderatoru.

> [!IMPORTANT]
> Aktualizujte pole **AzureRegion** a **CMSubscriptionKey** hodnotami identifikátoru oblasti a klíče předplatného.


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

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Vytvoření třídy pro přidružení interních informací o obsahu k ID revize

Do třídy **Program** přidejte následující třídu.
Tato třída slouží k přidružení ID revize k ID interního obsahu dané položky.

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

### <a name="initialize-application-specific-settings"></a>Inicializace nastavení specifických pro aplikaci

> [!NOTE]
> Klíč služby Content Moderatoru má limit četnosti požadavků za sekundu (RPS), a pokud ho překročíte, sada SDK zobrazí výjimku s chybovým kódem 429. 
>
> Klíč úrovně Free má limit nastavený na jeden požadavek za vteřinu.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Do třídy **Program** v souboru Program.cs přidejte následující konstanty.
    
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

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Do třídy **Program** v souboru Program.cs přidejte následující konstanty a statická pole.

Aktualizujte tyto hodnoty tak, aby obsahovaly informace specifické pro vaše předplatné a tým.

> [!NOTE]
> Konstantu TeamName nastavte na název, který jste použili při vytváření předplatného [nástroje Content Moderatoru pro revidování](https://contentmoderator.cognitive.microsoft.com/). Název týmu získáte z části **Credentials** (Přihlašovací údaje) v nabídce **Settings** (Nastavení – ozubené kolečko).
>
> Název týmu je hodnota pole **Id** v části **API**.

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

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Do třídy **Program** v souboru Program.cs přidejte následující statická pole.

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

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>Vytvoření metody k zapisování zpráv do souboru protokolu

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

## <a name="create-a-method-to-create-a-set-of-reviews"></a>Vytvoření metody k vytvoření sady revizí

Za normálních okolností byste k identifikaci příchozích obrázků, textů nebo videí, které je třeba revidovat, používali obchodní logiku. Tady však stačí používat pevně daný seznam obrázků.

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

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>Vytvoření metody k získání stavu existujících revizí

Do třídy **Program** přidejte následující metodu. 

> [!Note]
> V praxi byste nastavili adresu URL pro zpětné volání `CallbackEndpoint` na adresu URL, která by načítala výsledky ručních revizí (prostřednictvím žádosti HTTP POST).
> Tuto metodu byste mohli změnit, aby kontrolovala stav čekajících revizí.

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

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Přidání kódu k vytvoření sady revizí a kontrole jejího stavu

Do metody **Main** přidejte následující kód.

Tento kód simuluje mnoho operací, které provádíte při definování a správě seznamu i použití seznamu k vyhledávání obrázků. Funkce protokolování vám umožňují zobrazit objekty odpovědí vygenerované voláními sady SDK do služby Content Moderatoru.

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

## <a name="run-the-program-and-review-the-output"></a>Spuštění programu a kontrola výstupu

Uvidíte následující ukázkový výstup:

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.

Přihlaste se k nástroji Content Moderatoru pro revidování, abyste si zobrazili čekající revize obrázků s popiskem **sc**, který má hodnotu **true**. Můžete si také zobrazit výchozí značky **a** a **r** nebo libovolné vlastní značky, které jste si v nástroji pro revidování nadefinovali. 

Tlačítkem **Next** (Další) revizi odešlete.

![Revize obrázku lidskými moderátory](images/moderation-reviews-quickstart-dotnet.PNG)

Pak pokračujte stisknutím libovolné klávesy.

    Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...

## <a name="check-out-the-following-output-in-the-log-file"></a>Kontrola následujícího výstupu v souboru protokolu

> [!NOTE]
> Ve výstupním souboru odpovídají řetězce „\{teamname}“ a „\{callbackUrl}“ hodnotám polí `TeamName` a `CallbackEndpoint`.

ID revizí a adresy URL obsahu obrázků se při každém spuštění aplikace mění a po dokončení revize odpovídá pole `reviewerResultTags` tomu, jak revidující položku označil.

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

## <a name="your-callback-url-if-provided-receives-this-response"></a>Adresa URL pro zpětné volání (pokud je k dispozici) získá tuto odpověď

Zobrazí se odpověď, která vypadá přibližně takto:

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

Získejte pro tento rychlý start a jiné rychlé starty Content Moderatoru pro technologii .NET [sadu Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a [řešení Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) a začněte se svou integrací.
