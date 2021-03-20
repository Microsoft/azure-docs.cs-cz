---
title: Začínáme s úložištěm objektů BLOB pomocí sady Visual Studio (cloudové služby)
description: Jak začít používat úložiště objektů BLOB v Azure v projektu cloudové služby v aplikaci Visual Studio po připojení k účtu úložiště pomocí připojených služeb sady Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 89e0d6873ebfd8f8396c36185730c57a66af0dd9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96007021"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Začínáme s Azure Blob Storage a připojenými službami sady Visual Studio (projekty cloudových služeb)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Přehled
Tento článek popisuje, jak začít s Azure Blob Storage po vytvoření nebo odkazování Azure Storage účtu pomocí dialogového okna **Přidat připojené služby** sady Visual Studio v projektu cloudové služby Visual Studio. Ukážeme vám, jak používat a vytvářet kontejnery objektů BLOB a jak provádět běžné úlohy, jako je nahrávání, výpis a stahování objektů BLOB. Ukázky jsou napsané v jazyce C \# a používají [Microsoft Azure Storage klientské knihovny pro .NET](/previous-versions/azure/dn261237(v=azure.100)).

Azure Blob Storage je služba pro ukládání velkých objemů nestrukturovaných dat, ke kterým se dá dostat odkudkoli na světě přes HTTP nebo HTTPS. Jeden objekt BLOB může být libovolná velikost. Objekty blob můžou být například obrázky, zvukové soubory a videosoubory, nezpracovaná data a soubory dokumentů.

Stejně jako soubory v reálném čase ve složkách jsou objekty blob úložiště živé v kontejnerech. Po vytvoření úložiště můžete v úložišti vytvořit jeden nebo více kontejnerů. Například v úložišti s názvem "Scrapbook" můžete vytvořit kontejnery v úložišti s názvem "image" k ukládání obrázků a další s názvem "zvuk" pro ukládání zvukových souborů. Po vytvoření kontejnerů můžete do nich nahrát jednotlivé soubory objektů BLOB.

* Další informace o programové manipulaci s objekty blob najdete v tématu Začínáme [s úložištěm objektů BLOB v Azure pomocí .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md).
* Obecné informace o Azure Storage najdete v [dokumentaci k úložišti](https://azure.microsoft.com/documentation/services/storage/).
* Obecné informace o Azure Cloud Services najdete v [dokumentaci k Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/).
* Další informace o programování aplikací ASP.NET naleznete v tématu [ASP.NET](https://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Přístup k kontejnerům objektů BLOB v kódu
Chcete-li programově přistupovat k objektům blob v projektech cloudové služby, je nutné přidat následující položky, pokud již nejsou přítomny.

1. Přidejte následující deklarace oboru názvů kódu na začátek jakéhokoli souboru C#, ve kterém chcete programově získat přístup k Azure Storage.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. K získání připojovacího řetězce a informací o účtu úložiště z konfigurace služby Azure použijte následující kód.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
    ```
3. Získejte objekt **CloudBlobClient** , který bude odkazovat na existující kontejner v účtu úložiště.
   
    ```csharp
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```
4. Získá objekt **CloudBlobContainer** , který odkazuje na konkrétní kontejner objektů BLOB.
   
    ```csharp
    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

> [!NOTE]
> Použijte veškerý kód uvedený v předchozím postupu před kódem zobrazeným v následujících částech.
> 
> 

## <a name="create-a-container-in-code"></a>Vytvoření kontejneru v kódu
> [!NOTE]
> Některá rozhraní API, která provádějí volání na Azure Storage v ASP.NET, jsou asynchronní. Další informace najdete v tématu [asynchronní programování pomocí Async a operátoru await](/previous-versions/hh191443(v=vs.140)) . Kód v následujícím příkladu předpokládá, že používáte metody asynchronního programování.
> 
> 

Aby bylo možné vytvořit kontejner v účtu úložiště, stačí přidat volání **CreateIfNotExistsAsync** jako v následujícím kódu:

```csharp
// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```


Chcete-li zpřístupnit soubory v kontejneru všem uživatelům, můžete nastavit, aby byl kontejner veřejný, pomocí následujícího kódu.

```csharp
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```


Kdokoli na internetu může vidět objekty blob ve veřejném kontejneru, ale můžete je upravit nebo odstranit jenom v případě, že máte příslušný přístupový klíč.

## <a name="upload-a-blob-into-a-container"></a>Nahrání objektu blob do kontejneru
Azure Storage podporuje objekty blob bloku a objekty blob stránky. Ve většině případů se jako vhodný typ k použití doporučuje objekt blob bloku.

Když chcete nahrát soubor do objektu blob bloku, získejte odkaz na kontejner a použijte ho k získání odkazu objektu blob bloku. Jakmile získáte odkaz na objekt blob, můžete k němu nahrát jakýkoli proud dat voláním metody **UploadFromStream**. Tahle operace vytvoří objekt blob, pokud ještě dříve neexistoval, nebo ho přepíše, pokud už existoval. Následující příklad ukazuje, jak nahrát objekt blob do kontejneru, zároveň předpokládá, že kontejner byl již vytvořen.

```csharp
// Retrieve a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    blockBlob.UploadFromStream(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Seznam objektů blob v kontejneru
Pokud chcete mít seznam objektů blob v kontejneru, nejdřív získejte odkaz na kontejner. Pak můžete použít metodu kontejneru **ListBlobs** a načíst objekty blob a/nebo obsažené adresáře. Chcete-li získat přístup k bohatě se sadou vlastností a metod pro vrácenou **položky ilistblobitem**, je nutné ji přetypovat na objekt **CloudBlockBlob**, **CloudPageBlob** nebo **CloudBlobDirectory** . Pokud je typ neznámý, můžete použít kontrolu typu a zjistit, na který typ vysílat. Následující kód ukazuje, jak načíst a na výstupu zobrazit identifikátor URI pro každou položku v kontejneru **photos**:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, false))
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
```

Jak je znázorněno v předchozí ukázce kódu, služba BLOB má také koncept adresářů v kontejnerech. To je tak, abyste mohli objekty blob uspořádat do více struktur podobných složek. Můžete zvolit například následující sadu objektů blob bloku v kontejneru s názvem **photos**:

```output
photo1.jpg
2010/architecture/description.txt
2010/architecture/photo3.jpg
2010/architecture/photo4.jpg
2011/architecture/photo5.jpg
2011/architecture/photo6.jpg
2011/architecture/description.txt
2011/photo7.jpg
```

Když zavoláte **ListBlobs** na kontejneru (jako v předchozím příkladu), vrácená kolekce obsahuje objekty **CloudBlobDirectory** a **CloudBlockBlob** reprezentující adresáře a objekty blob, které jsou obsaženy na nejvyšší úrovni. Tady je výsledný výstup:

```output
Directory: https://<accountname>.blob.core.windows.net/photos/2010/
Directory: https://<accountname>.blob.core.windows.net/photos/2011/
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```


Volitelně můžete nastavit parametr **UseFlatBlobListing** metody **ListBlobs** na hodnotu **true**. Výsledkem je, že se každý objekt BLOB vrací jako **CloudBlockBlob** bez ohledu na adresář. Toto je volání **ListBlobs**:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, true))
{
    ...
}
```

a tady jsou výsledky:

```output
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

