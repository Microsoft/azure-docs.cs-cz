---
title: 'Kurz: Nástroj pro migraci databáze pro Azure Cosmos DB'
description: 'Kurz: Naučte se používat Open Source nástroje pro migraci dat Azure Cosmos DB k importu dat do Azure Cosmos DB z různých zdrojů, včetně MongoDB, SQL Server, Table Storage, Amazon DynamoDB, CSV a souborů JSON. Převod formátu CSV na JSON.'
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: dech
ms.openlocfilehash: 1d25a2c9a3fda48c2f7de01563e01dd0c7de7762
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79238689"
---
# <a name="tutorial-use-data-migration-tool-to-migrate-your-data-to-azure-cosmos-db"></a>Kurz: použití nástroje pro migraci dat k migraci dat do Azure Cosmos DB

Tento kurz obsahuje pokyny k použití nástroje pro migraci dat do služby Azure Cosmos DB, který dokáže importovat data z různých zdrojů do kontejnerů a tabulek Azure Cosmos. Můžete importovat z JSON soubory, sdíleného svazku clusteru soubory, SQL, MongoDB, Azure Table storage, Amazon DynamoDB a dokonce kolekce rozhraní SQL API služby Azure Cosmos DB. Migrujete data do kolekce a tabulky pro použití se službou Azure Cosmos DB. Nástroj pro migraci dat můžete použít také při migraci z kolekce s jedním oddílem do kolekce s více oddíly pro rozhraní SQL API.

Jaké rozhraní API budete se službou Azure Cosmos DB používat?

* **[SQL API](documentdb-introduction.md)** – Při importu dat můžete využít jakoukoli z možností zdroje, které nástroj pro migraci dat poskytuje.
* **[Table API](table-introduction.md)** – K importu dat můžete využít nástroj pro migraci dat nebo AzCopy. Další informace najdete v tématu [Import dat pro použití s rozhraním Table API služby Azure Cosmos DB](table-import.md).
* **[Rozhraní API pro MongoDB](mongodb-introduction.md)** – Nástroj pro migraci dat v současné době nepodporuje rozhraní Azure Cosmos DB API pro MongoDB buď jako zdroj, nebo jako cíl. Azure Cosmos DB Pokud chcete migrovat data do nebo z kolekcí v Azure Cosmos DB, přečtěte si téma [Postup migrace dat MongoDB databáze Cosmos pomocí rozhraní API Azure Cosmos DB pro MongoDB](mongodb-migrate.md) . Nástroj pro migraci dat můžete stále použít k exportu dat z MongoDB do kolekcí rozhraní SQL API služby Azure Cosmos DB pro použití s rozhraním SQL API.
* **[Rozhraní Gremlin API](graph-introduction.md)** – Nástroj pro migraci dat není v tuto chvíli podporovaným nástrojem pro import pro účty rozhraní Gremlin API.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Instalace nástroje pro migraci dat
> * Import dat z různých zdrojů dat
> * Export ze služby Azure Cosmos DB do formátu JSON

## <a id="Prerequisites"></a>Požadavky

Než budete postupovat podle pokynů tohoto článku, ujistěte se, že provedete následující kroky:

