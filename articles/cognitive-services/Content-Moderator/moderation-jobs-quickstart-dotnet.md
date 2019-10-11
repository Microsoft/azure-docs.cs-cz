---
title: Použití úloh moderování pomocí .NET-Content Moderator
titleSuffix: Azure Cognitive Services
description: Pomocí sady Content Moderator .NET SDK zahajte kompletní úlohy Moderování obsahu pro obrázek nebo textový obsah ve službě Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: bc20af10e2e5b2ceb26c1cc891a8f69eb44e5740
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242885"
---
# <a name="define-and-use-moderation-jobs-net"></a>Definování a použití úloh moderování (.NET)

Úloha moderování slouží jako typ obálky pro funkčnost Moderování obsahu, pracovních postupů a revizí. Tato příručka poskytuje informace a ukázky kódu, které vám pomohou začít používat [sadu Content moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) k těmto akcím:

- Spustit úlohu moderování pro kontrolu a vytváření revizí pro lidské moderování
- Získat stav nedokončené revize
- Sledovat a získat konečný stav kontroly
- Odeslat výsledky kontroly na adresu URL zpětného volání

## <a name="prerequisites"></a>Požadované součásti

- Přihlaste se nebo vytvořte účet na webu [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) Content moderator.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Zajistěte, aby váš klíč rozhraní API mohl volat kontrolu rozhraní API pro vytvoření revize.

Po dokončení předchozích kroků může dokončit dva Content Moderator klíče, pokud jste začali z Azure Portal.

Pokud máte v ukázce sady SDK používat klíč rozhraní API pro Azure, postupujte podle kroků uvedených v části [použití klíče Azure spolu s bodem recenze rozhraní API](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) , pokud chcete, aby aplikace volala rozhraní API pro revize a vytvořila recenze.

Pokud použijete bezplatný zkušební klíč vygenerovaný nástrojem pro kontrolu, váš účet nástroje pro kontrolu už o klíči ví, a proto nejsou potřeba žádné další kroky.

## <a name="define-a-custom-moderation-workflow"></a>Definovat vlastní pracovní postup moderování

Úloha moderování kontroluje obsah pomocí rozhraní API a používá **pracovní postup** k určení, jestli se mají vytvářet recenze nebo ne.
I když nástroj pro kontrolu obsahuje výchozí pracovní postup, pro tento rychlý Start [nadefinujeme vlastní pracovní postup](Review-Tool-User-Guide/Workflows.md) .

Ve svém kódu použijete název pracovního postupu, který spouští úlohu moderování.

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

### <a name="initialize-application-specific-settings"></a>Inicializovat nastavení specifické pro aplikaci

Do třídy **program** v program.cs přidejte následující konstanty a statická pole.

