---
title: Začínáme s Azure Table Storage s využitím sady Visual Studio (ASP.NET)
description: Jak začít používat úložiště Azure Table v projektu ASP.NET v aplikaci Visual Studio po připojení k účtu úložiště pomocí připojených služeb sady Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c49df689ae859c93046c19af043aa2001dbb5481
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75979626"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Začínáme s Azure Table Storage a připojenými službami sady Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Přehled

Azure Table Storage umožňuje ukládat velké objemy strukturovaných dat. Služba je úložiště dat NoSQL, které přijímá ověřená volání zevnitř i mimo cloud Azure. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat.

V tomto kurzu se dozvíte, jak napsat kód ASP.NET pro některé běžné scénáře s využitím entit služby Azure Table Storage. Mezi tyto scénáře patří vytvoření tabulky a přidání, dotazování a odstranění entit tabulky. 

## <a name="prerequisites"></a>Požadavky

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Účet služby Azure Storage](../storage/common/storage-account-create.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Vytvoření kontroleru MVC 

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na **řadiče**a v místní nabídce vyberte možnost **Přidat kontroler >**.

    ![Přidání kontroleru do aplikace ASP.NET MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost kontroler **MVC 5 – prázdné**a vyberte **Přidat**.

    ![Zadat typ kontroleru MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. V dialogovém okně **Přidat řadič** pojmenujte kontrolér *TablesController*a vyberte **Přidat**.

    ![Pojmenování kontroleru MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Do `TablesController.cs` souboru přidejte následující direktivy *using* :

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Vytvoření třídy modelu

Mnohé z příkladů v tomto článku používají třídu odvozenou od **TableEntity**s názvem **CustomerEntity**. Následující kroky vás provedou deklarováním této třídy jako třídy modelu:

1. V **Průzkumník řešení**klikněte pravým tlačítkem na **modely**a v místní nabídce vyberte **Přidat třídu >**.

1. V dialogovém okně **Přidat novou položku** pojmenujte třídu **CustomerEntity**.

1. Otevřete `CustomerEntity.cs` soubor a přidejte následující direktivu **using** :

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Upravte třídu tak, aby po dokončení byla třída deklarována jako v následujícím kódu. Třída deklaruje třídu entity s názvem **CustomerEntity** , která používá křestní jméno zákazníka jako klíč řádku a příjmení jako klíč oddílu.

    ```csharp
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }
    }
    ```

## <a name="create-a-table"></a>Vytvoření tabulky

Následující postup ukazuje, jak vytvořit tabulku:

> [!NOTE]
> 
> V této části se předpokládá, že jste dokončili kroky v tématu [Nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu nazvanou **Create** , která vrací **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci metody **Create** získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. Použijte následující kód k získání informací o připojovacím řetězci a účtu úložiště z konfigurace služby Azure: (změňte * &lt;název úložiště-účet-Name>* na název účtu úložiště Azure, ke kterému přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání objektu **cloudtableclient vám** představuje klienta služby Table Service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získejte objekt **cloudu** , který představuje odkaz na požadovaný název tabulky. Metoda **cloudtableclient vám. GetTableReference** nevytvoří požadavek na úložiště tabulek. Odkaz je vrácen bez ohledu na to, zda tabulka existuje. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Voláním metody **Cloud. CreateIfNotExists** vytvořte tabulku, pokud ještě neexistuje. Metoda **Cloud. CreateIfNotExists** vrátí **hodnotu true** , pokud tabulka neexistuje a je úspěšně vytvořena. V opačném případě se vrátí **hodnota false** .    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Aktualizujte **ViewBag** s názvem tabulky.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. V **Průzkumník řešení**rozbalte složku **zobrazení** , klikněte pravým tlačítkem myši na **tabulky**a v místní nabídce vyberte možnost **Přidat zobrazení >**.

1. V dialogovém okně **Přidat zobrazení** zadejte pro název zobrazení možnost **vytvořit** a vyberte **Přidat**.

1. Otevřete `CreateTable.cshtml`a upravte jej tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. V **Průzkumník řešení**rozbalte položku **zobrazení->sdílená** složka a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **vytvořit tabulku** , aby se zobrazily podobné výsledky jako na následujícím snímku obrazovky:
  
    ![Vytvoření tabulky](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Jak bylo zmíněno dříve, metoda **Cloud. CreateIfNotExists** vrátí **hodnotu true** pouze v případě, že tabulka neexistuje a je vytvořena. Proto pokud aplikaci spustíte, když tabulka existuje, vrátí metoda **hodnotu false**. Chcete-li aplikaci spustit několikrát, je nutné tabulku před spuštěním aplikace odstranit. Odstranění tabulky se dá provést pomocí metody **Cloud. Delete** . Tabulku můžete také odstranit pomocí [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) nebo [Průzkumník služby Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky

*Entity* se mapují na\# objekty jazyka C pomocí vlastní třídy odvozené z **TableEntity**. Když budete chtít do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti vaší entity. V této části se dozvíte, jak definovat třídu entity, která používá křestní jméno zákazníka jako klíč řádku a příjmení jako klíč oddílu. Společně pak klíč oddílu a řádku entity jednoznačně identifikují entitu v tabulce. Na entity se stejným klíčem oddílu je možné se (v porovnání s těmi, které mají různé klíče oddílů) rychleji dotazovat, ale používání různých klíčů oddílů umožňuje větší škálovatelnost paralelních operací. Pro jakoukoliv vlastnost, která by měla být uložena ve službě Table Service, musí být vlastnost veřejnou vlastností podporovaného typu, která zpřístupňuje nastavení a načítá hodnoty.
Třída entity *musí* deklarovat veřejný konstruktor bez parametrů.

> [!NOTE]
> 
> V této části se předpokládá, že jste dokončili kroky v tématu [Nastavení vývojového prostředí](#set-up-the-development-environment).

1. Otevřete soubor `TablesController.cs`.

1. Přidejte následující direktivu, aby kód v `TablesController.cs` souboru mohl přistupovat ke třídě **CustomerEntity** :

    ```csharp
    using StorageAspnet.Models;
    ```

1. Přidejte metodu s názvem **AddEntity** , která vrátí **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci metody **AddEntity** získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. Použijte následující kód k získání informací o připojovacím řetězci a účtu úložiště z konfigurace služby Azure: (změňte * &lt;název úložiště-účet-Name>* na název účtu úložiště Azure, ke kterému přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání objektu **cloudtableclient vám** představuje klienta služby Table Service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získejte objekt **cloudu** , který představuje odkaz na tabulku, do které se chystáte přidat novou entitu. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvořte instanci a inicializujte třídu **CustomerEntity** .

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Vytvořte objekt **TableOperation** , který vloží entitu zákazníka.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Spusťte operaci vložení voláním metody **Cloud. Execute** . Výsledek operace můžete ověřit kontrolou vlastnosti **při metody tableresult. HttpStatusCode** . Stavový kód 2xx indikuje, že akce, kterou klient požadoval, byl úspěšně zpracován. Například úspěšné vložení nových entit má za následek stavový kód HTTP 204, což znamená, že operace byla úspěšně zpracována a server nevrátil žádný obsah.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Aktualizujte **ViewBag** pomocí názvu tabulky a výsledků operace vložení.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. V **Průzkumník řešení**rozbalte složku **zobrazení** , klikněte pravým tlačítkem myši na **tabulky**a v místní nabídce vyberte možnost **Přidat zobrazení >**.

1. V dialogovém okně **Přidat zobrazení** jako název zobrazení zadejte **AddEntity** a vyberte **Přidat**.

1. Otevřete `AddEntity.cshtml`a upravte jej tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. V **Průzkumník řešení**rozbalte položku **zobrazení->sdílená** složka a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **Přidat entitu** a podívejte se na podobné výsledky jako na následujícím snímku obrazovky:
  
    ![Přidání entity](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Pomocí kroků v části si můžete ověřit, že se entita přidala, a [získat jednu entitu](#get-a-single-entity). Můžete také použít [Průzkumník služby Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) k zobrazení všech entit pro tabulky.

## <a name="add-a-batch-of-entities-to-a-table"></a>Přidání dávky entit do tabulky

Kromě toho, že je možné [Přidat entitu do tabulky po jednom](#add-an-entity-to-a-table), můžete také přidat entity do služby Batch. Přidáním entit do dávky se sníží počet přenosů mezi kódem a službou Azure Table. Následující postup ukazuje, jak přidat více entit do tabulky s jednou operací vložení:

> [!NOTE]
> 
> V této části se předpokládá, že jste dokončili kroky v tématu [Nastavení vývojového prostředí](#set-up-the-development-environment).

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu s názvem **AddEntities** , která vrátí **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci metody **AddEntities** získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. Použijte následující kód k získání informací o připojovacím řetězci a účtu úložiště z konfigurace služby Azure: (změňte * &lt;název úložiště-účet-Name>* na název účtu úložiště Azure, ke kterému přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání objektu **cloudtableclient vám** představuje klienta služby Table Service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získejte objekt **cloudu** , který představuje odkaz na tabulku, do které budete přidávat nové entity. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvořte instanci některých zákaznických objektů na základě třídy modelu **CustomerEntity** prezentované v části a [přidejte do tabulky entitu](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Získá objekt **TableBatchOperation** .

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Přidejte entity do objektu dávkového vložení operace.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Spusťte operaci dávkového vložení voláním metody **Cloud. ExecuteBatch** .   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. Metoda **Cloud. ExecuteBatch** vrátí seznam objektů **při metody tableresult** , ve kterých lze prozkoumat každý objekt **při metody tableresult** , abyste zjistili úspěch nebo selhání každé jednotlivé operace. V tomto příkladu předáte seznam do zobrazení a umožníte zobrazení zobrazit výsledky jednotlivých operací. 
 
    ```csharp
    return View(results);
    ```

1. V **Průzkumník řešení**rozbalte složku **zobrazení** , klikněte pravým tlačítkem myši na **tabulky**a v místní nabídce vyberte možnost **Přidat zobrazení >**.

1. V dialogovém okně **Přidat zobrazení** jako název zobrazení zadejte **AddEntities** a vyberte **Přidat**.

1. Otevřete `AddEntities.cshtml`a upravte jej tak, aby vypadal jako následující.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. V **Průzkumník řešení**rozbalte položku **zobrazení->sdílená** složka a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **Přidat entity** pro zobrazení výsledků podobně jako na následujícím snímku obrazovky:
  
    ![Přidání entit](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Pomocí kroků v části si můžete ověřit, že se entita přidala, a [získat jednu entitu](#get-a-single-entity). Můžete také použít [Průzkumník služby Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) k zobrazení všech entit pro tabulky.

## <a name="get-a-single-entity"></a>Získat jednu entitu

Tato část ukazuje, jak získat jednu entitu z tabulky pomocí klíče řádku entity a klíče oddílu. 

> [!NOTE]
> 
> V této části se předpokládá, že jste dokončili kroky v tématu [Nastavení vývojového prostředí](#set-up-the-development-environment)a použijete data z [Přidání dávky entit do tabulky](#add-a-batch-of-entities-to-a-table). 

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu s názvem **getsingle** , která vrátí **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci metody **getsingle** získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. Použijte následující kód k získání informací o připojovacím řetězci a účtu úložiště z konfigurace služby Azure: (změňte * &lt;název úložiště-účet-Name>* na název účtu úložiště Azure, ke kterému přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání objektu **cloudtableclient vám** představuje klienta služby Table Service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získejte objekt **cloudu** , který představuje odkaz na tabulku, ze které načítáte entitu. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvořte objekt operace načtení, který převezme objekt entity odvozený z **TableEntity**. První parametr je *partitionKey*a druhým parametrem je *rowKey*. Pomocí třídy **CustomerEntity** a dat uvedených v oddílu přidejte do [tabulky dávku entit](#add-a-batch-of-entities-to-a-table). následující fragment kódu dotazuje tabulku pro entitu **CustomerEntity** s hodnotou *partitionKey* "Smith" a *rowKey* hodnotou "Robert":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Spusťte operaci načtení.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Předejte výsledek do zobrazení pro zobrazení.

    ```csharp
    return View(result);
    ```

1. V **Průzkumník řešení**rozbalte složku **zobrazení** , klikněte pravým tlačítkem myši na **tabulky**a v místní nabídce vyberte možnost **Přidat zobrazení >**.

1. V dialogovém okně **Přidat zobrazení** zadejte **getsingle** pro název zobrazení a vyberte **Přidat**.

1. Otevřete `GetSingle.cshtml`a upravte jej tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. V **Průzkumník řešení**rozbalte položku **zobrazení->sdílená** složka a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Spusťte aplikaci a výběrem **získat jednoduché** zobrazte výsledky podobné následujícímu snímku obrazovky:
  
    ![Získat jednu](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Získá všechny entity v oddílu.

Jak je uvedeno v části, [Přidání entity do tabulky](#add-an-entity-to-a-table), kombinace oddílu a klíče řádku jedinečně identifikují entitu v tabulce. Na entity se stejným klíčem oddílu je možné zadávat dotazy rychleji než entity s různými klíči oddílů. Tato část ukazuje, jak zadat dotaz na tabulku pro všechny entity ze zadaného oddílu.  

> [!NOTE]
> 
> V této části se předpokládá, že jste dokončili kroky v tématu [Nastavení vývojového prostředí](#set-up-the-development-environment)a použijete data z [Přidání dávky entit do tabulky](#add-a-batch-of-entities-to-a-table). 

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu s názvem **getpartition** , která vrací **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci metody **getpartition** získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. Použijte následující kód k získání informací o připojovacím řetězci a účtu úložiště z konfigurace služby Azure: (změňte * &lt;název úložiště-účet-Name>* na název účtu úložiště Azure, ke kterému přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání objektu **cloudtableclient vám** představuje klienta služby Table Service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získejte objekt **cloudu** , který představuje odkaz na tabulku, ze které se entity načítají. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvořte instanci objektu **TableQuery** určujícího dotaz v klauzuli **WHERE** . Pomocí třídy **CustomerEntity** a dat uvedených v části přidejte do [tabulky dávku entit](#add-a-batch-of-entities-to-a-table). následující fragment kódu dotazuje tabulku pro všechny entity, kde **PartitionKey** (příjmení zákazníka) má hodnotu "Smith":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. V rámci smyčky zavolejte metodu **Cloud. ExecuteQuerySegmented** , která předává objekt dotazu, který jste vytvořili v předchozím kroku.  Metoda **Cloud. ExecuteQuerySegmented** vrátí objekt **TableContinuationToken** , který-when **null** – označuje, že neexistují žádné další entity, které by bylo možné načíst. V rámci smyčky použijte k iterování vrácených entit jinou smyčku. V následujícím příkladu kódu se každou vrácenou entitu přidá do seznamu. Po ukončení smyčky se seznam předává zobrazení pro zobrazení: 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. V **Průzkumník řešení**rozbalte složku **zobrazení** , klikněte pravým tlačítkem myši na **tabulky**a v místní nabídce vyberte možnost **Přidat zobrazení >**.

1. V dialogovém okně **Přidat zobrazení** zadejte **getpartition** pro název zobrazení a vyberte **Přidat**.

1. Otevřete `GetPartition.cshtml`a upravte jej tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. V **Průzkumník řešení**rozbalte položku **zobrazení->sdílená** složka a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **získat oddíl** , aby se zobrazily podobné výsledky jako na následujícím snímku obrazovky:
  
    ![Získat oddíl](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Odstranění entity

Tato část ukazuje, jak odstranit entitu z tabulky.

> [!NOTE]
> 
> V této části se předpokládá, že jste dokončili kroky v tématu [Nastavení vývojového prostředí](#set-up-the-development-environment)a použijete data z [Přidání dávky entit do tabulky](#add-a-batch-of-entities-to-a-table). 

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu s názvem **DeleteEntity** , která vrátí **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci metody **DeleteEntity** získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. Použijte následující kód k získání informací o připojovacím řetězci a účtu úložiště z konfigurace služby Azure: (změňte * &lt;název úložiště-účet-Name>* na název účtu úložiště Azure, ke kterému přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání objektu **cloudtableclient vám** představuje klienta služby Table Service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získejte objekt **cloudu** , který představuje odkaz na tabulku, ze které entitu odstraňujete. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvořte objekt operace odstranění, který převezme objekt entity odvozený z **TableEntity**. V tomto případě používáme třídu **CustomerEntity** a data uvedená v části [Přidání dávky entit do tabulky](#add-a-batch-of-entities-to-a-table). **Značka ETag** entity musí být nastavená na platnou hodnotu.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Spusťte operaci DELETE.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Předejte výsledek do zobrazení pro zobrazení.

    ```csharp
    return View(result);
    ```

1. V **Průzkumník řešení**rozbalte složku **zobrazení** , klikněte pravým tlačítkem myši na **tabulky**a v místní nabídce vyberte možnost **Přidat zobrazení >**.

1. V dialogovém okně **Přidat zobrazení** jako název zobrazení zadejte **DeleteEntity** a vyberte **Přidat**.

1. Otevřete `DeleteEntity.cshtml`a upravte jej tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. V **Průzkumník řešení**rozbalte položku **zobrazení->sdílená** složka a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **Odstranit entitu** , aby se zobrazily výsledky podobné následujícímu snímku obrazovky:
  
    ![Získat jednu](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Další kroky
Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.

  * [Začínáme se službou Azure Blob Storage a připojenými službami sady Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Začínáme s Azure Queue Storage a připojenými službami sady Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
