---
title: Řešení potíží s konektory služby Azure Data Factory
description: Zjistěte, jak řešit problémy s konektorem v Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9f3a13a097d7cce87aead4ec2d76ce7cbbb1a206
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75778222"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Řešení potíží s konektory služby Azure Data Factory

Tento článek zkoumá běžné metody řešení potíží pro konektory v Azure Data Factory.
  

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>Kód chyby: AzureBlobOperationFailed

- **Zpráva**:`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Příčina**: Problém s operací úložiště objektů blob.

- **Doporučení**: Zkontrolujte chybu v detailech. Viz dokument nápovědy https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codesk objektům blob: . V případě potřeby kontaktujte tým úložiště.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Kód chyby: AzureBlobServiceNotReturnExpectedDataLength

- **Zpráva**:`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Kód chyby: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Zpráva**:`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Kód chyby: AzureStorageOperationFailedConcurrentWrite

- **Zpráva**:`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Chybová zpráva: Velikost požadavku je příliš velká.

- **Příznaky**: Zkopírujete data do Azure Cosmos DB s výchozí velikostí dávky zápisu a chyba ***přístupů**" Velikost požadavku je příliš velká "*.

- **Příčina**: Cosmos DB omezuje velikost jednoho požadavku na 2 MB. Vzorec je Velikost požadavku = Velikost jednoho dokumentu * Velikost dávky zápisu. Pokud je velikost dokumentu velká, bude mít výchozí chování za následek příliš velkou velikost požadavku. Můžete optimalizovat velikost dávky zápisu.

