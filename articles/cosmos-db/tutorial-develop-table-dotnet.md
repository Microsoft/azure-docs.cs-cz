---
title: Vývoj s využitím rozhraní Table API pomocí sady .NET SDK
titleSuffix: Azure Cosmos DB
description: Další informace jak vyvíjet s rozhraním Table API ve službě Azure Cosmos DB s použitím sady .NET SDK
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: f10cb17f0300b6bd21d17b1e2ff204d57e4a7988
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53251177"
---
# <a name="develop-with-azure-cosmos-dbs-table-api-using-net-sdk"></a>Vývoj s využitím služby Azure Cosmos DB Table API pomocí sady .NET SDK

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru databáze Azure Cosmos.

Tento kurz se zabývá následujícími úkony: 

> [!div class="checklist"] 
> * Vytvoření účtu služby Azure Cosmos DB 
> * Povolení funkcí v souboru app.config 
> * Vytvoření tabulky pomocí rozhraní [Table API](table-introduction.md)
> * Přidání entity do tabulky 
> * Vložení dávky entit 
> * Načtení jedné entity 
> * Dotazování entit s využitím automatických sekundárních indexů 
> * Nahrazení entity 
> * Odstranění entity 
> * Odstranění tabulky
 
## <a name="tables-in-azure-cosmos-db"></a>Tabulky ve službě Azure Cosmos DB 

Azure Cosmos DB poskytuje rozhraní [Table API](table-introduction.md) pro aplikace, které potřebují úložiště párů klíč-hodnota s návrhem bez schématu. Rozhraní Table API služby Azure Cosmos DB i [Azure Table Storage](../storage/common/storage-introduction.md) teď podporují stejné sady SDK a stejná rozhraní REST API. Azure Cosmos DB můžete použít k vytvoření tabulek s požadavky na vysokou propustnost.

Tento kurz je určený pro vývojáře, kteří znají sadu SDK služby Azure Table Storage a chtějí využít prémiové funkce dostupné ve službě Azure Cosmos DB. Vychází z kurzu [Začínáme se službou Azure Table Storage pomocí .NET](table-storage-how-to-use-dotnet.md) a ukazuje, jak využívat další možnosti, jako jsou sekundární indexy, zřízená propustnost a vícenásobné navádění. Tento kurz popisuje použití webu Azure Portal k vytvoření účtu služby Azure Cosmos DB a následnému sestavení a nasazení aplikace Table API. Projdeme také příklady v .NET, ve kterých se vytvoří a odstraní tabulka a také vkládají, aktualizují a odstraňují data v tabulce nebo se na ně zadávají dotazy. 

Pokud aktuálně používáte službu Azure Table Storage, získáte s rozhraním Table API služby Azure Cosmos DB následující výhody:

