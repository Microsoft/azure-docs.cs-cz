---
title: Stažení velkých objemů náhodných dat z Azure Storage | Microsoft Docs
description: Zjistěte, jak pomocí sady Azure SDK stahovat velké objemy náhodných dat z účtu služby Azure Storage.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c029abd87e1b819cc4d96e906be8824c019f433
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584970"
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Stahování velkých objemů náhodných dat z úložiště Azure

Tento kurz je třetí částí série. V tomto kurzu se dozvíte, jak stahovat velké objemy dat z úložiště Azure.

Ve třetí části této série se naučíte:

> [!div class="checklist"]
> * Aktualizace aplikace
> * Spuštění aplikace
> * Ověření počtu připojení

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu je nutné dokončit předchozí kurz o službě Storage: [Paralelní nahrávání velkých objemů náhodných dat do úložiště Azure][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Vzdálené připojení k virtuálnímu počítači

 Pokud chcete s virtuálním počítačem vytvořit relaci vzdálené plochy, použijte na svém místním počítači následující příkaz. IP adresu nahraďte veřejnou IP adresou vašeho virtuálního počítače. Po zobrazení výzvy zadejte přihlašovací údaje, které jste použili při vytváření virtuálního počítače.

```console
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Aktualizace aplikace

V předchozím kurzu jste do účtu úložiště pouze nahrávali soubory. V textovém editoru otevřete soubor `D:\git\storage-dotnet-perf-scale-app\Program.cs`. Nahraďte metodu `Main` následující ukázkou. Tento příklad okomentuje úlohu nahrávání a odkomentuje úlohu stahování a úlohu, která po dokončení stahování odstraní příslušný obsah v účtu úložiště.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to 
    // ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage 
    // client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        // await UploadFilesAsync();

        // Uncomment the following line to enable downloading of files from the storage account.
        // This is commented out initially to support the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        await DownloadFilesAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.
        // This is commented out initially as the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        // has you upload only for one tutorial and download for the other.
        if (!exception)
        {
            // await DeleteExistingContainersAsync();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

Po aktualizaci aplikace je potřeba ji znovu sestavit. Otevřete `Command Prompt` (příkazový řádek) a přejděte do umístění `D:\git\storage-dotnet-perf-scale-app`. Znovu sestavte aplikaci spuštěním příkazu `dotnet build`, jak je vidět v následujícím příkladu:

```console
dotnet build
```

## <a name="run-the-application"></a>Spuštění aplikace

Když je teď aplikace znovu sestavená, je čas spustit aplikaci s aktualizovaným kódem. Pokud ještě není otevřený, otevřete `Command Prompt` (příkazový řádek) a přejděte do umístění `D:\git\storage-dotnet-perf-scale-app`.

Zadáním příkazu `dotnet run` spusťte aplikaci.

```console
dotnet run
```

Úloha `DownloadFilesAsync` je znázorněná v následujícím příkladu:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Aplikace načte kontejnery v účtu úložiště zadaném v hodnotě **storageconnectionstring** (připojovací řetězec úložiště). Projde objekty BLOB pomocí metody [Getblobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) a stáhne je do místního počítače pomocí metody [DownloadToAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_DownloadFilesAsync":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Aplikace načte kontejnery v účtu úložiště zadaném v hodnotě **storageconnectionstring** (připojovací řetězec úložiště). Projde objekty blob 10 v čase pomocí metody [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync) v kontejnerech a stáhne je do místního počítače pomocí metody [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) .

V následující tabulce je uveden [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) definovaný pro každý objekt BLOB při stažení.

|Vlastnost|Hodnota|Popis|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Tato vlastnost zakazuje kontrolu hodnoty hash MD5 nahrávaného obsahu. Zakázáním ověřování MD5 dosáhnete rychlejšího přenosu. Neprovádí se však potvrzení platnosti ani integrity přenášených souborů. |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false (nepravda)| Tato vlastnost určuje, jestli se počítá a ukládá hodnota hash MD5.   |

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestOptions on the download, including disabling MD5 
    // hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.
    // Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container, 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

---

### <a name="validate-the-connections"></a>Ověření připojení

V průběhu stahování souborů můžete ověřit počet souběžných připojení k vašemu účtu úložiště. Otevřete okno konzoly a zadejte `netstat -a | find /c "blob:https"` . Tento příkaz zobrazuje počet aktuálně otevřených připojení. Jak vidíte v následujícím příkladu, během stahování souborů z účtu úložiště se otevřelo více než 280 připojení.

```console
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Další kroky

Ve třetí části série jste se dozvěděli o stahování velkých objemů dat z účtu úložiště, včetně toho, jak:

> [!div class="checklist"]
> * Spuštění aplikace
> * Ověření počtu připojení

Přejít do čtvrté části série a ověřit metriky propustnosti a latence na portálu.

> [!div class="nextstepaction"]
> [Ověření metrik latence a propustnosti na portálu](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md
