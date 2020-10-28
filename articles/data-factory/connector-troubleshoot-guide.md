---
title: Řešení potíží s konektory služby Azure Data Factory
description: Naučte se řešit potíže s konektorem v Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: 2e54c0b09c3dbe398b0522d0ad9ad2314e29ed26
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638138"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Řešení potíží s konektory služby Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží pro konektory v Azure Data Factory.
  
## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>Kód chyby: AzureBlobOperationFailed

- **Zpráva** : `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Příčina** : došlo k potížím s voláním operace BLOB Storage.

- **Doporučení** : Podrobnosti najdete v podrobnostech o chybě. Podívejte se na dokument s nápovědě k objektu BLOB: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes . Pokud potřebujete, obraťte se na tým úložiště.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Kód chyby: AzureBlobServiceNotReturnExpectedDataLength

- **Zpráva** : `Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Kód chyby: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Zpráva** : `Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Kód chyby: AzureStorageOperationFailedConcurrentWrite

- **Zpráva** : `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Chybová zpráva: velikost požadavku je příliš velká.

- **Příznaky** : kopírujete data do Azure Cosmos DB s výchozí velikostí dávky pro zápis a chyba volání *" **Velikost požadavku je příliš velká** "* .

- **Příčina** : Cosmos DB omezuje velikost jednoho požadavku na 2 MB. Vzorec je, velikost požadavku = jedna velikost dokumentu * velikost dávky zápisu. Pokud je velikost dokumentu velká, výchozí chování bude mít za následek příliš velkou velikost požadavku. Můžete vyladit velikost dávky zápisu.

