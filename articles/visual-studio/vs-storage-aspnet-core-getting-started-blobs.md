---
title: Začínáme s Azure Blob storage a Visual Studio připojené služby (ASP.NET Core) | Dokumentace Microsoftu
description: Jak začít používat Azure Blob storage v projektu aplikace ASP.NET Core v sadě Visual Studio po připojení k účtu úložiště pomocí sady Visual Studio připojené služby
services: storage
documentationcenter: ''
author: camsoper
manager: wpickett
editor: ''
ms.service: storage
ms.workload: web
ms.custom: vs-azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: casoper
ms.openlocfilehash: 82c5fb0f3f3e8edad948b82f77c9c336636f3077
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42442737"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Začínáme s Azure Blob storage a Visual Studio připojené služby (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [Jádro ASP.NET](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob storage je služba, která ukládá Nestrukturovaná data v cloudu jako objekty nebo objekty BLOB. Do Blob storage se dá ukládat jakýkoli druh textu nebo binárních dat, jako je dokument, soubor médií nebo instalátor aplikace. Blob storage se také nazývá úložiště objektů.

Tento kurz ukazuje, jak napsat kód ASP.NET Core pro některé běžné scénáře, které používají úložiště objektů Blob. Scénáře zahrnují vytváření kontejner objektů blob a odesílání, výpis, stahování a odstraňování objektů BLOB.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Požadavky

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>Nastavení vývojového prostředí

Tato část vás provede nastavení vývojového prostředí. To zahrnuje vytvoření aplikace ASP.NET Model-View-Controller (MVC), přidání připojené služby připojení, přidání kontroleru a určení direktivy požadovaný obor názvů.

### <a name="create-an-aspnet-mvc-app-project"></a>Vytvoření projektu aplikace ASP.NET MVC

1. Otevřete sadu Visual Studio.

1. V hlavní nabídce vyberte **souboru** > **nový** > **projektu**.

