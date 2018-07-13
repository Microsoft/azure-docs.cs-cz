---
title: Začínáme s Azure table storage a Visual Studio připojené služby (ASP.NET) | Dokumentace Microsoftu
description: Jak začít používat Azure table storage v projektu aplikace ASP.NET v sadě Visual Studio po připojení k účtu úložiště pomocí připojených služeb sady Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ms.openlocfilehash: 39456380769e1c3b790d2bbc6fdf9c04c983d054
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38531385"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Začínáme s Azure table storage a Visual Studio připojené služby (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Přehled

Azure Table storage umožňuje ukládat velké objemy strukturovaných dat. Tato služba je úložiště dat typu NoSQL, která přijímá ověřených volání z uvnitř i mimo Azure cloud. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat.

Tento kurz ukazuje, jak napsat kód technologie ASP.NET pro některé běžné scénáře pomocí entity úložiště tabulek v Azure. Mezi tyto scénáře patří vytvoření tabulky a přidávání, dotazování a odstranění tabulkové entity. 

## <a name="prerequisites"></a>Požadavky

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Účet služby Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Vytvořit kontroler MVC 

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **řadiče**a v místní nabídce vyberte **Přidat -> řadiče**.

    ![Přidání kontroleru aplikace ASP.NET MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. Na **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **kontroler MVC 5 – prázdný**a vyberte **přidat**.

    ![Zadejte typ kontroleru MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. Na **přidat kontroler** dialogového okna, názvu kontroleru *TablesController*a vyberte **přidat**.

    ![Název kontroleru MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Přidejte následující *pomocí* direktivy `TablesController.cs` souboru:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Vytvořte třídu modelu

Mnoho příkladů v tomto článku používají **TableEntity**-odvozenou třídu s názvem **CustomerEntity**. Následující kroky vás provedou deklarace tuto třídu jako třídu modelu:

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **modely**a v místní nabídce vyberte **Přidat -> třída**.

1. Na **přidat novou položku** dialogového okna, název třídy, **CustomerEntity**.

1. Otevřít `CustomerEntity.cs` soubor a přidejte následující **pomocí** – direktiva:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Upravte třídu, takže až budete hotovi, třída je deklarována jako v následujícím kódu. Třída deklaruje třídu entity, která je volána **CustomerEntity** , která používá jméno zákazníka jako klíč řádku a jeho příjmení jako klíč oddílu.

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

Následující kroky ukazují, jak vytvořit tabulku:

> [!NOTE]
> 
> Této části se předpokládá dokončení kroků v [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `TablesController.cs`.

1. Přidat metodu nazvanou **CreateTable** , který vrátí **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **CreateTable** metoda, získání **CloudStorageAccount** objekt reprezentující informace o vašem účtu úložiště. Použijte následující kód k získání připojovacího řetězce k úložišti a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure jste přístup.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání **CloudTableClient** objekt představuje klienta služby table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získání **CloudTable** objekt, který představuje odkaz na název požadovanou tabulku. **CloudTableClient.GetTableReference** metoda neprovede požadavek proti úložišti tabulek. Odkaz se vrátí, zda existuje v tabulce. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Volání **CloudTable.CreateIfNotExists** metodu pro vytvoření tabulky, pokud ještě neexistuje. **CloudTable.CreateIfNotExists** vrátí metoda **true** Pokud tabulka neexistuje a byl úspěšně vytvořen. V opačném případě **false** je vrácena.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Aktualizace **objekt ViewBag** s názvem tabulky.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky, klikněte pravým tlačítkem na **tabulky**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **CreateTable** názvu zobrazení a vyberte **přidat**.

1. Otevřít `CreateTable.cshtml`a upravit ji tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení -> sdílený** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **Create table** zobrazíte výsledky podobně jako na následujícím snímku obrazovky:
  
    ![Vytvořit tabulku](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Jak už bylo zmíněno dříve, **CloudTable.CreateIfNotExists** vrátí metoda **true** pouze když neexistuje a je vytvořen v tabulce. Proto při spuštění aplikace, existuje v tabulce, metoda vrátí **false**. Ke spuštění aplikace více než jednou, je nutné odstranit tabulku před spuštěním aplikace. Odstraňuje se tabulka, můžete to udělat pomocí **CloudTable.Delete** metody. Můžete také odstranit pomocí tabulky [webu Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) nebo [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky

*Entity* mapu, aby C\# objekty pomocí vlastní třídy odvozené z **TableEntity**. Když budete chtít do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti vaší entity. V této části uvidíte, jak definovat třídu entity, která používá jméno zákazníka jako klíč řádku a jeho příjmení jako klíč oddílu. Společně pak klíč oddílu a řádku entity jednoznačně identifikují entitu v tabulce. Na entity se stejným klíčem oddílu je možné se (v porovnání s těmi, které mají různé klíče oddílů) rychleji dotazovat, ale používání různých klíčů oddílů umožňuje větší škálovatelnost paralelních operací. Jakákoli vlastnost, která by měla být uložena ve službě table service vlastnost musí být veřejná vlastnost podporovaného typu, která zpřístupňuje jak nastavení tak načítání hodnot.
Třída entity *musí* deklarovat veřejný konstruktor bez parametrů.

> [!NOTE]
> 
> Této části se předpokládá dokončení kroků v [nastavení vývojového prostředí](#set-up-the-development-environment).

1. Otevřete soubor `TablesController.cs`.

1. Přidejte následující direktivy tak, aby kód `TablesController.cs` přístup k souboru **CustomerEntity** třídy:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Přidat metodu nazvanou **AddEntity** , který vrátí **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **AddEntity** metoda, získání **CloudStorageAccount** objekt reprezentující informace o vašem účtu úložiště. Použijte následující kód k získání připojovacího řetězce k úložišti a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure jste přístup.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání **CloudTableClient** objekt představuje klienta služby table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získání **CloudTable** objekt, který reprezentuje odkaz na tabulku, do které se chystáte přidat novou entitu. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Konkretizovat a inicializovat **CustomerEntity** třídy.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Vytvoření **TableOperation** objekt, který se vkládá subjektem zákazníka.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Spuštění operace insert voláním **CloudTable.Execute** metody. Výsledek operace můžete ověřit kontrolou **TableResult.HttpStatusCode** vlastnost. Stavový kód 2xx označuje, že požadovaná klientem akce byl úspěšně zpracován. Například úspěšné vložení výsledků nové entity v stavový kód HTTP 204, což znamená, že operace byla úspěšně zpracována a server nevrátil žádný obsah.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Aktualizace **objekt ViewBag** pomocí názvu tabulky a výsledky operace insert.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky, klikněte pravým tlačítkem na **tabulky**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **AddEntity** názvu zobrazení a vyberte **přidat**.

1. Otevřít `AddEntity.cshtml`a upravit ji tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. V **Průzkumníka řešení**, rozbalte **zobrazení -> sdílený** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **Přidat entitu** zobrazíte výsledky podobně jako na následujícím snímku obrazovky:
  
    ![Přidání entity](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Můžete ověřit, že entita byla přidána pomocí následujících kroků v části [Get jedna entita](#get-a-single-entity). Můžete také použít [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) zobrazíte všechny entity pro tabulky.

## <a name="add-a-batch-of-entities-to-a-table"></a>Do tabulky přidat dávky entit

Kromě toho, že možnost [přidání entity do tabulky, jednoho po druhém](#add-an-entity-to-a-table), můžete také přidat entit ve službě batch. Přidávání entit ve službě batch zkracuje dobu odezvy vašeho kódu a služby Azure table service. Následující postup ukazuje, jak přidat více entity do tabulky s jedním vložit operace:

> [!NOTE]
> 
> Této části se předpokládá dokončení kroků v [nastavení vývojového prostředí](#set-up-the-development-environment).

1. Otevřete soubor `TablesController.cs`.

1. Přidat metodu nazvanou **AddEntities** , který vrátí **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **AddEntities** metoda, získání **CloudStorageAccount** objekt reprezentující informace o vašem účtu úložiště. Použijte následující kód k získání připojovacího řetězce k úložišti a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure jste přístup.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání **CloudTableClient** objekt představuje klienta služby table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získání **CloudTable** objekt, který reprezentuje odkaz na tabulku, do které se chystáte přidat nové entity. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvoření instance některých objektů zákazníka na základě **CustomerEntity** třída uvedené v části modelu [přidání entity do tabulky](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Získání **TableBatchOperation** objektu.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Přidání entity do objektu operace dávkové vložení.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Spuštění dávkové operace insert voláním **CloudTable.ExecuteBatch** metody.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. **CloudTable.ExecuteBatch** metoda vrátí seznam hodnot **TableResult** objekty kde každý **TableResult** objektu můžete prověřit, abyste zjistili úspěch nebo neúspěch jednotlivé operace. V tomto příkladu předat zobrazení seznamu a zobrazení zobrazte výsledky jednotlivých operací. 
 
    ```csharp
    return View(results);
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky, klikněte pravým tlačítkem na **tabulky**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **AddEntities** názvu zobrazení a vyberte **přidat**.

1. Otevřít `AddEntities.cshtml`a upravit ji tak, aby vypadal jako následující.

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

1. V **Průzkumníka řešení**, rozbalte **zobrazení -> sdílený** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **přidat entity** zobrazíte výsledky podobně jako na následujícím snímku obrazovky:
  
    ![Přidání entit](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Můžete ověřit, že entita byla přidána pomocí následujících kroků v části [Get jedna entita](#get-a-single-entity). Můžete také použít [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) zobrazíte všechny entity pro tabulky.

## <a name="get-a-single-entity"></a>Získat jednu entitu

Tato část ukazuje, jak získat jedné entity z tabulky pomocí klíč řádku entity a klíč oddílu. 

> [!NOTE]
> 
> Této části se předpokládá dokončení kroků v [nastavení vývojového prostředí](#set-up-the-development-environment)a používá data z [do tabulky přidat dávku entit](#add-a-batch-of-entities-to-a-table). 

1. Otevřete soubor `TablesController.cs`.

1. Přidat metodu nazvanou **GetSingle** , který vrátí **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **GetSingle** metoda, získání **CloudStorageAccount** objekt reprezentující informace o vašem účtu úložiště. Použijte následující kód k získání připojovacího řetězce k úložišti a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure jste přístup.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání **CloudTableClient** objekt představuje klienta služby table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získání **CloudTable** objekt, který reprezentuje odkaz na tabulku, ze kterého jsou načítání entit. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvoření objektu operaci načtení, který přebírá entity objektu odvozeného od **TableEntity**. První parametr je *partitionKey*, a druhý parametr je *rowKey*. Pomocí **CustomerEntity** třídy a data uvedená v části [do tabulky přidat dávku entit](#add-a-batch-of-entities-to-a-table), dotazuje tabulku pro následující fragment kódu **CustomerEntity**entitu *partitionKey* hodnotu "Novák" a *rowKey* hodnotu "Ben":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Provedení operace načtení.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Výsledek předána do zobrazení pro zobrazení.

    ```csharp
    return View(result);
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky, klikněte pravým tlačítkem na **tabulky**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **GetSingle** názvu zobrazení a vyberte **přidat**.

1. Otevřít `GetSingle.cshtml`a upravit ji tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumníka řešení**, rozbalte **zobrazení -> sdílený** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **získat jednotné** zobrazíte výsledky podobně jako na následujícím snímku obrazovky:
  
    ![Získání jedné](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Získání všech entit v oddílu

Jak je uvedeno v části [přidání entity do tabulky](#add-an-entity-to-a-table), kombinace oddílu a klíč řádku jednoznačně identifikují entitu v tabulce. Entity se stejným klíčem oddílu můžete dotazovat rychleji než entity mají různé klíče oddílů. Tato část ukazuje, jak dotaz na tabulku pro všechny entity z zadaný oddíl.  

> [!NOTE]
> 
> Této části se předpokládá dokončení kroků v [nastavení vývojového prostředí](#set-up-the-development-environment)a používá data z [do tabulky přidat dávku entit](#add-a-batch-of-entities-to-a-table). 

1. Otevřete soubor `TablesController.cs`.

1. Přidat metodu nazvanou **GetPartition** , který vrátí **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **GetPartition** metoda, získání **CloudStorageAccount** objekt reprezentující informace o vašem účtu úložiště. Použijte následující kód k získání připojovacího řetězce k úložišti a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure jste přístup.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání **CloudTableClient** objekt představuje klienta služby table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získání **CloudTable** objekt, který reprezentuje odkaz na tabulku, ze kterého jsou načítání entit. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvořit instanci **TableQuery** určující dotaz **kde** klauzuli. Pomocí **CustomerEntity** třídy a data uvedená v části [do tabulky přidat dávku entit](#add-a-batch-of-entities-to-a-table), následující fragment kódu dotazuje tabulku pro všechny entity ve kterém  **PartitionKey** (poslední název zákazníka) má hodnotu "Macek":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. V rámci smyčky, zavolejte **CloudTable.ExecuteQuerySegmented** metody předáním objektu dotazu je vytvořena instance v předchozím kroku.  **CloudTable.ExecuteQuerySegmented** metoda vrátí hodnotu **TableContinuationToken** objekt, který - při **null** – znamená, že neexistují žádné další entity načíst. V rámci smyčky použijte další smyčky k iteraci přes vrácené entity. V následujícím příkladu kódu je každá entita, vrácený přidána do seznamu. Jakmile smyčky konců seznamu předána do zobrazení pro zobrazení: 

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

1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky, klikněte pravým tlačítkem na **tabulky**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **GetPartition** názvu zobrazení a vyberte **přidat**.

1. Otevřít `GetPartition.cshtml`a upravit ji tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumníka řešení**, rozbalte **zobrazení -> sdílený** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **získat oddíl** zobrazíte výsledky podobně jako na následujícím snímku obrazovky:
  
    ![Získat oddíl](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Odstranění entity

Tato část ukazuje, jak odstranit entity z tabulky.

> [!NOTE]
> 
> Této části se předpokládá dokončení kroků v [nastavení vývojového prostředí](#set-up-the-development-environment)a používá data z [do tabulky přidat dávku entit](#add-a-batch-of-entities-to-a-table). 

1. Otevřete soubor `TablesController.cs`.

1. Přidat metodu nazvanou **DeleteEntity** , který vrátí **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **DeleteEntity** metoda, získání **CloudStorageAccount** objekt reprezentující informace o vašem účtu úložiště. Použijte následující kód k získání připojovacího řetězce k úložišti a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure jste přístup.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání **CloudTableClient** objekt představuje klienta služby table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získání **CloudTable** objekt, který reprezentuje odkaz na tabulku, ze kterého se odstraňuje se entita. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvoření objektu operace delete, který přebírá entity objektu odvozeného od **TableEntity**. V tomto případě používáme **CustomerEntity** třídy a data uvedená v části [do tabulky přidat dávku entit](#add-a-batch-of-entities-to-a-table). Entity **ETag** musí být nastavena na platnou hodnotu.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Provedení operace odstranění.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Výsledek předána do zobrazení pro zobrazení.

    ```csharp
    return View(result);
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky, klikněte pravým tlačítkem na **tabulky**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **DeleteEntity** názvu zobrazení a vyberte **přidat**.

1. Otevřít `DeleteEntity.cshtml`a upravit ji tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumníka řešení**, rozbalte **zobrazení -> sdílený** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **odstranit entitu** zobrazíte výsledky podobně jako na následujícím snímku obrazovky:
  
    ![Získání jedné](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Další postup
Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.

  * [Začínáme s úložištěm objektů blob v Azure a Visual Studio připojené služby (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Začínáme s Azure queue storage a Visual Studio připojené služby (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
