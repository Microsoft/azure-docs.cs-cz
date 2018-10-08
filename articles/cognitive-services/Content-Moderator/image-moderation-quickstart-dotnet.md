---
title: 'Rychlý start: Moderování obrázků pomocí prostředí .NET – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Jak moderovat obrázky pomocí sady Content Moderator SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: sajagtap
ms.openlocfilehash: d89d9b8a2e3b00155e82cc28105007ab39fc549c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226160"
---
# <a name="quickstart-moderate-images-using-net"></a>Rychlý start: Moderování obrázků pomocí prostředí .NET

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou začít používat [sadu Content Moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) k následujícím účelům: 

- Kontrola, zda se v obrázku vyskytuje obsah pro dospělé
- Rozpoznání a extrakce textu z obrázku
- Rozpoznávání tváří v obrázku

Tento článek předpokládá, že už máte zkušenosti se sadou Visual Studio a jazykem C#.

## <a name="sign-up-for-content-moderator-services"></a>Registrace do služeb Content Moderatoru

Než začnete služby Content Moderatoru prostřednictvím rozhraní REST API nebo sady SDK používat, budete potřebovat klíč rozhraní API a oblast vašeho účtu pro rozhraní API.
Na stránce [Rychlý start](quick-start.md) se můžete registrovat do Content Moderatoru a obdržet klíč i oblast.

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio

1. Přidejte ke svému řešení nový projekt **Konzolová aplikace (.NET Framework)**.

   Ve vzorovém kódu pojmenujte tento projekt **ImageModeration**.

1. Projekt vyberte jako jeden spouštěný projekt řešení.


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

Přidáním následujícího kódu vytvořte pro své předplatné klienta Content Moderatoru.

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

### <a name="initialize-application-specific-settings"></a>Inicializace nastavení specifických pro aplikaci

Do třídy **Program** v souboru Program.cs přidejte následující statická pole.

    ///<summary>
    ///The name of the file that contains the image URLs to evaluate.
    ///</summary>
    ///<remarks>You will need to create an input file and update 
    ///this path accordingly. Paths are relative to the execution directory.
    ///</remarks>
    private static string ImageUrlFile = "ImageFiles.txt";

    ///<summary>
    ///The name of the file to contain the output from the evaluation.
    ///</summary>
    ///<remarks>Paths are relative to the execution directory.
    ///</remarks>
    private static string OutputFile = "ModerationOutput.json";


> [!NOTE]
> Ukázka k generování výstupu pro tento rychlý start používá následující obrázky.
> - https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
> - https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png

## <a name="store-the-analysis-results"></a>Uložení výsledků analýzy

Do třídy **Program** přidejte následující třídu. Pokud chcete zaznamenávat výsledky moderování kontrolovaných obrázků, použijte instanci této třídy.

    /// <summary>
    /// Contains the image moderation results for an image, 
    /// including text and face detection results.
    /// </summary>
    public class EvaluationData
    {
        /// <summary>
        /// The URL of the evaluated image.
        /// </summary>
        public string ImageUrl;

        /// <summary>
        /// The image moderation results.
        /// </summary>
        public Evaluate ImageModeration;

        /// <summary>
        /// The text detection results.
        /// </summary>
        public OCR TextDetection;

        /// <summary>
        /// The face detection results;
        /// </summary>
        public FoundFaces FaceDetection;
    }

## <a name="evaluate-an-individual-image"></a>Vyhodnocení jednotlivého obrázku

Do třídy **Program** přidejte následující metodu. Tato metoda ohodnotí jeden obrázek a vrátí výsledky hodnocení.

> [!NOTE]
> Klíč služby Content Moderatoru má limit četnosti požadavků za sekundu (RPS), a pokud ho překročíte, sada SDK zobrazí výjimku s chybovým kódem 429. 
>
> Klíč úrovně Free má limit nastavený na 1 RPS za sekundu.


    /// <summary>
    /// Evaluates an image using the Image Moderation APIs.
    /// </summary>
    /// <param name="client">The Content Moderator API wrapper to use.</param>
    /// <param name="imageUrl">The URL of the image to evaluate.</param>
    /// <returns>Aggregated image moderation results for the image.</returns>
    /// <remarks>This method throttles calls to the API.
    /// Your Content Moderator service key will have a requests per second (RPS)
    /// rate limit, and the SDK will throw an exception with a 429 error code 
    /// if you exceed that limit. A free tier key has a 1 RPS rate limit.
    /// </remarks>
    private static EvaluationData EvaluateImage(
    ContentModeratorClient client, string imageUrl)
    {
        var url = new ImageUrl("URL", imageUrl.Trim());

        var imageData = new EvaluationData();

        imageData.ImageUrl = url.Value;

        // Evaluate for adult and racy content.
        imageData.ImageModeration =
            client.ImageModeration.EvaluateUrlInput("application/json", url, true);
        Thread.Sleep(1000);

        // Detect and extract text.
        imageData.TextDetection =
            client.ImageModeration.OCRUrlInput("eng", "application/json", url, true);
        Thread.Sleep(1000);

        // Detect faces.
        imageData.FaceDetection =
            client.ImageModeration.FindFacesUrlInput("application/json", url, true);
        Thread.Sleep(1000);

        return imageData;
    }

Metoda **EvaluateUrlInput** tvoří obálku pro rozhraní REST API moderování obrázků.
Návratová hodnota obsahuje objekt vrácený z volání rozhraní API.

Metoda **OCRUrlInput** tvoří obálku pro rozhraní REST API OCR obrázků.
Návratová hodnota obsahuje objekt vrácený z volání rozhraní API.

