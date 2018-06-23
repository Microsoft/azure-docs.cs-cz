---
title: Azure obsahu moderátora - Start přerušování úlohy pomocí rozhraní .NET | Microsoft Docs
description: Postup zahájení úlohy přerušování pomocí sady Azure obsahu moderátora SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/06/2018
ms.author: sajagtap
ms.openlocfilehash: a103875607355993e216ce1ddea02009fc8fa1c4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342468"
---
# <a name="start-moderation-jobs-using-net"></a>Spuštění úlohy přerušování pomocí rozhraní .NET

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou začít používat sadu SDK obsahu moderátora pro technologii .NET:
 
- Spustit úlohu přerušování kontrolovat a vytvořit recenze pro lidské moderátorů
- Načíst stav Čeká na přehled
- Sledování a získávání konečného stavu modulu zkontrolujte
- Odeslat výsledek, který má adresu Url zpětné volání

Tento článek předpokládá, že jste již obeznámeni s Visual Studio a C#.

## <a name="sign-up-for-content-moderator-services"></a>Zaregistrujte si obsahu moderátora služby

Před použitím služby obsahu moderátora přes rozhraní REST API nebo sady SDK, je nutné klíč předplatného.
Odkazovat [rychlý Start](quick-start.md) se dozvíte, jak můžete získat klíč.

## <a name="define-a-custom-moderation-workflow"></a>Definovat vlastní přerušování pracovního postupu

Úlohu přerušování kontroluje svůj obsah pomocí rozhraní API a používá **pracovního postupu** k určení, jestli se mají vytvořit recenze nebo ne.
Při Zkontrolujte nástroj obsahuje výchozí pracovní postup, můžeme [definovat vlastní pracovní postup](Review-Tool-User-Guide/Workflows.md) pro tento rychlý start.

Použijte název pracovního postupu ve vašem kódu, který spustí úlohu přerušování.

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. Přidejte nový **konzolovou aplikaci (rozhraní .NET Framework)** projekt pro vaše řešení.

   V ukázkovém kódu, název projektu **CreateReviews**.

1. Vyberte tento projekt jako jeden počáteční projekt pro řešení.

1. Přidat odkaz na **ModeratorHelper** sestavení, které jste vytvořili v projektu [obsahu moderátora klienta pomocná rychlý Start](content-moderator-helper-quickstart-dotnet.md).

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Nainstalujte následující balíčky NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizace programu je pomocí příkazů

Upravit program je pomocí příkazů.

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="initialize-application-specific-settings"></a>Inicializace nastavení pro konkrétní aplikace

Přidejte následující konstanty a statických polí na **Program** – třída v souboru Program.cs.

> [!NOTE]
> Nastavte název, který jste použili při vytvoření odběru obsahu moderátora TeamName konstanta. Načtení TeamName z [obsahu moderátora webu](https://westus.contentmoderator.cognitive.microsoft.com/).
> Jakmile se přihlásíte, vyberte **pověření** z **nastavení** nabídky (ozubené kolečko).
>
> Název vašeho týmu je hodnota **ID** pole **rozhraní API** části.


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

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Přidejte kód až automatické – střední, vytvořte kontrolu a získat podrobnosti úlohy

> [!Note]
> V praxi, nastavte adresu URL zpětné volání **CallbackEndpoint** na adresu URL, která přijímá výsledky Ruční kontrola (prostřednictvím požadavku HTTP POST).

Nejprve přidejte následující kód, který **hlavní** metoda.

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
> Klíč obsahu moderátora služby má požadavky na druhý omezení četnosti (RPS). Pokud limit překročíte, sadu SDK vyvolá výjimku s kódem 429 chyby. 
>
> Úroveň free klíč může mít jeden RPS rychlost.

## <a name="run-the-program-and-review-the-output"></a>Spusťte program a prohlédněte si výstup

Zobrazí následující ukázkový výstup v konzole:

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Přihlaste se k obsahu moderátora Zkontrolujte nástroj, zobrazí se čeká na obrázek, zkontrolujte.

Použití **Další** tlačítko Odeslat.

![Zkontrolujte bitové kopie pro lidské moderátorů](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Ukázkový výstup do souboru protokolu

> [!NOTE]
> Ve výstupním souboru, řetězce **Teamname**, **ContentId**, **CallBackEndpoint**, a **WorkflowId** podle hodnoty, které jste použili dříve.

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


## <a name="your-callback-url-if-provided-receives-this-response"></a>Adresa Url zpětné volání, pokud k dispozici, obdrží této odpovědi.

Zobrazí odpověď jako v následujícím příkladu:

> [!NOTE]
> V odpovědi zpětného volání, řetězce **ContentId** a **WorkflowId** podle hodnoty, které jste použili předtím.

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

[Stáhněte si řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) v tomto a dalších – elementy QuickStart obsahu moderátora pro platformu .NET a začít na svoji integraci.
