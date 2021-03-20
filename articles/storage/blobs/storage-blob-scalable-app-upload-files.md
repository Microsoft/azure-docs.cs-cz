---
title: Paralelní nahrávání velkých objemů náhodných dat do Azure Storage
description: Naučte se používat klientskou knihovnu Azure Storage k paralelnímu nahrání velkých objemů náhodných dat do Azure Storage účtu.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: ed7020a58f3f15403108934bcc3fab644bd1b627
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584461"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Paralelní nahrávání velkých objemů náhodných dat do úložiště Azure

Tento kurz je druhá část série. V tomto kurzu se dozvíte, jak nasadit aplikaci, která nahrává velký objem náhodných dat do účtu úložiště Azure.

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Konfigurace připojovacího řetězce
> * Sestavení aplikace
> * Spuštění aplikace
> * Ověření počtu připojení

Microsoft Azure Blob Storage poskytuje škálovatelnou službu pro ukládání vašich dat. Pro zajištění co nejlepšího výkonu vaší aplikace doporučujeme seznámit se se způsobem, jakým úložiště objektů blob funguje. Znalost omezení pro objekty blob Azure je důležitá. Další informace o těchto omezeních najdete v článku: [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../blobs/scalability-targets.md).

[Pojmenovávání oddílů](../blobs/storage-performance-checklist.md#partitioning) je další potenciálně důležitý faktor při návrhu vysoce výkonné aplikace pomocí objektů BLOB. Pro blokové velikosti větší než nebo se rovná 4 MiB se používají [objekty blob bloku s vysokou propustností](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) a pojmenování oddílů nebude mít vliv na výkon. Pro blokové velikosti menší než 4 MiB používá Azure Storage schéma dělení na základě rozsahu pro škálování a vyrovnávání zatížení. Tato konfigurace znamená, že se soubory s podobnými zásadami vytváření názvů nebo předponami umisťují do stejného oddílu. Tato logika zahrnuje název kontejneru, do kterého se soubory nahrávají. V tomto kurzu použijete soubory, které mají jako název GUID a obsahují náhodně vygenerovaný obsah. Tyto soubory se pak nahrají do pěti různých kontejnerů s náhodnými názvy.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu je nutné dokončit předchozí kurz o službě Storage: [Vytvoření virtuálního počítače a účtu úložiště pro škálovatelnou aplikaci][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Vzdálené připojení k virtuálnímu počítači

Pomocí následujícího příkazu na svém místním počítači vytvořte s virtuálním počítačem relaci vzdálené plochy. IP adresu nahraďte veřejnou IP adresou vašeho virtuálního počítače. Po zobrazení výzvy zadejte přihlašovací údaje, které jste použili při vytváření virtuálního počítače.

```console
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Konfigurace připojovacího řetězce

Na portálu Azure Portal přejděte k účtu úložiště. V části **Nastavení** v účtu úložiště vyberte **Přístupové klíče**. Zkopírujte **připojovací řetězec** z primárního nebo sekundárního klíče. Přihlaste se k virtuálnímu počítači, který jste vytvořili v předchozím kurzu. Otevřete **příkazový řádek** jako správce a spusťte příkaz `setx` s přepínačem `/m`. Tento příkaz uloží proměnnou prostředí s nastavením počítače. Proměnná prostředí není dostupná, dokud znovu nenačtete **příkazový řádek**. Nahraďte **\<storageConnectionString\>** v následující ukázce:

```console
setx storageconnectionstring "<storageConnectionString>" /m
```

Až budete hotovi, otevřete další **příkazový řádek**, přejděte do umístění `D:\git\storage-dotnet-perf-scale-app` a zadáním příkazu `dotnet build` sestavte aplikaci.

## <a name="run-the-application"></a>Spuštění aplikace

Přejděte na adresu `D:\git\storage-dotnet-perf-scale-app`.

Zadáním příkazu `dotnet run` spusťte aplikaci. Při prvním spuštění příkazu `dotnet` se za účelem zrychlení obnovení a umožnění přístupu offline naplní místní mezipaměť balíčků. Dokončení tohoto příkazu trvá až minutu a provede se pouze jednou.

```console
dotnet run
```

Aplikace vytvoří pět náhodně pojmenovaných kontejnerů a začne nahrávat soubory v pracovním adresáři do účtu úložiště.

`UploadFilesAsync`Metoda je zobrazená v následujícím příkladu:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_UploadFilesAsync":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Minimální a maximální počet vláken je nastaven na 100, aby se zajistilo, že je povolený velký počet souběžných připojení.

```csharp
private static async Task UploadFilesAsync()
{
    // Create five randomly named containers to store the uploaded files.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();

    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // Path to the directory to upload
    string uploadPath = currentdir + "\\upload";

    // Start a timer to measure how long it takes to upload all the files.
    Stopwatch time = Stopwatch.StartNew();

    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);

        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections
        // are retried with a back off policy. As multiple large files are being uploaded using
        // large block sizes, this can cause an issue if an exponential retry policy is not defined.
        // Additionally, parallel operations are enabled with a thread count of 8.
        // This should be a multiple of the number of processor cores in the machine.
        // Lastly, MD5 hash validation is disabled for this example, improving the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };

        // Create a new instance of the SemaphoreSlim class to 
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set the block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;

            await sem.WaitAsync();

            // Create a task for each file to upload. The tasks are
            // added to a collection and all run asynchronously.
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));

            count++;
        }

        // Run all the tasks asynchronously.
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
Kromě nastavení dělení na vlákna a omezení připojení se ve třídě [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) pro metodu [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) nakonfiguruje použití paralelismu a vypnutí ověřování hodnoty hash MD5. Soubory se nahrávají ve 100MB blocích. Tato konfigurace zajišťuje lepší výkon, ale může být nákladnější v případě, že používáte málo výkonnou síť, protože v případě selhání se opakuje nahrávání celého 100MB bloku.

