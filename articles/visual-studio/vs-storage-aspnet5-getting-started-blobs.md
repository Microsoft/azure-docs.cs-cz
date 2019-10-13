---
title: Začínáme s úložištěm objektů BLOB pomocí sady Visual Studio (ASP.NET Core)
description: Jak začít používat službu Azure Blob Storage v projektu Visual studia ASP.NET Core po vytvoření účtu úložiště pomocí připojených služeb sady Visual Studio
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
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298831"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Začínáme s Azure Blob Storage a připojenými službami sady Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Tento článek popisuje, jak začít používat úložiště objektů BLOB v Azure v aplikaci Visual Studio po vytvoření nebo odkazu na účet služby Azure Storage v ASP.NET Core projektu pomocí funkce **připojené služby** sady Visual Studio. Operace **připojené služby** nainstaluje příslušné balíčky NuGet pro přístup k úložišti Azure v projektu a přidá připojovací řetězec pro účet úložiště do konfiguračních souborů projektu. (Obecné informace o Azure Storage najdete v [dokumentaci k úložišti](https://azure.microsoft.com/documentation/services/storage/) .)

Azure Blob Storage je služba pro ukládání velkých objemů nestrukturovaných dat, ke kterým se dá dostat odkudkoli na světě přes HTTP nebo HTTPS. Jeden objekt BLOB může být libovolná velikost. Objekty blob můžou být například obrázky, zvukové soubory a videosoubory, nezpracovaná data a soubory dokumentů. Tento článek popisuje, jak začít se službou BLOB Storage po vytvoření účtu služby Azure Storage pomocí **připojených služeb** sady Visual Studio v projektu ASP.NET Core.

Stejně jako soubory v reálném čase ve složkách jsou objekty blob úložiště živé v kontejnerech. Po vytvoření objektu blob můžete v tomto objektu BLOB vytvořit jeden nebo více kontejnerů. Například v objektu BLOB s názvem "Scrapbook" můžete vytvořit kontejnery s názvem "image" k ukládání obrázků a další s názvem "zvuk" pro ukládání zvukových souborů. Po vytvoření kontejnerů můžete do nich nahrát jednotlivé soubory. Další informace o programové manipulaci s objekty blob najdete v tématu [rychlý Start: nahrání, stažení a výpis objektů BLOB pomocí rozhraní .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) .

Některá rozhraní API Azure Storage jsou asynchronní a kód v tomto článku předpokládá použití asynchronních metod. Další informace najdete v tématu [asynchronní programování](https://docs.microsoft.com/dotnet/csharp/async) .

## <a name="access-blob-containers-in-code"></a>Přístup k kontejnerům objektů BLOB v kódu

Chcete-li programově přistupovat k objektům blob v ASP.NET Core projekty, je nutné přidat následující kód, pokud ještě neexistuje:

1. Přidejte potřebné příkazy `using`:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Získejte objekt `CloudStorageAccount`, který představuje informace o vašem účtu úložiště. K získání připojovacího řetězce a informací o účtu úložiště z konfigurace služby Azure použijte následující kód:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. K získání odkazu `CloudBlobContainer` na existující kontejner v účtu úložiště použijte objekt `CloudBlobClient`:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Vytvoření kontejneru v kódu

Pomocí `CloudBlobClient` můžete také vytvořit kontejner v účtu úložiště voláním `CreateIfNotExistsAsync`:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Aby byly soubory v kontejneru dostupné všem, nastavte kontejner jako veřejný:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Nahrání objektu blob do kontejneru

Pokud chcete nahrát soubor objektu blob do kontejneru, získejte odkaz na kontejner a použijte ho k získání odkazu na objekt BLOB. Potom do tohoto odkazu nahrajte libovolný proud dat voláním metody `UploadFromStreamAsync`. Tato operace vytvoří objekt blob, pokud ještě neexistuje, a přepíše existující objekt BLOB. 

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

Chcete-li zobrazit seznam objektů BLOB v kontejneru, nejprve získejte odkaz na kontejner a potom zavolejte metodu `ListBlobsSegmentedAsync` pro načtení objektů BLOB a/nebo adresářů v ní. Chcete-li získat přístup k bohatou sadu vlastností a metod vrácených `IListBlobItem`, přetypujte je na objekt `CloudBlockBlob`, `CloudPageBlob` nebo `CloudBlobDirectory`. Pokud neznáte typ objektu blob, použijte kontrolu typu k určení, která z nich se má přetypovat.

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

Další způsoby, jak zobrazit obsah kontejneru objektů blob, najdete v tématu [rychlý Start: nahrání, stažení a výpis objektů BLOB pomocí rozhraní .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) .

## <a name="download-a-blob"></a>Stažení objektu blob

Chcete-li stáhnout objekt blob, nejprve získejte odkaz na objekt BLOB a zavolejte metodu `DownloadToStreamAsync`. Následující příklad používá metodu `DownloadToStreamAsync` pro přenos obsahu objektu blob do objektu Stream, který pak můžete uložit jako místní soubor.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Další způsoby, jak ukládat objekty BLOB jako soubory, najdete v tématu [rychlý Start: nahrání, stažení a výpis objektů BLOB pomocí rozhraní .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) .

## <a name="delete-a-blob"></a>Odstranění objektu blob

Pokud chcete odstranit objekt blob, nejdřív Získejte odkaz na objekt BLOB a potom zavolejte metodu `DeleteAsync`:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
