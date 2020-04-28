---
title: Začínáme s Azure Blob Storage s využitím sady Visual Studio (ASP.NET)
description: Jak začít používat úložiště objektů BLOB v Azure v projektu ASP.NET v aplikaci Visual Studio po připojení k účtu úložiště pomocí připojených služeb sady Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d6591cdb8f19484c87bb05f9007521adc34778f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "72298868"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Začínáme se službou Azure Blob Storage a připojenými službami sady Visual Studio (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob Storage je služba, která ukládá nestrukturovaná data v cloudu jako objekty nebo objekty blob. Do Blob storage se dá ukládat jakýkoli druh textu nebo binárních dat, jako je dokument, soubor médií nebo instalátor aplikace. Blob storage se také nazývá úložiště objektů. Další informace o službě BLOB Storage najdete v tématu [Úvod do úložiště objektů BLOB v Azure](../storage/blobs/storage-blobs-introduction.md).

V tomto kurzu se dozvíte, jak napsat kód ASP.NET pro některé běžné scénáře, které používají úložiště objektů BLOB. Scénáře zahrnují vytvoření kontejneru objektů BLOB a nahrávání, výpisy, stahování a odstraňování objektů BLOB.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Požadavky

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>Vytvoření kontroleru MVC 

1. V **Průzkumník řešení**klikněte pravým tlačítkem na **řadiče**.

2. V místní nabídce vyberte **Přidat** > **kontroler**.

    ![Snímek obrazovky Průzkumník řešení se zvýrazněnou možností přidat a kontroler](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost kontroler **MVC 5 – prázdné**a vyberte **Přidat**.

    ![Snímek obrazovky dialogového okna Přidat generování uživatelského rozhraní](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. V dialogovém okně **Přidat řadič** zadejte název kontroleru *BlobsController*a vyberte **Přidat**.

    ![Snímek obrazovky dialogového okna přidat řadič](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Do `BlobsController.cs` souboru přidejte `using` následující direktivy:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Připojte se k účtu úložiště a získejte odkaz na kontejner.

Kontejner objektů BLOB je vnořená hierarchie objektů BLOB a složek. Zbytek kroků v tomto dokumentu vyžaduje odkaz na kontejner objektů blob, takže by měl být kód umístěn do své vlastní metody pro účely opětovné použitelnosti.

Následující kroky vytvoří metodu pro připojení k účtu úložiště pomocí připojovacího řetězce v **souboru Web. config**. Kroky také vytvoří odkaz na kontejner.  Nastavení připojovacího řetězce v **souboru Web. config** má název ve formátu `<storageaccountname>_AzureStorageConnectionString`. 

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu s názvem **GetCloudBlobContainer** , která vrátí **CloudBlobContainer**.  Nezapomeňte nahradit `<storageaccountname>_AzureStorageConnectionString` skutečným názvem klíče v **souboru Web. config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
                CloudConfigurationManager.GetSetting("<storageaccountname>_AzureStorageConnectionString"));
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> I když *test-BLOB-Container* ještě neexistuje, tento kód vytvoří odkaz na něj. To je proto, že kontejner lze vytvořit pomocí `CreateIfNotExists` metody uvedené v dalším kroku.

## <a name="create-a-blob-container"></a>Vytvoření kontejneru objektů blob

Následující kroky ukazují, jak vytvořit kontejner objektů BLOB:

1. Přidejte metodu s názvem `CreateBlobContainer` , která vrací `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Získejte `CloudBlobContainer` objekt, který představuje odkaz na požadovaný název kontejneru objektů BLOB. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Zavolejte `CloudBlobContainer.CreateIfNotExists` metodu pro vytvoření kontejneru, pokud ještě neexistuje. `CloudBlobContainer.CreateIfNotExists` Metoda vrátí **hodnotu true** , pokud kontejner neexistuje a byl úspěšně vytvořen. V opačném případě metoda vrátí **hodnotu false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Aktualizujte `ViewBag` název kontejneru objektů BLOB.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Následující příklad ukazuje dokončenou `CreateBlobContainer` metodu:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku **zobrazení** .

1. Pokud se nejedná o složku **objektů BLOB** , vytvořte ji. V místní nabídce vyberte **Přidat** > **novou složku**. Pojmenujte nové *objekty blob*složky. 
 
1. V **Průzkumník řešení**rozbalte složku **zobrazení** a klikněte pravým tlačítkem myši na **objekty blob**.

1. V místní nabídce vyberte možnost **Přidat** > **zobrazení**.

1. V dialogovém okně **Přidat zobrazení** jako název zobrazení zadejte **CreateBlobContainer** a vyberte **Přidat**.

1. Otevřete `CreateBlobContainer.cshtml`a upravte jej tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. V **Průzkumník řešení**rozbalte položku**sdílená** složka **zobrazení** > a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Spusťte aplikaci a vyberte **vytvořit kontejner objektů BLOB** pro zobrazení výsledků podobných následujícímu snímku obrazovky:
  
    ![Snímek obrazovky s vytvořením kontejneru objektů BLOB](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Jak bylo zmíněno dříve `CloudBlobContainer.CreateIfNotExists` , metoda vrátí **hodnotu true** pouze v případě, že kontejner neexistuje a je vytvořen. Proto pokud je aplikace spuštěna, když existuje kontejner, metoda vrátí **hodnotu false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Nahrání objektu blob do kontejneru objektů BLOB

Po [vytvoření kontejneru objektů BLOB](#create-a-blob-container)nahrajte do tohoto kontejneru soubory. Tato část vás provede odesláním místního souboru do kontejneru objektů BLOB. Tento postup předpokládá, že existuje kontejner objektů BLOB s názvem *test-BLOB-Container*. 

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu s názvem `UploadBlob` , která vrací řetězec.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. V rámci `UploadBlob` metody Získejte `CloudBlobContainer` objekt, který představuje odkaz na požadovaný název kontejneru objektů BLOB. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Azure Storage podporuje různé typy objektů BLOB. Tento kurz používá objekty blob bloku. Chcete-li načíst odkaz na objekt blob bloku, zavolejte `CloudBlobContainer.GetBlockBlobReference` metodu.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Název objektu BLOB je součástí adresy URL, která se používá k načtení objektu blob, a může to být libovolný řetězec, včetně názvu souboru.

1. Po odkazování na objekt blob můžete do něj nahrát libovolný datový proud voláním `UploadFromStream` metody objektu BLOB reference. `UploadFromStream` Metoda vytvoří objekt blob, pokud neexistuje, nebo ho přepíše, pokud existuje. (Změna * &lt;souboru na nahrání>* na plně kvalifikovanou cestu k souboru, který se má nahrát.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    Následuje ukázka metody Completed `UploadBlob` (s plně kvalifikovanou cestou pro odeslání souboru):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStream(fileStream);
        }
        return "success!";
    }
    ```

1. V **Průzkumník řešení**rozbalte položku**sdílená** složka **zobrazení** > a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Spusťte aplikaci a vyberte **nahrát objekt BLOB**.  Slovo bylo *úspěšné!* by se měla zobrazit.
    
    ![Snímek obrazovky s úspěšným ověřením](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Výpis objektů BLOB v kontejneru objektů BLOB

Tato část ukazuje, jak zobrazit seznam objektů BLOB v kontejneru objektů BLOB. Vzorový kód odkazuje na *kontejner test-BLOB-Container* vytvořený v oddílu a [vytvoří kontejner objektů BLOB](#create-a-blob-container).

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu s názvem `ListBlobs` , která vrací `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. V rámci `ListBlobs` metody Získejte `CloudBlobContainer` objekt, který představuje odkaz na kontejner objektů BLOB. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Chcete-li zobrazit seznam objektů BLOB v kontejneru objektů BLOB `CloudBlobContainer.ListBlobs` , použijte metodu. `CloudBlobContainer.ListBlobs` Metoda vrátí `IListBlobItem` objekt, který lze přetypovat na objekt `CloudBlockBlob`, `CloudPageBlob`nebo. `CloudBlobDirectory` Následující fragment kódu vytvoří výčet všech objektů BLOB v kontejneru objektů BLOB. Každý objekt BLOB je přetypovat na příslušný objekt na základě jeho typu. Jeho název (nebo identifikátor URI v případě **CloudBlobDirectory**) se přidá do seznamu.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs())
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    Kromě objektů BLOB můžou kontejnery objektů BLOB obsahovat adresáře. Předpokládejme, že je k dispozici kontejner objektů BLOB s názvem *test-BLOB-Container*s následující hierarchií:

        foo.png
        dir1/bar.png
        dir2/baz.png

    V předchozím příkladu kódu seznam řetězců **objektů BLOB** obsahuje hodnoty podobné následujícímu:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Jak je znázorněno, seznam obsahuje pouze entity nejvyšší úrovně, nikoli vnořené (*bar. png* a *baz. png*). Chcete-li zobrazit seznam všech entit v rámci kontejneru objektů blob, změňte kód tak, aby metoda **CloudBlobContainer. ListBlobs** byla pro parametr **useFlatBlobListing** předána **true** .    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    Nastavení parametru **useFlatBlobListing** na **hodnotu true** vrátí nestrukturovaný výpis všech entit v kontejneru objektů BLOB. Výsledkem jsou následující výsledky:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    Následující příklad ukazuje dokončenou metodu **ListBlobs** :

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing: true))
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob blob = (CloudPageBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory dir = (CloudBlobDirectory)item;
                blobs.Add(dir.Uri.ToString());
            }
        }

        return View(blobs);
    }
    ```

1. V **Průzkumník řešení**rozbalte složku **zobrazení** a klikněte pravým tlačítkem myši na **objekty blob**.

2. V místní nabídce vyberte možnost **Přidat** > **zobrazení**.

1. V dialogovém okně **Přidat zobrazení** zadejte `ListBlobs` do pole název zobrazení a vyberte **Přidat**.

1. Otevřete `ListBlobs.cshtml`a nahraďte obsah následujícím kódem:

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. V **Průzkumník řešení**rozbalte položku**sdílená** složka **zobrazení** > a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Spusťte aplikaci a vyberte **seznam objektů BLOB** pro zobrazení výsledků podobně jako na následujícím snímku obrazovky:
  
    ![Snímek obrazovky seznamu objektů BLOB](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Stáhnout objekty blob

Tato část ukazuje, jak stáhnout objekt BLOB. Můžete ji buď zachovat v místním úložišti, nebo si obsah přečíst do řetězce. Vzorový kód odkazuje na *kontejner test-BLOB-Container* vytvořený v oddílu a [vytvoří kontejner objektů BLOB](#create-a-blob-container).

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu s názvem `DownloadBlob` , která vrací řetězec.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. V rámci `DownloadBlob` metody Získejte `CloudBlobContainer` objekt, který představuje odkaz na kontejner objektů BLOB.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Získejte odkazový objekt objektu BLOB voláním `CloudBlobContainer.GetBlockBlobReference` metody. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Pro stažení objektu BLOB použijte `CloudBlockBlob.DownloadToStream` metodu. Následující kód přenáší obsah objektu blob do objektu Stream. Tento objekt je pak uložený do místního souboru. (Změňte * &lt;místní-soubor-Name>* na plně kvalifikovaný název souboru, který představuje, kam se má objekt BLOB stáhnout.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    Následuje ukázka metody Completed `DownloadBlob` (s plně kvalifikovanou cestou pro místní soubor, který se vytváří):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStream(fileStream);
        }
        return "success!";
    }
    ```

1. V **Průzkumník řešení**rozbalte položku**sdílená** složka **zobrazení** > a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Spusťte aplikaci a vyberte **Stáhnout objekt BLOB** pro stažení objektu BLOB. Objekt BLOB zadaný ve volání `CloudBlobContainer.GetBlockBlobReference` metody se stahuje do umístění zadaného ve volání `File.OpenWrite` metody.  Text byl *úspěšný.* měl by se zobrazit v prohlížeči. 

## <a name="delete-blobs"></a>Odstranění objektů blob

Následující postup ukazuje, jak odstranit objekt BLOB:

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu s názvem `DeleteBlob` , která vrací řetězec.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. V rámci `DeleteBlob` metody Získejte `CloudBlobContainer` objekt, který představuje odkaz na kontejner objektů BLOB.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Získejte odkazový objekt objektu BLOB voláním `CloudBlobContainer.GetBlockBlobReference` metody. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. K odstranění objektu BLOB použijte `Delete` metodu.

    ```csharp
    blob.Delete();
    ```
    
    Metoda Completed `DeleteBlob` by měla vypadat takto:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. V **Průzkumník řešení**rozbalte položku**sdílená** složka **zobrazení** > a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Spusťte aplikaci a vyberte **Odstranit objekt BLOB** pro odstranění objektu BLOB zadaného ve volání `CloudBlobContainer.GetBlockBlobReference` metody. Text byl *úspěšný.* měl by se zobrazit v prohlížeči. Vyberte tlačítko **zpět** v prohlížeči a pak vyberte **seznam objektů BLOB** , abyste ověřili, že objekt BLOB už není v kontejneru.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak ukládat, vypisovat a načítat objekty BLOB v Azure Storage pomocí ASP.NET. Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.

  * [Začínáme s Azure Table Storage a připojenými službami sady Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Začínáme s Azure Queue Storage a připojenými službami sady Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