Metoda **FindFacesUrlInput** tvoří obálku pro rozhraní REST API rozpoznávání tváří v obrázcích.
Návratová hodnota obsahuje objekt vrácený z volání rozhraní API.

## <a name="process-the-image-urls-in-your-code"></a>Zpracování adres URL obrázků v kódu

Do metody **Main** přidejte následující kód.

    // Create an object to store the image moderation results.
    List<EvaluationData> evaluationData = new List<EvaluationData>();

    // Create an instance of the Content Moderator API wrapper.
    using (var client = Clients.NewClient())
    {
        // Read image URLs from the input file and evaluate each one.
        using (StreamReader inputReader = new StreamReader(ImageUrlFile))
        {
            while (!inputReader.EndOfStream)
            {
                string line = inputReader.ReadLine().Trim();
                if (line != String.Empty)
                {
                    EvaluationData imageData = EvaluateImage(client, line);
                    evaluationData.Add(imageData);
                }
            }
        }
    }

    // Save the moderation results to a file.
    using (StreamWriter outputWriter = new StreamWriter(OutputFile, false))
    {
        outputWriter.WriteLine(JsonConvert.SerializeObject(
            evaluationData, Formatting.Indented));

        outputWriter.Flush();
        outputWriter.Close();
    }

## <a name="run-the-program-and-review-the-output"></a>Spuštění programu a kontrola výstupu

Následující objekt JSON obsahuje výstup programu.

> [!NOTE]
> `isImageAdultClassified` představuje potenciální přítomnost obrázků, které lze v určitých situacích považovat za sexuálně explicitní nebo pouze pro dospělé.
> `isImageRacyClassified` představuje potenciální přítomnost obrázků, které lze v určitých situacích považovat za sexuálně sugestivní nebo pouze pro dospělé.
>

    [
    {
    "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg",
    "ImageModeration": {
      "cacheID": "7733c303-3b95-4710-a41e-7a322ae81a15_636488005858745661",
      "result": false,
      "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_687c356d-0f00-4aeb-ae5f-c7555af80247",
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      "advancedInfo": [
        {
          "key": "ImageDownloadTimeInMs",
          "value": "116"
        },
        {
          "key": "ImageSizeInBytes",
          "value": "273405"
        }
      ],
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      }
    },
    "TextDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      "metadata": [
        {
          "key": "ImageDownloadTimeInMs",
          "value": "12"
        },
        {
          "key": "ImageSizeInBytes",
          "value": "273405"
        }
      ],
      "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_814fa162-c5d6-4ca6-997b-30ed0686ca83",
      "cacheId": "3fb69496-c64b-4de9-affd-6dd6d23f3e78_636488005876558920",
      "language": "eng",
      "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
      "candidates": []
    },
    "FaceDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_a2c40dbe-609d-4eb8-b01c-9988388804ea",
      "cacheId": "e4c0b500-ea8e-4a31-8fb3-35f98c4fbd65_636488005889528303",
      "result": false,
      "count": 0,
      "advancedInfo": [
        {
          "key": "ImageDownloadTimeInMs",
          "value": "11"
        },
        {
          "key": "ImageSizeInBytes",
          "value": "273405"
        }
      ],
      "faces": []
    }
    },
    {
    "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
    "ImageModeration": {
      "cacheID": "b4866aa2-5e69-44ed-806a-f9a5d618c8ae_636488005930693926",
      "result": false,
      "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_fdce5510-f689-4791-b081-c2ad54dcfe78",
      "adultClassificationScore": 0.0035635426174849272,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.021369094029068947,
      "isImageRacyClassified": false,
      "advancedInfo": [
        {
          "key": "ImageDownloadTimeInMs",
          "value": "109"
        },
        {
          "key": "ImageSizeInBytes",
          "value": "2278902"
        }
      ],
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      }
    },
    "TextDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      "metadata": [
        {
          "key": "ImageDownloadTimeInMs",
          "value": "46"
        },
        {
          "key": "ImageSizeInBytes",
          "value": "2278902"
        }
      ],
      "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_08a4bc19-6010-41bb-a440-a77278e167d8",
      "cacheId": "28b37471-41b3-4f79-bd23-965498bcff51_636488005950851288",
      "language": "eng",
      "text": "",
      "candidates": []
    },
    "FaceDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_40f2ce07-14ba-47cd-ba09-58b557a89854",
      "cacheId": "ec9c1be3-99b7-4bd9-8bc4-dc958c74459f_636488005964914299",
      "result": true,
      "count": 6,
      "advancedInfo": [
        {
          "key": "ImageDownloadTimeInMs",
          "value": "60"
        },
        {
          "key": "ImageSizeInBytes",
          "value": "2278902"
        }
      ],
      "faces": [
        {
          "bottom": 598,
          "left": 44,
          "right": 268,
          "top": 374
        },
        {
          "bottom": 620,
          "left": 308,
          "right": 532,
          "top": 396
        },
        {
          "bottom": 575,
          "left": 594,
          "right": 773,
          "top": 396
        },
        {
          "bottom": 563,
          "left": 812,
          "right": 955,
          "top": 420
        },
        {
          "bottom": 611,
          "left": 972,
          "right": 1151,
          "top": 432
        },
        {
          "bottom": 510,
          "left": 1232,
          "right": 1456,
          "top": 286
        }
      ]
    }
    }
    ]


## <a name="next-steps---get-the-source-code"></a>Další kroky – získat zdrojový kód

Získejte pro tento rychlý start a jiné rychlé starty Content Moderatoru pro .NET [sadu Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a [řešení Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) a začněte se svou integrací.
