---
title: Začínáme s Azure table storage a Visual Studio připojené služby (ASP.NET) | Microsoft Docs
description: Jak začít používat úložiště tabulek Azure po připojení k účtu úložiště pomocí Visual Studio připojené služby v projektu ASP.NET v sadě Visual Studio
services: storage
documentationcenter: ''
author: kraigb
manager: ghogen
editor: ''
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: kraigb
ms.openlocfilehash: 646ff3a12d1b28f99376ea67af25f1b6858d675a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Začínáme s Azure table storage a Visual Studio připojené služby (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Přehled

Azure Table storage umožňuje ukládat velké množství strukturovaná data. Služba je úložištěm dat typu NoSQL, která přijímá ověřených volání z uvnitř i vně cloudu Azure. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat.

Tento kurz ukazuje, jak napsat kód ASP.NET pro některé běžné scénáře s využitím Azure table storage entity. Mezi tyto scénáře patří vytvoření tabulky a přidání, dotazování a odstranění entity tabulky. 

## <a name="prerequisites"></a>Požadavky

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Účet služby Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Vytvořit řadič MVC 

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na **řadiče**a v místní nabídce vyberte **Přidat -> řadiče**.

    ![Přidat řadič do aplikace ASP.NET MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. Na **přidat vygenerované uživatelské rozhraní** dialogovém okně, vyberte **kontroler MVC 5 – prázdný**a vyberte **přidat**.

    ![Zadejte typ řadiče MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. Na **přidat kontroler** dialogové okno, názvu kontroleru *TablesController*a vyberte **přidat**.

    ![Název řadiče MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Přidejte následující *pomocí* direktivy pro `TablesController.cs` souboru:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Vytvořte třídu modelu

Řadu příklady v tomto článku používají **TableEntity**-odvozené třídy s názvem **CustomerEntity**. Následující postup vás provede deklarace tuto třídu jako třídu modelu:

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na **modely**a v místní nabídce vyberte **Přidat -> třída**.

1. Na **přidat novou položku** dialogové okno, název třídy, **CustomerEntity**.

1. Otevřete `CustomerEntity.cs` souboru a přidejte následující **pomocí** – direktiva:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Třída upravte tak, aby po dokončení třída je deklarován jako v následujícím kódu. Třída deklaruje třídu entity, která je volána **CustomerEntity** která používá jméno zákazníka jako klíč řádku a jeho příjmení jako klíč oddílu.

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
> V této části se předpokládá dokončení kroků v [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu s názvem **CreateTable** , který vrací **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **CreateTable** metody get **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použít následující kód k získání připojovacího řetězce úložiště a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání **CloudTableClient** objekt představuje klienta služby table.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získání **CloudTable** objekt, který reprezentuje odkaz na název požadované tabulky. **CloudTableClient.GetTableReference** metoda neprovede požadavek tabulka úložiště. Odkaz se vrátí, zda existuje v tabulce. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Volání **CloudTable.CreateIfNotExists** metodu pro vytvoření tabulky, pokud ještě neexistuje. **CloudTable.CreateIfNotExists** metoda vrátí **true** Pokud tabulka neexistuje a je úspěšně vytvořen. V opačném **false** je vrácen.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Aktualizace **ViewBag** s názvem tabulky.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. V **Průzkumníku řešení**, rozbalte **zobrazení** složku, klikněte pravým tlačítkem na **tabulky**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **CreateTable** pro název zobrazení, vyberte **přidat**.

1. Otevřete `CreateTable.cshtml`a upravit ho tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. V **Průzkumníku řešení**, rozbalte **-zobrazení > sdílené** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **vytvořit tabulku** a zobrazte výsledky podobné následujícím snímku obrazovky:
  
    ![Vytvořit tabulku](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Jak je uvedeno nahoře, **CloudTable.CreateIfNotExists** metoda vrátí **true** pouze když neexistuje a k vytvoření tabulky. Proto pokud spustíte aplikaci v tabulce existuje, vrátí metoda **false**. Aplikace je třeba spustit vícekrát, je nutné odstranit tabulky před spuštěním aplikace znovu. Odstraňování tabulky, můžete to udělat pomocí **CloudTable.Delete** metoda. Můžete také odstranit pomocí tabulky [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) nebo [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky

*Entity* mapu, která C\# objekty pomocí vlastní třídy odvozené od **TableEntity**. Když budete chtít do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti vaší entity. V této části se zobrazí, jak definovat třídu entity, která používá jméno zákazníka jako klíč řádku a jeho příjmení jako klíč oddílu. Společně pak klíč oddílu a řádku entity jednoznačně identifikují entitu v tabulce. Na entity se stejným klíčem oddílu je možné se (v porovnání s těmi, které mají různé klíče oddílů) rychleji dotazovat, ale používání různých klíčů oddílů umožňuje větší škálovatelnost paralelních operací. Jakákoli vlastnost, která by měly být uložené ve službě table musí být vlastnost veřejná vlastnost podporovaného typu, který zveřejňuje nastavení i načítání hodnot.
Třídy entita *musí* deklarovat veřejný konstruktor bez parametrů.

> [!NOTE]
> 
> V této části se předpokládá dokončení kroků v [nastavení vývojového prostředí](#set-up-the-development-environment).

1. Otevřete soubor `TablesController.cs`.

1. Přidejte následující direktivu tak, aby kód `TablesController.cs` můžete přístup k souboru **CustomerEntity** – třída:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Přidejte metodu s názvem **AddEntity** , který vrací **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **AddEntity** metody get **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použít následující kód k získání připojovacího řetězce úložiště a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání **CloudTableClient** objekt představuje klienta služby table.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získání **CloudTable** objekt, který reprezentuje odkaz na tabulku, do které chcete přidat novou entitu. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytváření instancí a inicializace **CustomerEntity** třídy.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Vytvoření **TableOperation** objekt, který se vloží entitu zákazníka.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Spuštění operace insert voláním **CloudTable.Execute** metoda. Výsledek operace můžete ověřit zkontrolováním **TableResult.HttpStatusCode** vlastnost. Stavový kód 2xx označuje, že akce, kterou klient požaduje byl úspěšně zpracován. Například úspěšné vložení výsledků nové entity v kód stavu HTTP 204, což znamená, že operace byla úspěšně zpracována a server nevrátil žádný obsah.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Aktualizace **ViewBag** pomocí názvu tabulky a výsledky operace insert.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. V **Průzkumníku řešení**, rozbalte **zobrazení** složku, klikněte pravým tlačítkem na **tabulky**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **AddEntity** pro název zobrazení, vyberte **přidat**.

1. Otevřete `AddEntity.cshtml`a upravit ho tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. V **Průzkumníku řešení**, rozbalte **-zobrazení > sdílené** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **Přidat entitu** a zobrazte výsledky podobné následujícím snímku obrazovky:
  
    ![Přidání entity](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Můžete ověřit, že byl přidán entity podle pokynů v části [získat jedné entity](#get-a-single-entity). Můžete také [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) zobrazíte všechny entity pro vaše tabulky.

## <a name="add-a-batch-of-entities-to-a-table"></a>Přidat do tabulky dávky entit

Kromě toho moct [přidání entity do tabulky, jeden v čase](#add-an-entity-to-a-table), můžete také přidat entity v dávce. Přidávání entit v dávce zkracuje dobu odezvy mezi kódu a služby Azure table. Následující kroky ukazují, jak přidat více entit do tabulky s jedním vložit operace:

> [!NOTE]
> 
> V této části se předpokládá dokončení kroků v [nastavení vývojového prostředí](#set-up-the-development-environment).

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu s názvem **AddEntities** , který vrací **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **AddEntities** metody get **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použít následující kód k získání připojovacího řetězce úložiště a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání **CloudTableClient** objekt představuje klienta služby table.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získání **CloudTable** objekt, který reprezentuje odkaz na tabulku, do které chcete přidat nové entity. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvoření instance některých objektů zákazníka na základě **CustomerEntity** třída uvedené v části modelu [do tabulky přidat entitu](#add-an-entity-to-a-table).

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

1. Spuštění operace insert batch voláním **CloudTable.ExecuteBatch** metoda.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. **CloudTable.ExecuteBatch** metoda vrátí seznam hodnot **TableResult** objekty kde každý **TableResult** objekt můžete prověřit, abyste zjistili úspěch nebo neúspěch každé operace. V tomto příkladu předat zobrazení seznamu a umožní zobrazení zobrazit výsledky jednotlivých operací. 
 
    ```csharp
    return View(results);
    ```

1. V **Průzkumníku řešení**, rozbalte **zobrazení** složku, klikněte pravým tlačítkem na **tabulky**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **AddEntities** pro název zobrazení, vyberte **přidat**.

1. Otevřete `AddEntities.cshtml`a upravit ho tak, aby vypadal jako následující.

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

1. V **Průzkumníku řešení**, rozbalte **-zobrazení > sdílené** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **přidat entity** a zobrazte výsledky podobné následujícím snímku obrazovky:
  
    ![Přidání entit](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Můžete ověřit, že byl přidán entity podle pokynů v části [získat jedné entity](#get-a-single-entity). Můžete také [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) zobrazíte všechny entity pro vaše tabulky.

## <a name="get-a-single-entity"></a>Získat jedné entity

Tato část ukazuje, jak získat jedné entity z tabulky pomocí klíč řádku entity a klíč oddílu. 

> [!NOTE]
> 
> V této části se předpokládá dokončení kroků v [nastavení vývojového prostředí](#set-up-the-development-environment)a používá data z [dávky entit přidat do tabulky](#add-a-batch-of-entities-to-a-table). 

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu s názvem **GetSingle** , který vrací **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **GetSingle** metody get **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použít následující kód k získání připojovacího řetězce úložiště a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání **CloudTableClient** objekt představuje klienta služby table.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získání **CloudTable** objekt, který reprezentuje odkaz na tabulku, ze kterého jsou načítání entity. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvořit objekt operaci načtení, která přebírá objekt entity, který je odvozen od **TableEntity**. První parametr je *partitionKey*, a druhý parametr je *rowKey*. Pomocí **CustomerEntity** třídy a data uvedená v části [dávky entit přidat do tabulky](#add-a-batch-of-entities-to-a-table), následující fragment kódu dotazuje tabulku pro **CustomerEntity** entita s *partitionKey* hodnotu "Smith" a *rowKey* hodnotu "Ben":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Provedení operace načtení.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Výsledek předejte zobrazení pro zobrazení.

    ```csharp
    return View(result);
    ```

1. V **Průzkumníku řešení**, rozbalte **zobrazení** složku, klikněte pravým tlačítkem na **tabulky**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **GetSingle** pro název zobrazení, vyberte **přidat**.

1. Otevřete `GetSingle.cshtml`a upravit ho tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumníku řešení**, rozbalte **-zobrazení > sdílené** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **získat jeden** a zobrazte výsledky podobné následujícím snímku obrazovky:
  
    ![Získat jeden](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Získání všech entit v oddílu

Jak je uvedeno v části [do tabulky přidat entitu](#add-an-entity-to-a-table), kombinace oddílu a klíč řádku jednoznačně identifikují entitu v tabulce. Entity se stejným klíčem oddílu můžete položit dotaz na rychlejší než entity s různé klíče oddílů. V této části ukazuje, jak dotaz na tabulku pro všechny entity ze zadaného oddílu.  

> [!NOTE]
> 
> V této části se předpokládá dokončení kroků v [nastavení vývojového prostředí](#set-up-the-development-environment)a používá data z [dávky entit přidat do tabulky](#add-a-batch-of-entities-to-a-table). 

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu s názvem **GetPartition** , který vrací **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **GetPartition** metody get **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použít následující kód k získání připojovacího řetězce úložiště a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání **CloudTableClient** objekt představuje klienta služby table.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získání **CloudTable** objekt, který reprezentuje odkaz na tabulku, ze kterého jsou načítání entity. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytváření instancí **TableQuery** zadat dotaz v objektu **kde** klauzule. Pomocí **CustomerEntity** třídy a data uvedená v části [dávky entit přidat do tabulky](#add-a-batch-of-entities-to-a-table), následující fragment kódu dotazuje tabulku pro všechny entity kde **PartitionKey** (příjmení zákazníka) má hodnotu "Smith":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. V rámci smyčku, volání **CloudTable.ExecuteQuerySegmented** metoda předání objektu dotazu instanci v předchozím kroku.  **CloudTable.ExecuteQuerySegmented** metoda vrátí **TableContinuationToken** objektu, který - při **null** – označuje, že neexistují žádné další entity načíst. V rámci smyčky Iterujte přes vrácené entity pomocí jiné smyčky. V následujícím příkladu kódu je každou vrácenou entitu přidat do seznamu. Po skončení smyčky seznamu byla předána do zobrazení pro zobrazení: 

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

1. V **Průzkumníku řešení**, rozbalte **zobrazení** složku, klikněte pravým tlačítkem na **tabulky**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **GetPartition** pro název zobrazení, vyberte **přidat**.

1. Otevřete `GetPartition.cshtml`a upravit ho tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumníku řešení**, rozbalte **-zobrazení > sdílené** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **získat oddílu** a zobrazte výsledky podobné následujícím snímku obrazovky:
  
    ![Získat oddílu](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Odstranění entity

Tato část ukazuje postup odstranění entity z tabulky.

> [!NOTE]
> 
> V této části se předpokládá dokončení kroků v [nastavení vývojového prostředí](#set-up-the-development-environment)a používá data z [dávky entit přidat do tabulky](#add-a-batch-of-entities-to-a-table). 

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu s názvem **DeleteEntity** , který vrací **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **DeleteEntity** metody get **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použít následující kód k získání připojovacího řetězce úložiště a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání **CloudTableClient** objekt představuje klienta služby table.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získání **CloudTable** objekt, který reprezentuje odkaz na tabulku, ze kterého chcete odstranit entitu. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvořit objekt operaci odstranění, která přebírá objekt entity, který je odvozen od **TableEntity**. V tomto případě používáme **CustomerEntity** třídy a data uvedená v části [dávky entit přidat do tabulky](#add-a-batch-of-entities-to-a-table). Entity **značka ETag** musí být nastavena na platnou hodnotu.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Provést operaci odstranění.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Výsledek předejte zobrazení pro zobrazení.

    ```csharp
    return View(result);
    ```

1. V **Průzkumníku řešení**, rozbalte **zobrazení** složku, klikněte pravým tlačítkem na **tabulky**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **DeleteEntity** pro název zobrazení, vyberte **přidat**.

1. Otevřete `DeleteEntity.cshtml`a upravit ho tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumníku řešení**, rozbalte **-zobrazení > sdílené** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Spusťte aplikaci a vyberte **odstranit entity** a zobrazte výsledky podobné následujícím snímku obrazovky:
  
    ![Získat jeden](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Další postup
Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.

  * [Začínáme s Azure blob storage a Visual Studio připojené služby (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Začínáme s Azure queue storage a Visual Studio připojené služby (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
