---
title: Nahrání souborů do účtu Mediálních služeb pomocí rozhraní .NET | Dokumenty společnosti Microsoft
description: Naučte se, jak získat mediální obsah do mediálních služeb vytvořením a nahráním datových zdrojů.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 03b9995eab503ac1fcd4615882419dde31d4f8bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "64869442"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Odeslání souborů do účtu Azure Media Services pomocí .NET 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Ve službě Media Services můžete digitální soubory nahrát (nebo ingestovat) do prostředku. Entita **Asset** může obsahovat video, zvuk, obrázky, kolekce miniatur, textové stopy a soubory skrytých titulků (a metadata o těchto souborech).)  Jakmile jsou soubory nahrány, váš obsah je bezpečně uložen v cloudu pro další zpracování a streamování.

Soubory v prostředku se nazývají **soubory prostředku**. Instance **AssetFile** a skutečný mediální soubor jsou dva odlišné objekty. Instance AssetFile obsahuje metadata o mediálním souboru, zatímco mediální soubor obsahuje skutečný mediální obsah.

## <a name="considerations"></a>Požadavky

Platí následující důležité informace:
 
 * Služba Media Services používá hodnotu vlastnosti IAssetFile.Name při vytváření adres URL pro streamovaný obsah (například http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tohoto důvodu není povoleno kódování procentuální chod. Hodnota **Name** vlastnost nemůže mít žádné z následujících [procent-kódování vyhrazené znaky:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'();:@&=+$,/?%#[]". Také může existovat pouze jeden '.' pro příponu názvu souboru.
* Délka názvu by neměla být větší než 260 znaků.
* Maximální velikost souboru podporovaná při zpracování ve službě Media Services je omezená. Podrobnosti o omezení velikosti souboru najdete v [tomto článku](media-services-quotas-and-limitations.md).
* Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. Další informace naleznete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

Při vytváření datových zdrojů můžete zadat následující možnosti šifrování:

* **Žádné** – nepoužívá se žádné šifrování. Toto je výchozí hodnota. Při použití této možnosti není váš obsah chráněn při přenosu nebo v klidovém stavu.
  Pokud plánujete dodat MP4 pomocí postupného stahování, použijte tuto možnost: 
* **CommonEncryption** – Tuto možnost použijte, pokud nahráváte obsah, který již byl zašifrován a chráněn pomocí běžného šifrování nebo služby PlayReady DRM (například plynulé streamování chráněné pomocí služby PlayReady DRM).
* **EnvelopeEncrypted** – Tuto možnost použijte, pokud nahráváte HLS zašifrované pomocí AES. Pamatujte, že soubory musí být zakódované a zašifrované pomocí správce transformací.
* **StorageEncrypted** – zašifruje váš čistý obsah místně pomocí šifrování AES-256 bitů a pak ho nahraje do Služby Azure Storage, kde je uložen šifrovaný v klidovém stavu. Prostředky chráněné pomocí šifrování úložiště jsou před kódováním automaticky bez šifrování umístěny do systému souborů EFS a volitelně se znovu zašifrují před jejich odesláním zpět v podobě nového výstupního prostředku. Případem primárního použití šifrování úložiště je situace, kdy chcete zabezpečit soubory s vysoce kvalitními vstupními multimediálními soubory pomocí silného šifrování na disku.
  
    Služba Media Services poskytuje šifrování úložiště na disku pro vaše datové zdroje, nikoli pro přepojený systém, jako je Digital Rights Manager (DRM).
  
    Pokud váš asset používá šifrování úložiště, musíte nakonfigurovat zásady doručení assetu. Další informace naleznete [v tématu Konfigurace zásad doručování majetku](media-services-dotnet-configure-asset-delivery-policy.md).

Pokud zadáte, aby byl datový zdroj zašifrován pomocí **možnosti CommonEncrypted** nebo **EnvelopeEncrypted,** je třeba přidružit datový zdroj k **contentkey**. Další informace naleznete [v tématu Jak vytvořit contentkey](media-services-dotnet-create-contentkey.md). 

Pokud zadáte, aby byl datový zdroj zašifrován pomocí **možnosti StorageEncrypted,** sada SDK Media Services pro rozhraní .NET vytvoří pro váš datový zdroj **klíč** **Zašifrovaný úložištěm.**

Tento článek ukazuje, jak používat media services .NET SDK a také rozšíření Media Services .NET SDK k nahrání souborů do datového zdroje Media Services.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Nahrání jednoho souboru pomocí sady Media Services .NET SDK

Následující kód používá rozhraní .NET k nahrání jednoho souboru. AccessPolicy a Locator jsou vytvořeny a zničeny upload funkce. 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Nahrání více souborů pomocí sady Media Services .NET SDK
Následující kód ukazuje, jak vytvořit datový zdroj a nahrát více souborů.

Kód provádí následující akce:

* Vytvoří prázdný datový zdroj pomocí metody CreateEmptyAsset definované v předchozím kroku.
* Vytvoří instanci **AccessPolicy,** která definuje oprávnění a dobu trvání přístupu k prostředku.
* Vytvoří instanci **Locator,** která poskytuje přístup k datovému zdroji.
* Vytvoří instanci **Objektu blobTransferClient.** Tento typ představuje klienta, který pracuje na objektech BLOB Azure. V tomto příkladu klient sleduje průběh nahrávání. 
* Vytvoří úložiště souborů v zadaném adresáři a vytvoří instanci **AssetFile** pro každý soubor.
* Nahraje soubory do mediálních služeb pomocí metody **UploadAsync.** 

> [!NOTE]
> Pomocí metody UploadAsync můžete zajistit, že volání neblokují a že soubory jsou nahrávány paralelně.
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AssetFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


Při nahrávání velkého počtu datových zdrojů zvažte následující skutečnosti:

* Vytvořte nový objekt **CloudMediaContext** pro každé vlákno. Třída **CloudMediaContext** není bezpečná pro přístup z více vláken.
* Zvyšte NumberOfConcurrentTransfers z výchozí hodnoty 2 na vyšší hodnotu, jako je 5. Nastavení této vlastnosti ovlivní všechny instance **CloudMediaContext**. 
* Zachovat ParallelTransferThreadCount na výchozí hodnotu 10.

## <a name="ingesting-assets-in-bulk-using-media-services-net-sdk"></a><a id="ingest_in_bulk"></a>Hromadné ingestování datových zdrojů pomocí sady Media Services .NET SDK
Nahrávání velkých souborů datových zdrojů může být během vytváření datových zdrojů kritickým bodem. Ingestování prostředků ve velkém nebo "Hromadné ingestování" zahrnuje oddělení vytváření datových zdrojů od procesu nahrávání. Chcete-li použít přístup hromadného ingestování, vytvořte manifest (IngestManifest), který popisuje prostředek a jeho přidružené soubory. Potom pomocí metody nahrávání podle vašeho výběru nahrajte přidružené soubory do kontejneru objektů blob manifestu. Microsoft Azure Media Services sleduje kontejner objektů blob přidružené manifestu. Po nahrání souboru do kontejneru objektů blob dokončí Microsoft Azure Media Services vytvoření datového zdroje na základě konfigurace datového zdroje v manifestu (IngestManifestAsset).

Chcete-li vytvořit nový IngestManifest, volání Create metoda vystavena IngestManifests kolekce na CloudMediaContext. Tato metoda vytvoří nový IngestManifest s názvem manifestu, který zadáte.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Vytvořte datové zdroje, které jsou přidruženy k hromadnéMu IngestManifestu. Nakonfigurujte požadované možnosti šifrování na datovém zdroji pro hromadné ingestování.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

IngestManifestAsset přidruží asset s hromadné IngestManifest pro hromadné ingestování. Také přidruží AssetFiles, který tvoří každý Asset. Chcete-li vytvořit IngestManifestAsset, použijte Create metoda v kontextu serveru.

Následující příklad ukazuje přidání dvou nových IngestManifestAssets, které přidružují dva prostředky dříve vytvořené k manifestu hromadného ingestování. Každý IngestManifestAsset také přidruží sadu souborů, které jsou odeslány pro každý datový zdroj během hromadného ingestování.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Můžete použít libovolnou vysokorychlostní klientskou aplikaci schopnou nahrát soubory datových zdrojů do identifikátoru URI kontejneru úložiště objektů blob, který poskytuje vlastnost **IIngestManifest.BlobStorageUriForUpload** ingestmanifestu. 

Následující kód ukazuje, jak pomocí sady .NET SDK nahrát soubory datových zdrojů.

```csharp
    static void UploadBlobFile(string containerName, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

Kód pro nahrávání souborů datových zdrojů pro ukázku použitou v tomto článku je uveden v následujícím příkladu kódu:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Můžete určit průběh hromadného ingestování pro všechny prostředky přidružené k **IngestManifest** dotazováním Statistics vlastnost **IngestManifest**. Chcete-li aktualizovat informace o průběhu, musíte použít nový **CloudMediaContext** pokaždé, když dotazování Statistics vlastnost.

Následující příklad ukazuje dotazování IngestManifest jeho **ID**.

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>Nahrávání souborů pomocí rozšíření .NET SDK Extensions
Následující příklad ukazuje, jak nahrát jeden soubor pomocí přípon .NET SDK. V tomto případě **createFromFile** metoda se používá, ale asynchronní verze je také k dispozici (**CreateFromFileAsync**). Metoda **CreateFromFile** umožňuje zadat název souboru, možnost šifrování a zpětné volání, aby bylo možné hlásit průběh nahrávání souboru.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

Následující příklad volá funkci UploadFile a určuje šifrování úložiště jako možnost vytvoření datového zdroje.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>Další kroky

Nyní můžete kódovat nahrané assety. Další informace najdete v tématu [Kódování assetů](media-services-portal-encode.md).

Můžete také použít službu Azure Functions k aktivaci úlohy kódování při příchodu souboru do nakonfigurovaného kontejneru. Další informace najdete v [této ukázce](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Další krok
Nyní, když jste nahráli datový zdroj do služby Media Services, přejděte k článku [Jak získat mediální procesor.][How to Get a Media Processor]

[How to Get a Media Processor]: media-services-get-media-processor.md

