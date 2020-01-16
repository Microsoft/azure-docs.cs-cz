---
title: Začínáme s Azure Blob Storage s využitím sady Visual Studio (ASP.NET Core)
description: Jak začít používat službu Azure Blob Storage v projektu ASP.NET Core v aplikaci Visual Studio po připojení k účtu úložiště pomocí připojených služeb sady Visual Studio
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980738"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Začínáme s Azure Blob Storage a připojenými službami sady Visual Studio (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob Storage je služba, která ukládá nestrukturovaná data v cloudu jako objekty nebo objekty blob. Do Blob storage se dá ukládat jakýkoli druh textu nebo binárních dat, jako je dokument, soubor médií nebo instalátor aplikace. Blob storage se také nazývá úložiště objektů. Další informace o službě BLOB Storage najdete v tématu [Úvod do úložiště objektů BLOB v Azure](../storage/blobs/storage-blobs-introduction.md).

V tomto kurzu se dozvíte, jak napsat kód ASP.NET Core pro některé běžné scénáře, které používají úložiště objektů BLOB. Scénáře zahrnují vytvoření kontejneru objektů BLOB a nahrávání, výpisy, stahování a odstraňování objektů BLOB.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Požadavky

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

## <a name="set-up-the-development-environment"></a>Nastavení vývojového prostředí

Tato část vás provede nastavením vývojového prostředí. To zahrnuje vytvoření aplikace ASP.NET Model-View-Controller (MVC), přidání připojení připojené služby, přidání kontroleru a určení požadovaných direktiv oboru názvů.

### <a name="create-an-aspnet-mvc-app-project"></a>Vytvoření projektu aplikace ASP.NET MVC

1. Otevřete sadu Visual Studio.

1. V hlavní nabídce vyberte **soubor** > **Nový** > **projekt**.