|Vlastnost|Hodnota|Popis|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| Toto nastavení při nahrávání rozdělí objekty blob do bloků. Pro nejvyšší výkon by tato hodnota měla být osmi časy počtu jader. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Tato vlastnost zakazuje kontrolu hodnoty hash MD5 nahrávaného obsahu. Zakázáním ověřování MD5 dosáhnete rychlejšího přenosu. Neprovádí se však potvrzení platnosti ani integrity přenášených souborů.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false (nepravda)| Tato vlastnost určuje, jestli se počítá a společně se souborem ukládá hodnota hash MD5.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| 2sekundové omezení rychlosti a maximálně 10 opakování |Určuje zásady opakování požadavků. Chybná připojení se opakují. V tomto příkladu je v zásadě [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry) nakonfigurované 2sekundové omezení rychlosti a maximální počet 10 opakování. Toto nastavení je důležité, když se vaše aplikace blíží k dosažení cílů škálovatelnosti pro úložiště objektů BLOB. Další informace najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../blobs/scalability-targets.md).  |

---

Následující příklad je zkrácený výstup aplikace spuštěné v systému Windows.

```console
Created container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Created container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Created container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Created container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Created container 92480da9-f695-4a42-abe8-fb35e71eb887
Iterating in directory: C:\git\myapp\upload
Found 5 file(s)
Uploading 1d596d16-f6de-4c4c-8058-50ebd8141e4d.pdf to container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Uploading 242ff392-78be-41fb-b9d4-aee8152a6279.pdf to container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Uploading 38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.pdf to container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Uploading 45930d63-b0d0-425f-a766-cda27ff00d32.pdf to container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Uploading 5129b385-5781-43be-8bac-e2fbb7d2bd82.pdf to container 92480da9-f695-4a42-abe8-fb35e71eb887
Uploaded 5 files in 16.9552163 seconds
```

### <a name="validate-the-connections"></a>Ověření připojení

V průběhu nahrávání souborů můžete ověřit počet souběžných připojení k vašemu účtu úložiště. Otevřete okno konzoly a zadejte `netstat -a | find /c "blob:https"` . Tento příkaz zobrazuje počet aktuálně otevřených připojení. Jak vidíte v následujícím příkladu, 800 připojení byla při nahrávání náhodných souborů do účtu úložiště otevřená. Tato hodnota se v průběhu nahrávání mění. Díky paralelnímu nahrávání bloků dat se výrazně zkrátí čas potřebný k přenosu obsahu.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Další kroky

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
