---
title: Azure Content Moderator – spuštění úlohy moderování pomocí .NET | Dokumentace Microsoftu
description: Spuštění úlohy moderování pomocí Azure Content Moderator SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/10/2018
ms.author: sajagtap
ms.openlocfilehash: 0402d9dc1dfee5e146d3550d095f4fb53e52f12b
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720924"
---
# <a name="start-moderation-jobs-using-net"></a>Spuštění úloh moderování pomocí .NET

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou začít používat [Content Moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) na:
 
- Spustit úlohu moderování kontrolovat a vytvoření revize pro lidských moderátorů
- Získat stav Čeká na revizi
- Sledovat a získat konečný stav kontroly
- Odeslat výsledek, který má adresu Url zpětného volání

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

## <a name="define-a-custom-moderation-workflow"></a>Definovat vlastní moderování pracovního postupu

Kontroluje obsah pomocí rozhraní API a používá moderování úlohy **pracovního postupu** k určení, jestli se má vytvořit revize, nebo ne.
Nástroj pro recenze obsahuje výchozí pracovní postup, Pojďme [definovat vlastní pracovní postup](Review-Tool-User-Guide/Workflows.md) pro tento rychlý start.

Název pracovního postupu použijete v kódu, který se spustí úloha pro moderování.

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

            client.BaseUrl = AzureBaseURL;
            return client;
        }
    }

### <a name="initialize-application-specific-settings"></a>Inicializace nastavení specifické pro aplikaci

Přidejte následující konstanty a statická pole na **Program** třída v souboru Program.cs.

> [!NOTE]
> Nastavte název, který jste použili při vytvoření odběru Content Moderator TeamName – konstanta. Načíst TeamName z [Content Moderator webu](https://westus.contentmoderator.cognitive.microsoft.com/).
> Po přihlášení vyberte **pověření** z **nastavení** nabídky (ozubené kolo).
>
> Hodnota je název vašeho týmu **Id** pole **rozhraní API** oddílu.


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
    /// the Conent Moderator web site. Your team name is the Id associated 
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
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Revies show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Přidejte kód do střední automatické vytvoření recenze a získání podrobností o úlohách

> [!Note]
> V praxi, nastavte adresu URL zpětného volání **CallbackEndpoint** na adresu URL, která přijímá výsledky ruční kontrolu (prostřednictvím požadavku HTTP POST).

Začněte přidáním následujícího kódu **hlavní** metody.

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the nameof the workflow defined in the online review tool.
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

> [!NOTE]
> Klíč služby Content Moderator má požadavků za druhé omezení četnosti (předávajících stran). Při překročení limitu, sada SDK dojde k výjimce s kódem chyby 429. 
>
> Klíč úroveň free má omezení četnosti jeden RPS.

## <a name="run-the-program-and-review-the-output"></a>Spusťte program a prohlédněte si výstup

Následující ukázkový výstup v konzole zobrazí:

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Přihlaste se k Content Moderator Zkontrolujte nástroj zobrazte čekající image zkontrolovat.

Použití **Další** tlačítko Odeslat.

![Obrázek kontroly pro lidských moderátorů](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Ukázkový výstup do souboru protokolu

> [!NOTE]
> Ve výstupním souboru, řetězce **Teamname**, **ContentId**, **CallBackEndpoint**, a **WorkflowId** odrážejí hodnoty, které jste použili dříve.

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


## <a name="your-callback-url-if-provided-receives-this-response"></a>Vaše adresa Url zpětného volání-li zadán, obdrží tuto odpověď.

Zobrazí se odpověď jako v následujícím příkladu:

> [!NOTE]
> V odpovědi zpětné volání, řetězce **ContentId** a **WorkflowId** odrážejí hodnoty, které jste použili dříve.

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


## <a name="next-steps"></a>Další postup

Získejte [Content Moderator sady .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a [řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tuto a další rychlé starty Content Moderator pro platformu .NET a začít používat svoji integraci.