- **Řešení**: V jímce aktivity kopírování zmenšete hodnotu "Write batch size" (výchozí hodnota je 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Chybová zpráva: Porušení omezení jedinečného indexu

- **Příznaky**: Při kopírování dat do cosmos DB, narazí na následující chybu:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Příčina**: Existují dvě možné příčiny:

    - Pokud použijete **Vložit** jako chování zápisu, tato chyba znamená, že zdrojová data mají řádky nebo objekty se stejným ID.

    - Pokud použijete **Upsert** jako chování zápisu a nastavíte jiný jedinečný klíč do kontejneru, tato chyba znamená, že zdrojová data mají řádky nebo objekty s různými ID, ale stejnou hodnotu pro definovaný jedinečný klíč.

- **Rozlišení**: 

    - Pro cause1 nastavte **Upsert** jako chování zápisu.
    - Z důvodu 2 se ujistěte, že každý dokument má jinou hodnotu pro definovaný jedinečný klíč.

### <a name="error-message-request-rate-is-large"></a>Chybová zpráva: Míra požadavků je velká

- **Příznaky**: Při kopírování dat do cosmos DB, narazí na následující chybu:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Příčina**: Použité jednotky požadavku jsou větší než dostupné ru nakonfigurované v Cosmos DB. Zde se dozvíte, jak Cosmos DB vypočítá v RU [.](../cosmos-db/request-units.md#request-unit-considerations)

- **Řešení**: Zde jsou dvě řešení:

    1. **Zvyšte počet žp. na vyšší** hodnotu kontejneru v Cosmos DB, což zlepší výkon aktivity kopírování, i když v Cosmos DB vzniknou vyšší náklady. 

    2. Snížit **writeBatchSize** na menší hodnotu (například 1000) a nastavit **parallelCopies** na menší hodnotu, jako je například 1, což způsobí, že výkon spuštění kopie horší než aktuální, ale nebude mít větší náklady v Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Sloupec chybí v mapování sloupců

- **Příznaky**: Při importu schématu pro Cosmos DB pro mapování sloupců chybí některé sloupce. 

- **Příčina**: ADF odvodí schéma z prvních 10 dokumentů Cosmos DB. Pokud některé sloupce/vlastnosti nemají v těchto dokumentech hodnotu, nebudou adf detekovány, a proto se nezobrazí.

- **Řešení**: Můžete vyladit dotaz tak, jak je uvedeno níže, chcete-li vynutit sloupec, který se zobrazí v sadě výsledků s prázdnou hodnotou: (předpokládejme: v prvních dokumentech 10 chybí sloupec "nemožné"). Případně můžete ručně přidat sloupec pro mapování.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Chybová zpráva: GuidRepresentation pro čtenáře je CSharpLegacy

- **Příznaky**: Při kopírování dat z Cosmos DB MongoAPI/MongoDB s polem UUID se zobrazí následující chyba:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Příčina**: Existují dva způsoby, jak reprezentovat UUID v BSON - UuidStardard a UuidLegacy. Ve výchozím nastavení se UuidLegacy používá ke čtení dat. Dojde k chybě, pokud vaše uuid data v MongoDB je UuidStandard.

- **Rozlišení**: V připojovacím řetězci MongoDB přidejte možnost "**uuidRepresentation=standard**". Další informace naleznete v [připojovacím řetězci MongoDB](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Kód chyby: AdlsGen2OperationFailed

- **Zpráva**:`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Příčina**: ADLS Gen2 vyvolá chybu označující operace se nezdařila.

- **Doporučení:** Zkontrolujte podrobnou chybovou zprávu vyvrženou adls gen2. Pokud je to způsobeno přechodnou poruchou, zkuste to znovu. Pokud potřebujete další pomoc, obraťte se na podporu Azure Storage a zadejte ID žádosti v chybové zprávě.

- **Příčina**: Pokud chybová zpráva obsahuje "Zakázáno", instanční objekt nebo spravovanou identitu, kterou používáte, nemusí mít dostatečná oprávnění pro přístup k ADLS Gen2.

- **Doporučení**: Viz dokument https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authenticationnápovědy: .

- **Příčina**: Pokud chybová zpráva obsahuje chybu InternalServerError, je chyba vrácena gen2 Služby ADLS.

- **Doporučení**: To může být způsobeno přechodnou poruchou, opakujte akci. Pokud problém přetrvává, obraťte se na podporu Azure Storage a zadejte ID požadavku v chybové zprávě.


### <a name="error-code--adlsgen2invalidurl"></a>Kód chyby: AdlsGen2InvalidUrl

- **Zpráva**:`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Kód chyby: AdlsGen2InvalidFolderPath

- **Zpráva**:`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Kód chyby: AdlsGen2OperationFailedConcurrentWrite

- **Zpráva**:`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>Kód chyby: AdlsGen2TimeoutError

- **Zpráva**:`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Chybová zpráva: Vzdálený server vrátil chybu: (403) Zakázáno

- **Příznaky**: Aktivita kopírování se nezdaří s následující chybou: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Příčina**: Jednou z možných příčin je, že instanční objekt nebo spravovaná identita, kterou používáte, nemá oprávnění k přístupu k určité složce nebo souboru.

- **Řešení**: Udělte odpovídající oprávnění pro všechny složky a podsložky, které potřebujete zkopírovat. Viz [tento dokument](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Chybová zpráva: Nepodařilo se získat přístupový token pomocí instančního objektu. ADAL Chyba: service_unavailable

- **Příznaky**: Aktivita kopírování se nezdaří s následující chybou:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Příčina:** Pokud server tokenů služby (STS) vlastněný službou Azure Active Directory není k dispozici, tj. 

- **Rozlišení**: Znovu spusťte aktivitu kopírování po několika minutách.
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL Data Warehouse/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Kód chyby: SqlFailedToConnect

- **Zpráva**:`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Příčina:** Pokud chybová zpráva obsahuje "SqlException", sql database vyvolá chybu označující některé konkrétní operace se nezdařilo.

- Doporučení : Další **podrobnosti**naleznete podle kódu chyby SQL v tomto referenčním dokumentu: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Pokud potřebujete další pomoc, obraťte se na podporu Azure SQL.

- **Příčina**: Pokud chybová zpráva obsahuje "Klient s IP adresou ..." není povolen přístup k serveru", a pokoušíte se připojit k Azure SQL Database, obvykle je způsobena problémem brány firewall Azure SQL Database.

- **Doporučení:** V konfiguraci brány firewall Azure SQL Server povolte možnost Povolit služby Azure a prostředky pro přístup k tomuto serveru. Referenční dokument: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Kód chyby: SqlOperationFailed

- **Zpráva**:`A database operation failed. Please search error to get more details.`

- **Příčina:** Pokud chybová zpráva obsahuje "SqlException", sql database vyvolá chybu označující některé konkrétní operace se nezdařilo.

- **Doporučení:** Pokud chyba SQL není jasná, pokuste se změnit databázi na nejnovější úroveň kompatibility '150'. To může vyvolat nejnovější verze SQL chyby. Prosím, podívejte se https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompatna detail doc: .
        Řešení potíží s SQL naleznete v tomto referenčním dokumentu podle https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errorskódu chyby SQL: . Pokud potřebujete další pomoc, obraťte se na podporu Azure SQL.

- **Příčina**: Pokud chybová zpráva obsahuje "PdwManagedToNativeInteropException", obvykle je to způsobeno neshodou mezi velikostí zdrojového a jímanového sloupce.

- **Doporučení:** Zkontrolujte velikost zdrojových i jímacích sloupců. Pokud potřebujete další pomoc, obraťte se na podporu Azure SQL.

- **Příčina**: Pokud chybová zpráva obsahuje "InvalidOperationException", obvykle je to způsobeno neplatnými vstupními daty.

- **Doporučení:** Chcete-li zjistit, který řádek narazí na problém, povolte funkci odolnosti proti chybám při aktivitě kopírování, která může přesměrovat problematické řádky do úložiště pro další šetření. Referenční dokument: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>Kód chyby: SqlUnauthorizedAccess

- **Zpráva**:`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Příčina**: Pověření je nesprávné nebo přihlašovací účet nemá přístup k databázi SQL.

- **Doporučení**: Zkontrolujte, zda přihlašovací účet má dostatečná oprávnění pro přístup k databázi SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Kód chyby: SqlOpenConnectionTimeout

- **Zpráva**:`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Příčina**: Může být přechodná chyba databáze SQL.

- **Doporučení:** Pokuste se znovu aktualizovat propojený připojovací řetězec služby s větší hodnotou časového limitu připojení.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Kód chyby: SqlAutoCreateTableTypeMapFailed

- **Zpráva**:`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Příčina:** Tabulka automatického vytváření nemůže splnit požadavek zdroje.

- **Doporučení:** Aktualizujte typ sloupce v "mapování" nebo ručně vytvořte tabulku jímky na cílovém serveru.


### <a name="error-code--sqldatatypenotsupported"></a>Kód chyby: SqlDataTypeNotSupported

- **Zpráva**:`A database operation failed. Check the SQL errors.`

- **Příčina**: Pokud k problému dochází ve zdroji SQL a chyba souvisí s přetečením SqlDateTime, hodnota dat je přes rozsah typu logiky (1/1/1753 12:00:00 - 12/31/9999 11:59:59 PM).

- **Doporučení:** Přetypování typu na řetězec ve zdrojovém dotazu SQL nebo v mapování sloupce aktivity kopírování změňte typ sloupce na Řetězec.

- **Příčina**: Pokud k problému dochází na jímce SQL a chyba souvisí s přetečenísqlDateTime, hodnota dat je přes povolený rozsah v tabulce jímky.

- **Doporučení:** Aktualizace odpovídajícího typu sloupce na typ datetime2 v tabulce jímky.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Kód chyby: SqlInvalidDbStoredProcedure

- **Zpráva**:`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Příčina**: Zadaná uložená procedura není platná. To může být způsobeno tím, že uložená procedura nevrátí žádná data.

- **Doporučení**: Ověřte uloženou proceduru pomocí nástrojů SQL. Ujistěte se, že uložená procedura může vrátit data.


### <a name="error-code--sqlinvaliddbquerystring"></a>Kód chyby: SqlInvalidDbQueryString

- **Zpráva**:`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Příčina:** Zadaný dotaz SQL není platný. Může to být způsobeno tím, že dotaz nevrátí žádná data

- **Doporučení:** Ověřte dotaz SQL pomocí nástrojů SQL. Ujistěte se, že dotaz může vrátit data.


### <a name="error-code--sqlinvalidcolumnname"></a>Kód chyby: SqlInvalidColumnName

- **Zpráva**:`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Příčina**: Sloupec nelze najít. Možná konfigurace špatně.

- **Doporučení:** Ověřte sloupec v dotazu, "struktura" v datové sadě a "mapování" v aktivitě.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Kód chyby: SqlColumnNameMismatchByCaseSensitive

- **Zpráva**:`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Kód chyby: SqlBatchWriteTimeout

- **Zpráva**:`Timeouts in SQL write operation.`

- **Příčina**: Může být přechodná chyba databáze SQL.

- **Doporučení**: Opakujte akci. Pokud problém repro, obraťte se na podporu Azure SQL.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Kód chyby: SqlBatchWriteTransactionFailed

- **Zpráva**:`SQL transaction commits failed`

- **Příčina**: Pokud podrobnosti o výjimce neustále sdělují časový limit transakce, latence sítě mezi integračním runtimem a databází je vyšší než výchozí prahová hodnota jako 30 sekund.

- **Doporučení:** Aktualizace připojovacího řetězce propojené služby SQL s hodnotou "časový limit připojení" se rovná hodnotě 120 nebo vyšší a znovu spusťte aktivitu.

- **Příčina**: Pokud podrobnosti o výjimce občas sdělují sqlconnection přerušeno, může se jedná pouze o přechodné selhání sítě nebo problém na straně databáze SQL

- **Doporučení:** Opakujte aktivitu a zkontrolujte metriky na straně databáze SQL.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Kód chyby: SqlBulkCopyInvalidColumnLength

- **Zpráva**:`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Příčina:** Hromadná kopie SQL se nezdařila z důvodu přijetí neplatné délky sloupce z klienta bcp.

- **Doporučení:** Chcete-li zjistit, který řádek narazí na problém, povolte funkci odolnosti proti chybám při aktivitě kopírování, která může přesměrovat problematické řádky do úložiště pro další šetření. Referenční dokument: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Kód chyby: SqlConnectionIsClosed

- **Zpráva**:`The connection is closed by SQL Database.`

- **Příčina**: Připojení SQL je ukončeno databází SQL při vysokém souběžném spuštění a ukončení připojení serveru.

- **Doporučení:** Vzdálený server ukončil připojení SQL. Zkuste to prosím znovu. Pokud problém repro, obraťte se na podporu Azure SQL.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Kód chyby: SqlCreateTableFailedUnsupportedType

- **Zpráva**:`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Chybová zpráva: Převod se nezdařil při převodu z řetězce znaků na jedinečný identifikátor

- **Příznaky:** Při kopírování dat ze zdroje tabulkových dat (například SQL Server) do datového skladu Azure SQL pomocí fázované kopie a PolyBase, narazí na následující chybu:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Příčina:** Azure SQL Data Warehouse PolyBase nemůže převést prázdný řetězec na identifikátor GUID.

- **Rozlišení**: V propadu aktivity kopírování nastavte v části Nastavení polybase možnost **"use type default"** na hodnotu false.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Chybová zpráva: Očekávaný datový typ: DECIMAL(x,x), Problematický hodnota

- **Příznaky**: Při kopírování dat ze zdroje tabulkových dat (například SQL Server) do SQL DW pomocí fázované kopie a PolyBase, narazí na následující chybu:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Příčina:** Azure SQL Datový sklad Polybase nelze vložit prázdný řetězec (hodnota null) do desítkového sloupce.

- **Rozlišení**: V propadu aktivity kopírování nastavte v části Nastavení polybase možnost **"use type default"** na hodnotu false.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Chybová zpráva: Zpráva o výjimce java:HdfsBridge::CreateRecordReader

- **Příznaky**: Zkopírujete data do datového skladu Azure SQL pomocí PolyBase a hit následující chybu:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Příčina**: Možnou příčinou je, že schéma (celková šířka sloupce) je příliš velké (větší než 1 MB). Zkontrolujte schéma cílové tabulky SQL DW přidáním velikosti všech sloupců:

    - Int -> 4 bajty
    - Bigint -> 8 bajtů
    - Varchar(n),char(n),binary(n), varbinary(n) -> n bajtů
    - Nvarchar(n), nchar(n) -> n*2 bajty
    - Datum -> 6 bajtů
    - Datetime/(2), smalldatetime -> 16 bajtů
    - Datetimeoffset -> 20 bajtů
    - Desítkové -> 19 bajtů
    - Plovák -> 8 bajtů
    - Peníze -> 8 bajtů
    - Smallmoney -> 4 bajty
    - Real -> 4 bajty
    - Smallint -> 2 bajty
    - Čas -> 12 bajtů
    - Tinyint -> 1 bajt

- **Rozlišení:** Zmenšení šířky sloupce na menší než 1 MB

- Nebo použijte přístup hromadného vložení zakázáním Polybase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Chybová zpráva: Podmínka zadaná pomocí podmíněných záhlaví HTTP není splněna.

- **Příznaky**: Pomocí dotazu SQL naváděte data z Azure SQL Data Warehouse a dosáhnete následující chyby:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Příčina:** Problém s požadavkem na externí tabulku v Azure Storage v Azure Storage.

- **Řešení**: Spusťte stejný dotaz v SSMS a zkontrolujte, zda se zobrazí stejný výsledek. Pokud ano, pokračujte v řešení potíží otevřením lístku podpory pro službu Azure SQL Data Warehouse a zadáním názvu serveru a databáze SQL Data Warehouse.
            

## <a name="delimited-text-format"></a>Formát textu s oddělovačem

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Kód chyby: DelimitedTextColumnNameNotAllowNull

- **Zpráva**:`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Příčina**: Při nastavení 'firstRowAsHeader' v aktivitě, první řádek bude použit jako název sloupce. Tato chyba znamená, že první řádek obsahuje prázdnou hodnotu. Například: 'ColumnA,,ColumnB'.

- **Doporučení:** Zkontrolujte první řádek a opravte hodnotu, pokud je prázdná hodnota.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Kód chyby: Oddělovač textůVícesloupců Thandefined

- **Zpráva**:`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Příčina**: Počet sloupců problematického řádku je velký než počet sloupců prvního řádku. To může být způsobeno problémem s daty nebo nesprávným nastavením znaku oddělovače/uvozovky.

- **Doporučení:** Prosím, získejte počet řádků v chybové zprávě, zkontrolujte sloupec řádku a opravte data.

- **Příčina**: Pokud je očekávaný počet sloupců "1" v chybové zprávě, je možné, že jste zadali nesprávné nastavení komprese nebo formátu, což způsobilo, že adf nesprávně analyzovat soubory.

- **Doporučení:** Zkontrolujte nastavení formátu, abyste se ujistili, že se shoduje se zdrojovým souborem.

- **Příčina**: Pokud je zdrojem složka, je možné, že soubory pod určenou složkou mají jiné schéma.

- **Doporučení**: Ujistěte se, že soubory pod danou složkou mají stejné schéma.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Kód chyby: Oddělovač limitovaných textůTextIncorrectRowDeděl

- **Zpráva**:`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Kód chyby: DelimitedTextTooLargeColumnCount

- **Zpráva**:`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Kód chyby: DelimitedTextInvalidSettings

- **Zpráva**:`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Kód chyby: DynamicsCreateServiceClientError

- **Zpráva**:`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Příčina**: Jedná se o přechodný problém na straně serveru Dynamics.

- **Doporučení**: Znovu spusťte potrubí. Pokud se stále nedaří, pokuste se snížit paralelismus. Pokud se stále nezdaří, obraťte se na podporu dynamiky.



## <a name="json-format"></a>Formát JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Kód chyby: JsonInvalidArrayPathDefinition

- **Zpráva**:`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Kód chyby: JsonEmptyJObjectData

- **Zpráva**:`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Kód chyby: JsonNullValueInPathDefinition

- **Zpráva**:`Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Kód chyby: JsonUnsupportedHierarchicalComplexValue

- **Zpráva**:`The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Kód chyby: JsonConflictPartitionDiscoverySchema

- **Zpráva**:`Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Kód chyby: JsonInvalidDataFormat

- **Zpráva**:`Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Kód chyby: JsonInvalidDataMixedArrayAndObject

- **Zpráva**:`Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Formát Parquet

### <a name="error-code--parquetjavainvocationexception"></a>Kód chyby: ParketyJavaInvocationException

- **Zpráva**:`An error occurred when invoking java, message: %javaException;.`

- **Příčina**: Pokud chybová zpráva obsahuje 'java.lang.OutOfMemory', 'Java haldy prostor' a 'doubleCapacity', obvykle je to problém správy paměti ve staré verzi integrace runtime.

- **Doporučení:** Pokud používáte modul Runtime integrace s vlastním hostitelem a verze je starší než 3.20.7159.1, navrhněte upgrade na nejnovější verzi.

- **Příčina**: Pokud chybová zpráva obsahuje 'java.lang.OutOfMemory', integrační runtime nemá dostatek prostředků pro zpracování souborů.

- **Doporučení**: Omezte souběžné spuštění v modulu runtime integrace. Pro prostředí Integrace s vlastním hostitelem můžete vertikálně navýšit kapacitu na výkonný počítač s pamětí rovnou nebo větší než 8 GB.

- **Příčina**: Pokud chybová zpráva obsahuje 'NullPointerReference', je možné, že je přechodná chyba.

- **Doporučení**: Opakujte akci. Pokud problém přetrvává, obraťte se na podporu.


### <a name="error-code--parquetinvalidfile"></a>Kód chyby: ParketyInvalidFile

- **Zpráva**:`File is not a valid parquet file.`

- **Příčina**: Problém s parketovými soubory.

- **Doporučení:** Zkontrolujte, zda je vstup platný soubor parket.


### <a name="error-code--parquetnotsupportedtype"></a>Kód chyby: ParquetNotSupportedType

- **Zpráva**:`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Příčina**: Formát parket není v Azure Data Factory podporovaný.

- **Doporučení**: Zkontrolujte zdrojová data. Viz doc: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Kód chyby: ParketyMissedDecimalPrecisionScale

- **Zpráva**:`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Příčina**: Pokuste se analyzovat přesnost čísla a měřítko, ale žádné takové informace nejsou k dispozici.

- **Doporučení**: "Zdroj" nevrací správnou přesnost a měřítko. Zkontrolujte přesnost a měřítko sloupce problému.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Kód chyby: ParketyInvalidDecimalPrecisionScale

- **Zpráva**:`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Příčina**: Schéma je neplatné.

- **Doporučení:** Zkontrolujte přesnost a měřítko sloupce problému.


### <a name="error-code--parquetcolumnnotfound"></a>Kód chyby: ParketaColumnNotFound

- **Zpráva**:`Column %column; does not exist in Parquet file.`

- **Příčina**: Schéma zdroje je neshoda se schématem jímky.

- **Doporučení**: Zkontrolujte "mapování" v "aktivitě". Ujistěte se, že zdrojový sloupec lze mapovat na sloupec pravéjím houp.


### <a name="error-code--parquetinvaliddataformat"></a>Kód chyby: ParketyInvalidDataFormat

- **Zpráva**:`Incorrect format of %srcValue; for converting to %dstType;.`

- **Příčina:** Data nelze převést na typ určený v souboru mappings.source

- **Doporučení:** Zkontrolujte zdrojová data nebo určete správný datový typ pro tento sloupec v mapování sloupců aktivity kopírování. Viz doc: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Kód chyby: ParquetDataCountNotMatchColumnCount

- **Zpráva**:`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Příčina:** Neshoda počtu zdrojových sloupců a sloupců jímky

- **Doporučení:** Dvojitá kontrola počtu zdrojových sloupců je stejná jako počet sloupců jímky v "mapování".


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Kód chyby: ParquetDataTypeNotMatchColumnType

- **Zpráva**: Datový typ %srcType; neodpovídá danému typu sloupce %dstType; ve sloupci %columnIndex;'.

- **Příčina:** Data ze zdroje nelze převést na zadaný definovaný v jímce.

- **Doporučení:** Zadejte správný typ v mapping.sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Kód chyby: Parketa BridgeInvalidData

- **Zpráva**:`%message;`

- **Příčina:** Hodnota dat nad omezením

- **Doporučení**: Opakujte akci. Pokud problém přetrvává, kontaktujte nás.


### <a name="error-code--parquetunsupportedinterpretation"></a>Kód chyby: ParketyNepodporovanéInterpretace

- **Zpráva**:`The given interpretation '%interpretation;' of parquet format is not supported.`

- **Příčina**: Nepodporovaný scénář

- **Doporučení**: 'ParketquetInterpretFor' by neměl být 'sparkSql'.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Kód chyby: Varianta UnquetUnsupportFileLevelCompressionOption

- **Zpráva**:`File level compression is not supported for Parquet.`

- **Příčina**: Nepodporovaný scénář

- **Doporučení:** Odebrat 'CompressionType' v datové části.



## <a name="general-copy-activity-error"></a>Obecná chyba aktivity kopírování

### <a name="error-code--jrenotfound"></a>Kód chyby: JreNotFound

- **Zpráva**:`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Příčina**: Prostředí runtime integrace s vlastním hostitelem nemůže najít java runtime. Java Runtime je vyžadován pro čtení konkrétního zdroje.

- **Doporučení:** Zkontrolujte prostředí runtime integrace, referenční dokument:https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Kód chyby: Zástupný znak_

- **Zpráva**:`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Příčina**: Datová sada jímky nepodporuje zástupný znak.

- **Doporučení:** Zkontrolujte datovou sadu jímky a opravte cestu bez hodnoty zástupných symbolů.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Kód chyby: MappingInvalidPropertyWithEmptyValue

- **Zpráva**:`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Kód chyby: MappingInvalidPropertyWithNamePathAndOrdinal

- **Zpráva**:`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Kód chyby: MappingDuplicatedOrdinal

- **Zpráva**:`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Kód chyby: MappingInvalidOrdinalForSinkColumn

- **Zpráva**:`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Další kroky

Další nápovědu k řešení potíží naleznete v těchto zdrojích:

*  [Blog data factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Požadavky na funkce datové továrny](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Fórum přetečení zásobníku pro datovou továrnu](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter informace o Data Factory](https://twitter.com/hashtag/DataFactory)
            
