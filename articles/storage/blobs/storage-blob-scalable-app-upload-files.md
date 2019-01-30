---
title: Paralelní nahrávání velkých objemů náhodných dat do služby Azure Storage | Microsoft Docs
description: Zjistěte, jak pomocí sady Azure SDK nahrávat velké objemy náhodných dat do účtu služby Azure Storage.
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: a1dba92a9e156c82f49b9f6f85faf227fc652029
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240076"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Paralelní nahrávání velkých objemů náhodných dat do úložiště Azure

Tento kurz je druhá část série. V tomto kurzu se dozvíte, jak nasadit aplikaci, která nahrává velký objem náhodných dat do účtu úložiště Azure.

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Konfigurace připojovacího řetězce
> * Sestavení aplikace
> * Spuštění aplikace
> * Ověření počtu připojení

Úložiště objektů blob v Azure představuje škálovatelnou službu pro ukládání vašich dat. Pro zajištění co nejlepšího výkonu vaší aplikace doporučujeme seznámit se se způsobem, jakým úložiště objektů blob funguje. Důležitá je i znalost omezení objektů blob v Azure. Další informace o těchto omezeních najdete v tématu popisujícím [cíle škálovatelnosti úložiště objektů blob](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).

Dalším důležitým faktorem při návrhu vysoce výkonné aplikace využívající objekty blob je [pojmenování oddílů](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#subheading47). Úložiště Azure ke škálování a vyrovnávání zatížení používá schéma vytváření oddílů na základě rozsahu. Tato konfigurace znamená, že se soubory s podobnými zásadami vytváření názvů nebo předponami umisťují do stejného oddílu. Tato logika zahrnuje název kontejneru, do kterého se soubory nahrávají. V tomto kurzu použijete soubory, které mají jako název GUID a obsahují náhodně vygenerovaný obsah. Tyto soubory se pak nahrají do pěti různých kontejnerů s náhodnými názvy.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, je nutné dokončit předchozí kurz o službě Storage: [Vytvoření virtuálního počítače a účtu úložiště pro škálovatelnou aplikaci][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Vzdálené připojení k virtuálnímu počítači

Pomocí následujícího příkazu na svém místním počítači vytvořte s virtuálním počítačem relaci vzdálené plochy. IP adresu nahraďte veřejnou IP adresou vašeho virtuálního počítače. Po zobrazení výzvy zadejte přihlašovací údaje, které jste použili při vytváření virtuálního počítače.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Konfigurace připojovacího řetězce

Na portálu Azure Portal přejděte k účtu úložiště. V části **Nastavení** v účtu úložiště vyberte **Přístupové klíče**. Zkopírujte **připojovací řetězec** z primárního nebo sekundárního klíče. Přihlaste se k virtuálnímu počítači, který jste vytvořili v předchozím kurzu. Otevřete **příkazový řádek** jako správce a spusťte příkaz `setx` s přepínačem `/m`. Tento příkaz uloží proměnnou prostředí s nastavením počítače. Proměnná prostředí není dostupná, dokud znovu nenačtete **příkazový řádek**. Nahraďte **\<storageConnectionString\>** v následujícím příkladu:

```
setx storageconnectionstring "<storageConnectionString>" /m
```

Až budete hotovi, otevřete další **příkazový řádek**, přejděte do umístění `D:\git\storage-dotnet-perf-scale-app` a zadáním příkazu `dotnet build` sestavte aplikaci.

## <a name="run-the-application"></a>Spuštění aplikace

Přejděte na adresu `D:\git\storage-dotnet-perf-scale-app`.

Zadáním příkazu `dotnet run` spusťte aplikaci. Při prvním spuštění příkazu `dotnet` se za účelem zrychlení obnovení a umožnění přístupu offline naplní místní mezipaměť balíčků. Dokončení tohoto příkazu trvá až minutu a provede se pouze jednou.

```
dotnet run
```

Aplikace vytvoří pět náhodně pojmenovaných kontejnerů a začne nahrávat soubory v pracovním adresáři do účtu úložiště. Aplikace nastaví minimální počet vláken na 100 a hodnotu [DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) na 100, aby se při spuštění aplikace zajistilo povolení velkého počtu souběžných připojení.

Kromě nastavení dělení na vlákna a omezení připojení se ve třídě [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) pro metodu [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) nakonfiguruje použití paralelismu a vypnutí ověřování hodnoty hash MD5. Soubory se nahrávají ve 100MB blocích. Tato konfigurace zajišťuje lepší výkon, ale může být nákladnější v případě, že používáte málo výkonnou síť, protože v případě selhání se opakuje nahrávání celého 100MB bloku.

|Vlastnost|Value|Popis|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.paralleloperationthreadcount?view=azure-dotnet)| 8| Toto nastavení při nahrávání rozdělí objekty blob do bloků. Pro zajištění nejvyššího výkonu by tato hodnota měla být osminásobkem počtu jader. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| true (pravda)| Tato vlastnost zakazuje kontrolu hodnoty hash MD5 nahrávaného obsahu. Zakázáním ověřování MD5 dosáhnete rychlejšího přenosu. Neprovádí se však potvrzení platnosti ani integrity přenášených souborů.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false (nepravda)| Tato vlastnost určuje, jestli se počítá a společně se souborem ukládá hodnota hash MD5.   |
| [RetryPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.retrypolicy?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_RetryPolicy)| 2sekundové omezení rychlosti a maximálně 10 opakování |Určuje zásady opakování požadavků. Chybná připojení se opakují. V tomto příkladu je v zásadě [ExponentialRetry](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.exponentialretry?view=azure-dotnet) nakonfigurované 2sekundové omezení rychlosti a maximální počet 10 opakování. Toto nastavení je důležité, když se vaše aplikace blíží dosažení [cílů škálovatelnosti úložiště objektů blob](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).  |

Úloha `UploadFilesAsync` je znázorněná v následujícím příkladu:

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

Následující příklad je zkrácený výstup aplikace spuštěné v systému Windows.

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>Ověření připojení

V průběhu nahrávání souborů můžete ověřit počet souběžných připojení k vašemu účtu úložiště. Otevřete **příkazový řádek** a zadejte `netstat -a | find /c "blob:https"`. Tento příkaz zobrazí počet aktuálně otevřených připojení pomocí příkazu `netstat`. Když budete postupovat podle kurzu, zobrazí se podobný výstup jako v následujícím příkladu. Jak je vidět z příkladu, při nahrávání náhodných souborů do účtu úložiště se otevřelo 800 připojení. Tato hodnota se v průběhu nahrávání mění. Díky paralelnímu nahrávání bloků dat se výrazně zkrátí čas potřebný k přenosu obsahu.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Další postup

V druhé části série jste se seznámili s paralelním nahráváním velkých objemů náhodných dat do účtu úložiště a naučili jste se například:

> [!div class="checklist"]
> * Konfigurace připojovacího řetězce
> * Sestavení aplikace
> * Spuštění aplikace
> * Ověření počtu připojení

Přejděte ke třetí části série, kde z účtu úložiště stáhnete velké objemy dat.

> [!div class="nextstepaction"]
> [Stahování velkých objemů náhodných dat z úložiště Azure](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
