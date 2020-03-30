---
title: Začínáme s úložištěm objektů blob Azure pomocí Visual Studia (ASP.NET Core)
description: Jak začít používat úložiště objektů blob Azure v projektu ASP.NET Core v sadě Visual Studio po připojení k účtu úložiště pomocí připojených služeb Visual Studia
services: storage
documentationcenter: ''
author: ghogen
manager: jillfra
editor: ''
ms.service: storage
ms.workload: web
ms.custom: vs-azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff221a32ff6c995d019b13f20ca2c3f9e2027f63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980738"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Začínáme s úložištěm objektů blob Azure a připojenými službami Visual Studia (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob storage je služba, která ukládá nestrukturovaná data v cloudu jako objekty nebo objekty blob. Do Blob storage se dá ukládat jakýkoli druh textu nebo binárních dat, jako je dokument, soubor médií nebo instalátor aplikace. Blob storage se také nazývá úložiště objektů. Další informace o úložišti objektů Blob najdete [v tématu Úvod do úložiště objektů blob Azure](../storage/blobs/storage-blobs-introduction.md).

Tento kurz ukazuje, jak psát ASP.NET základní kód pro některé běžné scénáře, které používají úložiště objektů blob. Scénáře zahrnují vytvoření kontejneru objektů blob a nahrávání, výpis, stahování a odstranění objektů BLOB.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Požadavky

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

## <a name="set-up-the-development-environment"></a>Nastavení vývojového prostředí

Tato část prochází nastavení vývojového prostředí. To zahrnuje vytvoření aplikace ASP.NET Model-View-Controller (MVC), přidání připojení připojených služeb, přidání řadiče a určení požadovaných směrnic oboru názvů.

### <a name="create-an-aspnet-mvc-app-project"></a>Vytvoření ASP.NET projektu aplikace MVC

1. Otevřete sadu Visual Studio.

1. V hlavní nabídce vyberte **Soubor** > **nový** > **projekt**.

