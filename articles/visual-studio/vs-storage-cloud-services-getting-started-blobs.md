---
title: Začínáme s objektem blob storage a Visual Studio připojené služby (cloudové služby) | Microsoft Docs
description: Jak začít používat úložiště objektů Blob v Azure v projektu cloudové služby v sadě Visual Studio po připojení k účtu úložiště pomocí sady Visual Studio připojené služby
services: storage
author: ghogen
manager: douge
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 2df69d9f180303f6c14433140d862c017b97148d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Začínáme s Azure Blob Storage a Visual Studio připojené služby (projekty cloudových služeb)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Přehled
Tento článek popisuje, jak začít pracovat s Azure Blob Storage po vytvoření nebo odkazuje účet úložiště Azure pomocí sady Visual Studio **přidat připojení služby** dialogové okno v sadě Visual Studio cloudové služby projektu. Ukážeme vám přístupu a vytvoření kontejnerů objektů blob a jak provádět běžné úkoly, jako odesílání, výpis a stahování objekty BLOB. Ukázky jsou napsané v jazyce C\# a použít [Microsoft Azure Storage Client Library pro .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Azure Blob Storage je služba pro ukládání velkého objemu nestrukturovaných dat, která je přístupná z kdekoliv na světě prostřednictvím protokolu HTTP nebo HTTPS. Jediného objektu blob může mít libovolnou velikost. Objekty BLOB může být třeba bitové kopie, soubory audia a videa, nezpracovaná data a soubory dokumentu.

Stejně jako soubory live ve složkách, za provozu v kontejnerech objektů BLOB storage. Po vytvoření úložiště můžete vytvořit jeden nebo více kontejnerů v úložišti. Například v úložiště, který se nazývá "Výstřižků", vytvoříte kontejnerů v úložišti názvem "Image" k ukládání obrázků a jiné názvem "zvuk" k uložení zvukových souborů. Po vytvoření kontejnerů, můžete odeslat soubory jednotlivých objektů blob k nim.

