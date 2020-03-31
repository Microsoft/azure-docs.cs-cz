---
title: Začínáme s úložištěm objektů blob pomocí Sady Visual Studio (cloudové služby)
description: Jak začít používat úložiště objektů blob Azure v projektu cloudové služby v sadě Visual Studio po připojení k účtu úložiště pomocí připojených služeb Visual Studia
services: storage
author: ghogen
manager: jillfra
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d151e55f627166d8ad7d8affa53740e86cd1e501
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298808"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Začínáme s Azure Blob Storage a připojenými službami sady Visual Studio (projekty cloudových služeb)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Přehled
Tento článek popisuje, jak začít s Azure Blob Storage po vytvoření nebo odkazování na účet úložiště Azure pomocí dialogového okna **Přidat připojené služby** Visual Studio v projektu cloudových služeb Visual Studio. Ukážeme vám, jak přistupovat k kontejnerům objektů blob a vytvářet je a jak provádět běžné úlohy, jako je nahrávání, výpis a stahování objektů BLOB. Ukázky jsou zapsány v C\# a používají [knihovnu klienta úložiště Microsoft Azure pro .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Azure Blob Storage je služba pro ukládání velkého množství nestrukturovaných dat, ke kterým se dá přistupovat z libovolného místa na světě prostřednictvím protokolu HTTP nebo HTTPS. Jeden objekt blob může mít libovolnou velikost. Objekty BLOB mohou být například obrázky, zvukové soubory a soubory videa, nezpracovaná data a soubory dokumentů.

Stejně jako soubory žijí ve složkách, objekty BLOB úložiště žijí v kontejnerech. Po vytvoření úložiště vytvoříte jeden nebo více kontejnerů v úložišti. Například v úložišti s názvem "Scrapbook", můžete vytvořit kontejnery v úložišti s názvem "obrázky" pro ukládání obrázků a další s názvem "audio" pro ukládání zvukových souborů. Po vytvoření kontejnerů do nich můžete nahrát jednotlivé soubory objektů blob.

* Další informace o programové manipulaci s objekty BLOB najdete [v tématu Začínáme s úložištěm objektů blob Azure pomocí .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).
* Obecné informace o Azure Storage najdete v [dokumentaci k úložišti](https://azure.microsoft.com/documentation/services/storage/).
* Obecné informace o cloudových službách Azure najdete v [dokumentaci ke cloudovým službám](https://azure.microsoft.com/documentation/services/cloud-services/).
* Další informace o programování aplikací ASP.NET naleznete v [tématu ASP.NET](https://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Přístup k kontejnerům objektů blob v kódu
Chcete-li programově přistupovat k objektům BLOB v projektech cloudových služeb, musíte přidat následující položky, pokud ještě nejsou k dispozici.

1. Přidejte následující deklarace oboru názvů kódu na začátek libovolného souboru C#, ve kterém chcete programově přistupovat k úložišti Azure Storage.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Získejte objekt **CloudStorageAccount,** který představuje informace o vašem účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci úložiště a účtu úložiště z konfigurace služby Azure.
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
3. Získejte objekt **CloudBlobClient,** který odkazuje na existující kontejner ve vašem účtu úložiště.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
4. Získejte objekt **CloudBlobContainer,** který odkazuje na konkrétní kontejner objektů blob.
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> Použijte celý kód zobrazený v předchozím postupu před kódem zobrazeným v následujících částech.
> 
> 

## <a name="create-a-container-in-code"></a>Vytvoření kontejneru v kódu
> [!NOTE]
> Některá api, která provádějí volání služby Azure Storage v ASP.NET jsou asynchronní. Další informace naleznete [v tématu Asynchronní programování s Async a Await.](https://msdn.microsoft.com/library/hh191443.aspx) Kód v následujícím příkladu předpokládá, že používáte asynchronní programovací metody.
> 
> 

Chcete-li vytvořit kontejner ve vašem účtu úložiště, stačí přidat volání **CreateIfNotExistsAsync** jako v následujícím kódu:

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


Chcete-li zpřístupnit soubory v kontejneru všem uživatelům, můžete nastavit, aby byl kontejner veřejný, pomocí následujícího kódu.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Kdokoli na Internetu může zobrazit objekty BLOB ve veřejném kontejneru, ale můžete je upravit nebo odstranit pouze v případě, že máte příslušný přístupový klíč.

## <a name="upload-a-blob-into-a-container"></a>Nahrání objektu blob do kontejneru
Azure Storage podporuje objekty BLOB bloku a objekty BLOB stránky. Ve většině případů se jako vhodný typ k použití doporučuje objekt blob bloku.

Když chcete nahrát soubor do objektu blob bloku, získejte odkaz na kontejner a použijte ho k získání odkazu objektu blob bloku. Jakmile získáte odkaz na objekt blob, můžete k němu nahrát jakýkoli proud dat voláním metody **UploadFromStream**. Tahle operace vytvoří objekt blob, pokud ještě dříve neexistoval, nebo ho přepíše, pokud už existoval. Následující příklad ukazuje, jak nahrát objekt blob do kontejneru, zároveň předpokládá, že kontejner byl již vytvořen.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru
Pokud chcete mít seznam objektů blob v kontejneru, nejdřív získejte odkaz na kontejner. Pak můžete použít metodu kontejneru **ListBlobs** a načíst objekty blob a/nebo obsažené adresáře. Chcete-li získat přístup k bohaté sadě vlastností a metod pro vrácenou **položku IListBlobItem**, musíte ji přetypovat do objektu **CloudBlockBlob**, **CloudPageBlob**nebo **CloudBlobDirectory.** Pokud je typ neznámý, můžete použít kontrolu typu a zjistit, na který typ vysílat. Následující kód ukazuje, jak načíst a na výstupu zobrazit identifikátor URI pro každou položku v kontejneru **photos**:

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

Jak je znázorněno v předchozí ukázce kódu, služba objektu blob má také koncept adresářů v kontejnerech. To je tak, že můžete uspořádat objekty BLOB ve struktuře více jako složka. Můžete zvolit například následující sadu objektů blob bloku v kontejneru s názvem **photos**:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Při volání **ListBlobs** na kontejneru (stejně jako v předchozí ukázce), vrácené kolekce obsahuje **CloudBlobDirectory** a **CloudBlockBlob** objekty představující adresáře a objekty BLOB obsažené na nejvyšší úrovni. Zde je výsledný výstup:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Volitelně můžete nastavit parametr **UseFlatBlobListing** metody **ListBlobs** na hodnotu **true**. To má za následek každý objekt blob, který se vrací jako **CloudBlockBlob**, bez ohledu na adresář. Zde je volání **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

a zde jsou výsledky:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Další informace naleznete v tématu [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

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
Chcete-li odstranit objekt blob, nejprve získat odkaz na objekt blob a potom volat **Delete** metoda.

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

## <a name="next-steps"></a>Další kroky
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

