---
title: Začínáme s objektem blob storage a Visual Studio připojené služby (ASP.NET Core) | Microsoft Docs
description: Jak začít používat úložiště objektů Blob v Azure v projektu Visual Studio ASP.NET Core po vytvoření účtu úložiště pomocí sady Visual Studio připojené služby
services: storage
author: ghogen
manager: douge
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 566e2edc0157ccd02e0b44ae7df86c4b484858b0
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Začínáme s Azure Blob storage a Visual Studio připojené služby (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Tento článek popisuje, jak začít používat úložiště objektů Blob v Azure v sadě Visual Studio, po vytvoření a odkazuje pomocí sady Visual Studio účet úložiště Azure v projektu ASP.NET Core **připojené služby** funkce. **Připojené služby** operaci nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure ve vašem projektu a přidá připojovací řetězec pro účet úložiště v konfiguračních souborech projektu. (Viz [úložiště dokumentace](https://azure.microsoft.com/documentation/services/storage/) obecné informace o službě Azure Storage.)

Azure Blob storage je služba pro ukládání velkého objemu nestrukturovaných dat, která je přístupná z kdekoliv na světě prostřednictvím protokolu HTTP nebo HTTPS. Jediného objektu blob může mít libovolnou velikost. Objekty BLOB může být třeba bitové kopie, soubory audia a videa, nezpracovaná data a soubory dokumentu. Tento článek popisuje, jak začít pracovat s úložištěm blob po vytvoření účtu úložiště Azure pomocí sady Visual Studio **připojené služby** v projektu ASP.NET Core.

Stejně jako soubory live ve složkách, za provozu v kontejnerech objektů BLOB storage. Po vytvoření objektu blob, vytvoříte jeden nebo více kontejnerů v tomto objektu blob. Například v objektu blob názvem "Výstřižků", můžete vytvořit kontejnery názvem "Image" k ukládání obrázků a jiné názvem "zvuk" k uložení zvukových souborů. Po vytvoření kontejnerů, můžete k nim nahrát jednotlivé soubory. V tématu [rychlý start: odesílání, stahování a seznamu objektů BLOB pomocí rozhraní .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) Další informace o programu manipulace s objekty BLOB.

Některé z rozhraní API úložiště Azure jsou asynchronní a kód v tomto článku předpokládá, že asynchronní metody jsou používány. V tématu [asynchronní programování](https://docs.microsoft.com/dotnet/csharp/async) Další informace.

## <a name="access-blob-containers-in-code"></a>Kontejnery objektů blob přístup v kódu

K programovému přístupu ke objektů BLOB v projektů ASP.NET Core, budete muset přidejte následující kód, pokud již nejsou přítomny:

1. Přidat nezbytné `using` příkazy:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Získání `CloudStorageAccount` objekt, který reprezentuje informace o účtu úložiště. Chcete-li získat připojovací řetězec úložiště a informace o účtu úložiště z konfigurace služby Azure, použijte následující kód:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Použití `CloudBlobClient` objekt, který chcete získat `CloudBlobContainer` odkaz na existující kontejner ve vašem účtu úložiště:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Vytvořit kontejner v kódu

Můžete také `CloudBlobClient` vytvořit kontejner ve vašem účtu úložiště voláním `CreateIfNotExistsAsync`:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Soubory v kontejneru zpřístupnit všem uživatelům, nastavte jako veřejný:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Nahrání objektu blob do kontejneru

Nahrát soubor objektu blob do kontejneru, získejte odkaz na kontejner a umožňuje vám získat odkaz na objekt blob. Pak na tento odkaz nahrát jakýkoli proud dat voláním `UploadFromStreamAsync` metoda. Tato operace vytvoří objekt blob, pokud ještě není a přepíše existující objekt blob. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Pro zobrazení seznamu objektů BLOB v kontejneru, potom první get odkaz na kontejner volat jeho `ListBlobsSegmentedAsync` metoda pro načtení objektů BLOB a/nebo obsažené adresáře. Pro přístup k bohaté sadě vlastností a metod vrácené `IListBlobItem`, vysílat `CloudBlockBlob`, `CloudPageBlob`, nebo `CloudBlobDirectory` objektu. Pokud neznáte typ objektu blob, použijte k určení, které vysílat kontrolu typu.

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
        }

        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
        }

        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }
} while (token != null);
```

V tématu [rychlý start: odesílání, stahování a seznamu objektů BLOB pomocí rozhraní .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) pro další způsoby, jak zobrazit obsah kontejneru objektů blob.

## <a name="download-a-blob"></a>Stažení objektu blob

Ke stažení objektů blob, první get a odkaz na objekt blob, zavolejte `DownloadToStreamAsync` metoda. Následující příklad používá `DownloadToStreamAsync` způsob přenosu obsahu objektu blob na objekt proudu, který pak můžete uložit jako místního souboru.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

V tématu [rychlý start: odesílání, stahování a seznamu objektů BLOB pomocí rozhraní .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) pro jiné způsoby pro uložení objektů BLOB jako soubory.

## <a name="delete-a-blob"></a>Odstranění objektu blob

Pokud chcete odstranit objekt blob, první get a odkaz na objekt blob, zavolejte `DeleteAsync` metoda:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Další postup

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