- **Řešení** : v jímky aktivity kopírování snižte hodnotu "zapsat velikost dávky" (výchozí hodnota je 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Chybová zpráva: porušení omezení jedinečného indexu

- **Příznaky** : při kopírování dat do Cosmos DB se zobrazí následující chyba:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Příčina** : Existují dvě možné příčiny:

    - Pokud použijete funkci **Vložit** jako zápis, tato chyba znamená, že zdrojová data mají řádky nebo objekty se stejným ID.

    - Pokud použijete **Upsert** jako chování zápisu a nastavíte do kontejneru jiný jedinečný klíč, tato chyba znamená, že zdrojová data budou mít řádky nebo objekty s odlišnými identifikátory, ale stejnou hodnotu pro definovaný jedinečný klíč.

- **Řešení** : 

    - V případě cause1 nastavte **Upsert** jako chování zápisu.
    - V případě příčiny 2 se ujistěte, že každý dokument má odlišnou hodnotu pro definovaný jedinečný klíč.

### <a name="error-message-request-rate-is-large"></a>Chybová zpráva: frekvence požadavků je velká

- **Příznaky** : při kopírování dat do Cosmos DB se zobrazí následující chyba:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Příčina** : použité jednotky žádostí jsou větší než dostupný ru nakonfigurovaný v Cosmos DB. Přečtěte [si, jak](../cosmos-db/request-units.md#request-unit-considerations)Cosmos DB počítá ru.

- **Řešení** : tady jsou dvě řešení:

    1. **Zvyšte hodnotu v kontejneru ru** na větší hodnotu v Cosmos DB, což vylepší výkon aktivity kopírování, i když se v Cosmos DB účtuje větší náklady. 

    2. Zmenšete **writeBatchSize** na menší hodnotu (například 1000) a nastavte **parallelCopies** na menší hodnotu, jako je například 1, což způsobí, že kopírování bude mít horší výkon, než je aktuální, ale nebudou se vám účtovat žádné náklady Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>V mapování sloupce chybí sloupec.

- **Příznaky** : Když importujete schéma pro Cosmos DB mapování sloupců, chybí některé sloupce. 

- **Příčina** : ADF odvodí schéma z prvních 10 Cosmos DB dokumentů. Pokud některé sloupce nebo vlastnosti v těchto dokumentech nemají hodnotu, nebudou zjištěny pomocí ADF, takže se nebudou zobrazovat.

- **Řešení** : Tento dotaz můžete vyladit tak, aby se sloupec vynutil, aby se zobrazil v sadě výsledků s prázdnou hodnotou: (Předpokládejme, že ve prvních 10 dokumentech chybí sloupec nemožné). Případně můžete ručně přidat sloupec pro mapování.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Chybová zpráva: GuidRepresentation pro čtecí zařízení je CSharpLegacy

- **Příznaky** : při kopírování dat z Cosmos DB MongoAPI/MongoDB s polem UUID se zobrazí následující chyba:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Příčina** : Existují dva způsoby, jak vyjádřit UUID v bson-UuidStardard a UuidLegacy. Ve výchozím nastavení se pro čtení dat používá UuidLegacy. Pokud jsou vaše data UUID v MongoDB UuidStandard, bude se vám vyrazit chyba.

- **Řešení** : v připojovacím řetězci MongoDB přidejte možnost " **uuidRepresentation = Standard** ". Další informace najdete v tématu [připojovací řetězec MongoDB](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Kód chyby: AdlsGen2OperationFailed

- **Zpráva** : `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Příčina** : adls Gen2 vyvolá chybu oznamující, že operace se nezdařila.

- **Doporučení** : Přečtěte si podrobnou chybovou zprávu vyvolanou adls Gen2. Pokud je to způsobeno přechodným selháním, zkuste to prosím znovu. Pokud potřebujete další pomoc, obraťte se prosím na podporu Azure Storage a poskytněte ID žádosti v chybové zprávě.

- **Příčina** : Pokud chybová zpráva obsahuje zakázané, instanční objekt nebo spravovaná identita nemusí mít k dispozici dostatečná oprávnění pro přístup k adls Gen2.

- **Doporučení** : informace naleznete v dokumentu Help: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication .

- **Příčina** : Pokud chybová zpráva obsahuje hodnotu ' Nenalezeno ', je vrácena chyba adls Gen2.

- **Doporučení** : může to být způsobeno přechodným selháním. zkuste to prosím znovu. Pokud se problém nevyřeší, obraťte se prosím na podporu Azure Storage a poskytněte ID žádosti v chybové zprávě.


### <a name="error-code--adlsgen2invalidurl"></a>Kód chyby: AdlsGen2InvalidUrl

- **Zpráva** : `Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Kód chyby: AdlsGen2InvalidFolderPath

- **Zpráva** : `The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Kód chyby: AdlsGen2OperationFailedConcurrentWrite

- **Zpráva** : `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code-adlsgen2timeouterror"></a>Kód chyby: AdlsGen2TimeoutError

- **Zpráva** : `Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Chybová zpráva: základní připojení bylo ukončeno: nelze vytvořit vztah důvěryhodnosti pro zabezpečený kanál SSL/TLS.

- **Příznaky** : aktivita kopírování se nezdařila s následující chybou: 

    ```
    Message: Failure happened on 'Sink' side. ErrorCode=UserErrorFailedFileOperation,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Upload file failed at path STAGING/PLANT/INDIARENEWABLE/LiveData/2020/01/14\\20200114-0701-oem_gibtvl_mannur_data_10min.csv.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.Net.WebException,Message=The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.,Source=System,''Type=System.Security.Authentication.AuthenticationException,Message=The remote certificate is invalid according to the validation procedure.,Source=System,'.
    ```

- **Příčina** : ověření certifikátu během TLS handshake selhalo.

- **Řešení** : alternativní řešení: k přeskočení ověřování TLS pro adls Gen1 použijte dvoufázové kopírování. Je nutné reprodukování tohoto problému a shromáždění trasování Netmon a poté zapojením svého síťového týmu ke kontrole konfigurace místní sítě.

    ![Řešení potíží s ADLS Gen1](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Chybová zpráva: vzdálený server vrátil chybu: (403) zakázáno

- **Příznaky** : aktivita kopírování se nezdařila s následující chybou: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Příčina** : jedinou možnou příčinou je, že instanční objekt nebo spravovaná identita nemají oprávnění pro přístup k určité složce nebo souboru.

- **Řešení** : Udělte odpovídající oprávnění pro všechny složky a podsložky, které potřebujete zkopírovat. Přečtěte si [Tento dokument](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Chybová zpráva: Nepodařilo se získat přístupový token pomocí instančního objektu. Chyba ADAL: service_unavailable

- **Příznaky** : aktivita kopírování se nezdařila s následující chybou:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Příčina** : Pokud server tokenu služby (STS), který je vlastněn Azure Active Directory, není k dispozici, například příliš zaneprázdněný pro zpracování požadavků, vrátí chybu HTTP 503. 

- **Řešení** : po několika minutách znovu spusťte aktivitu kopírování.
                  

## <a name="azure-synapse-analytics-formerly-sql-data-warehouseazure-sql-databasesql-server"></a>Azure synapse Analytics (dříve SQL Data Warehouse)/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Kód chyby: SqlFailedToConnect

- **Zpráva** : `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Příčina** : Pokud chybová zpráva obsahuje "SqlException", SQL Database vyvolá chybu oznamující, že některá konkrétní operace selhala.

- **Doporučení** : Další podrobnosti najdete v kódu chyby SQL v tomto referenčním dokumentu: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Pokud potřebujete další pomoc, obraťte se na podporu Azure SQL.

- **Příčina** : Pokud chybová zpráva obsahuje "klient s IP adresou"... nemá povolený přístup k serveru a pokoušíte se připojit k Azure SQL Database, obvykle to způsobuje Azure SQL Database potížích s bránou firewall.

- **Doporučení** : v konfiguraci brány firewall logického serveru SQL povolte možnost Povolit službám a prostředkům Azure přístup k tomuto serveru. Referenční doc: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure .


### <a name="error-code--sqloperationfailed"></a>Kód chyby: SqlOperationFailed

- **Zpráva** : `A database operation failed. Please search error to get more details.`

- **Příčina** : Pokud chybová zpráva obsahuje "SqlException", SQL Database vyvolá chybu oznamující, že některá konkrétní operace selhala.

- **Doporučení** : Pokud chyba SQL není jasná, zkuste prosím změnit databázi na nejnovější úroveň kompatibility 150. Může vyvolat nejnovější verzi chyb SQL. Přečtěte si [dokument s podrobnostmi](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat).

    Pokud chcete řešit potíže s SQL serverem, vyhledejte v tomto referenčním dokumentu kód chyby SQL, kde najdete další podrobnosti: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Pokud potřebujete další pomoc, obraťte se na podporu Azure SQL.

- **Příčina** : Pokud chybová zpráva obsahuje "PdwManagedToNativeInteropException", obvykle se jedná o neshodu mezi zdrojem a velikostmi sloupců jímky.

- **Doporučení** : Ověřte velikost zdroje i sloupce jímky. Pokud potřebujete další pomoc, obraťte se na podporu Azure SQL.

- **Příčina** : Pokud chybová zpráva obsahuje "InvalidOperationException", obvykle je způsobena neplatnými vstupními daty.

- **Doporučení** : Chcete-li zjistit, který řádek narazí na problém, povolte funkci odolnost proti chybám u aktivity kopírování, která může přesměrovat problematické řádky do úložiště pro další šetření. Referenční doc: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .



### <a name="error-code--sqlunauthorizedaccess"></a>Kód chyby: SqlUnauthorizedAccess

- **Zpráva** : `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Příčina** : přihlašovací údaje jsou nesprávné nebo přihlašovací účet nemá přístup k SQL Database.

- **Doporučení** : Ověřte, že přihlašovací účet má dostatečná oprávnění pro přístup k SQL Database.


### <a name="error-code--sqlopenconnectiontimeout"></a>Kód chyby: SqlOpenConnectionTimeout

- **Zpráva** : `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Příčina** : SQL Database přechodného selhání.

- **Doporučení** : zkuste aktualizovat připojovací řetězec propojené služby s větší hodnotou časového limitu připojení.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Kód chyby: SqlAutoCreateTableTypeMapFailed

- **Zpráva** : `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Příčina** : tabulka automatického vytváření nemůže splňovat požadavek na zdroj.

- **Doporučení** : Aktualizujte typ sloupce v mapování nebo na cílovém serveru vytvořte tabulku jímky ručně.


### <a name="error-code--sqldatatypenotsupported"></a>Kód chyby: SqlDataTypeNotSupported

- **Zpráva** : `A database operation failed. Check the SQL errors.`

- **Příčina** : Pokud k problému dojde ve zdroji SQL a chyba se týká přetečení Hodnota SqlDateTime, hodnota dat je nad rozsahem typu logic (1/1/1753 12:00:00 dop. 12/31/9999 11:59:59 odp.).

- **Doporučení** : přetypování typu na řetězec ve zdrojovém dotazu SQL nebo v mapování sloupce aktivity kopírování změňte typ sloupce na String.

- **Příčina** : Pokud k problému dojde v jímky SQL a chyba se vztahuje k přetečení Hodnota SqlDateTime, hodnota dat je nad povoleným rozsahem v tabulce jímky.

- **Doporučení** : aktualizujte odpovídající typ sloupce na typ datetime2 v tabulce jímky.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Kód chyby: SqlInvalidDbStoredProcedure

- **Zpráva** : `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Příčina** : zadaná uložená procedura není platná. Příčinou může být to, že uložená procedura nevrací žádná data.

- **Doporučení** : Ověřte uloženou proceduru pomocí nástrojů SQL. Ujistěte se, že uložená procedura může vracet data.


### <a name="error-code--sqlinvaliddbquerystring"></a>Kód chyby: SqlInvalidDbQueryString

- **Zpráva** : `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Příčina** : zadaný dotaz SQL není platný. To může být způsobeno tím, že dotaz nevrátí žádná data.

- **Doporučení** : Ověřte dotaz SQL pomocí nástrojů SQL. Ujistěte se, že dotaz může vracet data.


### <a name="error-code--sqlinvalidcolumnname"></a>Kód chyby: SqlInvalidColumnName

- **Zpráva** : `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Příčina** : sloupec nelze najít. Možná konfigurace je chybná.

- **Doporučení** : Ověřte sloupec v dotazu, "Structure" v datové sadě a "Mappings" v aktivitě.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Kód chyby: SqlColumnNameMismatchByCaseSensitive

- **Zpráva** : `Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Kód chyby: SqlBatchWriteTimeout

- **Zpráva** : `Timeouts in SQL write operation.`

- **Příčina** : SQL Database přechodného selhání.

- **Doporučení** : zkuste to znovu. Pokud se problém reprodukci, obraťte se na podporu Azure SQL.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Kód chyby: SqlBatchWriteTransactionFailed

- **Zpráva** : `SQL transaction commits failed`

- **Příčina** : Pokud detaily výjimky neustále oznamují časový limit transakce, latence sítě mezi integrací modulem runtime a databází je vyšší než výchozí prahová hodnota 30 sekund.

- **Doporučení** : aktualizujte připojovací řetězec propojené služby SQL s hodnotou časový limit připojení se rovná 120 nebo vyšší a znovu spusťte aktivitu.

- **Příčina** : Pokud se v podrobnostech výjimky občas říká SqlConnection, může se jednat o přechodný výpadek sítě nebo problém na straně SQL Database.

- **Doporučení** : zkuste tuto aktivitu znovu a zkontrolujte SQL Database metriky na straně.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Kód chyby: SqlBulkCopyInvalidColumnLength

- **Zpráva** : `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Příčina** : hromadné kopírování SQL se nezdařilo, protože od klienta BCP byla přijata neplatná délka sloupce.

- **Doporučení** : Chcete-li zjistit, který řádek narazí na problém, povolte funkci odolnost proti chybám u aktivity kopírování, která může přesměrovat problematické řádky do úložiště pro další šetření. Referenční doc: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlconnectionisclosed"></a>Kód chyby: SqlConnectionIsClosed

- **Zpráva** : `The connection is closed by SQL Database.`

- **Příčina** : připojení SQL je ukončeno SQL Database při vysokém souběžném spuštění a ukončení připojení serveru.

- **Doporučení** : vzdálený server zavřel připojení SQL. Opakujte. Pokud se problém reprodukci, obraťte se na podporu Azure SQL.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Kód chyby: SqlCreateTableFailedUnsupportedType

- **Zpráva** : `Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Chybová zpráva: převod se nezdařil při převodu ze znakového řetězce na typ uniqueidentifier.

- **Příznaky** : Když zkopírujete data z tabulkového zdroje dat (například SQL Server) do služby Azure synapse Analytics pomocí připravené kopie a základu, zobrazí se následující chyba:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Příčina** : základní databáze Azure synapse Analytics nemůže převést prázdný řetězec na GUID.

- **Řešení** : v jímky aktivity kopírování v části nastavení základní hodnoty nastavte možnost **použít výchozí typ** na hodnotu NEPRAVDA.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Chybová zpráva: očekávaný datový typ: DECIMAL (x, x), problematická hodnota

- **Příznaky** : Když zkopírujete data z tabulkového zdroje dat (například SQL Server) do služby Azure synapse Analytics pomocí připravené kopie a základu, zobrazí se následující chyba:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Příčina** : základ analýzy Azure synapse Analytics nemůže vložit prázdný řetězec (hodnota null) do desetinného sloupce.

- **Řešení** : v jímky aktivity kopírování v části nastavení základní hodnoty nastavte možnost **použít výchozí typ** na hodnotu NEPRAVDA.

### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Chybová zpráva: zpráva o výjimce Java: HdfsBridge:: CreateRecordReader

- **Příznaky** : kopírujete data do služby Azure synapse Analytics pomocí základu dat a zaškrtnete tuto chybu:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Příčina** : možnou příčinou je, že schéma (celková šířka sloupce) je příliš velké (větší než 1 MB). Ověřte schéma cílové tabulky Azure synapse Analytics přidáním velikosti všech sloupců:

    - Int-> 4 bajty
    - Bigint – > 8 bajtů
    - Varchar (n), char (n), Binary (n), varbinary (n)-> n bajtů
    - Nvarchar (n), nchar (n)-> n * 2 bajty
    - Datum-> 6 bajtů
    - DateTime/(2), smalldatetime-> 16 bajtů
    - DateTimeOffset-> 20 bajtů
    - Desítkové – > 19 bajtů
    - Float-> 8 bajtů
    - Peníze – > 8 bajtů
    - Smallmoney-> 4 bajty
    - Reálné > 4 bajty
    - Smallint – > 2 bajty
    - Čas – > 12 bajtů
    - Tinyint-> 1 bajt

- **Řešení** : Zmenšete šířku sloupce tak, aby byla menší než 1 MB.

- Nebo použijte přístup k hromadnému vložení zakázáním základny

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Chybová zpráva: podmínka zadaná pomocí podmíněných hlaviček HTTP není splněna.

- **Příznaky** : k vyžádání dat ze služby Azure synapse Analytics použijete dotaz SQL a zobrazí se tato chyba:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Příčina** : při dotazování na externí tabulku v Azure Storage došlo k potížím se službou Azure synapse Analytics.

- **Řešení** : Spusťte stejný dotaz v SSMS a zkontrolujte, jestli vidíte stejný výsledek. Pokud ano, otevřete lístek podpory pro Azure synapse Analytics a poskytněte vašemu serveru Azure synapse Analytics Server a název databáze, abyste mohli dále řešit problémy.
            

## <a name="delimited-text-format"></a>Textový formát s oddělovači

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Kód chyby: DelimitedTextColumnNameNotAllowNull

- **Zpráva** : `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Příčina** : když se v aktivitě nastaví ' firstRowAsHeader ', jako název sloupce se použije první řádek. Tato chyba znamená, že první řádek obsahuje prázdnou hodnotu. Například: "Columns,; ColumnB".

- **Doporučení** : Ověřte první řádek a opravte hodnotu, pokud existuje prázdná hodnota.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Kód chyby: DelimitedTextMoreColumnsThanDefined

- **Zpráva** : `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Příčina** : problematický počet sloupců řádku je větší než počet sloupců prvního řádku. Může to být způsobeno problémem s daty nebo nesprávným nastavením oddělovače sloupců nebo uvozovek.

- **Doporučení** : Získá počet řádků v chybové zprávě, zkontroluje sloupec řádku a opraví data.

- **Příčina** : Pokud je v chybové zprávě očekávaný počet sloupců "1", je možné, že jste zadali nesprávná nastavení komprese nebo formátu, což způsobilo, že ADF nesprávně analyzuje soubory.

- **Doporučení** : Zkontrolujte nastavení formátu a ujistěte se, že se shoduje se zdrojovými soubory.

- **Příčina** : Pokud je zdrojem složka, je možné, že soubory v zadané složce mají jiné schéma.

- **Doporučení** : Ujistěte se, že soubory v dané složce mají stejné schéma.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Kód chyby: DelimitedTextIncorrectRowDelimiter

- **Zpráva** : `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Kód chyby: DelimitedTextTooLargeColumnCount

- **Zpráva** : `Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Kód chyby: DelimitedTextInvalidSettings

- **Zpráva** : `%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Kód chyby: DynamicsCreateServiceClientError

- **Zpráva** : `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Příčina** : Jedná se o přechodný problém na straně Dynamics serveru.

- **Doporučení** : Spusťte kanál znovu. Pokud budete pokračovat, zkuste omezit paralelismus. Pokud pořád selže, obraťte se prosím na podporu Dynamics.



## <a name="json-format"></a>Formát JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Kód chyby: JsonInvalidArrayPathDefinition

- **Zpráva** : `Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Kód chyby: JsonEmptyJObjectData

- **Zpráva** : `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Kód chyby: JsonNullValueInPathDefinition

- **Zpráva** : `Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Kód chyby: JsonUnsupportedHierarchicalComplexValue

- **Zpráva** : `The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Kód chyby: JsonConflictPartitionDiscoverySchema

- **Zpráva** : `Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Kód chyby: JsonInvalidDataFormat

- **Zpráva** : `Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Kód chyby: JsonInvalidDataMixedArrayAndObject

- **Zpráva** : `Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Formát Parquet

### <a name="error-code--parquetjavainvocationexception"></a>Kód chyby: ParquetJavaInvocationException

- **Zpráva** : `An error occurred when invoking java, message: %javaException;.`

- **Příčina** : Pokud chybová zpráva obsahuje "Java. lang. OutOfMemory", "prostor haldy jazyka Java" a "doubleCapacity", obvykle se jedná o problém se správou paměti ve staré verzi prostředí Integration runtime.

- **Doporučení** : pokud používáte Integration runtime v místním prostředí a verze je starší než 3.20.7159.1, navrhuje se upgradovat na nejnovější verzi.

- **Příčina** : Pokud chybová zpráva obsahuje "Java. lang. OutOfMemory", modul runtime integrace nemá dostatek prostředků ke zpracování těchto souborů.

- **Doporučení** : Omezte souběžné běhy prostředí Integration runtime. Pro Integration Runtime v místním prostředí, Škálujte až do výkonného počítače s pamětí rovnou nebo větší než 8 GB.

- **Příčina** : Pokud chybová zpráva obsahuje ' NullPointerReference ', je možné, že se jedná o přechodnou chybu.

- **Doporučení** : zkuste to znovu. Pokud se problém opakuje, obraťte se prosím na podporu.


### <a name="error-code--parquetinvalidfile"></a>Kód chyby: ParquetInvalidFile

- **Zpráva** : `File is not a valid parquet file.`

- **Příčina** : problém Parquet souboru.

- **Doporučení** : Ověřte, zda je vstup platným souborem Parquet.


### <a name="error-code--parquetnotsupportedtype"></a>Kód chyby: ParquetNotSupportedType

- **Zpráva** : `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Příčina** : formát Parquet není v Azure Data Factory podporován.

- **Doporučení** : poklikejte na zdrojová data. Podívejte se na dokument: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Kód chyby: ParquetMissedDecimalPrecisionScale

- **Zpráva** : `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Příčina** : zkuste analyzovat přesnost čísel a škálování, ale nejsou k dispozici žádné informace.

- **Doporučení** : ' source ' nevrací správnou přesnost a škálování. Podívejte se na přesnost a měřítko sloupce problému.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Kód chyby: ParquetInvalidDecimalPrecisionScale

- **Zpráva** : `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Příčina** : schéma je neplatné.

- **Doporučení** : Ověřte přesnost a měřítko sloupce problému.


### <a name="error-code--parquetcolumnnotfound"></a>Kód chyby: ParquetColumnNotFound

- **Zpráva** : `Column %column; does not exist in Parquet file.`

- **Příčina** : zdrojové schéma se neshoduje se schématem jímky.

- **Doporučení** : Ověřte the'mappings v aktivitě. Ujistěte se, že zdrojový sloupec lze namapovat do pravého sloupce jímky.


### <a name="error-code--parquetinvaliddataformat"></a>Kód chyby: ParquetInvalidDataFormat

- **Zpráva** : `Incorrect format of %srcValue; for converting to %dstType;.`

- **Příčina** : data nejde převést na typ zadaný v mapování. Source.

- **Doporučení** : poklikejte na zdrojová data nebo zadejte správný datový typ pro tento sloupec v mapování sloupců aktivity kopírování. Podívejte se na dokument: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Kód chyby: ParquetDataCountNotMatchColumnCount

- **Zpráva** : `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Příčina** : Neshoda počtu zdrojových sloupců a sloupců jímky

- **Doporučení** : typ zdrojového sloupce dvojité kontroly je stejný jako počet sloupců jímky v mapování.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Kód chyby: ParquetDataTypeNotMatchColumnType

- **Zpráva** : datový typ% srcType; se neshoduje s daným typem sloupce% dstType;. ve sloupci% columnIndex;.

- **Příčina** : data ze zdroje nejde převést na typované definované v jímky.

- **Doporučení** : Zadejte správný typ v mapování. jímka.


### <a name="error-code--parquetbridgeinvaliddata"></a>Kód chyby: ParquetBridgeInvalidData

- **Zpráva** : `%message;`

- **Příčina** : hodnota dat nad omezením

- **Doporučení** : zkuste to znovu. Pokud se problém opakuje, kontaktujte nás prosím.


### <a name="error-code--parquetunsupportedinterpretation"></a>Kód chyby: ParquetUnsupportedInterpretation

- **Zpráva** : `The given interpretation '%interpretation;' of parquet format is not supported.`

- **Příčina** : nepodporováný scénář

- **Doporučení** : ' ParquetInterpretFor ' by neměl být ' sparkSql '.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Kód chyby: ParquetUnsupportFileLevelCompressionOption

- **Zpráva** : `File level compression is not supported for Parquet.`

- **Příčina** : nepodporováný scénář

- **Doporučení** : Odeberte ' Argument ' v datové části.



## <a name="general-copy-activity-error"></a>Obecná chyba aktivity kopírování

### <a name="error-code--jrenotfound"></a>Kód chyby: JreNotFound

- **Zpráva** : `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Příčina** : modul runtime integrace v místním prostředí nemůže najít modul runtime Java. Modul Java Runtime je vyžadován pro čtení konkrétního zdroje.

- **Doporučení** : Podívejte se do prostředí Integration runtime, referenční dokument: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Kód chyby: WildcardPathSinkNotSupported

- **Zpráva** : `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Příčina** : datová sada jímky nepodporuje zástupný znak.

- **Doporučení** : Ověřte datovou sadu jímky a opravte cestu bez zástupných hodnot.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Kód chyby: MappingInvalidPropertyWithEmptyValue

- **Zpráva** : `One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Kód chyby: MappingInvalidPropertyWithNamePathAndOrdinal

- **Zpráva** : `Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Kód chyby: MappingDuplicatedOrdinal

- **Zpráva** : `Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Kód chyby: MappingInvalidOrdinalForSinkColumn

- **Zpráva** : `Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa k Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka s otázkou Microsoft Q&](/answers/topics/azure-data-factory.html)
*  [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