> [!NOTE]
> Nastavte konstantu týmem na název, který jste použili při vytváření předplatného Content Moderator. Na webu [Content moderator](https://westus.contentmoderator.cognitive.microsoft.com/)načtěte tým.
> Po přihlášení vyberte **přihlašovací údaje** z nabídky **Nastavení** (ozubené kolo).
>
> Název týmu je hodnota pole **ID** v oddílu **rozhraní API** .

```csharp
/// <summary>
/// The moderation job will use this workflow that you defined earlier.
/// See the quickstart article to learn how to setup custom workflows.
/// </summary>
private const string WorkflowName = "OCR";

/// <summary>
/// The name of the team to assign the job to.
/// </summary>
/// <remarks>This must be the team name you used to create your
/// Content Moderator account. You can retrieve your team name from
/// the Content Moderator web site. Your team name is the Id associated
/// with your subscription.</remarks>
private const string TeamName = "***";

/// <summary>
/// The URL of the image to create a review job for.
/// </summary>
private const string ImageUrl =
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

/// <summary>
/// The name of the log file to create.
/// </summary>
/// <remarks>Relative paths are relative to the execution directory.</remarks>
private const string OutputFile = "OutputLog.txt";

/// <summary>
/// The number of seconds to delay after a review has finished before
/// getting the review results from the server.
/// </summary>
private const int latencyDelay = 45;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team.
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "";
```

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Přidat kód pro automatické zvýšení střední hodnoty, vytvořit recenzi a získat podrobnosti o úloze

> [!Note]
> V praxi nastavíte adresu URL zpětného volání **CallbackEndpoint** na adresu URL, která obdrží výsledky ruční kontroly (prostřednictvím požadavku HTTP POST).

Začněte přidáním následujícího kódu do metody **Main** .

```csharp
using (TextWriter writer = new StreamWriter(OutputFile, false))
{
    using (var client = Clients.NewClient())
    {
        writer.WriteLine("Create review job for an image.");
        var content = new Content(ImageUrl);

        // The WorkflowName contains the name of the workflow defined in the online review tool.
        // See the quickstart article to learn more.
        var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

        // Record the job ID.
        var jobId = jobResult.Result.Body.JobIdProperty;

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
            jobResult.Result.Body, Formatting.Indented));

        Thread.Sleep(2000);
        writer.WriteLine();

        writer.WriteLine("Get review job status.");
        var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
                jobDetails.Result.Body, Formatting.Indented));

        Console.WriteLine();
        Console.WriteLine("Perform manual reviews on the Content Moderator site.");
        Console.WriteLine("Then, press any key to continue.");
        Console.ReadKey();

        Console.WriteLine();
        Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
        Thread.Sleep(latencyDelay * 1000);

        writer.WriteLine("Get review details.");
        jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
        TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
        jobDetails.Result.Body, Formatting.Indented));
    }
    writer.Flush();
    writer.Close();
}
```

> [!NOTE]
> Váš klíč služby Content Moderator má omezení četnosti požadavků za sekundu (RPS). Pokud překročíte limit, sada SDK vyvolá výjimku s kódem chyby 429.
>
> Klíč bezplatné úrovně má jeden limit RPS frekvence.

## <a name="run-the-program-and-review-the-output"></a>Spusťte program a zkontrolujte výstup.

V konzole se zobrazí následující vzorový výstup:

```console
Perform manual reviews on the Content Moderator site.
Then, press any key to continue.
```

Přihlaste se k nástroji Content Moderator recenze, abyste viděli nedokončenou kontrolu obrazu.

K odeslání použijte tlačítko **Další** .

![Recenze obrázků pro moderátory lidí](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Podívejte se na ukázkový výstup v souboru protokolu.

> [!NOTE]
> Ve výstupním souboru obsahují řetězce **Teams** **, ID obsahu**, **CallBackEndpoint**a **WorkflowId** hodnoty, které jste použili dříve.

```json
Create moderation job for an image.
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
}

Get review details.
{
    "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
    "TeamName": "some team name",
    "Status": "InProgress",
    "WorkflowId": "OCR",
    "Type": "Image",
    "CallBackEndpoint": "",
    "ReviewId": "",
    "ResultMetaData": [],
    "JobExecutionReport": [
    {
        "Ts": "2018-01-07T00:38:26.7714671",
        "Msg": "Successfully got hasText response from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:26.4181346",
        "Msg": "Getting hasText from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:25.5122828",
        "Msg": "Starting Execution - Try 1"
    }
    ]
}
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>Adresa URL vašeho zpětného volání, pokud je zadaná, obdrží tuto odpověď.

Zobrazí se odpověď podobná následujícímu příkladu:

> [!NOTE]
> Ve vaší odpovědi zpětného **volání řetězce ID** obsahu a **WorkflowId** odráží hodnoty, které jste použili dříve.

```json
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
    "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
    "WorkFlowId": "OCR",
    "Status": "Complete",
    "ContentType": "Image",
    "CallBackType": "Job",
    "ContentId": "contentID",
    "Metadata": {
        "hastext": "True",
        "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
        "imagename": "contentID"
    }
}
```

## <a name="next-steps"></a>Další kroky

Získejte [Content moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a řešení sady [Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tuto a další Content moderator rychlý Start pro .NET a začněte s integrací.
