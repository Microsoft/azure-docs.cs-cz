---
title: Začínáme s úložištěm objektů blob pomocí Visual Studia (ASP.NET Core)
description: Jak začít používat úložiště objektů blob Azure v projektu Visual Studio ASP.NET Core po vytvoření účtu úložiště pomocí připojených služeb Visual Studia
services: storage
author: ghogen
manager: jillfra
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 81df41470c893f569fd17345e8bdf4b29641ec64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298831"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Začínáme s úložištěm objektů blob Azure a připojenými službami Visual Studia (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Tento článek popisuje, jak začít používat úložiště objektů blob Azure ve Visual Studiu po vytvoření nebo odkazování na účet úložiště Azure v projektu ASP.NET Core pomocí funkce Visual Studio **Connected Services.** Operace **Připojené služby** nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure ve vašem projektu a přidá připojovací řetězec pro účet úložiště do konfiguračních souborů projektu. (Obecné informace o Azure Storage najdete v [dokumentaci k](https://azure.microsoft.com/documentation/services/storage/) úložišti.)

Azure Blob storage je služba pro ukládání velkého množství nestrukturovaných dat, ke kterým se dá přistupovat z libovolného místa na světě prostřednictvím protokolu HTTP nebo HTTPS. Jeden objekt blob může mít libovolnou velikost. Objekty BLOB mohou být například obrázky, zvukové soubory a soubory videa, nezpracovaná data a soubory dokumentů. Tento článek popisuje, jak začít s úložištěm objektů blob po vytvoření účtu úložiště Azure pomocí **služby** Visual Studio Connected Services v projektu ASP.NET Core.

Stejně jako soubory žijí ve složkách, objekty BLOB úložiště žijí v kontejnerech. Po vytvoření objektu blob vytvoříte jeden nebo více kontejnerů v tomto objektu blob. Například v objektu blob s názvem "Scrapbook", můžete vytvořit kontejnery s názvem "obrázky" pro ukládání obrázků a další s názvem "audio" pro ukládání zvukových souborů. Po vytvoření kontejnerů do nich můžete nahrát jednotlivé soubory. Další informace o programové manipulaci s objekty BLOB najdete v [tématu Rychlý start: Nahrát, stáhnout a zobrazit seznam objektů BLOB pomocí rozhraní .NET.](../storage/blobs/storage-quickstart-blobs-dotnet.md)

Některá api úložiště Azure jsou asynchronní a kód v tomto článku předpokládá, že se používají asynchronní metody. Další informace naleznete [v tématu Asynchronní programování.](https://docs.microsoft.com/dotnet/csharp/async)

## <a name="access-blob-containers-in-code"></a>Přístup k kontejnerům objektů blob v kódu

Chcete-li programově přistupovat k objektům BLOB v projektech ASP.NET Core, musíte přidat následující kód, pokud již není k dispozici:

1. Přidejte `using` potřebné příkazy:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Získejte `CloudStorageAccount` objekt, který představuje informace o účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci úložiště a účtu úložiště z konfigurace služby Azure:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Pomocí `CloudBlobClient` objektu můžete `CloudBlobContainer` získat odkaz na existující kontejner ve vašem účtu úložiště:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Vytvoření kontejneru v kódu

Můžete také vytvořit `CloudBlobClient` kontejner ve vašem účtu úložiště `CreateIfNotExistsAsync`voláním :

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Chcete-li zpřístupnit soubory v kontejneru všem uživatelům, nastavte kontejner tak, aby byl veřejný:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Nahrání objektu blob do kontejneru

Chcete-li nahrát soubor blob do kontejneru, získejte odkaz na kontejner a použijte ho k získání odkazu na objekt blob. Potom nahrajte libovolný datový proud do `UploadFromStreamAsync` tohoto odkazu voláním metody. Tato operace vytvoří objekt blob, pokud ještě není k dispozici, a přepíše existující objekt blob. 

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

Chcete-li seznam objektů BLOB v kontejneru, nejprve `ListBlobsSegmentedAsync` získat odkaz na kontejner, pak volání jeho metoda načíst objekty BLOB nebo adresáře v něm. Chcete-li získat přístup k bohaté `IListBlobItem`sadě vlastností `CloudBlockBlob`a `CloudPageBlob`metod `CloudBlobDirectory` pro vrácené , přetypovat do , nebo objekt. Pokud neznáte typ objektu blob, použijte kontrolu typu k určení, které chcete přetypovat.

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

Viz [Úvodní příručka: Nahrávání, stahování a seznam objektů BLOB pomocí rozhraní .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) pro další způsoby, jak zobrazit seznam obsahu kontejneru objektů blob.

## <a name="download-a-blob"></a>Stažení objektu blob

Chcete-li stáhnout objekt blob, nejprve získat odkaz `DownloadToStreamAsync` na objekt blob, pak volání metody. Následující příklad používá `DownloadToStreamAsync` metodu k přenosu obsahu objektu blob do objektu datového proudu, který pak můžete uložit jako místní soubor.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Viz [Úvodní příručka: Nahrávání, stahování a seznam objektů BLOB pomocí rozhraní .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) pro jiné způsoby ukládání objektů BLOB jako souborů.

## <a name="delete-a-blob"></a>Odstranění objektu blob

Chcete-li odstranit objekt blob, nejprve získat odkaz `DeleteAsync` na objekt blob, pak volání metody:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
