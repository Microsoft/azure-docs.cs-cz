---
title: Nahrání souborů do Media Services účtu pomocí .NET | Microsoft Docs
description: Naučte se, jak získat mediální obsah do Media Services vytvořením a nahráním prostředků pomocí .NET.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 83e9b0278e99867cafa7e633bc382e490ec273c1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250520"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Odeslání souborů do účtu Azure Media Services pomocí .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-from-v2-to-v3.md) .

Ve službě Media Services můžete digitální soubory nahrát (nebo ingestovat) do prostředku. Entita **assetu** může obsahovat video, zvuk, obrázky, kolekce miniatur, textové stopy a soubory titulků (a metadata o těchto souborech.)  Po nahrání souborů je váš obsah bezpečně uložen v cloudu pro další zpracování a streamování.

Soubory v prostředku se nazývají **soubory prostředku**. Instance **AssetFile** a skutečný mediální soubor jsou dva odlišné objekty. Instance AssetFile obsahuje metadata o souboru média, zatímco soubor média obsahuje skutečný mediální obsah.

## <a name="considerations"></a>Požadavky

Platí následující důležité informace:
 
 * Media Services používá hodnotu vlastnosti IAssetFile.Name při vytváření adres URL pro obsah streamování (například http://{AMSAccount}. Origin. MediaServices. Windows. NET/{GUID}/{IAssetFile. Name}/streamingParameters.) Z tohoto důvodu není povolena procentuální kódování. Hodnota vlastnosti **Name** nemůže obsahovat žádný z následujících [znaků rezervovaných v procentech](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * ' ();: @ &= + $,/?% # [] ". Přípona názvu souboru může mít taky jenom jeden znak.
* Délka názvu by neměla být větší než 260 znaků.
* Maximální velikost souboru podporovaná při zpracování ve službě Media Services je omezená. Podrobnosti o omezení velikosti souboru najdete v [tomto článku](media-services-quotas-and-limitations.md).
* Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

Při vytváření assetů můžete zadat následující možnosti šifrování:

* **Žádné** – nepoužívá se žádné šifrování. Toto je výchozí hodnota. Když použijete tuto možnost, váš obsah se nechrání během přenosu nebo v klidovém úložišti.
  Pokud máte v úmyslu doručovat MP4 pomocí postupného stahování, použijte tuto možnost: 
* **CommonEncryption** – tuto možnost použijte, pokud nahráváte obsah, který už je zašifrovaný a chráněný pomocí Common Encryption nebo PlayReady DRM (například Smooth Streaming chráněná pomocí technologie PlayReady DRM).
* **EnvelopeEncrypted** – tuto možnost použijte, pokud odesíláte HLS ŠIFROVANÉ pomocí AES. Pamatujte, že soubory musí být zakódované a zašifrované pomocí správce transformací.
* **StorageEncrypted** – šifruje obsah místně pomocí šifrování AES-256 a pak ho nahraje, aby Azure Storage tam, kde je uložený zašifrovaný v klidovém stavu. Prostředky chráněné pomocí šifrování úložiště jsou před kódováním automaticky bez šifrování umístěny do systému souborů EFS a volitelně se znovu zašifrují před jejich odesláním zpět v podobě nového výstupního prostředku. Případem primárního použití šifrování úložiště je situace, kdy chcete zabezpečit soubory s vysoce kvalitními vstupními multimediálními soubory pomocí silného šifrování na disku.
  
    Media Services poskytuje šifrování úložného prostoru na disku pro vaše prostředky, ne pro přenosnou službu jako DRM (Digital Rights Manager).
  
    Pokud váš asset používá šifrování úložiště, musíte nakonfigurovat zásady doručení assetu. Další informace najdete v tématu [Konfigurace zásad doručení assetu](media-services-dotnet-configure-asset-delivery-policy.md).

Pokud zadáte, aby se váš Asset zašifroval pomocí možnosti **CommonEncrypted** , nebo pomocí možnosti **EnvelopeEncrypted** , je potřeba přidružit svůj Asset k **ContentKey**. Další informace najdete v tématu [Vytvoření ContentKey](media-services-dotnet-create-contentkey.md). 

Pokud zadáte Media Services, aby se Asset zašifroval pomocí možnosti **StorageEncrypted** , sada SDK pro .NET vytvoří pro svůj Asset **StorageEncrypted** **ContentKey** .

Tento článek ukazuje, jak použít sadu Media Services .NET SDK a také Media Services rozšíření .NET SDK k nahrání souborů do Media Services assetu.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Nahrání jednoho souboru s Media Services .NET SDK

Následující kód používá rozhraní .NET k nahrání jednoho souboru. AccessPolicy a Lokátor jsou vytvářeny a zničeny funkcí upload. 

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


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Nahrání více souborů s Media Services .NET SDK
Následující kód ukazuje, jak vytvořit Asset a nahrát více souborů.

Kód provede následující:

* Vytvoří prázdný Asset pomocí metody CreateEmptyAsset definované v předchozím kroku.
* Vytvoří instanci **AccessPolicy** , která definuje oprávnění a dobu trvání přístupu k assetu.
* Vytvoří instanci **lokátoru** , která poskytuje přístup k assetu.
* Vytvoří instanci **BlobTransferClient** . Tento typ představuje klienta, který pracuje na objektech blob Azure. V tomto příkladu klient monitoruje průběh nahrávání. 
* Provede výčet souborů v zadaném adresáři a vytvoří instanci **AssetFile** pro každý soubor.
* Nahraje soubory do Media Services pomocí metody **UploadAsync** . 

> [!NOTE]
> Použijte metodu UploadAsync, abyste zajistili, že volání nejsou blokována a soubory jsou odeslány paralelně.
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


Při nahrávání velkého počtu prostředků Vezměte v úvahu následující skutečnosti:

* Vytvoří nový objekt **CloudMediaContext** pro každé vlákno. Třída **CloudMediaContext** není bezpečná pro přístup z více vláken.
* Zvětšete NumberOfConcurrentTransfers z výchozí hodnoty 2 na vyšší hodnotu, jako je 5. Nastavení této vlastnosti ovlivňuje všechny instance **CloudMediaContext**. 
* Nechte ParallelTransferThreadCount na výchozí hodnotu 10.

## <a name="ingesting-assets-in-bulk-using-media-services-net-sdk"></a><a id="ingest_in_bulk"></a>Hromadné ingestování prostředků pomocí sady Media Services .NET SDK
Nahrávání souborů velkých prostředků může být při vytváření prostředků kritickým bodem. Zpracování prostředků v hromadném nebo "hromadném ingestování" zahrnuje odkládání vytvoření prostředků z procesu nahrávání. Chcete-li použít postup hromadného ingestování, vytvořte manifest (IngestManifest), který popisuje Asset a přidružené soubory. Pak použijte metodu nahrání dle vašeho výběru a nahrajte přidružené soubory do kontejneru objektů BLOB manifestu. Microsoft Azure Media Services sleduje kontejner objektů BLOB přidružený k manifestu. Po nahrání souboru do kontejneru objektů BLOB Microsoft Azure Media Services dokončí vytváření assetů na základě konfigurace assetu v manifestu (IngestManifestAsset).

Chcete-li vytvořit nový IngestManifest, zavolejte metodu create, která je vystavena kolekcí IngestManifests na CloudMediaContext. Tato metoda vytvoří nový IngestManifest s názvem manifestu, který zadáte.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Vytvořte assety, které jsou spojeny s hromadně IngestManifest. Nakonfigurujte požadované možnosti šifrování assetu pro hromadné ingestování.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

IngestManifestAsset přidruží Asset k hromadnému IngestManifestu pro hromadné přijímání. Přidruží také AssetFiles, který tvoří každý Asset. Chcete-li vytvořit IngestManifestAsset, použijte metodu create v kontextu serveru.

Následující příklad ukazuje přidání dvou nových IngestManifestAssets, které přiřadí dříve vytvořené dva prostředky do manifestu hromadného přijímání. Každý IngestManifestAsset také přidruží sadu souborů, které se nahrají pro každý Asset během hromadného ingestování.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Můžete použít libovolnou vysokorychlostní klientskou aplikaci schopný odeslat soubory prostředků do identifikátoru URI kontejneru úložiště objektů blob, který poskytuje vlastnost **IIngestManifest. BlobStorageUriForUpload** IngestManifest. 

Následující kód ukazuje, jak použít sadu .NET SDK k nahrání souborů assetů.

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

Kód pro nahrání souborů assetu pro ukázku použitou v tomto článku je uveden v následujícím příkladu kódu:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Můžete určit průběh hromadného příjmu pro všechny prostředky přidružené k **IngestManifest** pomocí cyklického dotazování na vlastnost statistiky **IngestManifest**. Chcete-li aktualizovat informace o průběhu, je nutné při každém dotazování vlastnosti Statistika použít nový **CloudMediaContext** .

Následující příklad ukazuje cyklické dotazování na IngestManifest podle jeho **ID**.

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


## <a name="upload-files-using-net-sdk-extensions"></a>Nahrávání souborů pomocí rozšíření sady .NET SDK
Následující příklad ukazuje, jak nahrát jeden soubor pomocí rozšíření sady .NET SDK. V tomto případě je použita metoda **CreateFromFile** , ale asynchronní verze je také k dispozici (**CreateFromFileAsync**). Metoda **CreateFromFile** umožňuje zadat název souboru, možnost šifrování a zpětné volání, aby bylo možné ohlásit průběh nahrávání souboru.

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

Následující příklad volá funkci UploadFile a určuje šifrování úložiště jako možnost vytvoření assetu.  

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
Teď, když jste nahráli Asset k Media Services, přečtěte si článek [Jak získat multimediální procesor][How to Get a Media Processor] .

[How to Get a Media Processor]: media-services-get-media-processor.md