1. V dialogovém okně **Nový projekt** vyberte **možnost Web** > **ASP.NET Core Web Application** > **AspNetCoreStorage**. Pak vyberte **OK**.

    ![Snímek obrazovky s dialogovém oknem Nový projekt Visual Studia](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. V dialogovém **okně Nová ASP.NET základní webová aplikace** vyberte **položku .NET Core** > ASP.NET Webová aplikace**Core 2.0** > **(Model-View-Controller).** Pak vyberte **OK**.

    ![Snímek obrazovky s dialogovým oknem Nová ASP.NET základní webová aplikace](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Připojení k účtu úložiště Azure pomocí připojených služeb

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt.

2. V místní nabídce vyberte **Přidat** > **připojenou službu**.

1. V dialogovém okně **Připojené služby** vyberte **Cloudové úložiště s Azure Storage**a pak vyberte **Konfigurovat**.

    ![Snímek obrazovky s dialogovým oknem Připojené služby](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. V dialogovém okně **Azure Storage** vyberte účet úložiště Azure, který se použije pro tento kurz. Pokud chcete vytvořit nový účet úložiště Azure, vyberte **Vytvořit nový účet úložiště**a vyplňte formulář. Po výběru existujícího účtu úložiště nebo vytvoření nového účtu vyberte **Přidat**. Visual Studio nainstaluje balíček NuGet pro Azure Storage a připojovací řetězec úložiště do **appsettings.json**.

> [!TIP]
> Informace o tom, jak vytvořit účet úložiště na [webu Azure Portal](https://portal.azure.com), najdete v [tématu Vytvoření účtu úložiště](../storage/common/storage-account-create.md).
>
> Účet úložiště můžete také vytvořit pomocí [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure CLI](../storage/common/storage-azure-cli.md)nebo [Azure Cloud Shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Vytvoření řadiče MVC 

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **položku Řadiče**.

2. V místní nabídce vyberte **Přidat** > **řadič**.

    ![Snímek obrazovky Průzkumníka řešení](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. V dialogovém okně **Přidat kancovací okno** vyberte **MVC Controller - Empty**a vyberte **Přidat**.

    ![Snímek obrazovky s dialogovým oknem Přidat pomocí uživatelského rozhraní](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. V dialogovém okně **Přidat prázdný řadič MVC** pojmenujte objekt *BlobsController*řadiče a vyberte **Přidat**.

    ![Snímek obrazovky s dialogovým oknem Přidat prázdný řadič MVC](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Do souboru přidejte následující `using` direktivy: `BlobsController.cs`

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Připojení k účtu úložiště a získání odkazu na kontejner

Kontejner objektů blob je vnořená hierarchie objektů BLOB a složek. Zbývající kroky v tomto dokumentu vyžadují odkaz na kontejner objektů blob, takže kód by měl být umístěn ve vlastní metodě pro opětovné použití.

Následující kroky vytvoří metodu pro připojení k účtu úložiště pomocí připojovacího řetězce v **souboru appsettings.json**. Kroky také vytvořit odkaz na kontejner. Nastavení připojovacího řetězce v **souboru appsettings.json** je pojmenováno podle formátu `<storageaccountname>_AzureStorageConnectionString`. 

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu s názvem **GetCloudBlobContainer,** která vrací **CloudBlobContainer**. Nezapomeňte nahradit `<storageaccountname>_AzureStorageConnectionString` skutečný název klíče v **souboru Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> I když *testovací objekt blob ještě* neexistuje, tento kód vytvoří odkaz na něj. To je tak, že kontejner `CreateIfNotExists` lze vytvořit s metodou uvedenou v dalším kroku.

## <a name="create-a-blob-container"></a>Vytvoření kontejneru objektů blob

Následující kroky ilustrují, jak vytvořit kontejner objektů blob:

1. Přidejte metodu, `CreateBlobContainer` `ActionResult`která vrací .

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Získejte `CloudBlobContainer` objekt, který představuje odkaz na požadovaný název kontejneru objektů blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Volání `CloudBlobContainer.CreateIfNotExists` metody k vytvoření kontejneru, pokud ještě neexistuje. Metoda `CloudBlobContainer.CreateIfNotExists` vrátí **hodnotu true,** pokud kontejner neexistuje a je úspěšně vytvořen. V opačném případě metoda vrátí **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Aktualizujte `ViewBag` názvem kontejneru objektů blob.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Následující ukazuje dokončenou `CreateBlobContainer` metodu:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na složku **Zobrazení.**

2. V místní nabídce vyberte **Přidat** > **novou složku**. Pojmenujte novou složku *Objektů blob .* 

1. V **Průzkumníku řešení**rozbalte složku **Zobrazení** a klepněte pravým tlačítkem myši na **objekty BLOB**.

4. V místní nabídce vyberte **Přidat** > **zobrazení**.

1. V dialogovém okně **Přidat zobrazení** zadejte pro název zobrazení příkaz **CreateBlobContainer** a vyberte **Přidat**.

1. Otevřete `CreateBlobContainer.cshtml`a upravte tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. V **Průzkumníku řešení**rozbalte složku `_Layout.cshtml` **Zobrazení** > **sdílené** a otevřete .

1. Vyhledejte neuspořádaný seznam, který `<ul class="nav navbar-nav">`vypadá takto: .  Za poslední `<li>` prvek v seznamu přidejte následující kód HTML a přidejte další položku navigační nabídky:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Spusťte aplikaci a vyberte **Vytvořit kontejner objektů blob,** abyste viděli výsledky podobné následujícímu snímku obrazovky:
  
    ![Snímek obrazovky vytvořit kontejner objektů blob](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Jak již bylo `CloudBlobContainer.CreateIfNotExists` zmíněno dříve, metoda vrátí **true** pouze v případě, že kontejner neexistuje a je vytvořen. Proto pokud je aplikace spuštěna, když existuje kontejner, metoda vrátí **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Nahrání objektu blob do kontejneru objektů blob

Při [vytvoření kontejneru objektů blob](#create-a-blob-container)nahrajte soubory do tohoto kontejneru. Tato část vás provede nahráním místního souboru do kontejneru objektů blob. Kroky předpokládají, že je kontejner objektů blob s názvem *test-blob-container*. 

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu, `UploadBlob` která vrací řetězec.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. V `UploadBlob` rámci metody `CloudBlobContainer` získat objekt, který představuje odkaz na požadovaný název kontejneru objektu blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Azure storage podporuje různé typy objektů blob. Tento kurz používá objekty BLOB bloku. Chcete-li načíst odkaz na objekt `CloudBlobContainer.GetBlockBlobReference` blob bloku, zavolejte metodu.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Název objektu blob je součástí adresy URL použité k načtení objektu blob a může být libovolný řetězec, včetně názvu souboru.

1. Poté, co je odkaz na objekt blob, můžete nahrát libovolný datový `UploadFromStream` proud do něj voláním metody objektu odkazu objektu objektu. Metoda `UploadFromStream` vytvoří objekt blob, pokud neexistuje, nebo ji přepíše, pokud existuje. (Změňte * &lt;>nahrávání souboru* na plně kvalifikovanou cestu k souboru, který má být odeslán.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    Následující ukazuje dokončenou `UploadBlob` metodu (s plně kvalifikovanou cestou pro soubor, který má být odeslán):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. V **Průzkumníku řešení**rozbalte složku `_Layout.cshtml` **Zobrazení** > **sdílené** a otevřete .

1. Za poslední `<li>` prvek v seznamu přidejte následující kód HTML a přidejte další položku navigační nabídky:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Spusťte aplikaci a vyberte **Nahrát objekt blob**. Slovo *úspěch!* by se měly objevit.
    
    ![Snímek obrazovky s ověřením úspěchu](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Seznam objektů BLOB v kontejneru objektů blob

Tato část ukazuje, jak seznam objektů BLOB v kontejneru objektů blob. Ukázkový kód odkazuje na *testovací kontejner objektů blob* vytvořený v části [Create a blob container](#create-a-blob-container).

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu, `ListBlobs` `ActionResult`která vrací .

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. V `ListBlobs` rámci metody `CloudBlobContainer` získat objekt, který představuje odkaz na kontejner objektů blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Chcete-li seznam objektů BLOB v kontejneru objektů blob, použijte metodu. `CloudBlobContainer.ListBlobsSegmentedAsync` Metoda `CloudBlobContainer.ListBlobsSegmentedAsync` vrátí `BlobResultSegment`. Obsahuje `IListBlobItem` objekty, které `CloudBlockBlob`lze `CloudPageBlob`přetypovat do , nebo `CloudBlobDirectory` objekty. Následující fragment kódu vytvoří vyjmenovává všechny objekty BLOB v kontejneru objektů blob. Každý objekt blob je přetypován na příslušný objekt na základě jeho typu. Jeho název (nebo IDENTIFIKÁTOR URI `CloudBlobDirectory`v případě ) je přidán do seznamu.

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
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
    Následující ukazuje dokončenou `ListBlobs` metodu:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
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

1. V **Průzkumníku řešení**rozbalte složku **Zobrazení** a klepněte pravým tlačítkem myši na **objekty BLOB**.

2. V místní nabídce vyberte **Přidat** > **zobrazení**.

1. V dialogovém okně Přidat `ListBlobs` **zobrazení** zadejte název zobrazení a vyberte **Přidat**.

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

1. V **Průzkumníku řešení**rozbalte složku `_Layout.cshtml` **Zobrazení** > **sdílené** a otevřete .

1. Za poslední `<li>` prvek v seznamu přidejte následující kód HTML a přidejte další položku navigační nabídky:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Spusťte aplikaci a výběrem **možnosti Objekty BLOB seznamu** zobrazíte výsledky podobné následujícímu snímku obrazovky:
  
    ![Snímek obrazovky s objekty BLOB seznamu](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Stáhnout objekty blob

Tato část ukazuje, jak stáhnout objekt blob. Můžete buď zachovat do místního úložiště nebo číst obsah do řetězce. Ukázkový kód odkazuje na *testovací kontejner objektů blob* vytvořený v části [Create a blob container](#create-a-blob-container).

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu, `DownloadBlob` která vrací řetězec.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. V `DownloadBlob` rámci metody `CloudBlobContainer` získat objekt, který představuje odkaz na kontejner objektů blob.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Získejte objekt odkazu na `CloudBlobContainer.GetBlockBlobReference` objekt blob voláním metody. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Chcete-li stáhnout objekt `CloudBlockBlob.DownloadToStream` blob, použijte metodu. Následující kód přenáší obsah objektu blob na objekt datového proudu. Tento objekt je pak trvalé do místního souboru. (Změňte * &lt;název místního souboru>* na plně kvalifikovaný název souboru představující místo, kde má být objekt blob stažen.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    Následující ukazuje dokončenou `ListBlobs` metodu (s plně kvalifikovanou cestou pro vytvářený místní soubor):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. V **Průzkumníku řešení**rozbalte složku `_Layout.cshtml` **Zobrazení** > **sdílené** a otevřete .

1. Za poslední `<li>` prvek v seznamu přidejte následující kód HTML a přidejte další položku navigační nabídky:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Spusťte aplikaci a vyberte **Stáhnout objekt blob** a stáhněte objekt blob. Objekt blob zadaný v `CloudBlobContainer.GetBlockBlobReference` volání metody se `File.OpenWrite` stáhne do umístění určeného ve volání metody. Text *úspěch!* by se měl yobjevit v prohlížeči. 

## <a name="delete-blobs"></a>Odstranění objektů blob

Následující kroky ilustrují, jak odstranit objekt blob:

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu, `DeleteBlob` která vrací řetězec.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. V `DeleteBlob` rámci metody `CloudBlobContainer` získat objekt, který představuje odkaz na kontejner objektů blob.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Získejte objekt odkazu na `CloudBlobContainer.GetBlockBlobReference` objekt blob voláním metody. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Chcete-li odstranit objekt `Delete` blob, použijte metodu.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    Dokončená `DeleteBlob` metoda by měla být následující:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. V **Průzkumníku řešení**rozbalte složku `_Layout.cshtml` **Zobrazení** > **sdílené** a otevřete .

1. Za poslední `<li>` prvek v seznamu přidejte následující kód HTML a přidejte další položku navigační nabídky:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Spusťte aplikaci a vyberte **Odstranit objekt blob,** chcete-li odstranit objekt blob zadaný ve volání `CloudBlobContainer.GetBlockBlobReference` metody. Text *úspěch!* by se měl yobjevit v prohlížeči. Vyberte tlačítko **Zpět** v prohlížeči a pak vyberte **objekty BLOB seznamu,** abyste ověřili, že objekt blob již není v kontejneru.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili ukládat, seznamovat a načítat objekty BLOB ve službě Azure Storage pomocí ASP.NET Core. Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.

  * [Začínáme s úložištěm Azure Table a připojenými službami Visual Studia (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Začínáme s úložištěm služby Azure Queue a připojenými službami Visual Studia (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
