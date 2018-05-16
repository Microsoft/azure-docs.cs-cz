---
title: Nahrávání, kódování a streamování pomocí služby Azure Media Services | Microsoft Docs
description: Tento kurz popisuje, jak nahrát soubor, zakódovat video a streamovat obsah v Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: 1f0ce5599cce7fc830075e57af1bcba80d0e69e7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-upload-encode-and-stream-videos-using-apis"></a>Kurz: Nahrávání, kódování a streamování videí pomocí rozhraní API

V tomto kurzu se dozvíte, jak nahrávat, kódovat a streamovat videosoubory pomocí služby Azure Media Services. Svůj obsah můžete streamovat i ve formátech Apple, jako je HLS, MPEG DASH nebo CMAF, takže půjde přehrávat v nejrůznějších přehrávačích a zařízeních. Video je před streamováním potřeba odpovídajícím způsobem zašifrovat a zabalit.

Tento kurz vás provede kroky nahrání videa, ale můžete také zakódovat obsah, který přes adresu URL protokolu HTTPS zpřístupníte ve svém účtu Media Services.

![Přehrávání videa](./media/stream-files-tutorial-with-api/final-video.png)

V tomto kurzu získáte informace o následujících postupech:    

> [!div class="checklist"]
> * Spuštění služby Azure Cloud Shell
> * Vytvoření účtu Media Services
> * Přístup k rozhraní API služby Media Services
> * Konfigurace ukázkové aplikace
> * Podrobné prozkoumání kódu
> * Spuštění aplikace
> * Testování adresy URL pro streamování
> * Vyčištění prostředků

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pokud nemáte nainstalovanou sadu Visual Studio, můžete získat sadu [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Stažení ukázky

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s ukázkou streamování .NET:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code"></a>Kontrola kódu

Tato část popisuje funkce definované v souboru [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) projektu *UploadEncodeAndStreamFiles*.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Pokud chcete začít používat rozhraní API služby Media Services se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu je potřeba zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. Nejdřív musíte získat token a potom z vráceného tokenu vytvořit objekt **ClientCredential**. K získání tokenu slouží objekt **ArmClientCredential** v kódu, který jste naklonovali na začátku článku.  

```csharp
private static IAzureMediaServicesClient CreateMediaServicesClient(ConfigWrapper config)
{
    ArmClientCredentials credentials = new ArmClientCredentials(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Vytvoření vstupního prostředku a nahrání místního souboru do tohoto prostředku 

Funkce **CreateInputAsset** vytvoří nový vstupní prostředek a nahraje do něj zadaný místní videosoubor. Tento prostředek se použije jako vstup ve vaší úloze kódování. Ve službě Media Services v3 můžete jako vstup úlohy použít buď prostředek, nebo obsah, který jste přes adresu URL protokolu HTTPS zpřístupnili ve svém účtu služby Media Services. Postup zakódování obsahu přes adresu URL protokolu HTTPS najdete v [tomto](job-input-from-http-how-to.md) článku.  

Ve službě Media Services v3 slouží k nahrání souborů rozhraní API služby Azure Storage. Následující fragment kódu .NET vám ukáže, jak na to.

Uvedená funkce provede následující akce:

* Vytvoří prostředek. 
* Získá [adresu SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) [kontejneru v úložišti](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) prostředku s možností zápisu.
* Přes adresu SAS odešle soubor do kontejneru v úložišti.

```csharp
private static Asset CreateInputAsset(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName, string fileToUpload)
{
    // Check if an Asset already exists.
    Asset asset = client.Assets.Get(resourceGroupName, accountName, assetName);

    if (asset == null)
    {
        asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

        var response = client.Assets.ListContainerSas(
                resourceGroupName,
                accountName,
                assetName,
                permissions: AssetContainerPermission.ReadWrite,
                expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime()
            );

        var sasUri = new Uri(response.AssetContainerSasUrls.First());
        CloudBlobContainer container = new CloudBlobContainer(sasUri);
        var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
        blob.UploadFromFile(fileToUpload);
    }

    // In this sample method, we are going to assume that if an Asset already exists with the desired name, 
    // then we can go ahead an use it for encoding or analyzing.

    return asset;
}
```

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Vytvoření výstupního prostředku k uložení výsledku úlohy 

Výstupní prostředek uloží výsledek vaší úlohy kódování. Projekt definuje funkci **DownloadResults**, která stáhne výsledky z tohoto výstupního prostředku do výstupní složky, kde si je můžete zkontrolovat.

```csharp
private static Asset CreateOutputAsset(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName)
{
    // Check if an Asset already exists
    Asset outputAsset = client.Assets.Get(resourceGroupName, accountName, assetName);
    Asset asset = new Asset();
    string outputAssetName = assetName;

    if (outputAsset != null)
    {
        // Name collision! In order to get the sample to work, let's just go ahead and create a unique asset name
        // Note that the returned Asset can have a different name than the one specified as an input parameter.
        // You may want to update this part to throw an Exception instead, and handle name collisions differently.
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        outputAssetName += uniqueness;
    }

    return client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, asset);
}
```

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Vytvoření transformace a úlohy, která nahraný soubor zakóduje
Když kódujete nebo zpracováváte obsah v Media Services, kódování se obvykle nastaví jako předpis. Potom stačí odeslat **Úlohu**, která tento předpis použije pro video. Když odešlete novou Úlohu pro každé nové video, použije se tento předpis pro všechna videa ve vaší knihovně. V Media Services se pro předpis používá označení **transformace**. Další informace najdete v tématu [Transformace a úlohy](transform-concept.md). Ukázka popsaná v tomto kurzu definuje předpis, který zakóduje video tak, aby se dalo streamovat na nejrůznějších zařízeních s iOSem a Androidem. 

#### <a name="transform"></a>Transformace

Když vytváříte novou instanci **Transformace**, musíte určit, co má být jejím výstupem. Objekt **TransformOutput** v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu **TransformOutput**. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Tato předvolba zakóduje vstupní video na základě vstupního rozlišení a přenosové rychlosti do automaticky generované dvojice přenosová rychlost / rozlišení (tzv. bitrate ladder) a vytvoří soubory ISO MP4 s videem H.264 a zvukem AAC odpovídající jednotlivým dvojicím přenosová rychlost / rozlišení. Informace o této předvolbě najdete v tématu o [automatickém generování dvojic bitrate ladder](autogen-bitrate-ladder.md).

Můžete použít další předdefinovanou předvolbu EncoderNamedPreset, nebo si vytvořit vlastní. 

Než začnete vytvářet **transformaci**, ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje (viz kód níže).  Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

```csharp
private static Transform EnsureTransformExists(IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Start by defining the desired outputs.
        TransformOutput[] outputs = new TransformOutput[]
        {
            new TransformOutput
            {
                Preset = new BuiltInStandardEncoderPreset()
                {
                    PresetName = EncoderNamedPreset.AdaptiveStreaming
                }
            }
        };

        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs);
    }

    return transform;
}
```

#### <a name="job"></a>Úloha

Jak je uvedeno výše, objekt **Transformace** je předpis a **Úloha** je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup.

V tomto příkladu je vstupní video nahrané z místního počítače. Postup zakódování obsahu přes adresu URL protokolu HTTPS najdete v [tomto](job-input-from-http-how-to.md) článku.

```csharp
private static Job SubmitJob(IAzureMediaServicesClient client, 
    string resourceGroupName, 
    string accountName, 
    string transformName, 
    string jobName, 
    JobInput jobInput, 
    string outputAssetName)
{
    string uniqueJobName = jobName;
    Job job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

    if (job != null)
    {
        // Job already exists with the same name, so let's append a GUID
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        uniqueJobName += uniqueness;
    }

    JobOutput[] jobOutputs =
    {
        new JobOutputAsset(outputAssetName),
    };

    job = client.Jobs.Create(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job
        {
            Input = jobInput,
            Outputs = jobOutputs,
        });

    return job;
}
```

### <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Následující ukázka kódu je příkladem toho, jak se ve službě dotazovat na stav úlohy. Dotazování nepatří mezi doporučené postupy, jak sestavovat aplikace, protože může mít prodlevu. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid.

Služba Event Grid je navržená pro vysokou dostupnost, konzistentní výkon a dynamické škálování. Díky službě Event Grid můžou vaše aplikace naslouchat událostem a reagovat na ně, ať už pocházejí z kterékoli služby Azure. Události můžou pocházet i z vlastních zdrojů. Jednoduché, reaktivní zpracování událostí založené na protokolu HTTP pomáhá sestavovat efektivní řešení prostřednictvím inteligentního filtrování a směrování událostí.  Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](job-state-events-cli-how-to.md).

**Úloha** obvykle prochází následujícími stavy: **Naplánováno**, **Ve frontě**, **Zpracovávání** a **Dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Když úlohu zrušíte, změní se její stav na **Rušení** a potom na **Zrušeno**.

```csharp
private static Job WaitForJobToFinish(IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName)
{
    int SleepInterval = 60 * 1000;

    Job job = null;

    while (true)
    {
        job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

        if (job.State == JobState.Finished || job.State == JobState.Error || job.State == JobState.Canceled)
        {
            break;
        }

        Console.WriteLine($"Job is {job.State}.");
        for (int i = 0; i < job.Outputs.Count; i++)
        {
            JobOutput output = job.Outputs[i];
            Console.Write($"\tJobOutput[{i}] is {output.State}.");
            if (output.State == JobState.Processing)
            {
                Console.Write($"  Progress: {output.Progress}");
            }
            Console.WriteLine();
        }
        System.Threading.Thread.Sleep(SleepInterval);
    }

    return job;
}
```

### <a name="get-a-streaminglocator"></a>Vytvoření streamovacího lokátoru

Po dokončení kódování následuje zpřístupnění videa ve výstupním prostředku, kde je k dispozici klientům pro přehrávání. Video můžete zpřístupnit ve dvou krocích: nejdřív vytvořte streamovací lokátor (**StreamingLocator**) a pak adresy URL pro streamování, které budou klienti používat. 

Proces vytváření **streamovacího lokátoru** označujeme jako publikování. Pokud nenakonfigurujete volitelný počáteční a koncový čas, je **streamovací lokátor** ve výchozím nastavení platný hned po zavolání rozhraní API a jeho platnost zrušíte až jeho odstraněním. 

Když vytváříte **streamovací lokátor**, je potřeba zadat požadovaný název zásad streamování (**StreamingPolicyName**). V tomto příkladu budete streamovat čistý nebo také nešifrovaný obsah, takže bude možné použít předdefinovanou zásadu čistého streamování **PredefinedStreamingPolicy.ClearStreamingOnly**.

> [!IMPORTANT]
> Pokud chcete definovat vlastní zásady streamování, doporučujeme navrhnout pro účet služby Media Service omezený počet takovýchto zásad a používat je opakovaně pro streamovací lokátory, kdykoli potřebujete stejné protokoly a možnosti šifrování. Počet záznamů StreamingPolicy je pro účty služby Media Service omezený kvótou. Neměli byste vytvářet samostatnou zásadu streamování pro každý streamovací lokátor.

Následující kód předpokládá, že funkci voláte s jedinečným názvem lokátoru.

```csharp
private static StreamingLocator CreateStreamingLocator(IAzureMediaServicesClient client,
                                                        string resourceGroup,
                                                        string accountName,
                                                        string assetName,
                                                        string locatorName)
{
    StreamingLocator locator =
        client.StreamingLocators.Create(resourceGroup,
        accountName,
        locatorName,
        new StreamingLocator()
        {
            AssetName = assetName,
            StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        });

    return locator;
}
```

I když se ukázka v tomto tématu vztahuje na streamování, stejné volání můžete použít i k vytvoření streamovacího lokátoru pro doručování videoobsahu formou progresivního stahování.

### <a name="get-streaming-urls"></a>Vytvoření adres URL pro streamování

Teď, když máte vytvořený streamovací lokátor, můžete vytvořit adresy URL pro streamování, jak to znázorňuje metoda **GetStreamingURLs** v níže uvedeném příkladu. Pokud chcete vytvořit adresu URL, musíte zřetězit název hostitele **koncového bodu hostování** a cestu **streamovacího lokátoru**. V této ukázce je použit *výchozí* **koncový bod streamování**. Když poprvé vytvoříte účet Media Service, tento *výchozí* **koncový bod streamování** bude v zastaveném stavu, proto je potřeba zavolat **spuštění**.

> [!NOTE]
> V této metodě budete potřebovat název lokátoru, který jste použili při vytváření **streamovacího lokátoru** pro výstupní prostředek.

```csharp
static IList<string> GetStreamingURLs(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    String locatorName)
{
    IList<string> streamingURLs = new List<string>();

    string streamingUrlPrefx = "";

    StreamingEndpoint streamingEndpoint = client.StreamingEndpoints.Get(resourceGroupName, accountName, "default");

    if (streamingEndpoint != null)
    {
        streamingUrlPrefx = streamingEndpoint.HostName;

        if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
            client.StreamingEndpoints.Start(resourceGroupName, accountName, "default");
    }

    foreach (var path in client.StreamingLocators.ListPaths(resourceGroupName, accountName, locatorName).StreamingPaths)
    {
        streamingURLs.Add("http://" + streamingUrlPrefx + path.Paths[0].ToString());
    }

    return streamingURLs;
}
```

### <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně platí, že byste měli vyčistit všechno kromě objektů, které máte v plánu použít znovu, (obvykle jsou to transformace, streamovací lokátory apod.). Pokud chcete účet po experimentování vyčistit, měli byste odstranit prostředky, které nemáte v plánu znovu použít.  Následující kód například odstraní Úlohy.

```csharp
static void CleanUp(IAzureMediaServicesClient client,
        string resourceGroupName,
        string accountName,
        string transformName)
{
    foreach (var job in client.Jobs.List(resourceGroupName, accountName, transformName))
    {
        client.Jobs.Delete(resourceGroupName, accountName, transformName, job.Name);
    }

    foreach (var asset in client.Assets.List(resourceGroupName, accountName))
    {
        client.Assets.Delete(resourceGroupName, accountName, asset.Name);
    }
}
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