* Další informace o programu manipulace s objekty BLOB najdete v tématu [Začínáme s Azure Blob storage pomocí rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).
* Obecné informace o službě Azure Storage najdete v tématu [úložiště dokumentace](https://azure.microsoft.com/documentation/services/storage/).
* Obecné informace o Azure Cloud Services najdete v tématu [cloudové služby dokumentaci](https://azure.microsoft.com/documentation/services/cloud-services/).
* Další informace o programování aplikace ASP.NET najdete v tématu [ASP.NET](http://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Kontejnery objektů blob přístup v kódu
K programovému přístupu ke objektů BLOB v projekty cloudových služeb, musíte přidat následující položky, pokud nejsou již existuje.

1. Přidejte následující deklarace oborů názvů kódu do horní části souboru žádné C# ve které chcete k programovému přístupu ke službě Azure Storage.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Získání **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použít následující kód k získání připojovací řetězec úložiště a informace o účtu úložiště z konfigurace služby Azure.
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
3. Získání **CloudBlobClient** objekt, který má odkazovat na existující kontejner ve vašem účtu úložiště.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
4. Získání **CloudBlobContainer** objekt, který má odkazovat na konkrétní objekt blob kontejneru.
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> Použijte všechny uvedeném v předchozím postupu před uvedeném v následujících částech kódu kódu.
> 
> 

## <a name="create-a-container-in-code"></a>Vytvořit kontejner v kódu
> [!NOTE]
> Některé rozhraní API, které provádět volání se do služby Azure Storage v ASP.NET jsou asynchronní. V tématu [asynchronní programování s Async a Await](http://msdn.microsoft.com/library/hh191443.aspx) Další informace. Kód v následujícím příkladu se předpokládá, že používáte asynchronní programování metody.
> 
> 

Pokud chcete vytvořit kontejner ve vašem účtu úložiště, musíte udělat je přidáte volání **CreateIfNotExistsAsync** jako v následujícím kódu:

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


Soubory v kontejneru zpřístupnit všem uživatelům, můžete nastavit kontejner, aby se veřejné pomocí následující kód.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Kdokoli na Internetu může vidět objekty BLOB ve veřejném kontejneru, ale můžete upravit nebo odstranit pouze v případě, že máte příslušný přístupový klíč.

## <a name="upload-a-blob-into-a-container"></a>Nahrání objektu blob do kontejneru
Úložiště Azure podporuje objekty BLOB bloku a objekty BLOB stránky. Ve většině případů se jako vhodný typ k použití doporučuje objekt blob bloku.

Když chcete nahrát soubor do objektu blob bloku, získejte odkaz na kontejner a použijte ho k získání odkazu objektu blob bloku. Jakmile získáte odkaz na objekt blob, můžete k němu nahrát jakýkoli proud dat voláním metody **UploadFromStream**. Tahle operace vytvoří objekt blob, pokud ještě dříve neexistoval, nebo ho přepíše, pokud už existoval. Následující příklad ukazuje, jak nahrát objekt blob do kontejneru, zároveň předpokládá, že kontejner byl již vytvořen.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru
Pokud chcete mít seznam objektů blob v kontejneru, nejdřív získejte odkaz na kontejner. Pak můžete použít metodu kontejneru **ListBlobs** a načíst objekty blob a/nebo obsažené adresáře. Pro přístup k bohaté sadě vlastností a metod vrácené **IListBlobItem**, musíte vysílat na **CloudBlockBlob**, **CloudPageBlob**, nebo  **CloudBlobDirectory** objektu. Pokud je typ neznámý, můžete použít kontrolu typu a zjistit, na který typ vysílat. Následující kód ukazuje, jak načíst a na výstupu zobrazit identifikátor URI pro každou položku v kontejneru **photos**:

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

Jak je znázorněno v předchozí ukázce kódu, služby objektů blob obsahuje koncepci adresáře v rámci kontejnery také. Toto je tak, aby můžete uspořádat, objektů BLOB do více stromové struktury. Můžete zvolit například následující sadu objektů blob bloku v kontejneru s názvem **photos**:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Při volání **ListBlobs** na kontejneru (jako v předchozím příkladu), vrátí kolekce obsahuje **CloudBlobDirectory** a **CloudBlockBlob** objekty představují adresáře a objekty BLOB obsažené na nejvyšší úrovni. Zde je výsledný výstup:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Volitelně můžete nastavit parametr **UseFlatBlobListing** metody **ListBlobs** na hodnotu **pravda**. Výsledkem je každý objekt blob se vrátí jako **CloudBlockBlob**, bez ohledu na to adresáře. Tady je volání **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

a tady jsou výsledky:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Další informace najdete v tématu [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

## <a name="download-blobs"></a>Stáhnout objekty blob
Když chcete stáhnout objekty blob, nejdřív načtěte odkaz objektu blob a potom spusťte volání metody **DownloadToStream**. Následující příklad používá metodu **DownloadToStream** k přenosu obsahu objektu blob na objekt proudu, který potom můžete zachovat trvale v místním souboru.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Můžete také použít metodu **DownloadToStream** a stáhnout obsah objektu blob jako textový řetězec.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Odstranění objektů blob
Chcete-li odstranit objekt blob, nejdřív získejte odkaz na objekt blob a potom volejte **odstranit** metoda.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Asynchronní zobrazení seznamu objektů blob na stránkách
Když provádíte výpis velkého počtu objektů blob nebo chcete mít přehled o počtu výsledků, které vrátíte v rámci jedné operace výpisu, můžete vytvořit seznam objektů blob na stránkách s výsledky. Tento příklad ukazuje, jak vracet výsledky na stránkách asynchronně tak, aby čekání na vrácení velké sady výsledků neblokovalo provádění.

Tento příklad ukazuje plochý výpis objektu blob, můžete ale také provést hierarchický výpis nastavením parametru **useFlatBlobListing** metody **ListBlobsSegmentedAsync** na **false**.

Vzhledem k tomu, že metoda ukázky volá asynchronní metodu, musí být uvedena klíčovým slovem **async** a musí vrátit objekt **Úloha**. Klíčové slovo await, určené pro metodu **ListBlobsSegmentedAsync** pozastaví spuštění metody ukázky až do dokončení úlohy vytváření seznamu.

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

## <a name="next-steps"></a>Další postup
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

