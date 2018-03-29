---
title: Stahování velkých objemů náhodných dat ze služby Azure Storage | Microsoft Docs
description: Zjistěte, jak pomocí sady Azure SDK stahovat velké objemy náhodných dat z účtu služby Azure Storage.
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: 21186d3a2fd7d33cd78db3c3e45ff69240e7310d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Stahování velkých objemů náhodných dat z úložiště Azure

Tento kurz je třetí částí série. V tomto kurzu se dozvíte, jak stahovat velké objemy dat z úložiště Azure.

Ve třetí části této série se naučíte:

> [!div class="checklist"]
> * Aktualizace aplikace
> * Spuštění aplikace
> * Ověření počtu připojení

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu je nutné dokončit předchozí kurz o službě Storage: [Paralelní nahrávání velkých objemů náhodných dat do úložiště Azure][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Vzdálené připojení k virtuálnímu počítači

 Pokud chcete s virtuálním počítačem vytvořit relaci vzdálené plochy, použijte na svém místním počítači následující příkaz. IP adresu nahraďte veřejnou IP adresou vašeho virtuálního počítače. Po zobrazení výzvy zadejte přihlašovací údaje, které jste použili při vytváření virtuálního počítače.

```
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Aktualizace aplikace

V předchozím kurzu jste do účtu úložiště pouze nahrávali soubory. V textovém editoru otevřete soubor `D:\git\storage-dotnet-perf-scale-app\Program.cs`. Nahraďte metodu `Main` následující ukázkou. Tento příklad okomentuje úlohu nahrávání a odkomentuje úlohu stahování a úlohu, která po dokončení stahování odstraní příslušný obsah v účtu úložiště.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        UploadFilesAsync().GetAwaiter().GetResult();

        // Uncomment the following line to enable downloading of files from the storage account.  This is commented out
        // initially to support the tutorial at https://docs.microsoft.com/azure/storage/blobs/storage-blob-scaleable-app-download-files.
        // DownloadFilesAsync().GetAwaiter().GetResult();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.  This is commented out initialy
        // As the tutorial at https://docs.microsoft.com/azure/storage/blobs/storage-blob-scaleable-app-download-files has you upload only for one tutorial and download for the other. 
        if (!exception)
        {
            // DeleteExistingContainersAsync().GetAwaiter().GetResult();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

Po aktualizaci aplikace je potřeba ji znovu sestavit. Otevřete `Command Prompt` (příkazový řádek) a přejděte do umístění `D:\git\storage-dotnet-perf-scale-app`. Znovu sestavte aplikaci spuštěním příkazu `dotnet build`, jak je vidět v následujícím příkladu:

```
dotnet build
```

## <a name="run-the-application"></a>Spuštění aplikace

Když je teď aplikace znovu sestavená, je čas spustit aplikaci s aktualizovaným kódem. Pokud ještě není otevřený, otevřete `Command Prompt` (příkazový řádek) a přejděte do umístění `D:\git\storage-dotnet-perf-scale-app`.

Zadáním příkazu `dotnet run` spusťte aplikaci.

```
dotnet run
```

Aplikace načte kontejnery v účtu úložiště zadaném v hodnotě **storageconnectionstring** (připojovací řetězec úložiště). Pomocí metody [ListBlobsSegmented](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmented?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmented_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) prochází objekty blob v kontejnerech po 10 najednou a pomocí metody [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) je stahuje do místního počítače.
Následující tabulka obsahuje možnosti [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet), které se při stahování definují pro každý objekt blob.

|Vlastnost|Hodnota|Popis|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| true (pravda)| Tato vlastnost zakazuje kontrolu hodnoty hash MD5 nahrávaného obsahu. Zakázáním ověřování MD5 dosáhnete rychlejšího přenosu. Neprovádí se však potvrzení platnosti ani integrity přenášených souborů. |
|[StorBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false (nepravda)| Tato vlastnost určuje, jestli se počítá a ukládá hodnota hash MD5.   |

Úloha `DownloadFilesAsync` je znázorněná v následujícím příkladu:

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestionOptions on the download, including disabling MD5 hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.  Create a directory and configure variables for use later.
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

        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container lazily 10 at a time.
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

### <a name="validate-the-connections"></a>Ověření připojení

V průběhu stahování souborů můžete ověřit počet souběžných připojení k vašemu účtu úložiště. Otevřete `Command Prompt` (příkazový řádek) a zadejte `netstat -a | find /c "blob:https"`. Tento příkaz zobrazí počet aktuálně otevřených připojení pomocí příkazu `netstat`. Když budete postupovat podle kurzu, zobrazí se podobný výstup jako v následujícím příkladu. Jak je vidět z příkladu, při stahování náhodných souborů z účtu úložiště se otevřelo více než 280 připojení.

```
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Další kroky

V třetí části série jste se seznámili se stahováním velkých objemů náhodných dat z účtu úložiště a naučili jste se například:

> [!div class="checklist"]
> * Spuštění aplikace
> * Ověření počtu připojení

Přejděte ke čtvrté části série, kde na portálu ověříte metriky latence a propustnosti.

> [!div class="nextstepaction"]
> [Ověření metrik latence a propustnosti na portálu](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md