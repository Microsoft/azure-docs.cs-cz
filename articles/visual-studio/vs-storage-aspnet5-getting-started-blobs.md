---
title: Začínáme s blob storage a Visual Studio připojené služby (ASP.NET Core) | Dokumentace Microsoftu
description: Jak začít používat Azure Blob storage v projektu Visual Studio ASP.NET Core, po vytvoření účtu úložiště pomocí sady Visual Studio připojené služby
services: storage
author: ghogen
manager: douge
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: b2b707585df4a7ec26f689b4213be74bdaab680d
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42058377"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Začínáme s Azure Blob storage a Visual Studio připojené služby (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Tento článek popisuje, jak začít používat Azure Blob storage v sadě Visual Studio po odkazovat účtu služby Azure storage v projektu aplikace ASP.NET Core pomocí sady Visual Studio nebo vytvořili **připojené služby** funkce. **Připojené služby** operace nainstaluje příslušné balíčky NuGet pro přístup k Azure storage ve vašem projektu a přidá připojovací řetězec pro účet úložiště pro konfigurační soubory projektu. (Viz [dokumentace ke službě Storage](https://azure.microsoft.com/documentation/services/storage/) obecné informace o službě Azure Storage.)

Azure Blob storage je služba pro ukládání velkých objemů nestrukturovaných dat, který je přístupný z kdekoli na světě prostřednictvím protokolu HTTP nebo HTTPS. Jeden objekt blob může být libovolné velikosti. Objekty BLOB může být třeba obrázky, zvukových souborů a videosouborů, nezpracovaná data a soubory dokumentů. Tento článek popisuje, jak začít pracovat s úložišti objektů blob po vytvoření účtu služby Azure storage pomocí sady Visual Studio **připojené služby** v projektu aplikace ASP.NET Core.

Stejně jako živé soubory ve složkách, živé úložiště objektů BLOB v kontejnerech. Po vytvoření objektu blob, vytvořte jeden nebo více kontejnerů v tohoto objektu blob. Například v objektu blob, nazývá "Výstřižků", můžete vytvořit kontejnery nazývá "Image" k ukládání obrázků a druhý s názvem "zvuku" pro uložení zvukové soubory. Po vytvoření kontejnerů můžete nahrát jednotlivých souborů na ně. Zobrazit [rychlý start: nahrávání, stahování a výpis objektů BLOB pomocí .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) Další informace o programově manipulace s objekty BLOB.

Některé z rozhraní API služby Azure Storage jsou asynchronní a kódu v tomto článku se předpokládá, že se používají asynchronní metody. Zobrazit [asynchronní programování](https://docs.microsoft.com/dotnet/csharp/async) Další informace.

## <a name="access-blob-containers-in-code"></a>Kontejnery objektů blob přístup v kódu

Programově přístup k objektům BLOB v projektech ASP.NET Core, budete muset přidat následující kód, pokud není již k dispozici:

1. Přidat nezbytné `using` příkazy:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Získání `CloudStorageAccount` objekt reprezentující informace o vašem účtu úložiště. Chcete-li získat připojovací řetězec úložiště a informace o účtu úložiště z konfigurace služby Azure pomocí následujícího kódu:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Použití `CloudBlobClient` můžete získat `CloudBlobContainer` odkaz na existující kontejner v účtu úložiště:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Vytvořte kontejner v kódu

Můžete také použít `CloudBlobClient` k vytvoření kontejneru v účtu úložiště pomocí volání `CreateIfNotExistsAsync`:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Aby soubory v kontejneru dostupné pro všechny uživatele, nastavte byly veřejné:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Nahrání objektu blob do kontejneru

K nahrání souboru objektu blob do kontejneru, získejte odkaz na kontejner a použijte ho k získání odkazu objektu blob. Pak na tento odkaz nahrát jakýkoli proud dat voláním `UploadFromStreamAsync` metody. Tato operace vytvoří objekt blob, pokud už tam není a přepíše existující objekt blob. 

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

Seznam objektů BLOB v kontejneru, první získat odkaz na kontejner, zavolejte jeho `ListBlobsSegmentedAsync` metodu pro načtení objektů BLOB a/nebo obsažené adresáře. Pro přístup k bohaté sadě vlastností a metod vrácené `IListBlobItem`, přetypujte ji `CloudBlockBlob`, `CloudPageBlob`, nebo `CloudBlobDirectory` objektu. Pokud neznáte typ objektu blob, použijte kontrolu typu určit, který typ vysílat.

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

Zobrazit [rychlý start: nahrávání, stahování a výpis objektů BLOB pomocí .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) pro další možnosti, jak zobrazit obsah kontejneru objektů blob.

## <a name="download-a-blob"></a>Stažení objektu blob

Ke stažení objektů blob, první získat odkaz na objekt blob, zavolejte `DownloadToStreamAsync` metody. V následujícím příkladu `DownloadToStreamAsync` způsob přenosu obsahu objektu blob na objekt datového proudu, který potom můžete uložit do místního souboru.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Zobrazit [rychlý start: nahrávání, stahování a výpis objektů BLOB pomocí .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) další možnosti pro ukládání objektů BLOB jako soubory.

## <a name="delete-a-blob"></a>Odstranění objektu blob

Pokud chcete odstranit objekt blob, první získat odkaz na objekt blob, zavolejte `DeleteAsync` metody:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Další postup

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