1. Stisknutím kombinace kláves Ctrl+F5 spusťte aplikaci *EncodeAndStreamFiles*.
2. Zkopírujte jednu z adresy URL pro streamování z konzoly.

Tento příklad uvádí adresy URL, které můžete použít k přehrávání videa pomocí různých protokolů:

![Výstup](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testování adresy URL pro streamování

Tento článek používá k otestování streamu přehrávač Azure Media Player. 

> [!NOTE]
> Pokud se přehrávač hostuje na webu HTTPS, nezapomeňte adresu URL aktualizovat tak, aby obsahovala „https“. 

1. Otevřete webový prohlížeč a přejděte na adresu [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Do pole **URL:** vložte jednu z hodnot adres URL pro streamování, které jste získali při spuštění aplikace. 
3. Stiskněte **Update Player** (Aktualizovat přehrávač).

Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte. Můžete použít nástroj **CloudShell**.

V nástroji **CloudShell** spusťte následující příkaz:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Sady SDK služby Azure Media Services v3 nejsou bezpečné pro přístup z více vláken. Při vývoji vícevláknové aplikace byste měli pro každé vlákno vygenerovat a používat samostatný objekt AzureMediaServicesClient.

## <a name="next-steps"></a>Další kroky

Teď, když už víte, jak nahrávat, kódovat a streamovat videa, podívejte se na následující článek: 

> [!div class="nextstepaction"]
> [Analýza videí](analyze-videos-tutorial-with-api.md)
