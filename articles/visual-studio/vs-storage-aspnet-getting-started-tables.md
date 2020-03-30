---
title: Začínáme s úložištěm tabulek Azure pomocí Visual Studia (ASP.NET)
description: Jak začít používat úložiště tabulek Azure v projektu ASP.NET v sadě Visual Studio po připojení k účtu úložiště pomocí služby Visual Studio Connected Services
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979626"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Začínáme s úložištěm tabulek Azure a připojenými službami Visual Studia (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Přehled

Azure Table storage umožňuje ukládat velké množství strukturovaných dat. Služba je úložiště dat NoSQL, které přijímá ověřená volání z cloudu Azure i mimo něj. Tabulky Azure jsou ideální pro ukládání strukturovaných, nerelačních dat.

Tento kurz ukazuje, jak psát ASP.NET kód pro některé běžné scénáře pomocí entit úložiště tabulek Azure. Tyto scénáře zahrnují vytvoření tabulky a přidání, dotazování a odstranění entit tabulky. 

## <a name="prerequisites"></a>Požadavky

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Účet služby Azure Storage](../storage/common/storage-account-create.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Vytvoření řadiče MVC 

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **řadiče**a v místní nabídce vyberte **položku Správce >doplňků**.

    ![Přidání ovladače do aplikace ASP.NET MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. V dialogovém okně **Přidat kancovací okno** vyberte **MVC 5 Controller - Empty**a vyberte **Přidat**.

    ![Zadejte typ řadiče MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. V dialogovém okně **Přidat řadič** pojmenujte řadič *TablesController*a vyberte **Přidat**.

    ![Pojmenování řadiče MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Do souboru přidejte `TablesController.cs` následující *příkazy pomocí* direktiv:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Vytvoření třídy modelu

Mnoho příkladů v tomto článku používá třídu odvoděnou odtíráno **tabulkou**s názvem **CustomerEntity**. Následující kroky vás provedou deklarováním této třídy jako třídy modelu:

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **položku Modely**a v místní nabídce vyberte **položku Přidat >třídu**.

1. V dialogovém okně **Přidat novou položku** pojmenujte třídu **CustomerEntity**.

1. Otevřete `CustomerEntity.cs` soubor a přidejte následující **pomocí** směrnice:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Upravte třídu tak, aby po dokončení byla třída deklarována jako v následujícím kódu. Třída deklaruje třídu entity nazvanou **CustomerEntity,** která používá křestní jméno zákazníka jako klíč řádku a příjmení jako klíč oddílu.

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

Následující kroky ilustrují, jak vytvořit tabulku:

> [!NOTE]
> 
> Tato část předpokládá, že jste dokončili kroky v [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu s názvem **CreateTable,** která vrací **hodnotu ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **CreateTable** metoda získat **Objekt CloudStorageAccount,** který představuje informace o účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci * &lt;* úložiště a účtu úložiště z konfigurace služby Azure: (Změňte>název účtu úložiště na název účtu úložiště Azure, ke které přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získejte **Objekt CloudTableClient** představuje klienta služby table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získejte **Objekt CloudTable,** který představuje odkaz na požadovaný název tabulky. Metoda **CloudTableClient.GetTableReference** neprovádí požadavek proti úložišti tabulky. Odkaz je vrácena, zda tabulka existuje nebo ne. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Volání **CloudTable.CreateIfNotExists** metoda k vytvoření tabulky, pokud ještě neexistuje. Metoda **CloudTable.CreateIfNotExists** vrátí **hodnotu true,** pokud tabulka neexistuje a je úspěšně vytvořena. V opačném případě je **vrácena false.**    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Aktualizujte **ViewBag** s názvem tabulky.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. V **Průzkumníku řešení**rozbalte složku **Zobrazení,** klepněte pravým **tlačítkem**myši na tabulky a v místní nabídce vyberte **příkaz Přidat >zobrazení**.

1. V dialogovém okně **Přidat zobrazení** zadejte pro název zobrazení položku **Vytvořittabulku** a vyberte **Přidat**.

1. Otevřete `CreateTable.cshtml`a upravte tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. V **Průzkumníku řešení**rozbalte složku **Sdílená zobrazení >** a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**přidejte následující **html.actionlink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Spusťte aplikaci a výběrem **možnosti Vytvořit tabulku** zobrazte výsledky podobné následujícímu snímku obrazovky:
  
    ![Vytvoření tabulky](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Jak již bylo zmíněno dříve, **CloudTable.CreateIfNotExists** metoda vrátí **true** pouze v případě, že tabulka neexistuje a je vytvořen. Proto pokud spustíte aplikaci, když tabulka existuje, metoda vrátí **false**. Chcete-li aplikaci spustit vícekrát, musíte tabulku před znovu spuštěním odstranit. Odstranění tabulky lze provést pomocí **metody CloudTable.Delete.** Tabulku můžete odstranit taky pomocí [portálu Azure nebo](https://go.microsoft.com/fwlink/p/?LinkID=525040) [Průzkumníka úložiště Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky

*Entity* mapovat\# na objekty C pomocí vlastní třídy odvozené z **TableEntity**. Když budete chtít do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti vaší entity. V této části uvidíte, jak definovat třídu entity, která používá křestní jméno zákazníka jako klíč řádku a příjmení jako klíč oddílu. Společně pak klíč oddílu a řádku entity jednoznačně identifikují entitu v tabulce. Na entity se stejným klíčem oddílu je možné se (v porovnání s těmi, které mají různé klíče oddílů) rychleji dotazovat, ale používání různých klíčů oddílů umožňuje větší škálovatelnost paralelních operací. Pro všechny vlastnosti, které by měly být uloženy ve službě table, vlastnost musí být veřejná vlastnost podporovaného typu, který zveřejňuje nastavení a načítání hodnoty.
Třída entity *musí* deklarovat veřejný konstruktor bez parametrů.

> [!NOTE]
> 
> Tato část předpokládá, že jste dokončili kroky v [nastavení vývojového prostředí](#set-up-the-development-environment).

1. Otevřete soubor `TablesController.cs`.

1. Přidejte následující direktivu, aby kód v souboru `TablesController.cs` měl přístup ke třídě **CustomerEntity:**

    ```csharp
    using StorageAspnet.Models;
    ```

1. Přidejte metodu s názvem **AddEntity,** která vrací **hodnotu ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **AddEntity** metoda získat **Objekt CloudStorageAccount,** který představuje informace o účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci * &lt;* úložiště a účtu úložiště z konfigurace služby Azure: (Změňte>název účtu úložiště na název účtu úložiště Azure, ke které přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získejte **Objekt CloudTableClient** představuje klienta služby table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získejte **Objekt CloudTable,** který představuje odkaz na tabulku, do které se chystáte přidat novou entitu. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvořte inializujte a inicializovat třídu **CustomerEntity.**

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Vytvořte objekt **TableOperation,** který vloží entitu zákazníka.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Spusťte operaci vložení voláním metody **CloudTable.Execute.** Výsledek operace můžete ověřit kontrolou vlastnosti **TableResult.HttpStatusCode.** Stavový kód 2xx označuje, že akce požadovaná klientem byla úspěšně zpracována. Například úspěšné vložení nových entit má za následek stavový kód HTTP 204, což znamená, že operace byla úspěšně zpracována a server nevrátil žádný obsah.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Aktualizujte **ViewBag** s názvem tabulky a výsledky operace vložení.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. V **Průzkumníku řešení**rozbalte složku **Zobrazení,** klepněte pravým **tlačítkem**myši na tabulky a v místní nabídce vyberte **příkaz Přidat >zobrazení**.

1. V dialogovém okně **Přidat zobrazení** zadejte **addentity** pro název zobrazení a vyberte **Přidat**.

1. Otevřete `AddEntity.cshtml`a upravte tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. V **Průzkumníku řešení**rozbalte složku **Sdílená zobrazení >** a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**přidejte následující **html.actionlink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Spusťte aplikaci a výběrem **možnosti Přidat entitu** zobrazíte výsledky podobné následujícímu snímku obrazovky:
  
    ![Přidání entity](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Můžete ověřit, že entita byla přidána podle kroků v části [Získat jednu entitu](#get-a-single-entity). Explorer [úložiště Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) můžete také použít k zobrazení všech entit pro vaše tabulky.

## <a name="add-a-batch-of-entities-to-a-table"></a>Přidání dávky entit do tabulky

Kromě toho, že můžete [přidávat entitu do tabulky jeden po druhém](#add-an-entity-to-a-table), můžete také přidat entity v dávce. Přidání entit v dávce snižuje počet zpátečních cest mezi kódem a službou azure table service. Následující kroky ilustrují, jak přidat více entit do tabulky s operací jedné vložení:

> [!NOTE]
> 
> Tato část předpokládá, že jste dokončili kroky v [nastavení vývojového prostředí](#set-up-the-development-environment).

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu s názvem **AddEntities,** která vrací **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **AddEntities** metoda získat **Objekt CloudStorageAccount,** který představuje informace o účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci * &lt;* úložiště a účtu úložiště z konfigurace služby Azure: (Změňte>název účtu úložiště na název účtu úložiště Azure, ke které přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získejte **Objekt CloudTableClient** představuje klienta služby table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získejte **Objekt CloudTable,** který představuje odkaz na tabulku, do které se chystáte přidat nové entity. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvořte instanci některých objektů zákazníka na základě třídy modelu **CustomerEntity** uvedené v části [Přidat entitu do tabulky](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Získejte objekt **TableBatchOperation.**

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Přidejte entity do objektu operace dávkové vložení.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Spusťte operaci dávkového vložení voláním metody **CloudTable.ExecuteBatch.**   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. Metoda **CloudTable.ExecuteBatch** vrátí seznam objektů **TableResult,** kde každý objekt **TableResult** může být zkontrolován, aby se zjistilo, že každá jednotlivá operace je úspěšná nebo neúspěšná. V tomto příkladu předaj seznam do zobrazení a nechte zobrazení zobrazit výsledky každé operace. 
 
    ```csharp
    return View(results);
    ```

1. V **Průzkumníku řešení**rozbalte složku **Zobrazení,** klepněte pravým **tlačítkem**myši na tabulky a v místní nabídce vyberte **příkaz Přidat >zobrazení**.

1. V dialogovém okně **Přidat zobrazení** zadejte pro název zobrazení **položku AddEntities** a vyberte **Přidat**.

1. Otevřete `AddEntities.cshtml`a upravte tak, aby vypadal takto.

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

1. V **Průzkumníku řešení**rozbalte složku **Sdílená zobrazení >** a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**přidejte následující **html.actionlink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Spusťte aplikaci a výběrem **možnosti Přidat entity** zobrazíte výsledky podobné následujícímu snímku obrazovky:
  
    ![Přidání entit](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Můžete ověřit, že entita byla přidána podle kroků v části [Získat jednu entitu](#get-a-single-entity). Explorer [úložiště Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) můžete také použít k zobrazení všech entit pro vaše tabulky.

## <a name="get-a-single-entity"></a>Získání jedné entity

Tato část ukazuje, jak získat jednu entitu z tabulky pomocí klíče řádku entity a klíče oddílu. 

> [!NOTE]
> 
> Tato část předpokládá, že jste dokončili kroky v [nastavení vývojového prostředí](#set-up-the-development-environment)a používá data z [přidat dávku entit do tabulky](#add-a-batch-of-entities-to-a-table). 

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu s názvem **GetSingle,** která vrací **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **GetSingle** metoda získat **objekt CloudStorageAccount,** který představuje informace o účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci * &lt;* úložiště a účtu úložiště z konfigurace služby Azure: (Změňte>název účtu úložiště na název účtu úložiště Azure, ke které přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získejte **Objekt CloudTableClient** představuje klienta služby table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získejte **Objekt CloudTable,** který představuje odkaz na tabulku, ze které načítáte entitu. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvořte objekt načtení operace, který přebírá objekt entity odvozený z **TableEntity**. První parametr je *partitionKey*a druhý parametr je *rowKey*. Pomocí třídy **CustomerEntity** a dat uvedených v části [Přidejte dávku entit do tabulky](#add-a-batch-of-entities-to-a-table), následující fragment kódu se dotazuje na tabulku entity **CustomerEntity** s hodnotou *partitionKey* "Smith" a hodnotou *rowKey* "Ben":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Spusťte operaci načtení.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Předaj výsledek do zobrazení pro zobrazení.

    ```csharp
    return View(result);
    ```

1. V **Průzkumníku řešení**rozbalte složku **Zobrazení,** klepněte pravým **tlačítkem**myši na tabulky a v místní nabídce vyberte **příkaz Přidat >zobrazení**.

1. V dialogovém okně **Přidat zobrazení** zadejte pro název zobrazení příkaz **GetSingle** a vyberte **Přidat**.

1. Otevřete `GetSingle.cshtml`a upravte tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumníku řešení**rozbalte složku **Sdílená zobrazení >** a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**přidejte následující **html.actionlink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Spusťte aplikaci a výběrem **možnosti Získat single** zobrazte výsledky podobné následujícímu snímku obrazovky:
  
    ![Získejte jeden](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Získání všech entit v oddílu

Jak je uvedeno v části [Přidání entity do tabulky](#add-an-entity-to-a-table), kombinace oddílu a klíče řádku jednoznačně identifikovat entitu v tabulce. Entity se stejným klíčem oddílu mohou být dotazovány rychleji než entity s různými klíči oddílu. Tato část ukazuje, jak dotaz ovat tabulku pro všechny entity ze zadaného oddílu.  

> [!NOTE]
> 
> Tato část předpokládá, že jste dokončili kroky v [nastavení vývojového prostředí](#set-up-the-development-environment)a používá data z [přidat dávku entit do tabulky](#add-a-batch-of-entities-to-a-table). 

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu s názvem **GetPartition,** která vrací **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **GetPartition** metoda získat **Objekt CloudStorageAccount,** který představuje informace o účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci * &lt;* úložiště a účtu úložiště z konfigurace služby Azure: (Změňte>název účtu úložiště na název účtu úložiště Azure, ke které přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získejte **Objekt CloudTableClient** představuje klienta služby table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získejte **Objekt CloudTable,** který představuje odkaz na tabulku, ze které načítáte entity. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvořte sutipři objektu **TableQuery** určujícího dotaz v klauzuli **Where.** Pomocí třídy **CustomerEntity** a dat uvedených v části [Přidejte dávku entit do tabulky](#add-a-batch-of-entities-to-a-table), zobrazí se následující fragment kódu v tabulce pro všechny entity, kde má **partitionkey** (příjmení zákazníka) hodnotu "Novák":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. V rámci smyčky volejte metodu **CloudTable.ExecuteQuerySegmented,** která předává objekt dotazu, který jste instancitoubyli v předchozím kroku.  Metoda **CloudTable.ExecuteQuerySegmented** vrátí objekt **TableContinuationToken,** který - pokud **je null** - znamená, že neexistují žádné další entity, které by bylo možné načíst. V rámci smyčky použijte jinou smyčku k iterace přes vrácené entity. V následujícím příkladu kódu je každá vrácená entita přidána do seznamu. Po ukončení smyčky je seznam předán do zobrazení pro zobrazení: 

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

1. V **Průzkumníku řešení**rozbalte složku **Zobrazení,** klepněte pravým **tlačítkem**myši na tabulky a v místní nabídce vyberte **příkaz Přidat >zobrazení**.

1. V dialogovém okně **Přidat zobrazení** zadejte pro název zobrazení **příkaz GetPartition** a vyberte **Přidat**.

1. Otevřete `GetPartition.cshtml`a upravte tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumníku řešení**rozbalte složku **Sdílená zobrazení >** a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**přidejte následující **html.actionlink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Spusťte aplikaci a výběrem **možnosti Získat oddíl** zobrazte výsledky podobné následujícímu snímku obrazovky:
  
    ![Získat oddíl](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Odstranění entity

Tato část ukazuje, jak odstranit entitu z tabulky.

> [!NOTE]
> 
> Tato část předpokládá, že jste dokončili kroky v [nastavení vývojového prostředí](#set-up-the-development-environment)a používá data z [přidat dávku entit do tabulky](#add-a-batch-of-entities-to-a-table). 

1. Otevřete soubor `TablesController.cs`.

1. Přidejte metodu s názvem **DeleteEntity,** která vrací **hodnotu ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **DeleteEntity** metoda získat **objekt CloudStorageAccount,** který představuje informace o účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci * &lt;* úložiště a účtu úložiště z konfigurace služby Azure: (Změňte>název účtu úložiště na název účtu úložiště Azure, ke které přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získejte **Objekt CloudTableClient** představuje klienta služby table service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Získejte **Objekt CloudTable,** který představuje odkaz na tabulku, ze které jsou odstranění entity. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Vytvořte objekt operace delete, který přebírá objekt entity odvozený z **TableEntity**. V tomto případě použijeme třídu **CustomerEntity** a data uvedená v části [Přidat dávku entit do tabulky](#add-a-batch-of-entities-to-a-table). **ETag** entity musí být nastaven na platnou hodnotu.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Proveďte operaci odstranění.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Předaj výsledek do zobrazení pro zobrazení.

    ```csharp
    return View(result);
    ```

1. V **Průzkumníku řešení**rozbalte složku **Zobrazení,** klepněte pravým **tlačítkem**myši na tabulky a v místní nabídce vyberte **příkaz Přidat >zobrazení**.

1. V dialogovém okně **Přidat zobrazení** zadejte pro název zobrazení položku **DeleteEntity** a vyberte **Přidat**.

1. Otevřete `DeleteEntity.cshtml`a upravte tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumníku řešení**rozbalte složku **Sdílená zobrazení >** a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**přidejte následující **html.actionlink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Spusťte aplikaci a výběrem **možnosti Odstranit entitu** zobrazíte výsledky podobné následujícímu snímku obrazovky:
  
    ![Získejte jeden](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Další kroky
Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.

  * [Začínáme s úložištěm objektů blob Azure a připojenými službami Visual Studia (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Začínáme s úložištěm front Azure a připojenými službami Visual Studia (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
