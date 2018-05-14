---
title: Nástroj pro migraci databáze pro databázi Azure Cosmos | Microsoft Docs
description: Další informace o použití nástrojů pro migraci dat Azure Cosmos DB s otevřeným zdrojem pro import dat do Azure Cosmos databáze z různých zdrojů, včetně souborů MongoDB, systému SQL Server, úložiště Table, Amazon DynamoDB, CSV a JSON. CSV k převodu formátu JSON.
keywords: CSV do formátu json, nástrojů pro migraci databáze, převést na json sdíleného svazku clusteru
services: cosmos-db
author: andrewhoh
manager: kfile
editor: monicar
documentationcenter: ''
ms.assetid: d173581d-782a-445c-98d9-5e3c49b00e25
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: a14dbaffe6bfa68e7606d117823195144250c230
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB: Nástroj pro migraci dat

V tomto kurzu poskytuje pokyny k používání nástroj Azure Cosmos DB Data migrace, který můžete importovat data z různých zdrojů do kolekce Azure Cosmos databáze a tabulky. Můžete importovat z JSON soubory, CSV soubory, SQL, MongoDB, Azure Table úložiště, Amazon DynamoDB a kolekce i rozhraní API pro Azure Cosmos databáze SQL a provedete migraci, že data do kolekcí a tabulky pro použití s Azure Cosmos DB. Nástroj pro migraci dat lze také při migraci z kolekce tvořené jedním oddílem kolekci více oddílů pro rozhraní SQL API.

Které rozhraní API budete používat s Azure Cosmos DB? 
* **[Rozhraní API pro SQL](documentdb-introduction.md)**  -některé z možností na zdroj nástroj pro migraci dat můžete importovat data.
* **[Tabulka rozhraní API](table-introduction.md)**  -k importu dat můžete použít nástroj pro migraci dat nebo AzCopy. V tématu [Import dat pro použití s rozhraním API pro Azure Cosmos DB tabulky](table-import.md) Další informace.
* **[Rozhraní API MongoDB](mongodb-introduction.md)**  – nástroj pro migraci dat aktuálně nepodporuje rozhraní API služby Azure Cosmos DB MongoDB jako zdroj nebo jako cíl. Pokud chcete migrovat data do nebo z kolekce MongoDB rozhraní API v Azure Cosmos DB, podívejte se na [Cosmos databázi Azure: migrace dat pro rozhraní API MongoDB](mongodb-migrate.md) pokyny. Nástroj pro migraci dat stále můžete exportovat data z MongoDB do kolekce rozhraní API pro Azure Cosmos databáze SQL pro použití s rozhraním API SQL. 
* **[Graph API](graph-introduction.md)**  – nástroj pro migraci dat není podporované import nástroj pro rozhraní Graph API účty v tuto chvíli. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Instalace nástroje pro migraci dat
> * Import dat z různých zdrojů dat.
> * Export z Azure Cosmos DB do formátu JSON

## <a id="Prerequisites"></a>Požadavky
Než budete postupovat podle pokynů v tomto článku, ujistěte se, že máte nainstalované tyto položky:

* [Rozhraní Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) nebo vyšší.

* Zvýšit propustnost: trvání migrace dat závisí na množství propustnosti, můžete nastavit pro jednotlivé kolekce nebo sady kolekcí. Ujistěte se, že zvýšit propustnost pro větší dat migrace. Po dokončení migrace, snížit propustnosti, abyste ušetřili náklady. Další informace o zvýšení propustnosti na portálu Azure najdete v tématu úrovně výkonu a cenové úrovně v Azure Cosmos DB.

## <a id="Overviewl"></a>Přehled
Nástroj pro migraci dat je řešení open source, který importuje data do Azure Cosmos databáze z různých zdrojů, včetně:

* Soubory JSON
* MongoDB
* SQL Server
* CSV soubory
* Azure Table Storage
* Amazon DynamoDB
* HBase
* Azure Cosmos DB kolekce

Při importu nástroj obsahuje grafické uživatelské rozhraní (dtui.exe), můžete také vycházejí z příkazového řádku (dt.exe). Ve skutečnosti je možnost výstup přidružený příkaz po nastavení importu v uživatelském rozhraní. Tabulkové zdroje dat (např. SQL Server nebo CSV soubory) lze je transformovat tak, aby hierarchické vztahy (vnořené dokumenty) lze vytvořit při importu. Zachovat čtení Další informace o možnosti nastavení zdroje, ukázkové příkazové řádky pro import z každého zdroje, target – možnosti a import zobrazení výsledků.