1. V dialogovém okně **Nový projekt** **vyberte web** > **ASP.NET Core webové aplikace** > **AspNetCoreStorage**. Pak vyberte **OK**.

    ![Snímek obrazovky dialogového okna Nový projekt aplikace Visual Studio](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. V dialogovém okně **Nová webová aplikace ASP.NET Core** vyberte možnost **.net Core** > **ASP.NET Core 2,0** > **Webová aplikace (model-zobrazení-kontroler)** . Pak vyberte **OK**.

    ![Snímek obrazovky dialogového okna Nová webová aplikace ASP.NET Core](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Připojení k účtu služby Azure Storage pomocí připojených služeb

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.

2. V místní nabídce vyberte **přidat** > **připojená služba**.

1. V dialogovém okně **připojené služby** vyberte **cloudové úložiště s Azure Storage**a pak vyberte **Konfigurovat**.

    ![Snímek obrazovky dialogového okna připojené služby](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. V dialogovém okně **Azure Storage** vyberte účet služby Azure Storage, který se má použít pro tento kurz. Pokud chcete vytvořit nový účet úložiště Azure, vyberte **vytvořit nový účet úložiště**a vyplňte formulář. Po výběru některého z existujících účtů úložiště nebo vytvoření nového vyberte **Přidat**. Sada Visual Studio nainstaluje balíček NuGet pro Azure Storage a připojovací řetězec úložiště do souboru **appSettings. JSON**.

> [!TIP]
> Informace o tom, jak vytvořit účet úložiště pomocí [Azure Portal](https://portal.azure.com), najdete v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).
>
> Účet úložiště můžete vytvořit také pomocí [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), rozhraní příkazového [řádku Azure](../storage/common/storage-azure-cli.md)nebo [Azure Cloud Shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Vytvoření kontroleru MVC 

1. V **Průzkumník řešení**klikněte pravým tlačítkem na **řadiče**.

2. V místní nabídce vyberte **přidat** > **kontroler**.

    ![Snímek obrazovky s Průzkumník řešení](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. V dialogovém okně **Přidat vygenerované uživatelské rozhraní** vyberte možnost **kontroler MVC – prázdné**a vyberte **Přidat**.

    ![Snímek obrazovky dialogového okna Přidat generování uživatelského rozhraní](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. V dialogovém okně **přidat prázdný řadič MVC** zadejte název kontroleru *BlobsController*a vyberte **Přidat**.

    ![Snímek obrazovky dialogového okna přidat prázdný řadič MVC](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Do `BlobsController.cs` souboru přidejte následující direktivy `using`:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Připojte se k účtu úložiště a získejte odkaz na kontejner.

Kontejner objektů BLOB je vnořená hierarchie objektů BLOB a složek. Zbytek kroků v tomto dokumentu vyžaduje odkaz na kontejner objektů blob, takže by měl být kód umístěn do své vlastní metody pro účely opětovné použitelnosti.

Následující kroky vytvoří metodu pro připojení k účtu úložiště pomocí připojovacího řetězce v souboru **appSettings. JSON**. Kroky také vytvoří odkaz na kontejner. Nastavení připojovacího řetězce v souboru **appSettings. JSON** má název `<storageaccountname>_AzureStorageConnectionString`formátu. 

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu s názvem **GetCloudBlobContainer** , která vrátí **CloudBlobContainer**. Nezapomeňte nahradit `<storageaccountname>_AzureStorageConnectionString` skutečným názvem klíče v **souboru Web. config**.
    
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
> I když *test-BLOB-Container* ještě neexistuje, tento kód vytvoří odkaz na něj. To je proto, že kontejner lze vytvořit s metodou `CreateIfNotExists` zobrazenou v dalším kroku.

## <a name="create-a-blob-container"></a>Vytvoření kontejneru objektů blob

Následující kroky ukazují, jak vytvořit kontejner objektů BLOB:

1. Přidejte metodu s názvem `CreateBlobContainer`, která vrací `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Získejte objekt `CloudBlobContainer`, který představuje odkaz na požadovaný název kontejneru objektů BLOB. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Zavolejte metodu `CloudBlobContainer.CreateIfNotExists` pro vytvoření kontejneru, pokud ještě neexistuje. Metoda `CloudBlobContainer.CreateIfNotExists` vrátí **hodnotu true** , pokud kontejner neexistuje a byl úspěšně vytvořen. V opačném případě metoda vrátí **hodnotu false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Aktualizujte `ViewBag` s názvem kontejneru objektů BLOB.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Následující příklad ukazuje dokončenou `CreateBlobContainer` metodu:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku **zobrazení** .

2. V místní nabídce vyberte **přidat** > **Nová složka**. Pojmenujte nové *objekty blob*složky. 

1. V **Průzkumník řešení**rozbalte složku **zobrazení** a klikněte pravým tlačítkem myši na **objekty blob**.

4. V místní nabídce vyberte **přidat** > **zobrazení**.

1. V dialogovém okně **Přidat zobrazení** jako název zobrazení zadejte **CreateBlobContainer** a vyberte **Přidat**.

1. Otevřete `CreateBlobContainer.cshtml`a upravte jej tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. V **Průzkumník řešení**rozbalte **zobrazení** > **sdílená** složka a otevřete `_Layout.cshtml`.

1. Vyhledejte Neseřazený seznam, který vypadá takto: `<ul class="nav navbar-nav">`.  Po posledním `<li>` elementu v seznamu přidejte následující kód HTML pro přidání další položky navigační nabídky:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Spusťte aplikaci a vyberte **vytvořit kontejner objektů BLOB** pro zobrazení výsledků podobných následujícímu snímku obrazovky:
  
    ![Snímek obrazovky s vytvořením kontejneru objektů BLOB](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Jak bylo zmíněno dříve, metoda `CloudBlobContainer.CreateIfNotExists` vrátí **hodnotu true** pouze v případě, že kontejner neexistuje a je vytvořen. Proto pokud je aplikace spuštěna, když existuje kontejner, metoda vrátí **hodnotu false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Nahrání objektu blob do kontejneru objektů BLOB

Po [vytvoření kontejneru objektů BLOB](#create-a-blob-container)nahrajte do tohoto kontejneru soubory. Tato část vás provede odesláním místního souboru do kontejneru objektů BLOB. Tento postup předpokládá, že existuje kontejner objektů BLOB s názvem *test-BLOB-Container*. 

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu s názvem `UploadBlob`, která vrací řetězec.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. V rámci metody `UploadBlob` získejte objekt `CloudBlobContainer`, který představuje odkaz na požadovaný název kontejneru objektů BLOB. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Azure Storage podporuje různé typy objektů BLOB. Tento kurz používá objekty blob bloku. Chcete-li načíst odkaz na objekt blob bloku, zavolejte metodu `CloudBlobContainer.GetBlockBlobReference`.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Název objektu BLOB je součástí adresy URL, která se používá k načtení objektu blob, a může to být libovolný řetězec, včetně názvu souboru.

1. Po odkazování na objekt blob můžete do něj nahrát libovolný datový proud voláním metody `UploadFromStream` objektu BLOB reference. Metoda `UploadFromStream` vytvoří objekt blob, pokud neexistuje, nebo ho přepíše, pokud existuje. (Změnu *&lt;souboru pro nahrání >* na plně kvalifikovanou cestu k souboru, který se má nahrát.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    Následuje ukázka metody Completed `UploadBlob` (s úplnou cestou pro odeslání souboru):

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

1. V **Průzkumník řešení**rozbalte **zobrazení** > **sdílená** složka a otevřete `_Layout.cshtml`.

1. Po posledním `<li>` elementu v seznamu přidejte následující kód HTML pro přidání další položky navigační nabídky:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Spusťte aplikaci a vyberte **nahrát objekt BLOB**. Slovo bylo *úspěšné!* by se měla zobrazit.
    
    ![Snímek obrazovky s úspěšným ověřením](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Výpis objektů BLOB v kontejneru objektů BLOB

Tato část ukazuje, jak zobrazit seznam objektů BLOB v kontejneru objektů BLOB. Vzorový kód odkazuje na *kontejner test-BLOB-Container* vytvořený v oddílu a [vytvoří kontejner objektů BLOB](#create-a-blob-container).

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu s názvem `ListBlobs`, která vrací `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. V rámci metody `ListBlobs` získejte objekt `CloudBlobContainer`, který představuje odkaz na kontejner objektů BLOB. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Chcete-li zobrazit seznam objektů BLOB v kontejneru objektů blob, použijte metodu `CloudBlobContainer.ListBlobsSegmentedAsync`. Metoda `CloudBlobContainer.ListBlobsSegmentedAsync` vrátí `BlobResultSegment`. Obsahuje `IListBlobItem` objekty, které mohou být přetypování do objektů `CloudBlockBlob`, `CloudPageBlob`nebo `CloudBlobDirectory`. Následující fragment kódu vytvoří výčet všech objektů BLOB v kontejneru objektů BLOB. Každý objekt BLOB je přetypovat na příslušný objekt na základě jeho typu. Do seznamu se přidá název (nebo identifikátor URI v případě `CloudBlobDirectory`).

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
    Následující příklad ukazuje dokončenou `ListBlobs` metodu:

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

1. V **Průzkumník řešení**rozbalte složku **zobrazení** a klikněte pravým tlačítkem myši na **objekty blob**.

2. V místní nabídce vyberte **přidat** > **zobrazení**.

1. V dialogovém okně **Přidat zobrazení** zadejte `ListBlobs` pro název zobrazení a vyberte **Přidat**.

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

1. V **Průzkumník řešení**rozbalte **zobrazení** > **sdílená** složka a otevřete `_Layout.cshtml`.

1. Po posledním `<li>` elementu v seznamu přidejte následující kód HTML pro přidání další položky navigační nabídky:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Spusťte aplikaci a vyberte **seznam objektů BLOB** pro zobrazení výsledků podobně jako na následujícím snímku obrazovky:
  
    ![Snímek obrazovky seznamu objektů BLOB](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Stáhnout objekty blob

Tato část ukazuje, jak stáhnout objekt BLOB. Můžete ji buď zachovat v místním úložišti, nebo si obsah přečíst do řetězce. Vzorový kód odkazuje na *kontejner test-BLOB-Container* vytvořený v oddílu a [vytvoří kontejner objektů BLOB](#create-a-blob-container).

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu s názvem `DownloadBlob`, která vrací řetězec.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. V rámci metody `DownloadBlob` získejte objekt `CloudBlobContainer`, který představuje odkaz na kontejner objektů BLOB.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Získejte odkazový objekt objektu BLOB voláním metody `CloudBlobContainer.GetBlockBlobReference`. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Chcete-li stáhnout objekt blob, použijte metodu `CloudBlockBlob.DownloadToStream`. Následující kód přenáší obsah objektu blob do objektu Stream. Tento objekt je pak uložený do místního souboru. (Změňte *&lt;místní-soubor-název >* na plně kvalifikovaný název souboru, který představuje, kam se má objekt BLOB stáhnout.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    Následuje ukázka metody Completed `ListBlobs` (s plně kvalifikovanou cestou pro místní soubor, který se vytváří):
    
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

1. V **Průzkumník řešení**rozbalte **zobrazení** > **sdílená** složka a otevřete `_Layout.cshtml`.

1. Po posledním `<li>` elementu v seznamu přidejte následující kód HTML pro přidání další položky navigační nabídky:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Spusťte aplikaci a vyberte **Stáhnout objekt BLOB** pro stažení objektu BLOB. Objekt BLOB zadaný ve volání metody `CloudBlobContainer.GetBlockBlobReference` stahuje do umístění zadaného ve volání metody `File.OpenWrite`. Text byl *úspěšný.* měl by se zobrazit v prohlížeči. 

## <a name="delete-blobs"></a>Odstranění objektů blob

Následující postup ukazuje, jak odstranit objekt BLOB:

1. Otevřete soubor `BlobsController.cs`.

1. Přidejte metodu s názvem `DeleteBlob`, která vrací řetězec.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. V rámci metody `DeleteBlob` získejte objekt `CloudBlobContainer`, který představuje odkaz na kontejner objektů BLOB.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Získejte odkazový objekt objektu BLOB voláním metody `CloudBlobContainer.GetBlockBlobReference`. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Chcete-li odstranit objekt blob, použijte metodu `Delete`.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    Metoda Completed `DeleteBlob` by měla vypadat takto:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. V **Průzkumník řešení**rozbalte **zobrazení** > **sdílená** složka a otevřete `_Layout.cshtml`.

1. Po posledním `<li>` elementu v seznamu přidejte následující kód HTML pro přidání další položky navigační nabídky:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Spusťte aplikaci a vyberte **Odstranit objekt BLOB** pro odstranění objektu BLOB zadaného ve volání metody `CloudBlobContainer.GetBlockBlobReference`. Text byl *úspěšný.* měl by se zobrazit v prohlížeči. Vyberte tlačítko **zpět** v prohlížeči a pak vyberte **seznam objektů BLOB** , abyste ověřili, že objekt BLOB už není v kontejneru.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak ukládat, vypisovat a načítat objekty BLOB v Azure Storage pomocí ASP.NET Core. Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.

  * [Začínáme s Azure Table Storage a připojenými službami sady Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Začínáme s Azure Queue Storage a připojenými službami sady Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
