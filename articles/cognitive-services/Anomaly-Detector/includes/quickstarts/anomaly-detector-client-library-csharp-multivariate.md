---
title: Rychlý Start klientské knihovny lineární detektoru anomálií .NET
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: a657b8f5bf967131a0168dbea5bb1db86b3b559e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800362"
---
Začněte s klientskou knihovnou anomálie lineární pro .NET. Pomocí těchto kroků balíček nainstalujte a začněte používat algoritmy poskytované službou. Nová rozhraní API pro detekci anomálií lineární umožňují vývojářům snadnou integraci pokročilého AI pro detekci anomálií ze skupin metrik, aniž by to mělo znalosti strojového učení nebo data s popisky. Závislosti a vzájemné korelace mezi různými signály se automaticky počítají jako klíčové faktory. To vám pomůže aktivně chránit komplexní systémy před chybami.

Pomocí klientské knihovny lineární detektoru anomálií pro .NET:

* Zjišťuje anomálie na úrovni systému ze skupiny časových řad.
* Když kterákoli z jednotlivých časových řad neřekne hodně a musíte se podívat na všechny signály a zjistit problém.
* Predicative údržbu drahých fyzických prostředků s desítkou až stovkami různých typů senzorů, které měří různé aspekty stavu systému.

[Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/anomalydetector/Azure.AI.AnomalyDetector)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.3)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" vytvořte prostředek pro detekci anomálií "  target="_blank"> vytvořením prostředku detektoru anomálií </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Počkejte na nasazení a vyberte tlačítko **Přejít k prostředku** .
    * K připojení aplikace k rozhraní API detektoru anomálií budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Vložte klíč a koncový bod do níže uvedeného kódu později v rychlém startu.
    K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-net-core-application"></a>Vytvoření nové aplikace .NET Core

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `anomaly-detector-quickstart-multivariate` . Tento příkaz vytvoří jednoduchý projekt "Hello World" s jedním zdrojovým souborem C#: *program. cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart-multivariate
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```dotnetcli
dotnet build
```

Výstup sestavení by neměl obsahovat žádná upozornění ani chyby.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte knihovnu klienta pro rozpoznávání anomálií pro .NET pomocí následujícího příkazu:

```dotnetcli
dotnet add package Azure.AI.AnomalyDetector --version 3.0.0-preview.3
```

V adresáři projektu otevřete soubor *program. cs* a přidejte následující příkaz `directives` :

```csharp
using System;
using System.Collections.Generic;
using System.Drawing.Text;
using System.IO;
using System.Linq;
using System.Linq.Expressions;
using System.Net.NetworkInformation;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Azure.AI.AnomalyDetector.Models;
using Azure.Core.TestFramework;
using Microsoft.Identity.Client;
using NUnit.Framework;
```

V `main()` metodě aplikace vytvořte proměnné pro koncový bod prostředku Azure, klíč rozhraní API a vlastní zdroj dat.

```csharp
string endpoint = "YOUR_API_KEY";
string apiKey =  "YOUR_ENDPOINT";
string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Aby bylo možné používat rozhraní API pro detekci anomálií lineární, musíme před použitím detekce vyškolit náš vlastní model. Data použitá pro školení jsou dávkou časových řad, přičemž každá časová řada by měla být ve formátu CSV se dvěma sloupci, časovým razítkem a hodnotou. Všechny časové řady by měly být v jednom souboru zip a nahrány do [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs). Ve výchozím nastavení se název souboru použije k reprezentaci proměnné pro časovou řadu. Alternativně je možné do souboru ZIP zahrnout další meta.js, pokud chcete, aby název proměnné byl jiný než název souboru. zip. Jakmile vygenerujeme [adresu URL objektu BLOB SAS (Shared Access signaturs)](../../../../storage/common/storage-sas-overview.md), můžeme pro školení použít adresu URL souboru ZIP.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny anomálií lineární pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Trénování modelu](#train-the-model)
* [Detekovat anomálie](#detect-anomalies)
* [Exportovat model](#export-model)
* [Odstranit model](#delete-model)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci klienta detektoru anomálií s vaším koncovým bodem a klíčem.

```csharp
var endpointUri = new Uri(endpoint);
var credential = new AzureKeyCredential(apiKey)

AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);
```

## <a name="train-the-model"></a>Trénování modelu

Vytvořte novou soukromou asynchronní úlohu, která bude sloužit k obsluze vašeho modelu. Použijete `TrainMultivariateModel` k výuce modelu a kontrole, `GetMultivariateModelAysnc` jestli je školení dokončené.

```csharp
private async Task trainAsync(AnomalyDetectorClient client, string datasource, DateTimeOffset start_time, DateTimeOffset end_time, int max_tryout = 500)
{
    try
    {
        Console.WriteLine("Training new model...");

        int model_number = await getModelNumberAsync(client, false).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models before training.", model_number));

        ModelInfo data_feed = new ModelInfo(datasource, start_time, end_time);
        Response response_header = client.TrainMultivariateModel(data_feed);
        response_header.Headers.TryGetValue("Location", out string trained_model_id_path);
        Guid trained_model_id = Guid.Parse(trained_model_id_path.Split('/').LastOrDefault());
        Console.WriteLine(trained_model_id);

        // Wait until the model is ready. It usually takes several minutes
        Response<Model> get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
        ModelStatus? model_status = null;
        int tryout_count = 0;
        TimeSpan create_limit = new TimeSpan(0, 3, 0);
        while (tryout_count < max_tryout & model_status != ModelStatus.Ready)
        {
            System.Threading.Thread.Sleep(10000);
            get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
            ModelInfo model_info = get_response.Value.ModelInfo;
            Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}, status: {3}.", get_response.Value.ModelId, get_response.Value.CreatedTime, get_response.Value.LastUpdatedTime, model_info.Status));

            if (model_info != null)
            {
                model_status = model_info.Status;
            }
            tryout_count += 1;
        };
        get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);

        if (model_status != ModelStatus.Ready)
        {
            Console.WriteLine(String.Format("Request timeout after {0} tryouts", max_tryout));
        }

        model_number = await getModelNumberAsync(client).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models after training.", model_number));
        return trained_model_id;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Train error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="detect-anomalies"></a>Detekovat anomálie

K detekci anomálií pomocí nově vyučeného modelu vytvořte `private async Task` pojmenované `detectAsync` . Vytvoříte nový `DetectionRequest` a předáte ho jako parametr do `DetectAnomalyAsync` .