## <a id="Install"></a>Instalace
Migrace nástroj zdrojový kód je k dispozici na Githubu v [toto úložiště](https://github.com/azure/azure-documentdb-datamigrationtool). Můžete stáhnout a zkompilovat řešení místně, nebo [stáhnout předem kompilovaném binární](https://cosmosdbportalstorage.blob.core.windows.net/datamigrationtool/2018.02.28-1.8.1/dt-1.8.1.zip), spusťte buď:

* **Dtui.exe**: grafické rozhraní verze nástroje
* **DT.exe**: příkazového řádku verze nástroje

## <a name="select-data-source"></a>Vyberte zdroj dat

Po instalaci tohoto nástroje je čas lze importovat data. Jaký typ dat chcete importovat?

* [Soubory JSON](#JSON)
* [MongoDB](#MongoDB)
* [MongoDB exportní soubory](#MongoDBExport)
* [SQL Server](#SQL)
* [CSV soubory](#CSV)
* [Azure Table storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Objekt BLOB](#BlobImport)
* [Azure Cosmos DB kolekce](#SQLSource)
* [HBase](#HBaseSource)
* [Azure Cosmos DB hromadného importu](#SQLBulkImport)
* [Sekvenční záznam importu do Azure Cosmos DB](#DocumentDSeqTarget)


## <a id="JSON"></a>Import souborů JSON
Možnost program pro import zdrojového souboru JSON umožňuje importovat jeden nebo více jednotlivý dokument JSON soubory nebo soubory JSON, že každý obsahovat pole dokumentů JSON. Při přidávání složek, které obsahují soubory JSON pro import, máte možnost rekurzivní hledání souborů v podsložkách.

![Možnosti nastavení zdroje souboru snímek JSON - nástrojů pro migraci databáze](./media/import-data/jsonsource.png)

Zde jsou některé ukázky příkazového řádku k importu souborů JSON:

    #Import a single JSON file
    dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory of JSON files
    dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory (including sub-directories) of JSON files
    dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

    #Import a directory (single), directory (recursive), and individual JSON files
    dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

    #Import a single JSON file and partition the data across 4 collections
    dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500

## <a id="MongoDB"></a>Importovat z MongoDB

> [!IMPORTANT]
> Pokud importujete k účtu Azure Cosmos DB s podporou pro MongoDB, postupujte podle těchto [pokyny](mongodb-migrate.md).
> 
> 

Možnost MongoDB zdrojový program pro import můžete importovat z kolekci jednotlivých MongoDB a volitelně filtrovat dokumentů pomocí dotazu nebo změna struktury dokumentu pomocí projekce.  

![Možnosti nastavení zdroje snímek MongoDB](./media/import-data/mongodbsource.png)

Připojovací řetězec je ve formátu standard MongoDB:

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [!NOTE]
> Pomocí příkazu ověřte, zda je přístupná instance MongoDB, zadaný v poli.
> 
> 

Zadejte název kolekce, ze kterého budou importována data. Může volitelně zadejte nebo zadejte soubor pro dotaz (například {pop: {$gt: 5000}}) nebo projekce (například {loc:0}) pro filtrování i data, která bude importována obrazce.

Zde jsou některé ukázky příkazového řádku k importu z MongoDB:

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

## <a id="MongoDBExport"></a>Import MongoDB exportní soubory

> [!IMPORTANT]
> Pokud importujete k účtu Azure Cosmos DB s podporou pro MongoDB, postupujte podle těchto [pokyny](mongodb-migrate.md).
> 
> 

Program pro import možnost MongoDB export souboru JSON zdroj umožňuje importovat jeden nebo více souborů JSON vytvořeného z nástroj mongoexport.  

![Možnosti nastavení zdroje export snímek MongoDB](./media/import-data/mongodbexportsource.png)

Při přidávání složek, které obsahují soubory JSON export MongoDB pro import, máte možnost rekurzivní hledání souborů v podsložkách.

Zde je ukázka příkazového řádku k importu z soubory JSON export MongoDB:

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

## <a id="SQL"></a>Import ze serveru SQL Server
Možnost program pro import zdroje SQL umožňuje importovat z jednotlivých databáze systému SQL Server a volitelně filtrování záznamů k importu pomocí dotazu. Kromě toho můžete upravit strukturu dokumentu zadáním vnoření oddělovače (podrobnosti o této za chvíli).  

![Možnosti nastavení zdroje snímek SQL – nástroje pro migraci databáze](./media/import-data/sqlexportsource.png)

Formát připojovacího řetězce je standardní formátu řetězce připojení SQL.

> [!NOTE]
> Pomocí příkazu ověřte Ujistěte se, že instance systému SQL Server zadané v poli je přístupná.
> 
> 

Vnoření oddělovače vlastnost se používá k vytvoření hierarchické relací (dílčí dokumenty) během importu. Vezměte v úvahu následujícího dotazu SQL:

*jako Id, názvu, AddressType jako [Address.AddressType], AddressLine1 jako [Address.AddressLine1], města jako [Address.Location.City], StateProvinceName jako [Address.Location.StateProvinceName], PostalCode jako [vyberte PŘETYPOVÁNÍ (BusinessEntityID AS varchar) Address.PostalCode] CountryRegionName jako [Address.CountryRegionName] z Sales.vStoreWithAddresses kde AddressType = "hlavní kanceláře.*

Která vrací následující výsledky (částečné):

![Snímek obrazovky SQL výsledky dotazu](./media/import-data/sqlqueryresults.png)

Poznámka: aliasy například Address.AddressType a Address.Location.StateProvinceName. Zadáním oddělovač vnoření z '.', nástroj pro import během importu vytvoří adresu a Address.Location vnořených dokumentů. Tady je příklad výsledné dokumentu v Azure Cosmos DB:

*{"id": "956", "název": "Jemnějšího prodeje a služba", "Adres": {"AddressType": "Hlavní Office", "AddressLine1": "#500 75 O'Connor ulici", "Umístění": {"City": "Ottawa", "StateProvinceName": "Ontario"}, "PostalCode": "K4B 1S2", "CountryRegionName": " Kanada"}}*

Zde jsou některé ukázky příkazového řádku k importu ze serveru SQL Server:

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

## <a id="CSV"></a>Import souborů sdíleného svazku clusteru a CSV převést na JSON
Možnost – Importér zdrojového souboru CSV umožňuje importovat jeden nebo více souborů CSV. Při přidávání složek, které obsahují soubory sdíleného svazku clusteru pro import, máte možnost rekurzivní hledání souborů v podsložkách.

![Možnosti nastavení zdroje snímek CSV - CSV do formátu JSON](media/import-data/csvsource.png)

Podobně jako u zdroje SQL, vlastnost vnoření oddělovače lze vytvořit hierarchické relace (dílčí dokumenty) během importu. Zvažte následující CSV záhlaví řádků a data řádky:

![Snímek obrazovky CSV ukázkové záznamy - CSV do formátu JSON](./media/import-data/csvsample.png)

Poznámka: aliasy například DomainInfo.Domain_Name a RedirectInfo.Redirecting. Zadáním oddělovač vnoření z '.', nástroj pro import vytvoří během importu DomainInfo a RedirectInfo vnořených dokumentů. Tady je příklad výsledné dokumentu v Azure Cosmos DB:

*{"DomainInfo": {"Domain_Name": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV" }, "Federal agenturou": "správu konferenční z USA", "RedirectInfo": {"Přesměrování": "0", "Redirect_Destination": ""}, "id": " 9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d"}*

Nástroj pro import se pokusí odvození informací o typu nekotovaných hodnoty v souborů CSV (hodnoty v uvozovkách se vždy pracuje jako řetězce).  Typy jsou označeny v následujícím pořadí: číslo, datum a čas, logická hodnota.  

Existují dvě věci, které je třeba o importu souboru CSV:

1. Ve výchozím nastavení, jsou nekotovaných hodnoty vždy oříznut pro karty a prostory, zatímco se zachovají hodnoty v uvozovkách jako-je. Toto chování lze přepsat pomocí možnosti příkazového řádku /s.TrimQuoted nebo políčka Trim hodnoty v uvozovkách.
2. Ve výchozím nastavení nekotovaných null považován za hodnotu null. Toto chování můžete přepsat (tedy považovat za nekotovaných null řetězec "null") se zpracovávat nekotované jako řetězec zaškrtávací políčko nebo možnost příkazového řádku /s.NoUnquotedNulls hodnotu NULL.

Zde je ukázka příkazového řádku pro importu souboru CSV:

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

## <a id="AzureTableSource"></a>Importovat z Azure Table storage
Možnost zdroje program pro import úložiště Azure Table umožňuje importovat z jednotlivé tabulky Azure Table storage. Volitelně můžete filtrovat entity tabulky určených k importu. 

Výstupní tabulky Azure Cosmos DB a entity, pro použití s rozhraním API pro tabulky, nebo do kolekcí a dokumenty pro použití s rozhraním API pro SQL lze data importovat z úložiště tabulek Azure. Ale; Je k dispozici jako cíl v nástroj příkazového řádku pouze tabulky rozhraní API, nemůžete exportovat do tabulky API přes uživatelské rozhraní pro nástroj pro migraci dat. Další informace najdete v tématu [Import dat pro použití s rozhraním API pro Azure Cosmos DB tabulky](table-import.md). 

![Možnosti nastavení zdroje snímek Azure Table storage](./media/import-data/azuretablesource.png)

Formát připojovacího řetězce Azure Table storage je:

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [!NOTE]
> Pomocí příkazu ověřte, zda je přístupná zadané v poli instanci úložiště Azure Table.
> 
> 

Zadejte název tabulky Azure z import z. Volitelně můžete určit [filtru](https://msdn.microsoft.com/library/azure/ff683669.aspx).

Možnost zdroje program pro import úložiště Azure Table obsahuje následující další možnosti:

1. Zahrnutí interní polí
   1. Všechny - obsahovat všechny interní pole (klíč oddílu, RowKey a časové razítko)
   2. Žádný - vyloučit všechny interní pole
   3. RowKey – obsahovat pouze pole RowKey
2. Vybrat sloupce
   1. Azure Table storage filtry nepodporují projekce. Pokud chcete importovat pouze vlastnosti entity specifické Azure Table, přidejte je do seznamu vyberte sloupce. Všechny ostatní vlastnosti entity se ignorují.

Zde je ukázka příkazového řádku k importu z úložiště tabulek Azure:

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

## <a id="DynamoDBSource"></a>Importovat z Amazon DynamoDB
Možnost Amazon DynamoDB zdrojový program pro import můžete importovat z jednotlivé tabulky Amazon DynamoDB a volitelně filtrování entity určených k importu. Tak, aby nastavení importu se co největší jsou k dispozici několik šablon.

![Možnosti nastavení zdroje DynamoDB Amazon – snímek obrazovky - nástrojů pro migraci databáze](./media/import-data/dynamodbsource1.png)

![Možnosti nastavení zdroje DynamoDB Amazon – snímek obrazovky - nástrojů pro migraci databáze](./media/import-data/dynamodbsource2.png)

Formát Amazon DynamoDB připojovacího řetězce je:

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [!NOTE]
> Pomocí příkazu ověřte, zda je přístupná zadané v poli instanci Amazon DynamoDB.
> 
> 

Zde je ukázka příkazového řádku k importu z Amazon DynamoDB:

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos DB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

## <a id="BlobImport"></a>Importovat z Azure Blob storage
Soubor JSON, MongoDB export souboru a možnosti program pro import zdrojového souboru CSV umožňují importovat jeden nebo více souborů z úložiště objektů Blob v Azure. Po zadání adresy URL kontejneru objektu Blob a klíč účtu, zadejte regulární výraz k vyberte soubory, které chcete importovat.

![Možnosti nastavení zdroje snímek objektu Blob souboru](./media/import-data/blobsource.png)

Zde je ukázka příkazového řádku k importu souborů JSON z Azure Blob storage:

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest

## <a id="SQLSource"></a>Importovat z kolekce rozhraní SQL API
Možnost Azure Cosmos DB zdrojový program pro import umožňuje importovat data z jedné nebo více kolekcí Azure Cosmos DB a volitelně filtrování dokumentů pomocí dotazu.  

![Možnosti nastavení zdroje snímek databázi Cosmos Azure](./media/import-data/documentdbsource.png)

Formát Azure Cosmos DB připojovacího řetězce je:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Připojovací řetězce k účtu Azure Cosmos DB můžete načíst ze stránky klíče na portálu Azure, jak je popsáno v [Správa účtu Azure Cosmos DB](manage-account.md), ale název databáze musí být připojenou k připojovací řetězec v následujícím formátu:

    Database=<CosmosDB Database>;

> [!NOTE]
> Pomocí příkazu ověřte, zda je přístupná zadané v poli instanci Azure Cosmos DB.
> 
> 

Chcete-li importovat z jedné kolekce Azure Cosmos DB, zadejte název kolekce pro import dat z. Chcete-li importovat z více kolekcí Azure Cosmos DB, zadat regulární výraz tak, aby odpovídaly jeden nebo více názvů kolekcí (například collection01 | collection02 | collection03). Může volitelně zadejte nebo zadejte soubor pro dotaz na filtr a tvar dat určených k importu.

> [!NOTE]
> Vzhledem k tomu, že kolekce pole přijímá regulární výrazy, při importu z jedné kolekce, jejichž název obsahuje regulární výraz znaků, musí tyto znaky uvozené odpovídajícím způsobem.
> 
> 

Možnost program pro import zdroje Azure Cosmos DB má následující rozšířené možnosti:

1. Zahrnutí interní polí: Určuje, zda mají být zahrnuty vlastnosti dokumentu systému Azure Cosmos DB export (například _rid, _ts).
2. Počet opakovaných pokusů o selhání: Určuje počet opakovaných pokusů připojení k databázi Azure Cosmos v případě přechodných chyb (například přerušení připojení k síti).
3. Interval opakování: Určuje dobu čekání mezi opakování pokusu o připojení k databázi Azure Cosmos v případě přechodných chyb (například přerušení připojení k síti).
4. Režim připojení: Určuje režim připojení pro použití s Azure Cosmos DB. Dostupné možnosti jsou DirectTcp, DirectHttps a brány. Režimy přímé připojení je rychlejší, zatímco režimu brány je další brány firewall popisný jako pouze používá port 443.

![Snímek obrazovky databázi Cosmos Azure zdroj rozšířené možnosti](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> Nástroj pro import výchozí režim připojení DirectTcp. Pokud máte brány firewall problémy, přejděte do režimu připojení brány, jak vyžaduje jenom port 443.
> 
> 

Zde jsou některé příkazového řádku ukázky pro import z databáze Cosmos Azure:

    #Migrate data from one Azure Cosmos DB collection to another Azure Cosmos DB collections
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple Azure Cosmos DB collections to a single Azure Cosmos DB collection
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export an Azure Cosmos DB collection to a JSON file
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

> [!TIP]
> Cosmos DB Data importovat nástroj Azure také podporuje import dat z [emulátoru DB Cosmos Azure](local-emulator.md). Při importu dat z místní emulátoru, nastavte koncový bod na `https://localhost:<port>`. 
> 
> 

## <a id="HBaseSource"></a>Importovat z HBase
Možnost program pro import zdroje HBase umožňuje importovat data z tabulky HBase a volitelně data filtrovat. Tak, aby nastavení importu se co největší jsou k dispozici několik šablon.

![Možnosti nastavení zdroje snímek HBase](./media/import-data/hbasesource1.png)

![Možnosti nastavení zdroje snímek HBase](./media/import-data/hbasesource2.png)

Formát HBase Stargate připojovacího řetězce je:

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [!NOTE]
> Pomocí příkazu ověřte Ujistěte se, že zadané v poli instanci HBase je přístupná.
> 
> 

Zde je ukázka příkazového řádku k importu z HBase:

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport

## <a id="SQLBulkTarget"></a>Importovat do rozhraní SQL API (hromadným importem)
Program pro import hromadné DB Cosmos Azure umožňuje importovat z jakéhokoli z dostupných možností, pomocí Azure DB Cosmos uložené procedury pro efektivitu. Nástroj podporuje importovat do jedné kolekce rozdělena na oddíly jedním Azure Cosmos DB, a také horizontálně dělené importu, které je dat rozděleného mezi více kolekcí oddíly jedním Azure Cosmos DB. Další informace o segmentace dat naleznete v tématu [dělení a škálování v Azure Cosmos DB](partition-data.md). Nástroj vytvoří, spustí a odstraní z kolekcí cíl uložené procedury.  

![Možnosti hromadné snímek databázi Cosmos Azure](./media/import-data/documentdbbulk.png)

Formát Azure Cosmos DB připojovacího řetězce je:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Připojovací řetězce k účtu Azure Cosmos DB můžete načíst ze stránky klíče na portálu Azure, jak je popsáno v [Správa účtu Azure Cosmos DB](manage-account.md), ale název databáze musí být připojenou k připojovací řetězec v následujícím formátu:

    Database=<CosmosDB Database>;

> [!NOTE]
> Pomocí příkazu ověřte, zda je přístupná zadané v poli instanci Azure Cosmos DB.
> 
> 

Import do jedné kolekce, zadejte název kolekce a importovat data z kliknutím na tlačítko Přidat. Import do více kolekcí, zadejte jednotlivé názvy kolekce jednotlivě nebo použijte následující syntaxi zadat více kolekcí: *collection_prefix*[index - end index počátečního]. Při zadávání více kolekcí prostřednictvím výše uvedené syntaxe, mějte tyto pokyny:

1. Jsou podporovány pouze celé číslo vzory názvů rozsahu. Například zadání kolekce [0-3] vytvoří následující kolekce: collection0, collection1, collection2, collection3.
2. Můžete použít zkrácené syntaxe: kolekce [3] Vytvoří stejnou sadu kolekcí uveden v kroku 1.
3. Lze zadat více než jeden nahrazení. Například kolekce [0-1] [0-9] generuje 20 názvů kolekcí úvodními nulami (collection01, ..02, ..03).

Jakmile názvy kolekce byly nastaveny, vyberte požadované propustnost kolekcí (400 RUs na 10 000 RUs). Pro nejlepší výkon import zvolte vyšší propustnost. Další informace o úrovně výkonu najdete v tématu [úrovně výkonu v Azure Cosmos DB](performance-levels.md).

> [!NOTE]
> Nastavení výkonu propustnost platí jenom pro vytvoření kolekce. Pokud zadané kolekce už existuje, její propustnosti se nemění.
> 
> 

Při importu do více kolekcí, podporuje nástroj pro import na základě hodnoty hash horizontálního dělení. V tomto scénáři zadejte vlastnost dokumentu, které chcete použít jako klíč oddílu (Pokud klíč oddílu je prázdné, dokumenty jsou horizontálně dělené náhodně mezi kolekcemi cíl).

Můžete volitelně můžete určit, které pole ve zdroji import má být použit jako vlastnost id dokumentu Azure Cosmos DB během importu (Všimněte si, že pokud dokumenty neobsahují tuto vlastnost, pak nástroj pro import vytvoří identifikátor GUID jako hodnota vlastnosti id).

Během importu nejsou k dispozici několik upřesňujících možností. Nejprve při zahrnuje nástroj výchozí hromadně importovat uložené procedury (BulkInsert.js), můžete zadat vlastní import uložené procedury:

 ![Snímek obrazovky databázi Cosmos Azure hromadné vložení sproc možnost](./media/import-data/bulkinsertsp.png)

Kromě toho při importu datum typy (například ze systému SQL Server nebo MongoDB), můžete zvolit tři možnosti importu:

 ![Snímek obrazovky databázi Cosmos Azure datum čas import možnosti](./media/import-data/datetimeoptions.png)

* Řetězec: Zachovat jako hodnotu řetězce
* Epoch: Zachovat jako číslo hodnota Epoch
* Oba: Zachovat řetězec a Epoch číselné hodnoty. Tato možnost vytvoří vnořený, například: "date_joined": {"Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245}

Program pro import hromadné DB Cosmos Azure má následující další rozšířené možnosti:

1. Velikost dávky: Nástroj výchozí velikost dávky 50.  Pokud jsou dokumenty, které se importovat velké, zvažte, což snižuje velikost dávky. Pokud dokumenty, které se importovat malé, zvažte naopak vyvolání velikost dávky.
2. Skript maximální velikost (bajty): použije se výchozí hodnota nástroje skriptu maximální velikost 512 KB.
3. Zakázat automatické generování Id: Pokud každému dokumentu určených k importu obsahuje id pole, pak výběrem této možnosti můžete zvýšit výkon. Chybí hodnota v poli jedinečné id dokumenty nebudou importovány.
4. Aktualizace stávající dokumenty: Nástroj výchozí není nahrazení stávající dokumenty docházet ke konfliktům id. Výběrem této možnosti povolíte přepsal stávající dokumenty s odpovídajícím ID. Tato funkce je užitečná pro naplánované data migrace, které aktualizovat existující dokumenty.
5. Počet opakovaných pokusů o selhání: Určuje počet opakovaných pokusů připojení k databázi Azure Cosmos v případě přechodných chyb (například přerušení připojení k síti).
6. Interval opakování: Určuje dobu čekání mezi opakování pokusu o připojení k databázi Azure Cosmos v případě přechodných chyb (například přerušení připojení k síti).
7. Režim připojení: Určuje režim připojení pro použití s Azure Cosmos DB. Dostupné možnosti jsou DirectTcp, DirectHttps a brány. Režimy přímé připojení je rychlejší, zatímco režimu brány je další brány firewall popisný jako pouze používá port 443.

![Snímek obrazovky databázi Cosmos Azure hromadným importem rozšířené možnosti](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> Nástroj pro import výchozí režim připojení DirectTcp. Pokud máte brány firewall problémy, přejděte do režimu připojení brány, jak vyžaduje jenom port 443.
> 
> 

## <a id="SQLSeqTarget"></a>Importovat do rozhraní SQL API (po sobě jdoucích záznam Import)
– Importér sekvenční záznam Azure Cosmos DB umožňuje importovat z jakéhokoli z dostupných možností na základě záznamu podle. Tuto možnost můžete zvolit, pokud při importu do existující kolekce, která dosáhla své kvóty uložené procedury. Nástroj podporuje import do Azure Cosmos DB kolekce jedním (jedním oddílem i více oddílu), stejně jako horizontálně dělené importu, které jsou data rozdělena mezi více kolekcí Azure Cosmos DB jedním oddílem nebo více oddílů. Další informace o segmentace dat naleznete v tématu [dělení a škálování v Azure Cosmos DB](partition-data.md).

![Snímek obrazovky databázi Cosmos Azure možnosti sekvenční záznamů importu](./media/import-data/documentdbsequential.png)

Formát Azure Cosmos DB připojovacího řetězce je:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Připojovací řetězce k účtu Azure Cosmos DB můžete načíst ze stránky klíče na portálu Azure, jak je popsáno v [Správa účtu Azure Cosmos DB](manage-account.md), ale název databáze musí být připojenou k připojovací řetězec v následujícím formátu:

    Database=<Azure Cosmos DB Database>;

> [!NOTE]
> Pomocí příkazu ověřte, zda je přístupná zadané v poli instanci Azure Cosmos DB.
> 
> 

Chcete-li importovat na jedinou kolekci, zadejte název kolekce, do které data budou naimportovány a klikněte na tlačítko Přidat. Import do více kolekcí, zadejte jednotlivé názvy kolekce jednotlivě nebo použijte následující syntaxi zadat více kolekcí: *collection_prefix*[index - end index počátečního]. Při zadávání více kolekcí prostřednictvím výše uvedené syntaxe, mějte tyto pokyny:

1. Jsou podporovány pouze celé číslo vzory názvů rozsahu. Například zadání kolekce [0-3] vytvoří následující kolekce: collection0, collection1, collection2, collection3.
2. Můžete použít zkrácené syntaxe: kolekce [3] Vytvoří stejnou sadu kolekcí uveden v kroku 1.
3. Lze zadat více než jeden nahrazení. Například kolekce [0-1] [0-9] vytvoří 20 názvů kolekcí úvodními nulami (collection01, ..02, ..03).

Jakmile názvy kolekce byly nastaveny, vyberte požadované propustnost kolekcí (400 RUs na 250 000 RUs). Pro nejlepší výkon import zvolte vyšší propustnost. Další informace o úrovně výkonu najdete v tématu [úrovně výkonu v Azure Cosmos DB](performance-levels.md). Všechny import do kolekcí s propustností > 10 000 RUs vyžadují klíč oddílu. Pokud zvolíte možnost mít více než 250 000 RUs, musíte do souboru požadavek na portálu pro svůj účet vyšší.

> [!NOTE]
> Propustnost nastavení platí jenom pro kolekci nebo vytvoření databáze. Pokud existuje zadané kolekci, se nezmění jeho propustnost.
> 
> 

Při importu do více kolekcí, podporuje nástroj pro import na základě hodnoty hash horizontálního dělení. V tomto scénáři zadejte vlastnost dokumentu, které chcete použít jako klíč oddílu (Pokud klíč oddílu je prázdné, dokumenty jsou horizontálně dělené náhodně mezi kolekcemi cíl).

Můžete volitelně můžete určit, které pole ve zdroji import má být použit jako vlastnost id dokumentu Azure Cosmos DB během importu (Všimněte si, že pokud dokumenty neobsahují tuto vlastnost, pak nástroj pro import vytvoří identifikátor GUID jako hodnota vlastnosti id).

Během importu nejsou k dispozici několik upřesňujících možností. První při importu datum typy (například ze systému SQL Server nebo MongoDB), můžete zvolit tři možnosti importu:

 ![Snímek obrazovky databázi Cosmos Azure datum čas import možnosti](./media/import-data/datetimeoptions.png)

* Řetězec: Zachovat jako hodnotu řetězce
* Epoch: Zachovat jako číslo hodnota Epoch
* Oba: Zachovat řetězec a Epoch číselné hodnoty. Tato možnost vytvoří vnořený, například: "date_joined": {"Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245}

Azure Cosmos DB - program pro import po sobě jdoucích záznam obsahuje následující další pokročilé možnosti:

1. Počet požadavků na paralelní: nástroj výchozí paralelní dva požadavky. Dokumenty, které se importovat jsou malé, vezměte v úvahu vyvolání počet paralelní požadavků. Všimněte si, že pokud toto číslo se vyvolá příliš mnoho, importu může dojít k omezení šířky pásma.
2. Zakázat automatické generování Id: Pokud každému dokumentu určených k importu obsahuje id pole, pak výběrem této možnosti můžete zvýšit výkon. Chybí hodnota v poli jedinečné id dokumenty nebudou importovány.
3. Aktualizace stávající dokumenty: Nástroj výchozí není nahrazení stávající dokumenty docházet ke konfliktům id. Výběrem této možnosti povolíte přepsal stávající dokumenty s odpovídajícím ID. Tato funkce je užitečná pro naplánované data migrace, které aktualizovat existující dokumenty.
4. Počet opakovaných pokusů o selhání: Určuje počet opakovaných pokusů připojení k databázi Azure Cosmos v případě přechodných chyb (například přerušení připojení k síti).
5. Interval opakování: Určuje dobu čekání mezi opakování pokusu o připojení k databázi Azure Cosmos v případě přechodných chyb (například přerušení připojení k síti).
6. Režim připojení: Určuje režim připojení pro použití s Azure Cosmos DB. Dostupné možnosti jsou DirectTcp, DirectHttps a brány. Režimy přímé připojení je rychlejší, zatímco režimu brány je další brány firewall popisný jako pouze používá port 443.

![Snímek obrazovky databázi Cosmos Azure import sekvenční záznam rozšířené možnosti](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> Nástroj pro import výchozí režim připojení DirectTcp. Pokud máte brány firewall problémy, přejděte do režimu připojení brány, jak vyžaduje jenom port 443.
> 
> 

## <a id="IndexingPolicy"></a>Zadejte zásady indexování
Když povolíte nástroj pro migraci při vytváření kolekcí rozhraní API pro Azure Cosmos databáze SQL během importu, můžete zadat zásady indexování kolekcí. V části Rozšířené možnosti Azure DB Cosmos po sobě jdoucích záznamů možností a Azure Cosmos DB hromadného importu přejděte do části zásady indexování.

![Snímek obrazovky Azure Cosmos DB indexování zásad rozšířené možnosti](./media/import-data/indexingpolicy1.png)

Pomocí zásady indexování rozšířené možnosti, můžete vybrat indexování soubor zásad, ručně zadejte zásady indexování nebo vybrat ze sady výchozích šablon (podle pravým tlačítkem myši na do textového pole indexování zásady).

Šablony zásad, které tento nástroj nabízí jsou:

* Výchozí hodnota. Tato zásada je vhodné, když jste provádění dotazy na rovnost pro řetězce a pomocí klauzule ORDER BY, rozsah a dotazy na rovnost pro čísla. Tato zásada má nižší režijní náklady úložiště index než rozsah.
* Rozsah. Tato zásada je vhodné, když používáte ORDER BY, rozsah a dotazy na rovnost na čísla i řetězce. Tato zásada nemá vyšší index režijní náklady na úložiště než výchozí nebo hodnoty Hash.

![Snímek obrazovky Azure Cosmos DB indexování zásad rozšířené možnosti](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Pokud nezadáte zásady indexování, se použije výchozí zásady. Další informace o indexování zásad najdete v tématu [Azure DB Cosmos indexování zásady](indexing-policies.md).
> 
> 

## <a name="export-to-json-file"></a>Exportovat do souboru JSON
Exportu Azure Cosmos DB JSON umožňuje některé z dostupných možností exportovat do souboru JSON, který obsahuje pole dokumentů JSON. Nástroj zpracuje exportu pro vás, nebo můžete k zobrazení výsledné příkaz migrace a spusťte příkaz. Výsledný soubor JSON může být uložená místně nebo v úložiště objektů Blob v Azure.

![Snímek obrazovky z Azure Cosmos DB JSON možností exportu místního souboru](./media/import-data/jsontarget.png)

![Možností exportu snímek z Azure Cosmos DB JSON Azure Blob storage](./media/import-data/jsontarget2.png)

Volitelně můžete prettify výsledný formát JSON, který zvýší velikost výsledného dokumentu při vytváření obsahu více číselné vyjádření.

    Standard JSON export
    [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]

    Prettified JSON export
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

## <a name="advanced-configuration"></a>Pokročilá konfigurace
V dialogovém okně Upřesnit konfiguraci zadejte umístění souboru protokolu, do kterého chcete všechny chyby zapsána. Na této stránce platí následující pravidla:

1. Pokud není zadaný název souboru, jsou vráceny všechny chyby na stránce výsledky.
2. Pokud je název souboru zadaný bez adresáře, pak soubor je vytvořen (nebo přepsat) v aktuálním adresáři prostředí.
3. Pokud vyberete existující soubor, a soubor se přepíše, není žádná možnost připojit.

Potom vyberte, zda chcete protokolovat všechny, kritické, nebo žádná chybová zpráva. Nakonec rozhodnout, jak často na obrazovce se aktualizuje přenos zpráv s jejím průběhu.

    ![Screenshot of Advanced configuration screen](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Potvrďte nastavení pro import a zobrazení příkazového řádku
1. Po zadání informace o zdroji, informace o cílové a pokročilou konfiguraci, zkontrolujte souhrn migrace a, volitelně, zobrazení nebo zkopírování příkaz výsledné migrace (kopírování příkazu je užitečné k automatizaci operací import):
   
    ![Snímek obrazovky souhrn](./media/import-data/summary.png)
   
    ![Snímek obrazovky souhrn](./media/import-data/summarycommand.png)
2. Jakmile budete spokojeni s možnosti zdroje a cíle, klikněte na možnost **Import**. Uplynulý čas, přenášená počet a informace o selhání (Pokud název souboru v pokročilé konfiguraci neposkytli) aktualizovat, protože proces importu. Po dokončení můžete exportovat výsledky (například k řešení případných selhání import).
   
    ![Snímek obrazovky z Azure Cosmos DB JSON možností exportu](./media/import-data/viewresults.png)
3. Nový import, může spustit také zachovat stávající nastavení (například připojovací řetězec informace, zdroje a cíle volba atd.) nebo resetování všechny hodnoty.
   
    ![Snímek obrazovky z Azure Cosmos DB JSON možností exportu](./media/import-data/newimport.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste provést následující úlohy:

> [!div class="checklist"]
> * Nainstalovaný nástroj pro migraci dat
> * Importovaná data z různých zdrojů dat.
> * Export z Azure Cosmos DB do formátu JSON

Teď můžete pokračovat v dalším kurzu a zjistěte, jak zadávat dotazy na data pomocí Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Postup dotazování dat?](../cosmos-db/tutorial-query-sql-api.md)