* **Nainstalujte** [Microsoft .NET Framework 4,51](https://www.microsoft.com/download/developer-tools.aspx) nebo novější.

* **Zvýšená propustnost:** Doba trvání migrace dat závisí na propustnosti, kterou pro jednotlivé kolekce nebo sady kolekcí nastavíte. V případě rozsáhlejších migrací dat nezapomeňte propustnost zvýšit. Po dokončení migrace propustnost snižte, abyste dosáhli nižších nákladů. Další informace o zvýšení propustnosti v Azure Portal najdete v tématu [úrovně výkonu](performance-levels.md) a [cenové úrovně](https://azure.microsoft.com/pricing/details/cosmos-db/) v Azure Cosmos DB.

* **Vytvoření prostředků služby Azure Cosmos DB:** Ještě před zahájením migrace dat vytvořte všechny kolekce na webu Azure Portal. Pokud chcete migrovat na účet Azure Cosmos DB, který má propustnost na úrovni databáze, poskytněte při vytváření kontejnerů Azure Cosmos klíč oddílu.

## <a id="Overviewl"></a>Přehled

Nástroj pro migraci dat je open source řešení umožňující import dat do služby Azure Cosmos DB z různých zdrojů, včetně:

* Soubory JSON
* MongoDB
* SQL Server
* Soubory CSV
* Azure Table Storage
* Amazon DynamoDB
* HBase
* Kontejnery služby Azure Cosmos

Přestože nástroj pro import obsahuje grafické uživatelské rozhraní (dtui.exe), dá se ovládat i z příkazového řádku (dt.exe). Ve skutečnosti je možnost výstupu přidružený příkaz po nastavení importu přes uživatelské rozhraní. Můžete transformovat tabulkové zdroje dat, jako jsou SQL Server nebo souborů CSV, chcete-li vytvořit hierarchické vztahy (vnořené dokumenty) během importu. V dalších částech tohoto článku se dozvíte více o možnostech zdroje, ukázkových příkazech pro import z jednotlivých zdrojů, možnostech cíle a zobrazení výsledků importu.

## <a id="Install"></a>Instalace

Zdrojový kód nástroje pro migraci je k dispozici na GitHubu v [tomto úložišti](https://github.com/azure/azure-documentdb-datamigrationtool). Řešení si můžete stáhnout a místně zkompilovat nebo si můžete [stáhnout předkompilovaný binární soubor](https://aka.ms/csdmtool) a pak spustit:

* **Dtui.exe:** Verze nástroje s grafickým rozhraním
* **Dt.exe:** Verze nástroje pro příkazový řádek

## <a name="select-data-source"></a>Výběr zdroje dat

Po instalaci nástroje je čas importovat data. Jaký druh dat chcete importovat?

* [Soubory JSON](#JSON)
* [MongoDB](#MongoDB)
* [Exportované soubory MongoDB](#MongoDBExport)
* [SQL Server](#SQL)
* [Soubory CSV](#CSV)
* [Azure Table storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Objekt blob](#BlobImport)
* [Kontejnery Azure Cosmos](#SQLSource)
* [HBase](#HBaseSource)
* [Hromadný import Azure Cosmos DB](#SQLBulkTarget)
* [Sekvenční import záznamů Azure Cosmos DB](#SQLSeqTarget)

## <a id="JSON"></a>Import souborů JSON

Možnost programu pro import zdrojového souboru JSON umožňuje importovat jeden nebo víc souborů JSON jednoho dokumentu nebo soubory JSON, mají celou řadu dokumentů JSON. Při přidávání složek, které mají soubory JSON pro import, máte možnost vyhledávat soubory v podsložkách rekurzivně.

![Snímek obrazovky s možnostmi zdrojového souboru JSON – nástroje pro migraci databází](./media/import-data/jsonsource.png)

Připojovací řetězec je v následujícím formátu:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>`

* `<CosmosDB Endpoint>` je identifikátor URI koncového bodu. Tuto hodnotu můžete získat z Azure Portal. Přejděte k účtu Azure Cosmos. Otevřete podokno **Přehled** a zkopírujte hodnotu **identifikátoru URI** .
* `<AccountKey>` je heslo nebo **primární klíč**. Tuto hodnotu můžete získat z Azure Portal. Přejděte k účtu Azure Cosmos. Otevřete podokno **připojovací řetězce** nebo **klíče** a zkopírujte hodnotu "heslo" nebo **primární klíč** .
* `<CosmosDB Database>` je název databáze CosmosDB.

Příklad: `AccountEndpoint=https://myCosmosDBName.documents.azure.com:443/;AccountKey=wJmFRYna6ttQ79ATmrTMKql8vPri84QBiHTt6oinFkZRvoe7Vv81x9sn6zlVlBY10bEPMgGM982wfYXpWXWB9w==;Database=myDatabaseName`

> [!NOTE]
> Pomocí příkazu Verify zajistěte, aby byl k dispozici účet Cosmos DB zadaný v poli připojovací řetězec.

Tady je několik ukázek příkazového řádku pro import souborů JSON:

```console
#Import a single JSON file
dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory of JSON files
dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory (including sub-directories) of JSON files
dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

#Import a directory (single), directory (recursive), and individual JSON files
dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

#Import a single JSON file and partition the data across 4 collections
dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500
```

## <a id="MongoDB"></a>Import z MongoDB

> [!IMPORTANT]
> Pokud importujete do účtu Cosmos nakonfigurovaného s rozhraním API Azure Cosmos DB pro MongoDB, postupujte podle těchto [pokynů](mongodb-migrate.md).

S možností programu pro import zdroje MongoDB můžete importovat z jedné kolekce MongoDB, volitelně filtruje dokumentů pomocí dotazu a změnit strukturu dokumentu s použitím projekce.  

![Snímek obrazovky s možnostmi zdroje MongoDB](./media/import-data/mongodbsource.png)

Připojovací řetězec je ve standardním formátu MongoDB:

`mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>`

> [!NOTE]
> Pomocí příkazu Verify se ujistěte, že je instance MongoDB zadaná v poli připojovacího řetězce přístupná.

Zadejte název kolekce, ze které se data budou importovat. Volitelně můžete zadat nebo zadat soubor pro dotaz, například `{pop: {$gt:5000}}`, nebo projekci, jako je například `{loc:0}`, pro filtrování i tvarování importovaných dat.

Tady je několik ukázek příkazového řádku pro import z MongoDB:

```console
#Import all documents from a MongoDB collection
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

#Import documents from a MongoDB collection which match the query and exclude the loc field
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500
```

## <a id="MongoDBExport"></a>Import exportovaných souborů MongoDB

> [!IMPORTANT]
> Pokud importujete na účet Azure Cosmos DB s podporou MongoDB, postupujte podle těchto [pokynů](mongodb-migrate.md).

Možnost importu ze zdrojového exportovaného souboru JSON z MongoDB umožňuje importovat jeden nebo několik souborů JSON vygenerovaných nástrojem mongoexport.  

![Snímek obrazovky s možnostmi zdroje exportu MongoDB](./media/import-data/mongodbexportsource.png)

Při přidávání složek, které obsahují soubory JSON export MongoDB pro import, máte možnost vyhledávat soubory v podsložkách rekurzivně.

Tady je ukázka příkazového řádku pro import z exportovaných souborů JSON z MongoDB:

```console
dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500
```

## <a id="SQL"></a>Import z SQL Serveru

Možnost importu ze zdroje SQL umožňuje importovat z jednotlivých databází SQL Serveru a volitelně pomocí dotazu filtrovat záznamy určené k importu. Kromě toho můžete upravit strukturu dokumentu zadáním oddělovače vnořování (další informace najdete níže).  

![Snímek obrazovky s možnostmi zdroje SQL – nástroje pro migraci databází](./media/import-data/sqlexportsource.png)

Připojovací řetězec je ve standardním formátu připojovacího řetězce SQL.

> [!NOTE]
> Pomocí příkazu Verify se ujistěte, že je instance SQL Serveru zadaná v poli připojovacího řetězce přístupná.

Vlastnost oddělovače vnořování slouží k vytvoření hierarchických vztahů (vnořených dokumentů) během importu. Představte si následující dotaz SQL:

`select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'`

Dotaz vrátí následující (částečné) výsledky:

![Snímek obrazovky s výsledky dotazu SQL](./media/import-data/sqlqueryresults.png)

Všimněte si aliasů, jako jsou Address.AddressType a Address.Location.StateProvinceName. Díky zadání oddělovače vnořování („.“) nástroj pro import během importu vytvoří vnořené dokumenty Address a Address.Location. Tady je příklad výsledného dokumentu ve službě Azure Cosmos DB:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Tady je několik ukázek příkazového řádku pro import z SQL Serveru:

```console
#Import records from SQL which match a query
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

#Import records from sql which match a query and create hierarchical relationships
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500
```

## <a id="CSV"></a>Import souborů CSV a převod formátu CSV na JSON

Možnost importu ze zdrojového souboru CSV umožňuje importovat jeden nebo několik souborů CSV. Při přidávání složek, které obsahují soubory sdíleného svazku clusteru pro import, máte možnost vyhledávat soubory v podsložkách rekurzivně.

![Snímek obrazovky s možnostmi zdroje CSV – z CSV na JSON](media/import-data/csvsource.png)

Podobně jako u zdroje SQL můžete použít vlastnost oddělovače vnořování k vytvoření hierarchických vztahů (vnořených dokumentů) během importu. Představte si následující řádek záhlaví a datové řádky CSV:

![Snímek obrazovky s ukázkovými záznamy CSV – z CSV na JSON](./media/import-data/csvsample.png)

Všimněte si aliasů, jako jsou DomainInfo.Domain_Name a RedirectInfo.Redirecting. Díky zadání oddělovače vnořování („.“) nástroj pro import během importu vytvoří vnořené dokumenty DomainInfo a RedirectInfo. Tady je příklad výsledného dokumentu ve službě Azure Cosmos DB:

*{"DomainInfo": {"Domain_Name": "ACUS.GOV", "Domain_Name_Address": "https:\//www.ACUS.GOV"}, "federální agentura": "správní konference USA", "RedirectInfo": {"přesměrování": "0", "Redirect_Destination": ""}, "ID": "9cc565c5-EBCD-1c03-ebd3-cc3e2ecd814d"}*

Nástroj pro import se pokusí odvodit informace o typu hodnoty bez uvozovek v souborech CSV (hodnoty v uvozovkách jsou vždy považovány za řetězce).  Typy se identifikují v následujícím pořadí: číslo, datetime, logická hodnota.  

O importu CSV byste měli vědět ještě dvě věci:

1. Ve výchozím nastavení se v hodnotách bez uvozovek vždy oříznou tabulátory a mezery, zatímco hodnoty v uvozovkách se zachovají tak, jak jsou. Toto chování je možné přepsat pomocí zaškrtávacího políčka Trim quoted values (Oříznout hodnoty v uvozovkách) nebo možnosti příkazového řádku /s.TrimQuoted.
2. Ve výchozím nastavení se řetězec null bez uvozovek považuje za hodnotu null. Toto chování je možné přepsat (tedy aby se řetězec null bez uvozovek považoval za řetězec null) pomocí zaškrtávacího políčka Treat unquoted NULL as string (Považovat hodnotu NULL bez uvozovek za řetězec) nebo možnosti příkazového řádku /s.NoUnquotedNulls.

Tady je ukázka příkazového řádku pro import CSV:

```console
dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500
```

## <a id="AzureTableSource"></a>Import ze služby Azure Table Storage

Možnost importu ze zdroje Azure Table Storage umožňuje importovat z jednotlivých tabulek Azure Table Storage. Entity tabulky, které se mají importovat, můžete volitelně filtrovat.

Může výstupní data, která byla importována ze služby Azure Table Storage do služby Azure Cosmos DB tabulky a entity pro použití s rozhraním Table API. Importovaná data lze také výstup do kolekce a dokumenty pro použití s rozhraním SQL API. Rozhraní API tabulky je však pouze k dispozici jako cíl v nástroji příkazového řádku. Nelze exportovat do rozhraní Table API s použitím migrace dat nástroje uživatelského rozhraní. Další informace najdete v tématu [Import dat pro použití s rozhraním Table API služby Azure Cosmos DB](table-import.md).

![Snímek obrazovky s možnostmi zdroje Azure Table Storage](./media/import-data/azuretablesource.png)

Formát připojovacího řetězce Azure Table Storage je následující:

`DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;`

> [!NOTE]
> Pomocí příkazu Verify se ujistěte, že je instance služby Azure Table Storage zadaná v poli připojovacího řetězce přístupná.

Zadejte název tabulky Azure, ze které se má importovat. Volitelně můžete zadat [filtr](../vs-azure-tools-table-designer-construct-filter-strings.md).

Možnost importu ze zdroje Azure Table Storage nabízí následující další možnosti:

1. Include Internal Fields (Zahrnout interní pole)
   1. All (Vše) – Zahrnou se všechna interní pole (PartitionKey, RowKey a Timestamp)
   2. None (Žádné) – Všechna interní pole se vyloučí
   3. RowKey – Zahrne se pouze pole RowKey
2. Select Columns (Vybrané sloupce)
   1. Azure Table storage filtry nepodporují projekce. Pokud chcete importovat pouze určité vlastnosti entit tabulky Azure, přidejte se je do seznamu Select Columns (Vybrané sloupce). Všechny ostatní vlastnosti entit se budou ignorovat.

Tady je ukázka příkazového řádku pro import ze služby Azure Table Storage:

```console
dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500
```

## <a id="DynamoDBSource"></a>Import z Amazon DynamoDB

Možnost programu pro import zdroje Amazon DynamoDB umožňuje importovat z jedné tabulky Amazon DynamoDB. Volitelně ji můžete filtrovat entity, které chcete importovat. K dispozici je několik šablon, které nastavení importu co nejvíce zjednodušují.

![Snímek obrazovky s možnostmi zdroje Amazon DynamoDB – nástroje pro migraci databází](./media/import-data/dynamodbsource1.png)

![Snímek obrazovky s možnostmi zdroje Amazon DynamoDB – nástroje pro migraci databází](./media/import-data/dynamodbsource2.png)

Formát připojovacího řetězce Amazon DynamoDB je následující:

`ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;`

> [!NOTE]
> Pomocí příkazu Verify se ujistěte, že je instance Amazon DynamoDB zadaná v poli připojovacího řetězce přístupná.

Tady je ukázka příkazového řádku pro import z Amazon DynamoDB:

```console
dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500
```

## <a id="BlobImport"></a>Import ze služby Azure Blob Storage

Možnosti importu ze zdrojového souboru JSON, exportovaného souboru MongoDB a souboru CSV umožňují importovat jeden nebo několik souborů ze služby Azure Blob Storage. Po zadání adresy URL kontejneru objektů blob a klíče účtu zadejte regulární výraz pro výběr souborů, které se mají importovat.

![Snímek obrazovky s možnostmi zdrojového souboru objektu blob](./media/import-data/blobsource.png)

Tady je ukázka příkazového řádku pro import souborů JSON ze služby Azure Blob Storage:

```console
dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest
```

## <a id="SQLSource"></a>Import z kolekce rozhraní SQL API

Možnost Import zdrojového kódu Azure Cosmos DB umožňuje importovat data z jednoho nebo více kontejnerů Azure Cosmos a volitelně filtrovat dokumenty pomocí dotazu.  

![Snímek obrazovky s možnostmi zdroje Azure Cosmos DB](./media/import-data/documentdbsource.png)

Formát připojovacího řetězce Azure Cosmos DB je následující:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Připojovací řetězec účtu Azure Cosmos DB můžete načíst ze stránky klíče Azure Portal, jak je popsáno v tématu [jak spravovat Azure Cosmos DB účet](manage-account.md). Název databáze je však potřeba připojeno k připojovací řetězec v následujícím formátu:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Pomocí příkazu Verify se ujistěte, že je instance služby Azure Cosmos DB zadaná v poli připojovacího řetězce přístupná.

Pokud chcete importovat z jednoho kontejneru Azure Cosmos, zadejte název kolekce, ze které se mají importovat data. Pokud chcete importovat z více než jednoho kontejneru Azure Cosmos, zadejte regulární výraz, který bude odpovídat jednomu nebo více názvům kolekcí (například collection01 | collection02 | collection03). Volitelně může zadat nebo zadejte soubor pro dotaz na filtr a obrazec, který importujete data.

> [!NOTE]
> Protože pole kolekce přijímá regulárních výrazů, když importujete z jedné kolekce, jejichž název obsahuje znaky regulárního výrazu, musí tyto znaky uvozený odpovídajícím způsobem.

Možnost importu ze zdroje Azure Cosmos DB nabízí následující pokročilé možnosti:

1. Include Internal Fields (Zahrnout interní pole): Určuje, jestli se mají do exportu zahrnout systémové vlastnosti dokumentu Azure Cosmos DB (například _rid, _ts).
2. Number of Retries on Failure (Počet opakování v případě selhání): Určuje, kolikrát se má opakovat pokus o připojení ke službě Azure Cosmos DB v případě přechodného selhání (například přerušení síťového připojení).
3. Retry Interval (Interval opakování): Určuje, jak dlouho se má počkat před zopakováním pokusu o připojení ke službě Azure Cosmos DB v případě přechodného selhání (například přerušení síťového připojení).
4. Connection Mode (Režim připojení): Určuje režim připojení ke službě Azure Cosmos DB. Dostupné možnosti jsou DirectTcp, DirectHttps a Gateway. Režimy přímého připojení jsou rychlejší, zatímco režim brány lépe vyhovuje požadavkům brány firewall, protože využívá pouze port 443.

![Snímek obrazovky s pokročilými možnostmi zdroje Azure Cosmos DB](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> Nástroj pro import jako výchozí režim používá DirectTcp. Pokud máte problémy s bránou firewall, přepněte režim připojení na Gateway, protože vyžaduje pouze port 443.

Tady je několik ukázek příkazového řádku pro import ze služby Azure Cosmos DB:

```console
#Migrate data from one Azure Cosmos container to another Azure Cosmos containers
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

#Migrate data from more than one Azure Cosmos container to a single Azure Cosmos container
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

#Export an Azure Cosmos container to a JSON file
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500
```

> [!TIP]
> Nástroj pro import dat Azure Cosmos DB podporuje také import dat z [emulátoru služby Azure Cosmos DB](local-emulator.md). Pokud importujete data z místního emulátoru, nastavte koncový bod na `https://localhost:<port>`.

## <a id="HBaseSource"></a>Import z HBase

Možnost importu ze zdroje HBase umožňuje importovat data z tabulky HBase a volitelně data filtrovat. K dispozici je několik šablon, které nastavení importu co nejvíce zjednodušují.

![Snímek obrazovky s možnostmi zdroje HBase](./media/import-data/hbasesource1.png)

![Snímek obrazovky s možnostmi zdroje HBase](./media/import-data/hbasesource2.png)

Formát připojovacího řetězce HBase Stargate je následující:

`ServiceURL=<server-address>;Username=<username>;Password=<password>`

> [!NOTE]
> Pomocí příkazu Verify se ujistěte, že je instance HBase zadaná v poli připojovacího řetězce přístupná.

Tady je ukázka příkazového řádku pro import z HBase:

```console
dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport
```

## <a id="SQLBulkTarget"></a>Import do rozhraní SQL API (hromadný import)

Nástroj pro hromadný import Azure Cosmos DB umožňuje importovat z jakékoli dostupné možnosti zdroje a k zajištění efektivity využívá uloženou proceduru Azure Cosmos DB. Nástroj podporuje import do jednoho kontejneru Azure Cosmos s jedním oddílem. Podporuje také horizontálně dělené import, při kterém jsou data rozdělená do více než jednoho kontejneru Azure Cosmos s jedním oddílem. Další informace o dělení dat najdete v tématu [Dělení a škálování ve službě Azure Cosmos DB](partition-data.md). Nástroj vytvoří a spustí uloženou proceduru a pak ji odstraní z cílových kolekcí.  

![Snímek obrazovky s možnostmi hromadného importu do služby Azure Cosmos DB](./media/import-data/documentdbbulk.png)

Formát připojovacího řetězce Azure Cosmos DB je následující:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Připojovací řetězec účtu služby Azure Cosmos DB můžete načíst ze stránky Klíče na webu Azure Portal, jak je popsáno v tématu [Správa účtu služby Azure Cosmos DB](manage-account.md), ale k připojovacímu řetězci je potřeba připojit název databáze v následujícím formátu:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Pomocí příkazu Verify se ujistěte, že je instance služby Azure Cosmos DB zadaná v poli připojovacího řetězce přístupná.

Pokud chcete importovat do jedné kolekce, zadejte název kolekce, ze které se mají data importovat, a klikněte na tlačítko Add (Přidat). Pokud chcete importovat do více než jedné kolekce, buď zadejte jednotlivé názvy kolekcí jednotlivě, nebo použijte následující syntaxi k určení více než jedné kolekce: *collection_prefix*[začátek indexu-end index]. Při zadávání více než jednu kolekci pomocí výše uvedené syntaxe, mějte podle následujících pokynů:

1. Podporují se pouze vzory pojmenování s rozsahem celých čísel. Například zadáním collection[0-3] se vytvoří následující kolekce: collection0, collection1, collection2, collection3.
2. Můžete použít zkrácenou syntaxi: collection[3] vytvoří stejnou sadu kolekcí uvedenou v kroku 1.
3. Je možné zadat více než jedno nahrazení. Například kolekce [0-1] [0-9] generuje 20 názvů kolekcí úvodními nulami (collection01, ..02, ..03).

Po zadání názvů kolekcí zvolte požadovanou propustnost kolekcí (400 až 10 000 RU/s). K zajištění nejlepšího výkonu zvolte vyšší propustnost. Další informace o úrovních výkonu najdete v tématu [Úrovně výkonu ve službě Azure Cosmos DB](performance-levels.md).

> [!NOTE]
> Nastavení propustnosti výkonu se použije pouze při vytváření kolekce. Pokud zadaná kolekce již existuje, jeho propustnosti se nezmění.

Při importu do více než jednu kolekci, nástroj pro import podporuje horizontální dělení podle algoritmu hash. V tomto scénáři Určete vlastnost dokumentu, který chcete použít jako klíč oddílu. (Pokud je klíč oddílu je ponecháno prázdné, dokumenty se horizontálně dělené náhodně napříč cílové kolekce.)

Volitelně může zadat, které pole ve zdroji importu má být použit jako vlastnost ID dokumentu Azure Cosmos DB během importu. Pokud tuto vlastnost nemáte k dispozici dokumenty, nástroj pro import vytvoří identifikátor GUID jako hodnota vlastnosti ID.

Během importu je k dispozici několik pokročilých možností. Za prvé, přestože nástroj vkládá výchozí uloženou proceduru hromadného importu (BulkInsert.js), můžete zadat svou vlastní uloženou proceduru importu:

 ![Snímek obrazovky s možností uložené procedury hromadného vložení do služby Azure Cosmos DB](./media/import-data/bulkinsertsp.png)

Kromě toho máte při importování datových typů (například z SQL Serveru nebo MongoDB) na výběr ze tří možností importu:

 ![Snímek obrazovky s možnostmi importu data a času do služby Azure Cosmos DB](./media/import-data/datetimeoptions.png)

* String (Řetězec): Zachování jako hodnoty řetězce
* Epoch (Epocha): Zachování jako hodnoty unixového času
* Both (Obojí): Zachování hodnoty řetězce i hodnoty unixového času. Tato možnost vytvoří vnořený dokument, například: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Nástroj pro hromadný import ze služby Azure Cosmos DB nabízí následující další pokročilé možnosti:

1. Batch Size (Velikost dávky): Nástroj jako výchozí velikost dávky používá 50.  Pokud jsou dokumenty určené k importu velké, zvažte snížení velikosti dávky. Pokud jsou dokumenty určené k importu malé, zvažte naopak zvýšení velikosti dávky.
2. Max Script Size (bytes) (Maximální velikost skriptu v bajtech): Nástroj jako maximální velikost skriptu používá 512 kB.
3. Zakázat automatické generování Id: Pokud má každý dokument k importu pole ID, pak výběrem této možnosti může zvýšit výkon. Chybějící jedinečné ID pole dokumenty nebudou importovány.
4. Aktualizace existující dokumenty: Nástroj výchozí hodnoty nelze nahradit stávající dokumenty s ID je v konfliktu. Tato volba umožňuje přepsání s odpovídajícím identifikátorem ID existující dokumenty. Tato funkce je užitečná pro plánované migrace dat, při kterých se aktualizují stávající dokumenty.
5. Počet opakovaných pokusů při selhání: Určuje, jak často se má pokus o připojení ke službě Azure Cosmos DB při přechodném selhání (například připojení k přerušení sítě).
6. Retry Interval (Interval opakování): Určuje, jak dlouho se má počkat před zopakováním pokusu o připojení ke službě Azure Cosmos DB v případě přechodného selhání (například přerušení síťového připojení).
7. Connection Mode (Režim připojení): Určuje režim připojení ke službě Azure Cosmos DB. Dostupné možnosti jsou DirectTcp, DirectHttps a Gateway. Režimy přímého připojení jsou rychlejší, zatímco režim brány lépe vyhovuje požadavkům brány firewall, protože využívá pouze port 443.

![Snímek obrazovky s pokročilými možnostmi hromadného importu do služby Azure Cosmos DB](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> Nástroj pro import jako výchozí režim používá DirectTcp. Pokud máte problémy s bránou firewall, přepněte režim připojení na Gateway, protože vyžaduje pouze port 443.

## <a id="SQLSeqTarget"></a>Import do rozhraní SQL API (sekvenční import záznamů)

Importér sekvenční záznam služby Azure Cosmos DB umožňuje importovat z možnost k dispozici zdroj na základě záznamu záznamu. Tuto možnost můžete zvolit, pokud importujete do stávající kolekce, která dosáhla kvóty uložených procedur. Nástroj podporuje import do jednoho kontejneru Azure Cosmos (jeden oddíl i více oddílů). Podporuje také horizontálně dělené import, při kterém jsou data rozdělená na více než jeden oddíl nebo více oddílů Azure Cosmos Container. Další informace o dělení dat najdete v tématu [Dělení a škálování ve službě Azure Cosmos DB](partition-data.md).

![Snímek obrazovky s možnostmi sekvenčního importu záznamů do služby Azure Cosmos DB](./media/import-data/documentdbsequential.png)

Formát připojovacího řetězce Azure Cosmos DB je následující:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Připojovací řetězec pro účet Azure Cosmos DB můžete načíst ze stránky klíče Azure Portal, jak je popsáno v tématu [jak spravovat Azure Cosmos DB účet](manage-account.md). Název databáze je však potřeba připojeno k připojovací řetězec v následujícím formátu:

`Database=<Azure Cosmos database>;`

> [!NOTE]
> Pomocí příkazu Verify se ujistěte, že je instance služby Azure Cosmos DB zadaná v poli připojovacího řetězce přístupná.

Import do jedné kolekce, zadejte název kolekce, kterou chcete importovat data do a potom klikněte na tlačítko Přidat. Import do více než jednu kolekci, zadejte název každé kolekci jednotlivě. K určení více než jedné kolekce můžete použít také následující syntaxi: *collection_prefix*[začátek indexu-end index]. Při zadávání více než jednu kolekci prostřednictvím výše uvedené syntaxe, mějte podle následujících pokynů:

1. Podporují se pouze vzory pojmenování s rozsahem celých čísel. Například zadáním collection[0-3] se vytvoří následující kolekce: collection0, collection1, collection2, collection3.
2. Můžete použít zkrácenou syntaxi: collection[3] vytvoří stejnou sadu kolekcí uvedenou v kroku 1.
3. Je možné zadat více než jedno nahrazení. Například kolekce [0-1] [0-9] vytvoří 20 názvů kolekcí úvodními nulami (collection01, ..02, ..03).

Po zadání názvů kolekcí zvolte požadovanou propustnost kolekcí (400 až 250 000 RU/s). K zajištění nejlepšího výkonu zvolte vyšší propustnost. Další informace o úrovních výkonu najdete v tématu [Úrovně výkonu ve službě Azure Cosmos DB](performance-levels.md). Jakýkoli import do kolekce s propustností větší než 10 000 RU/s vyžaduje klíč oddílu. Pokud se rozhodnete, že potřebujete více než 250 000 RU/s, musíte na portálu podat žádost o navýšení limitu účtu.

> [!NOTE]
> Nastavení propustnosti se použije pouze při vytváření kolekce nebo databáze. Pokud zadaná kolekce již existuje, jeho propustnosti se nezmění.

Při importu do více než jednu kolekci, nástroj pro import podporuje horizontální dělení podle algoritmu hash. V tomto scénáři Určete vlastnost dokumentu, který chcete použít jako klíč oddílu. (Pokud je klíč oddílu je ponecháno prázdné, dokumenty se horizontálně dělené náhodně napříč cílové kolekce.)

Volitelně může zadat, které pole ve zdroji importu má být použit jako vlastnost ID dokumentu Azure Cosmos DB během importu. (Pokud tato vlastnost nemáte k dispozici dokumenty, pak nástroj pro import vytvoří identifikátor GUID jako hodnota vlastnosti ID.)

Během importu je k dispozici několik pokročilých možností. Za prvé, při importování datových typů (například z SQL Serveru nebo MongoDB) máte na výběr ze tří možností importu:

 ![Snímek obrazovky s možnostmi importu data a času do služby Azure Cosmos DB](./media/import-data/datetimeoptions.png)

* String (Řetězec): Zachování jako hodnoty řetězce
* Epoch (Epocha): Zachování jako hodnoty unixového času
* Both (Obojí): Zachování hodnoty řetězce i hodnoty unixového času. Tato možnost vytvoří vnořený dokument, například: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Nástroj pro sekvenční import záznamů Azure Cosmos DB nabízí následující další pokročilé možnosti:

1. Number of Parallel Requests (Počet paralelních požadavků): Nástroj ve výchozím nastavení využívá dva paralelní požadavky. Pokud jsou dokumenty určené k importu malé, zvažte zvýšení počtu paralelních požadavků. Pokud toto číslo zvýšíte příliš, může docházet k omezování rychlosti importu.
2. Zakázat automatické generování Id: Pokud má každý dokument k importu pole ID, pak výběrem této možnosti může zvýšit výkon. Chybějící jedinečné ID pole dokumenty nebudou importovány.
3. Aktualizace existující dokumenty: Nástroj výchozí hodnoty nelze nahradit stávající dokumenty s ID je v konfliktu. Tato volba umožňuje přepsání s odpovídajícím identifikátorem ID existující dokumenty. Tato funkce je užitečná pro plánované migrace dat, při kterých se aktualizují stávající dokumenty.
4. Počet opakovaných pokusů při selhání: Určuje, jak často se má pokus o připojení ke službě Azure Cosmos DB při přechodném selhání (například připojení k přerušení sítě).
5. Interval opakování: Určuje, jak dlouho chcete čekat mezi opakování pokusu o připojení ke službě Azure Cosmos DB při přechodném selhání (například připojení k přerušení sítě).
6. Connection Mode (Režim připojení): Určuje režim připojení ke službě Azure Cosmos DB. Dostupné možnosti jsou DirectTcp, DirectHttps a Gateway. Režimy přímého připojení jsou rychlejší, zatímco režim brány lépe vyhovuje požadavkům brány firewall, protože využívá pouze port 443.

![Snímek obrazovky s pokročilými možnostmi sekvenčního importu záznamů do služby Azure Cosmos DB](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> Nástroj pro import jako výchozí režim používá DirectTcp. Pokud máte problémy s bránou firewall, přepněte režim připojení na Gateway, protože vyžaduje pouze port 443.

## <a id="IndexingPolicy"></a>Zadání zásady indexování

Když nástroji pro migraci povolíte vytváření kolekcí rozhraní SQL API služby Azure Cosmos DB během importu, můžete zadat zásadu indexování kolekcí. V části pokročilých možností hromadného importu do služby Azure Cosmos DB a sekvenčního importu záznamů do služby Azure Cosmos DB přejděte do části Indexing Policy (Zásady indexování).

![Snímek obrazovky s pokročilými možnostmi zásad indexování Azure Cosmos DB](./media/import-data/indexingpolicy1.png)

Pomocí pokročilé možnosti Indexing Policy (Zásady indexování) můžete vybrat soubor zásad indexování, ručně zadat zásadu indexování nebo si vybrat ze sady výchozích šablon (kliknutím pravým tlačítkem do textového pole pro zásady indexování).

Nástroj poskytuje následující šablony zásad:

* Default (Výchozí). Tato zásada je nejvhodnější při provádění dotazy na rovnost řetězců. Funguje i pokud používáte klauzule ORDER BY, rozsah a dotazy na rovnost pro čísla. Tato zásada má nižší režijní náklady na úložiště indexů než Range (Rozsah).
* Range (Rozsah). Tato zásada je nejvhodnější při použití klauzule ORDER BY, rozsah a rovnost dotazy na čísla i řetězce. Tato zásada má vyšší režijní náklady na úložiště indexů než Default (Výchozí) a Hash (Hodnota hash).

![Snímek obrazovky s pokročilými možnostmi zásad indexování Azure Cosmos DB](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Pokud nechcete zadat zásady indexování, je použita výchozí zásada. Další informace o zásadách indexování najdete v tématu [Zásady indexování ve službě Azure Cosmos DB](index-policy.md).

## <a name="export-to-json-file"></a>Export do souboru JSON

Azure Cosmos DB JSON Exportér umožňuje exportovat některou z možností k dispozici zdrojový soubor JSON, který obsahuje celou řadu dokumentů JSON. Nástroj exportuje za vás. Alternativně můžete k zobrazení výsledné příkaz migrace a spusťte příkaz. Výsledný soubor JSON je možné uložit místně nebo ve službě Azure Blob Storage.

![Snímek obrazovky s možností exportu ze služby Azure Cosmos DB do místního souboru JSON](./media/import-data/jsontarget.png)

![Snímek obrazovky s možností exportu ze služby Azure Cosmos DB do souboru JSON ve službě Azure Blob Storage](./media/import-data/jsontarget2.png)

Můžete volitelně prettify výsledný kód JSON. Tato akce zvýší velikost výsledné dokument při nastavování obsah více lidsky čitelné.

* Standardní export JSON

  ```JSON
  [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
  ```

* Export prettified JSON

  ```JSON
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]
  ```

Tady je ukázka příkazového řádku pro export souboru JSON do služby Azure Blob Storage:

```console
dt.exe /ErrorDetails:All /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB database_name>" /s.Collection:<CosmosDB collection_name>
/t:JsonFile /t.File:"blobs://<Storage account key>@<Storage account name>.blob.core.windows.net:443/<Container_name>/<Blob_name>"
/t.Overwrite
```

## <a name="advanced-configuration"></a>Pokročilá konfigurace

Na obrazovce Advanced configuration (Pokročilá konfigurace) zadejte umístění souboru protokolu, do kterého chcete zapisovat případné chyby. Na tuto stránku se vztahují následující pravidla:

1. Pokud není zadaný název souboru, budou vráceny všechny chyby na stránce výsledky.
2. Pokud je zadaný název souboru bez adresáře, soubor se vytvoří (nebo přepíše) v aktuálním adresáři prostředí.
3. Pokud vyberete existující soubor a soubor se přepíše, není možnost připojit.
4. Pak zvolte, jestli se mají protokolovat všechny chybové zprávy, kritické chybové zprávy, nebo se nemají protokolovat žádné. Nakonec se rozhodněte, jak často se bude aktualizovat průběh ve zprávě o přenosu na obrazovce.

   ![Snímek obrazovky rozšířené konfigurace](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Potvrďte nastavení pro import a zobrazení příkazového řádku

1. Po určení zdrojové informace, informace o cílové a pokročilou konfiguraci, zkontrolujte shrnutí migrace a zobrazit nebo zkopírujte výsledný příkaz migrace, chcete-li. (Příkaz kopírování je vhodný k automatizaci operací importu.)

    ![Snímek obrazovky souhrnu](./media/import-data/summary.png)

    ![Snímek obrazovky souhrnu](./media/import-data/summarycommand.png)

2. Jakmile budete s možnostmi zdroje a cíle spokojeni, klikněte na **Import** (Importovat). V průběhu importu se aktualizuje uplynulý čas, počet přenesených souborů a informace o selháních (pokud jste nezadali název souboru v pokročilé konfiguraci). Po dokončení můžete výsledky exportovat (například pro účely řešení případných selhání importu).

    ![Snímek obrazovky s možností exportu ze služby Azure Cosmos DB do souboru JSON](./media/import-data/viewresults.png)

3. Resetuje se všechny hodnoty nebo zachovat stávající nastavení může spustit také nové import. (Například můžete rozhodnout zachovat informace o připojovacím řetězci, volba zdroj a cíl a další.)

    ![Snímek obrazovky s možností exportu ze služby Azure Cosmos DB do souboru JSON](./media/import-data/newimport.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující úlohy:

> [!div class="checklist"]
> * Instalace nástroje pro migraci dat
> * Import dat z různých zdrojů dat
> * Export ze služby Azure Cosmos DB do formátu JSON

Teď můžete pokračovat k dalšímu kurzu, kde zjistíte, jak dotazovat data pomocí služby Azure Cosmos DB.

> [!div class="nextstepaction"]
>[Jak dotazovat data?](../cosmos-db/tutorial-query-sql-api.md)