1. V **nový projekt** dialogu **webové** > **webové aplikace ASP.NET Core** > **AspNetCoreStorage**. Pak vyberte **OK**.

    ![Dialogové okno snímek obrazovky Visual Studio nový projekt](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. V **nová webová aplikace ASP.NET Core** dialogu **.NET Core** > **ASP.NET Core 2.0** > **(webová aplikace Model-View-Controller)**. Pak vyberte **OK**.

    ![Dialogové okno snímek obrazovky z nové webové aplikace ASP.NET Core](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Připojte se k účtu služby Azure storage pomocí připojených služeb

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.

2. V místní nabídce vyberte **přidat** > **připojenou službu**.

1. V **připojené služby** dialogu **cloudové úložiště se službou Azure Storage**a pak vyberte **konfigurovat**.

    ![Dialogové okno snímek obrazovky připojené služby](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. V **služby Azure Storage** dialogovém okně vyberte účet úložiště Azure se použije pro účely tohoto kurzu. Chcete-li vytvořit nový účet úložiště Azure, vyberte **vytvořit nový účet úložiště**a vyplňte formulář. Po výběru buď existující účet úložiště nebo vytvoří nový, vyberte **přidat**. Visual Studio nainstaluje balíček NuGet pro služby Azure Storage a připojovací řetězec úložiště do **appsettings.json**.

> [!TIP]
> Další informace o vytvoření účtu úložiště pomocí [webu Azure portal](https://portal.azure.com), naleznete v tématu [vytvořit účet úložiště](../storage/common/storage-quickstart-create-account.md).
>
> Můžete také vytvořit účet úložiště pomocí [prostředí Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [rozhraní příkazového řádku Azure](../storage/common/storage-azure-cli.md), nebo [Azure Cloud Shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Vytvořit kontroler MVC 

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **řadiče**.

2. V místní nabídce vyberte **přidat** > **řadič**.

    ![Snímek obrazovky Průzkumníka řešení](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. V **přidat vygenerované uživatelské rozhraní** dialogu **kontroler MVC – prázdný**a vyberte **přidat**.

    ![Dialogové okno snímek obrazovky z přidat vygenerované uživatelské rozhraní](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. V **přidat prázdný kontroler MVC** dialogové okno, názvu kontroleru *BlobsController*a vyberte **přidat**.

    ![Dialogové okno snímek obrazovky z přidat prázdný kontroler MVC](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Přidejte následující `using` direktivy `BlobsController.cs` souboru:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Připojte se k účtu úložiště a získejte odkaz na kontejner

Kontejner objektů blob je vnořené hierarchie objektů BLOB a složek. Zbývající kroky v tomto dokumentu vyžadují odkaz na kontejner objektů blob tak, aby kód musí být umístěné ve své vlastní metody pro opětovné použití.

Následující kroky slouží k vytvoření metody pro připojení k účtu úložiště pomocí připojovacího řetězce v **appsettings.json**. Kroky také vytvořit odkaz na kontejner. Nastavení připojovacího řetězce v **appsettings.json** názvem ve formátu `<storageaccountname>_AzureStorageConnectionString`. 

1. Otevřete soubor `BlobsController.cs`.

1. Přidat metodu nazvanou **GetCloudBlobContainer** , která vrací **CloudBlobContainer**. Nezapomeňte nahradit `<storageaccountname>_AzureStorageConnectionString` skutečným názvem klíče v **Web.config**.
    
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
> I když *test kontejneru objektů blob* neexistuje, tento kód vytvoří na ni odkaz. Toto je, takže je možné vytvořit kontejner s `CreateIfNotExists` metodu uvedenou v dalším kroku.

## <a name="create-a-blob-container"></a>Vytvoření kontejneru objektů blob

Následující postup ukazuje, jak vytvořit kontejner objektů blob:

1. Přidat metodu nazvanou `CreateBlobContainer` , který vrátí `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Získání `CloudBlobContainer` objekt, který reprezentuje odkaz na název požadovaného objektů blob v kontejneru. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Volání `CloudBlobContainer.CreateIfNotExists` metodu pro vytvoření kontejneru, pokud ještě neexistuje. `CloudBlobContainer.CreateIfNotExists` Vrátí metoda **true** Pokud kontejner neexistuje a je úspěšně vytvořen. V opačném případě vrátí metoda **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Aktualizace `ViewBag` s názvem kontejneru objektů blob.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Následující příklad zobrazuje kompletní `CreateBlobContainer` metody:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem myši **zobrazení** složky.

2. V místní nabídce vyberte **přidat** > **novou složku**. Název nové složky *objekty BLOB*. 

1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky a klikněte pravým tlačítkem na **objekty BLOB**.

4. V místní nabídce vyberte **přidat** > **zobrazení**.

1. V **přidat zobrazení** dialogového okna zadejte **CreateBlobContainer** názvu zobrazení a vyberte **přidat**.

1. Otevřít `CreateBlobContainer.cshtml`a upravit ji tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení** > **Shared** složky a otevřete `_Layout.cshtml`.

1. Vyhledejte neuspořádaný seznam, který vypadá takto: `<ul class="nav navbar-nav">`.  Za poslední `<li>` prvek v seznamu, přidejte následující kód HTML pro přidání další položky nabídky navigace:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Spusťte aplikaci a vyberte **vytvořit kontejner objektů Blob** zobrazíte výsledky podobně jako na následujícím snímku obrazovky:
  
    ![Snímek obrazovky vytvořit kontejner objektů blob](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Jak už bylo zmíněno dříve, `CloudBlobContainer.CreateIfNotExists` vrátí metoda **true** pouze při kontejner neexistuje, je vytvořen. Proto pokud se aplikace spustí, když kontejner existuje, metoda vrátí **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Nahrání objektu blob do kontejneru objektů blob

Když [se vytvoří kontejner objektů blob](#create-a-blob-container), nahrávání souborů do tohoto kontejneru. Tato část vás provede nahrání místního souboru do kontejneru objektů blob. Kroky předpokládají, že neexistuje kontejner objektů blob s názvem *test kontejneru objektů blob*. 

1. Otevřete soubor `BlobsController.cs`.

1. Přidat metodu nazvanou `UploadBlob` , která vrátí hodnotu typu string.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. V rámci `UploadBlob` metoda, získání `CloudBlobContainer` objekt, který reprezentuje odkaz na název požadovaného objektů blob v kontejneru. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Azure storage podporuje typy různých objektů blob. Tento kurz používá objekty BLOB bloku. Chcete-li načíst odkaz na objekt blob bloku, zavolejte `CloudBlobContainer.GetBlockBlobReference` metody.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Název objektu blob je součástí adresy URL se používá k načtení objektu blob a může být libovolný řetězec, včetně názvu souboru.

1. Po odkaz na objekt blob se můžete nahrát jakýkoli proud dat k němu voláním odkaz na objekt blob `UploadFromStream` metody. `UploadFromStream` Metoda vytvoří objekt blob, pokud neexistuje, nebo ho přepíše, pokud už existoval. (Změnit  *&lt;nahrání souboru >* na plně kvalifikovanou cestu k souboru k odeslání.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    Následující příklad zobrazuje kompletní `UploadBlob` – metoda (s plně kvalifikovanou cestu souboru k odeslání):

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

1. V **Průzkumníka řešení**, rozbalte **zobrazení** > **Shared** složky a otevřete `_Layout.cshtml`.

1. Za poslední `<li>` prvek v seznamu, přidejte následující kód HTML pro přidání další položky nabídky navigace:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Spusťte aplikaci a vyberte **nahrát objekt blob**. Slovo *úspěch!* by se zobrazit.
    
    ![Snímek obrazovky úspěšné ověření](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Výpis objektů BLOB v kontejneru objektů blob

Tato část ukazuje, jak vypisovat objekty BLOB v kontejneru objektů blob. Odkazy na ukázkový kód *test kontejneru objektů blob* vytvořili v části [vytvořte kontejner objektů blob](#create-a-blob-container).

1. Otevřete soubor `BlobsController.cs`.

1. Přidat metodu nazvanou `ListBlobs` , který vrátí `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. V rámci `ListBlobs` metody get `CloudBlobContainer` objekt, který představuje odkaz na kontejner objektů blob. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. K zobrazení seznamu objektů BLOB v kontejneru objektů blob, použijte `CloudBlobContainer.ListBlobsSegmentedAsync` metody. `CloudBlobContainer.ListBlobsSegmentedAsync` Metoda vrátí hodnotu `BlobResultSegment`. Tato položka obsahuje `IListBlobItem` objekty, které lze převést na `CloudBlockBlob`, `CloudPageBlob`, nebo `CloudBlobDirectory` objekty. Následující fragment kódu vytvoří výčet všech objektů BLOB v kontejneru objektů blob. Každý objekt blob je přetypován na příslušný objekt, na základě jeho typu. Název (nebo identifikátor URI v případě třídy `CloudBlobDirectory`) se přidá do seznamu.

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
    Následující příklad zobrazuje kompletní `ListBlobs` metody:

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

1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky a klikněte pravým tlačítkem na **objekty BLOB**.

2. V místní nabídce vyberte **přidat** > **zobrazení**.

1. V **přidat zobrazení** dialogového okna zadejte `ListBlobs` názvu zobrazení a vyberte **přidat**.

1. Otevřít `ListBlobs.cshtml`a nahraďte jeho obsah následujícím kódem:

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

1. V **Průzkumníka řešení**, rozbalte **zobrazení** > **Shared** složky a otevřete `_Layout.cshtml`.

1. Za poslední `<li>` prvek v seznamu, přidejte následující kód HTML pro přidání další položky nabídky navigace:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Spusťte aplikaci a vyberte **vypisovat objekty BLOB** zobrazíte výsledky podobně jako na následujícím snímku obrazovky:
  
    ![Snímek obrazovky výpis objektů BLOB](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Stáhnout objekty blob

Tato část ukazuje, jak stáhnout objekt blob. Můžete zachovat do místního úložiště nebo číst obsah do řetězce. Odkazy na ukázkový kód *test kontejneru objektů blob* vytvořili v části [vytvořte kontejner objektů blob](#create-a-blob-container).

1. Otevřete soubor `BlobsController.cs`.

1. Přidat metodu nazvanou `DownloadBlob` , která vrátí hodnotu typu string.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. V rámci `DownloadBlob` metody get `CloudBlobContainer` objekt, který představuje odkaz na kontejner objektů blob.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Získat odkaz na objekt blob voláním `CloudBlobContainer.GetBlockBlobReference` metody. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Chcete-li stáhnout objekt blob, použijte `CloudBlockBlob.DownloadToStream` metody. Následující kód přenáší obsah k objektu blob na objekt datového proudu. Tento objekt se potom ukládají do místního souboru. (Změnit  *&lt;místní název souboru >* na plně kvalifikovaný název představující, ve kterém se stáhnout objekt blob.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    Následující příklad zobrazuje kompletní `ListBlobs` – metoda (s plně kvalifikovanou cestu souboru místní vytváří):
    
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

1. V **Průzkumníka řešení**, rozbalte **zobrazení** > **Shared** složky a otevřete `_Layout.cshtml`.

1. Za poslední `<li>` prvek v seznamu, přidejte následující kód HTML pro přidání další položky nabídky navigace:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Spusťte aplikaci a vyberte **stažení objektů blob** stáhnout objekt blob. Zadané v objektu blob `CloudBlobContainer.GetBlockBlobReference` volání metody stáhne do zadaného v umístění `File.OpenWrite` volání metody. Text *úspěch!* by se zobrazit v prohlížeči. 

## <a name="delete-blobs"></a>Odstranění objektů blob

Následující kroky ukazují, jak odstranit objekt blob:

1. Otevřete soubor `BlobsController.cs`.

1. Přidat metodu nazvanou `DeleteBlob` , která vrátí hodnotu typu string.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. V rámci `DeleteBlob` metody get `CloudBlobContainer` objekt, který představuje odkaz na kontejner objektů blob.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Získat odkaz na objekt blob voláním `CloudBlobContainer.GetBlockBlobReference` metody. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Chcete-li odstranit objekt blob, použijte `Delete` metody.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    Dokončené `DeleteBlob` metoda by měla vypadat následovně:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení** > **Shared** složky a otevřete `_Layout.cshtml`.

1. Za poslední `<li>` prvek v seznamu, přidejte následující kód HTML pro přidání další položky nabídky navigace:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Spusťte aplikaci a vyberte **odstranění objektů blob** odstranit zadané v objektu blob `CloudBlobContainer.GetBlockBlobReference` volání metody. Text *úspěch!* by se zobrazit v prohlížeči. Vybrat prohlížeče **zpět** tlačítko a pak vyberte **vypisovat objekty BLOB** k ověření, že objekt blob je již v kontejneru.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak ukládat, seznamu a načíst objekty BLOB ve službě Azure Storage pomocí ASP.NET Core. Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.

  * [Začínáme s Azure Table storage a Visual Studio připojené služby (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Začínáme s Azure Queue storage a Visual Studio připojené služby (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