- [Globální distribuce](distribute-data-globally.md) na klíč s vícenásobným naváděním a [automatickým i ručním převzetím služeb při selhání](high-availability.md)
- Podpora automatického indexování nezávislého na schématu pro všechny vlastnosti (sekundární indexy) a rychlé dotazy 
- Podpora [nezávislého škálování úložiště a propustnosti](partition-data.md) v jakémkoli počtu oblastí
- Podpora [vyhrazené propustnosti pro jednotlivé tabulky](request-units.md), kterou je možné škálovat na stovky až miliony požadavků za sekundu
- Podpora [pěti přizpůsobitelných úrovní konzistence](consistency-levels.md) pro využití dostupnosti, latence a konzistence na základě potřeb vašich aplikací
- 99,99% dostupnost v rámci jedné oblasti, možnost přidat další oblasti pro zajištění vyšší dostupnosti a [nejlepší komplexní smlouvy SLA v oboru](https://azure.microsoft.com/support/legal/sla/cosmos-db/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) týkající se obecné dostupnosti
- Práce s existující sadou .NET SDK pro Azure Storage beze změn kódu aplikace

Tento kurz se věnuje používání rozhraní Table API služby Azure Cosmos DB s využitím sady .NET SDK. Sadu [.NET SDK pro rozhraní Table API služby Azure Cosmos DB](https://aka.ms/tableapinuget) si můžete stáhnout z NuGet.

Další informace o složitých úlohách služby Azure Table Storage najdete tady:

* [Úvod do rozhraní Table API služby Azure Cosmos DB](table-introduction.md)
* Referenční dokumentace ke službě Table service s úplnými podrobnostmi o dostupných rozhraních API: [Sada .NET SDK pro rozhraní Table API služby Azure Cosmos DB](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)

### <a name="about-this-tutorial"></a>O tomto kurzu
Tento kurz je určený pro vývojáře, kteří znají sadu SDK služby Azure Table Storage a chtějí využít prémiové funkce dostupné ve službě Azure Cosmos DB. Vychází z kurzu [Začínáme se službou Azure Table Storage pomocí .NET](table-storage-how-to-use-dotnet.md) a ukazuje, jak využívat další možnosti, jako jsou sekundární indexy, zřízená propustnost a vícenásobné navádění. Popisujeme použití webu Azure Portal k vytvoření účtu služby Azure Cosmos DB a následnému sestavení a nasazení aplikace Table. Projdeme také příklady v .NET, ve kterých se vytvoří a odstraní tabulka a také vkládají, aktualizují a odstraňují data v tabulce nebo se na ně zadávají dotazy. 

Pokud ještě nemáte nainstalovanou sadu Visual Studio 2017, můžete stáhnout a použít **bezplatnou verzi** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Začněme vytvořením účtu služby Azure Cosmos DB na webu Azure Portal.  
 
> [!IMPORTANT]  
> Abyste mohli pracovat s obecně dostupnými sadami Table API SDK, musíte si vytvořit nový účet Table API. Obecně dostupné sady SDK nepodporují účty Table API vytvořené během období Preview. 
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujeme aplikaci Table z GitHubu, nastavíme připojovací řetězec a spustíme ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu. 

1. Otevřete okno terminálu Git, třeba Git Bash, a pomocí příkazu `cd` přejděte do složky, do které chcete nainstalovat ukázkovou aplikaci. 

    ```bash
    cd "C:\git-samples"
    ```

2. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Potom otevřete soubor řešení v sadě Visual Studio. 

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace. Tím aplikaci umožníte komunikovat s hostovanou databází. 

1. Na webu [Azure Portal](https://portal.azure.com/) klikněte na **Připojovací řetězec**. 

    Pomocí tlačítka pro kopírování na pravé straně obrazovky zkopírujte PRIMÁRNÍ PŘIPOJOVACÍ ŘETĚZEC.

    ![Zobrazení a zkopírování hodnoty PŘIPOJOVACÍ ŘETĚZEC v podokně Připojovací řetězec](./media/create-table-dotnet/connection-string.png)

2. V sadě Visual Studio otevřete soubor app.config. 

3. Vzhledem k tomu, že v tomto kurzu se nepoužívá emulátor úložiště, zrušte komentář u StorageConnectionString na řádku 8 a přidejte komentář u StorageConnectionString na řádku 7. Řádky 7 a 8 by teď měly vypadat takto:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Vložte PRIMÁRNÍ PŘIPOJOVACÍ ŘETĚZEC z portálu do hodnoty StorageConnectionString na řádku 8. Řetězec vložte do uvozovek.
   
    > [!IMPORTANT]
    > Pokud váš koncový bod používá adresu documents.azure.com, znamená to, že máte účet Preview, a pokud chcete pracovat s obecně dostupnou sadou Table API SDK, musíte si vytvořit [nový účet Table API](#create-a-database-account). 
    >

    Řádek 8 by teď měl vypadat nějak takto:

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. Uložte soubor app.config.

Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. 

## <a name="azure-cosmos-db-capabilities"></a>Možnosti služby Azure Cosmos DB
Azure Cosmos DB podporuje řadu možností, které nejsou dostupné v rozhraní API služby Azure Table Storage. 

K určitým funkcím se přistupuje přes nová přetížení CreateCloudTableClient, která umožňují zadat zásady připojení a úroveň konzistence.

| Nastavení připojení tabulky | Popis |
| --- | --- |
| Režim připojení  | Azure Cosmos DB podporuje dva režimy připojení. V režimu `Gateway` se požadavky vždy provádějí na bránu služby Azure Cosmos DB, která je směruje do odpovídajících datových oddílů. V režimu připojení `Direct` klient načítá mapování tabulek na oddíly a požadavky se provádějí přímo na datové oddíly. Doporučujeme výchozí režim `Direct`.  |
| Protokol připojení | Azure Cosmos DB podporuje dva protokoly připojení – `Https` a `Tcp`. Protokol `Tcp` je výchozí a doporučený, protože je jednodušší. |
| Upřednostňovaná umístění | Čárkami oddělený seznam upřednostňovaných (vícenásobné navádění) umístění pro čtení. Ke každému účtu služby Azure Cosmos DB je možné přiřadit 1 až více než 30 oblastí. Každá instance klienta může určit podmnožinu těchto oblastí v upřednostňovaném pořadí pro zajištění nízké latence čtení. Oblasti musí být pojmenované pomocí jejich [zobrazovaného názvu](https://msdn.microsoft.com/library/azure/gg441293.aspx), například `West US`. Viz také [Rozhraní API pro vícenásobné navádění](tutorial-global-distribution-table.md). |
| Úrovně konzistence | Můžete zvolit kompromis mezi latencí, konzistencí a dostupností, a to volbou z pěti jasně definovaných úrovní konzistence: `Strong`, `Session`, `Bounded-Staleness`, `ConsistentPrefix` a `Eventual`. Výchozí je `Session`. Volba úrovně konzistence má za následek výrazný rozdíl ve výkonu v nastaveních s více oblastmi. Podrobnosti najdete v tématu [Úrovně konzistence](consistency-levels.md). |

Další funkce je možné povolit prostřednictvím následujících hodnot konfigurace `appSettings`.

| Klíč | Popis |
| --- | --- |
| TableQueryMaxItemCount | Konfiguruje maximální počet položek vracených jednotlivými dotazy na tabulku v jedné době odezvy. Výchozí hodnota je `-1`, která umožňuje službě Azure Cosmos DB dynamicky určovat hodnotu za běhu. |
| TableQueryEnableScan | Pokud dotaz pro nějaký filtr nemůže použít index, přesto se spustí s využitím prohledávání. Výchozí hodnota je `false`.|
| TableQueryMaxDegreeOfParallelism | Stupeň paralelismu provádění dotazu napříč oddíly. Hodnota `0` znamená sériové provádění bez předběžného načítání, hodnota `1` znamená sériové provádění s předběžným načítáním a vyšší hodnoty zvyšují míru paralelismu. Výchozí hodnota je `-1`, která umožňuje službě Azure Cosmos DB dynamicky určovat hodnotu za běhu. |

Pokud chcete změnit výchozí hodnotu, z Průzkumníku řešení v sadě Visual Studio otevřete soubor `app.config`. Přidejte obsah níže uvedeného prvku `<appSettings>`. Hodnotu `account-name` nahraďte názvem svého účtu úložiště a hodnotu `account-key` nahraďte přístupovým klíčem účtu. 

```xml
<configuration>
    ...
    <appSettings>
      <!-- Client options -->
      <add key="CosmosDBStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://account-name.table.cosmosdb.azure.com" />
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

Ještě jednou se stručně podívejme na to, co se v aplikaci děje. Otevřete soubor `Program.cs` a zjistíte, že tyto řádky kódu vytvářejí prostředky tabulky. 

## <a name="create-the-table-client"></a>Vytvoření klienta tabulky
Pro připojení k účtu tabulky inicializujete klienta `CloudTableClient`.

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
Tento klient se inicializuje s použitím hodnot konfigurace `TableConnectionMode`, `TableConnectionProtocol`, `TableConsistencyLevel` a `TablePreferredLocations`, pokud jsou zadané v nastavení aplikace.

## <a name="create-a-table"></a>Vytvoření tabulky

Pak vytvoříte tabulku pomocí metody `CloudTable`. Tabulky ve službě Azure Cosmos DB se můžou nezávisle škálovat z hlediska úložiště a propustnosti a služba automaticky zajišťuje jejich dělení. 

```csharp
CloudTable table = tableClient.GetTableReference("people");
400
table.CreateIfNotExists(throughput: 800);
```

Ve způsobu vytváření tabulek je důležitý rozdíl. Azure Cosmos DB vyhrazuje propustnost, na rozdíl od modelu založeném na spotřebě transakcí úložiště Azure. Vaše propustnost je vyhrazená, takže nikdy nedojde k omezení, pokud vaše frekvence požadavků dosáhne stejné nebo nižší hodnoty, než je vaše zřízená propustnost.

Výchozí propustnost můžete nakonfigurovat tím, že ji vložíte jako parametr metody CreateIfNotExists.

Čtení 1kB entity se normalizuje jako 1 RU a další operace se normalizují na pevnou hodnotu RU na základě jejich spotřeby CPU, paměti a IOPS. Další informace o [jednotkách žádostí ve službě Azure Cosmos DB](request-units.md), zejména pro [úložiště párů klíč-hodnota](key-value-store-cost.md).

Dále si projdeme jednoduché operace čtení a zápisu (CRUD) pomocí sady SDK služby Azure Table Storage. Tento kurz předvádí předvídatelné nízké latence v řádu milisekund a rychlé dotazy, které poskytuje Azure Cosmos DB.

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky
Entity ve službě Azure Table Storage vycházejí z třídy `TableEntity` a musí mít vlastnosti `PartitionKey` a `RowKey`. Tady je ukázková definice entity zákazníka.

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

    public string PhoneNumber { get; set; }
}
```

Následující fragment kódu ukazuje přidání entity pomocí sady SDK úložiště Azure. Služba Azure Cosmos DB je navržená pro zajištění záruky nízké latence v jakémkoli měřítku po celém světě.

Zápisy se dokončují do 15 ms při p99 a přibližně do 6 ms při p50 pro aplikace spuštěné ve stejné oblasti jako účet služby Azure Cosmos DB. Tato doba trvání je způsobená také tím, že se do klienta zpětně potvrdí zápisy teprve po jejich synchronní replikaci, odolném potvrzení a indexování veškerého obsahu.


```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Vložení dávky entit
Azure Table Storage podporuje rozhraní API pro dávkové operace, které umožňuje kombinovat aktualizace, odstraňování a vkládání ve stejné dávkové operaci.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```
## <a name="retrieve-a-single-entity"></a>Načtení jedné entity
Načítání (GET) ve službě Azure Cosmos DB se dokončují do 10 ms při p99 a přibližně do 1 ms při p50 ve stejné oblasti Azure. Do svého účtu můžete přidat jakýkoli počet oblastí pro zajištění nízké latence čtení a nasazovat aplikace pro čtení z místní oblasti (s vícenásobným naváděním) pomocí nastavení `TablePreferredLocations`. 

Pomocí následujícího fragmentu kódu můžete načíst jednu entitu:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> Informace o rozhraních API pro vícenásobné navádění najdete v tématu [Vývoj s využitím více oblastí](tutorial-global-distribution-table.md).
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>Dotazování entit s využitím automatických sekundárních indexů
Tabulky je možné dotazovat pomocí třídy `TableQuery`. Azure Cosmos DB má databázový stroj optimalizovaný pro zápisy, který automaticky indexuje všechny sloupce ve vaší tabulce. Indexování ve službě Azure Cosmos DB je nezávislé na schématu. Proto se schéma automaticky indexuje i v případě, že se liší mezi řádky nebo se v průběhu času vyvíjí. Vzhledem k tomu, že Azure Cosmos DB podporuje automatické sekundární indexy, můžou index využívat dotazy na jakoukoli vlastnost, které se díky tomu zpracují efektivně.

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

Azure Cosmos DB podporuje v rozhraní Table API stejné funkce dotazů jako Azure Table Storage. Azure Cosmos DB podporuje také řazení, agregace, geoprostorové dotazy, hierarchie a širokou škálu integrovaných funkcí. Přehled těchto možností najdete v tématu [Dotazování služby Azure Cosmos DB](how-to-sql-query.md). 

## <a name="replace-an-entity"></a>Nahrazení entity
Pokud chcete entitu aktualizovat, načtěte ji ze služby Table service, upravte objekt entity a potom uložte změny zpět do služby Table service. Následující kód změní telefonní číslo stávajícího zákazníka. 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
Podobným způsobem můžete provádět operace `InsertOrMerge` nebo `Merge`.  

## <a name="delete-an-entity"></a>Odstranění entity
Entitu můžete po jejím načtení snadno odstranit, a to pomocí stejného vzoru zobrazovaného pro aktualizaci entity. Následující kód načte a odstraní entitu zákazníka.

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>Odstranění tabulky
Následující příklad kódu nakonec odstraní tabulku z účtu úložiště. Pomocí služby Azure Cosmos DB můžete odstranit tabulku a okamžitě ji znovu vytvořit.

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jsme se věnovali začátkům používání služby Azure Cosmos DB s rozhraním Table API a provedli jste následující: 

> [!div class="checklist"] 
> * Vytvořili jste účet služby Azure Cosmos DB. 
> * Povolili jste funkce v souboru app.config. 
> * Vytvořili jste tabulku. 
> * Přidali jste do tabulky entitu. 
> * Vložili jste dávku entit. 
> * Načetli jste jednu entitu. 
> * Dotazovali jste entity s využitím automatických sekundárních indexů. 
> * Nahradili jste entitu. 
> * Odstranili jste entitu. 
> * Odstranili jste tabulku.  

Teď můžete pokračovat k dalšímu kurzu, kde najdete další informace o dotazování tabulkových dat. 

> [!div class="nextstepaction"]
> [Dotazování pomocí rozhraní Table API](tutorial-query-table.md)
