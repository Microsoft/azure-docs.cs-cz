---
title: Nahrání souborů do účtu Media Services pomocí .NET | Dokumentace Microsoftu
description: Další informace o získání mediálního obsahu do Media Services pomocí vytváření a nahrávání prostředky.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2018
ms.author: juliako
ms.openlocfilehash: 43da365a6424fa9d489db1f5cd74ff6aeb8f0d7c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819879"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Nahrání souborů do účtu Azure Media Services pomocí rozhraní .NET
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Azure Portal](media-services-portal-upload-files.md)
> 
> 

Ve službě Media Services můžete digitální soubory nahrát (nebo ingestovat) do prostředku. **Asset** entita může obsahovat video, zvuk, obrázky, kolekci miniatur, text sleduje a titulků soubory (a metadata o těchto souborech.)  Jakmile soubory odešlete, bude váš obsah bezpečně uložen v cloudu pro další zpracování a streamování.

Soubory v prostředku se nazývají **soubory prostředku**. **AssetFile** instance a samotný mediální soubor jsou dva různé objekty. AssetFile instance obsahuje metadata do souboru média, zatímco mediální soubor obsahuje skutečné mediálního obsahu.

> [!NOTE]
> Platí následující aspekty:
> 
> * Služba Media Services využívá hodnoty vlastnosti IAssetFile.Name při vytváření adres URL pro streamování obsahu (například http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tohoto důvodu není povoleno kódování procent. Hodnota **název** vlastností nesmí obsahovat žádný z následujících [procent kódování – vyhrazené znaky](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Kromě toho může existovat pouze jeden "." pro příponu názvu souboru.
> * Délka názvu nesmí být delší než 260 znaků.
> * Maximální velikost souboru podporovaná při zpracování ve službě Media Services je omezená. Podrobnosti o omezení velikosti souboru najdete v [tomto článku](media-services-quotas-and-limitations.md).
> * Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.
> 

Když vytvoříte prostředky, můžete zadat následující možnosti šifrování:

* **Žádné** – nepoužívá se žádné šifrování. Toto je výchozí hodnota. Při použití této možnosti není váš obsah chráněný během přenosu ani při umístění v úložišti.
  Pokud chcete dodávat obsah ve formátu MP4 pomocí progresivního stahování, použijte tuto možnost: 
* **CommonEncryption** – tuto možnost použijte, pokud nahráváte obsah, který byl zašifrován a chráněný běžným šifrováním nebo DRM PlayReady (například technologie Smooth Streaming chráněná pomocí PlayReady DRM).
* **EnvelopeEncrypted** – tuto možnost použijte, pokud odesíláte HLS se šifrováním pomocí standardu AES. Pamatujte, že soubory musí být zakódované a zašifrované pomocí správce transformací.
* **StorageEncrypted** – šifruje vaše nešifrovaného obsahu pomocí 256bitového šifrování AES-256 a nahraje ho do služby Azure Storage kde jsou uložená v klidovém stavu zašifrovaná. Prostředky chráněné pomocí šifrování úložiště jsou před kódováním automaticky bez šifrování umístěny do systému souborů EFS a volitelně se znovu zašifrují před jejich odesláním zpět v podobě nového výstupního prostředku. Případem primárního použití šifrování úložiště je situace, kdy chcete zabezpečit soubory s vysoce kvalitními vstupními multimediálními soubory pomocí silného šifrování na disku.
  
    Služba Media Services poskytuje šifrování na diskové úložiště pro vaše prostředky, ne přes přenosu jako správce digitálních práv (DRM).
  
    Pokud váš asset používá šifrování úložiště, musíte nakonfigurovat zásady doručení assetu. Další informace najdete v tématu [konfigurace zásad doručení assetu](media-services-dotnet-configure-asset-delivery-policy.md).

Pokud zadáte pro váš prostředek mají být šifrována pomocí **CommonEncrypted** možnost, nebo **EnvelopeEncrypted** možnost, je nutné přidružit asset s **ContentKey**. Další informace najdete v tématu [jak vytvořit ContentKey](media-services-dotnet-create-contentkey.md). 

Pokud zadáte pro váš prostředek mají být šifrována pomocí **StorageEncrypted** možnost, Media Services SDK pro .NET vytvoří **StorageEncrypted** **ContentKey** pro váš prostředek.

Tento článek ukazuje, jak používat sadu Media Services .NET SDK, tak jak rozšíření Media Services .NET SDK k nahrání souborů do assetu Media Services.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Nahrát jeden soubor s Media Services .NET SDK
Následující kód používá .NET k nahrání jednoho souboru. AccessPolicy a Lokátor se vytvořen a zničen pomocí funkce nahrání. 

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


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Odeslání více souborů s využitím Media Services .NET SDK
Následující kód ukazuje, jak vytvořit asset a odeslání více souborů.

Kód provede následující akce:

* Vytvoří prázdný prostředku pomocí CreateEmptyAsset metody definované v předchozím kroku.
* Vytvoří **AccessPolicy** instanci, která určuje oprávnění a dobu trvání přístup k assetu.
* Vytvoří **Lokátor** instanci, která poskytuje přístup k assetu.
* Vytvoří **BlobTransferClient** instance. Tento typ reprezentuje klienta, který funguje na objektech BLOB Azure. V tomto příkladu klienta sleduje jejich průběh nahrávání. 
* Vytvoří výčet prostřednictvím soubory v zadaném adresáři a vytvoří **AssetFile** instance pro každý soubor.
* Tyto soubory nahraje do Media Services pomocí **UploadAsync** metody. 

> [!NOTE]
> Pomocí této metody UploadAsync zajistěte, aby volání neblokují a soubory se nahrávají paralelně.
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


Při nahrávání velký počet prostředků, zvažte následující:

* Vytvořte nový **CloudMediaContext** objektu na vlákno. **CloudMediaContext** třída není bezpečná pro vlákno.
* Zvyšte NumberOfConcurrentTransfers z výchozí hodnoty 2 na vyšší hodnotu, jako je 5. Nastavení této vlastnosti ovlivňuje všechny výskyty **CloudMediaContext**. 
* Zachovejte si ParallelTransferThreadCount na výchozí hodnotu 10.

## <a id="ingest_in_bulk"></a>Zpracování prostředků hromadně pomocí sady Media Services .NET SDK
Nahrávání souborů velkých prostředků může být kritickým bodem při vytváření prostředku. Zpracování prostředků v hromadné nebo "Hromadné Ingestovat", zahrnuje oddělení vytváření prostředků z procesu nahrávání. Použít hromadné ingestovat přístup, vytvořte manifest (IngestManifest), které popisují prostředek a jeho přidružené soubory. Potom použijte metodu nahrávání podle vašeho výběru k nahrání přidružené soubory do kontejneru objektů blob v manifestu. Microsoft Azure Media Services sleduje kontejner objektů blob, který je přidružený k manifestu. Po nahrání souboru do kontejneru objektů blob Microsoft Azure Media Services dokončí vytváření prostředků v závislosti na konfiguraci prostředků v manifestu (IngestManifestAsset).

Chcete-li vytvořit nové IngestManifest, zavolejte metodu vytvořit vystavené IngestManifests kolekce na CloudMediaContext. Tato metoda vytvoří nový IngestManifest manifestu názvem, který poskytnete.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Vytvořte prostředky, které jsou spojeny s hromadnou IngestManifest. Nakonfigurujte možnosti požadované šifrování prostředků pro příjem hromadně.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

IngestManifestAsset přidruží prostředek hromadné IngestManifest pro příjem hromadně. Přidružuje také AssetFiles tvoří každého prostředku. K vytvoření IngestManifestAsset, použijte metodu vytvořit v kontextu serveru.

Následující příklad ukazuje přidání dvou nových IngestManifestAssets, které spojují dva prostředky, které jste vytvořili za účelem hromadného ingestování manifestu. Každý IngestManifestAsset také přidruží sady souborů, které jsou odeslány pro každý prostředek během hromadné ingestovat.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Můžete použít libovolný vysokorychlostní klienta aplikace schopná nahrávání souborů prostředků do kontejneru úložiště objektů blob identifikátorů URI poskytnutých **IIngestManifest.BlobStorageUriForUpload** vlastnost IngestManifest. 

Následující kód ukazuje, jak používat sadu .NET SDK k nahrání souborů prostředků.

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

Kód pro nahrávání souborů prostředků pro ukázky použité v tomto článku můžete vidět v následujícím příkladu kódu:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Můžete určit průběh hromadné ingestovat pro všechny prostředky přidružené **IngestManifest** pomocí cyklického dotazování je vlastnost statistiky **IngestManifest**. Abyste mohli aktualizovat informace o průběhu, je nutné použít nový **CloudMediaContext** pokaždé, když dotazovat vlastnost statistiky.

Následující příklad ukazuje dotazování IngestManifest podle jeho **Id**.

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


## <a name="upload-files-using-net-sdk-extensions"></a>Nahrávání souborů s využitím rozšíření sady SDK pro .NET
Následující příklad ukazuje, jak nahrát jeden soubor pomocí rozšíření sady SDK pro .NET. V tomto případě **CreateFromFile** metoda se používá, ale je taky dostupná verze asynchronní (**CreateFromFileAsync**). **CreateFromFile** metoda umožňuje zadat název souboru, možnost šifrování a zpětné volání za účelem hlášení průběhu odesílání souboru.

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

Následující příklad volá funkci UploadFile a určuje úložiště šifrování jako možnost vytvoření prostředku.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>Další postup

Nyní můžete kódovat nahrané assety. Další informace najdete v tématu [Kódování assetů](media-services-portal-encode.md).

Můžete také použít službu Azure Functions k aktivaci úlohy kódování při příchodu souboru do nakonfigurovaného kontejneru. Další informace najdete v [této ukázce](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Další krok
Teď, když jste odeslali prostředek služby Media Services, pokračujte [získání mediálním procesorem] [ How to Get a Media Processor] článku.

[How to Get a Media Processor]: media-services-get-media-processor.md