```csharp
private async Task<DetectionResult> detectAsync(AnomalyDetectorClient client, string datasource, Guid model_id, DateTimeOffset start_time, DateTimeOffset end_time, int max_tryout = 500)
{
    try
    {
        Console.WriteLine("Start detect...");
        Response<Model> get_response = await client.GetMultivariateModelAsync(model_id).ConfigureAwait(false);

        DetectionRequest detectionRequest = new DetectionRequest(datasource, start_time, end_time);
        Response result_response = await client.DetectAnomalyAsync(model_id, detectionRequest).ConfigureAwait(false);
        var ok = result_response.Headers.TryGetValue("Location", out string result_id_path);
        Guid result_id = Guid.Parse(result_id_path.Split('/').LastOrDefault());
        // get detection result
        Response<DetectionResult> result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
        int tryout_count = 0;
        while (result.Value.Summary.Status != DetectionStatus.Ready & tryout_count < max_tryout)
        {
            System.Threading.Thread.Sleep(2000);
            result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
            tryout_count += 1;
        }

        if (result.Value.Summary.Status != DetectionStatus.Ready)
        {
            Console.WriteLine(String.Format("Request timeout after {0} tryouts", max_tryout));
            return null;
        }

        return result.Value;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Detection error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="export-model"></a>Exportovat model

Chcete-li exportovat model, který jste již dříve promluvili, vytvořte `private async Task` pojmenované `exportAysnc` . Použijete `ExportModelAsync` a předáte ID modelu modelu, který chcete exportovat.

```csharp
private async Task exportAsync(AnomalyDetectorClient client, Guid model_id, string model_path = "model.zip")
{
    try
    {
        Stream model = await client.ExportModelAsync(model_id).ConfigureAwait(false);
        if (model != null)
        {
            var fileStream = File.Create(model_path);
            model.Seek(0, SeekOrigin.Begin);
            model.CopyTo(fileStream);
            fileStream.Close();
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Export error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="delete-model"></a>Odstranit model

Chcete-li odstranit model, který jste vytvořili dříve, `DeleteMultivariateModelAsync` a předejte ID modelu modelu, který chcete odstranit. Pokud chcete načíst ID modelu, můžete nás `getModelNumberAsync` :

```csharp
private async Task deleteAsync(AnomalyDetectorClient client, Guid model_id)
{
    await client.DeleteMultivariateModelAsync(model_id).ConfigureAwait(false);
    int model_number = await getModelNumberAsync(client).ConfigureAwait(false);
    Console.WriteLine(String.Format("{0} available models after deletion.", model_number));
}
private async Task<int> getModelNumberAsync(AnomalyDetectorClient client, bool delete = false)
{
    int count = 0;
    AsyncPageable<ModelSnapshot> model_list = client.ListMultivariateModelAsync(0, 10000);
    await foreach (ModelSnapshot x in model_list)
    {
        count += 1;
        Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}.", x.ModelId, x.CreatedTime, x.LastUpdatedTime));
        if (delete & count < 4)
        {
            await client.DeleteMultivariateModelAsync(x.ModelId).ConfigureAwait(false);
        }
    }
    return count;
}
```

## <a name="main-method"></a>Metoda Main

Teď, když máte všechny součásti komponenty, musíte do hlavní metody přidat další kód, který bude volat nově vytvořené úkoly.

```csharp

{
    //read endpoint and apiKey
     string endpoint = "YOUR_API_KEY";
    string apiKey =  "YOUR_ENDPOINT";
    string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
    Console.WriteLine(endpoint);
    var endpointUri = new Uri(endpoint);
    var credential = new AzureKeyCredential(apiKey);

    //create client
    AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);

    // train
    TimeSpan offset = new TimeSpan(0);
    DateTimeOffset start_time = new DateTimeOffset(2021, 1, 1, 0, 0, 0, offset);
    DateTimeOffset end_time = new DateTimeOffset(2021, 1, 2, 12, 0, 0, offset);
    Guid? model_id_raw = null;
    try
    {
        model_id_raw = await trainAsync(client, datasource, start_time, end_time).ConfigureAwait(false);
        Console.WriteLine(model_id_raw);
        Guid model_id = model_id_raw.GetValueOrDefault();

        // detect
        start_time = end_time;
        end_time = new DateTimeOffset(2021, 1, 3, 0, 0, 0, offset);
        DetectionResult result = await detectAsync(client, datasource, model_id, start_time, end_time).ConfigureAwait(false);
        if (result != null)
        {
            Console.WriteLine(String.Format("Result ID: {0}", result.ResultId));
            Console.WriteLine(String.Format("Result summary: {0}", result.Summary));
            Console.WriteLine(String.Format("Result length: {0}", result.Results.Count));
        }

        // export model
        await exportAsync(client, model_id).ConfigureAwait(false);

        // delete
        await deleteAsync(client, model_id).ConfigureAwait(false);
    }
    catch (Exception e)
    {
        String msg = String.Format("Multivariate error. {0}", e.Message);
        if (model_id_raw != null)
        {
            await deleteAsync(client, model_id_raw.GetValueOrDefault()).ConfigureAwait(false);
        }
        Console.WriteLine(msg);
        throw new Exception(msg);
    }
}

```

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `dotnet run` příkazu z adresáře aplikace.

```dotnetcli
dotnet run
```

## <a name="next-steps"></a>Další kroky

* [Osvědčené postupy pro detekci anomálií lineární](../../concepts/best-practices-multivariate.md)
