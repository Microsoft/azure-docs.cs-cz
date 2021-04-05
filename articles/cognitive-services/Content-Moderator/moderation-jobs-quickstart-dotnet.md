---
title: Použití úloh moderování pomocí .NET-Content Moderator
titleSuffix: Azure Cognitive Services
description: Pomocí sady Content Moderator .NET SDK zahajte kompletní úlohy Moderování obsahu pro obrázek nebo textový obsah ve službě Azure Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: ec101786f33aa6f2525d685993d6b6c891ab2e9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "88936312"
---
# <a name="define-and-use-moderation-jobs-net"></a>Definování a použití úloh moderování (.NET)

Úloha moderování slouží jako typ obálky pro funkčnost Moderování obsahu, pracovních postupů a revizí. Tato příručka poskytuje informace a ukázky kódu, které vám pomohou začít používat [sadu Content moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) k těmto akcím:

- Spuštění úlohy moderování, která vyhledá a vytvoří kontroly pro lidské moderátory
- Získání stavu čekající kontroly
- Sledování kontroly a získání jejího konečného stavu
- Odeslat výsledky kontroly na adresu URL zpětného volání

## <a name="prerequisites"></a>Požadavky

- Přihlaste se nebo vytvořte účet na webu [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) Content moderator.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Kontrola, že klíč rozhraní API může volat rozhraní API pro kontroly, aby se mohla vytvořit kontrola

Pokud jste začínali na portálu Azure Portal, můžete po dokončení předchozích kroků získat dva klíče Content Moderatoru.

Pokud plánujete ve své ukázkové sadě SDK používat klíč rozhraní API poskytnutý službou Azure, postupujte podle pokynů v části o [použití klíče Azure s rozhraním API pro kontroly](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis), aby aplikace mohla volat rozhraní API pro kontroly a vytvářet kontroly.

Pokud použijete klíč bezplatné zkušební verze vygenerovaný nástrojem pro kontrolu, váš účet nástroje pro kontrolu už klíč zná, proto není nutné dělat nic dalšího.

## <a name="define-a-custom-moderation-workflow"></a>Definice vlastního pracovního postupu moderování

Úloha moderování zkontroluje obsah pomocí rozhraní API a použije **pracovní postup**, pomocí kterého určí, jestli se mají vytvořit kontroly, nebo ne.
I když nástroj pro kontrolu obsahuje výchozí pracovní postup, pro tento rychlý start si [definujeme vlastní pracovní postup](Review-Tool-User-Guide/Workflows.md).

Použijete název pracovního postupu v kódu, který zahájí úlohu moderování.

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio

1. Přidejte do svého řešení nový projekt **Konzolová aplikace (.NET Framework)**.

   Ve vzorovém kódu pojmenujte tento projekt **CreateReviews**.

1. Projekt vyberte jako jeden spouštěný projekt řešení.

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Nainstalujte následující balíčky NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizace příkazů using programu

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

### <a name="create-the-content-moderator-client"></a>Vytvoření klienta Content Moderatoru

Přidejte následující kód, abyste pro své předplatné vytvořili klienta Content Moderatoru.

> [!IMPORTANT]
> Aktualizujte pole **AzureEndpoint** a **CMSubscriptionKey** hodnotami adresy URL koncového bodu a klíče předplatného.

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
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

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

        client.Endpoint = AzureEndpoint;
        return client;
    }
}
```

### <a name="initialize-application-specific-settings"></a>Inicializace nastavení specifických pro aplikaci

Do třídy **Program** v souboru Program.cs přidejte následující konstanty a statická pole.

> [!NOTE]
> Konstantu TeamName nastavte na název, který jste použili při vytváření předplatného Content Moderatoru. TeamName najdete na webu Content Moderatoru.
> Až se přihlásíte, vyberte z nabídky **Nastavení** (ozubené kolo) možnost **Přihlašovací údaje**.
>
> Název týmu je hodnota pole **Id** v části **API**.

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

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Přidání kódu, který provede automatické moderování, vytvoří kontrolu a získá podrobnosti o úloze

> [!Note]
> V praxi byste nastavili adresu URL pro zpětné volání **CallbackEndpoint** na adresu URL, která by načítala výsledky ručních kontrol (prostřednictvím požadavku HTTP POST).

Začněte tak, že do metody **Main** přidáte následující kód.

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
> Klíč služby Content Moderator má omezenou rychlost v jednotkách RPS (žádosti za sekundu). Když tento limit překročíte, sada SDK vyvolá výjimku s kódem chyby 429.
>
> Klíč úrovně Free má limit nastavený na 1 RPS za sekundu.

## <a name="run-the-program-and-review-the-output"></a>Spuštění programu a kontrola výstupu

V konzole uvidíte následující ukázkový výstup:

```console
Perform manual reviews on the Content Moderator site.
Then, press any key to continue.
```

Přihlaste se k nástroji Content Moderatoru pro kontrolu, abyste si zobrazili čekající kontroly obrázků.

Tlačítkem **Next** (Další) revizi odešlete.

![Kontrola obrázku lidskými moderátory](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Zobrazení ukázkového výstupu v souboru protokolu

> [!NOTE]
> Řetězce **Teamname**, **ContentId**, **CallBackEndpoint** a **WorkflowId** ve výstupním souboru odrážejí hodnoty, které jste použili dříve.

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

## <a name="your-callback-url-if-provided-receives-this-response"></a>Adresa URL pro zpětné volání (pokud je k dispozici) získá tuto odpověď

Zobrazí se odpověď, která vypadá jako následující příklad:

> [!NOTE]
> Řetězce **ContentId** a **WorkflowId** v odpovědi zpětného volání odrážejí hodnoty, které jste použili dříve.

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

Získejte pro tento rychlý start a jiné rychlé starty Content Moderatoru pro .NET [sadu Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a [řešení Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) a začněte se svou integrací.