Další informace najdete v tématu [CloudBlobContainer. ListBlobs](/rest/api/storageservices/List-Blobs).

## <a name="download-blobs"></a>Stáhnout objekty blob
Když chcete stáhnout objekty blob, nejdřív načtěte odkaz objektu blob a potom spusťte volání metody **DownloadToStream**. Následující příklad používá metodu **DownloadToStream** k přenosu obsahu objektu blob na objekt proudu, který potom můžete zachovat trvale v místním souboru.

```csharp
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save blob contents to a file.
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    blockBlob.DownloadToStream(fileStream);
}
```

Můžete také použít metodu **DownloadToStream** a stáhnout obsah objektu blob jako textový řetězec.

```csharp
// Get a reference to a blob named "myblob.txt"
CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

string text;
using (var memoryStream = new MemoryStream())
{
    blockBlob2.DownloadToStream(memoryStream);
    text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
}
```

## <a name="delete-blobs"></a>Odstraňovat objekty blob
Pokud chcete odstranit objekt blob, nejdřív Získejte odkaz na objekt BLOB a potom zavolejte metodu **Delete** .

```csharp
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
blockBlob.Delete();
```


## <a name="list-blobs-in-pages-asynchronously"></a>Asynchronní zobrazení seznamu objektů blob na stránkách
Když provádíte výpis velkého počtu objektů blob nebo chcete mít přehled o počtu výsledků, které vrátíte v rámci jedné operace výpisu, můžete vytvořit seznam objektů blob na stránkách s výsledky. Tento příklad ukazuje, jak vracet výsledky na stránkách asynchronně tak, aby čekání na vrácení velké sady výsledků neblokovalo provádění.

Tento příklad ukazuje plochý výpis objektu blob, můžete ale také provést hierarchický výpis nastavením parametru **useFlatBlobListing** metody **ListBlobsSegmentedAsync** na **false**.

Vzhledem k tomu, že metoda ukázky volá asynchronní metodu, musí být uvedena klíčovým slovem **async** a musí vrátit objekt **Úloha**. Klíčové slovo await, určené pro metodu **ListBlobsSegmentedAsync** pozastaví spuštění metody ukázky až do dokončení úlohy vytváření seznamu.

```csharp
async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
{
    // List blobs to the console window, with paging.
    Console.WriteLine("List blobs in pages:");

    int i = 0;
    BlobContinuationToken continuationToken = null;
    BlobResultSegment resultSegment = null;

    // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    // When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        // or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

## <a name="next-steps"></a>Další kroky
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]