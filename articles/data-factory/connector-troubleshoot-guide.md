---
title: Řešení potíží s konektory služby Azure Data Factory
description: Naučte se řešit potíže s konektorem v Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/08/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 574c4967c1e45ce1ae2be92d8648d654322e2244
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727817"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Řešení potíží s konektory služby Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné způsoby řešení potíží s Azure Data Factory konektory.

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>Kód chyby: AzureBlobOperationFailed

- **Zpráva**: operace BLOB se nezdařila. ContainerName:% Container;, cesta:% Path;.

- **Příčina**: problém s operací BLOB Storage.

- **Doporučení**: Pokud chcete zjistit podrobnosti o chybě, přečtěte si téma [kódy chyb BLOB Storage](/rest/api/storageservices/blob-service-error-codes). Chcete-li získat další pomoc, obraťte se na tým Blob Storage.


### <a name="invalid-property-during-copy-activity"></a>Neplatná vlastnost během aktivity kopírování

- **Zpráva**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Příčina**: typ definovaný v datové sadě je nekonzistentní s typem zdroje nebo jímky, který je definován v aktivitě kopírování.

- **Řešení**: Upravte definici JSON datové sady nebo kanálu a zajistěte konzistentní typy a pak znovu spusťte nasazení.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Chybová zpráva: velikost požadavku je příliš velká.

- **Příznaky**: při kopírování dat do Azure Cosmos DB s výchozí velikostí dávky zápisu, se zobrazí následující chyba: `Request size is too large.`

- **Příčina**: Azure Cosmos DB omezuje velikost jednoho požadavku na 2 MB. Vzorec je *Velikost požadavku = jedna velikost dokumentu \* zapsat velikost dávky*. Pokud je velikost dokumentu velká, výchozí chování bude mít za následek příliš velkou velikost požadavku. Můžete vyladit velikost dávky zápisu.

- **Řešení**: v jímky aktivity kopírování snižte hodnotu *velikost dávky zápisu* (výchozí hodnota je 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Chybová zpráva: porušení omezení jedinečného indexu

- **Příznaky**: při kopírování dat do Azure Cosmos DB se zobrazí následující chyba:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Příčina**: Existují dvě možné příčiny:

    - Příčina 1: Pokud použijete funkci **Vložit** jako způsob zápisu, tato chyba znamená, že vaše zdrojová data obsahují řádky nebo objekty se stejným ID.
    - Příčina 2: Pokud použijete **Upsert** jako chování při zápisu a nastavíte do kontejneru jiný jedinečný klíč, tato chyba znamená, že vaše zdrojová data obsahují řádky nebo objekty s různými identifikátory, ale stejnou hodnotu pro definovaný jedinečný klíč.

- **Řešení**: 

    - V případě příčiny 1 nastavte **Upsert** jako chování při zápisu.
    - V případě příčiny 2 se ujistěte, že každý dokument má jinou hodnotu pro definovaný jedinečný klíč.

### <a name="error-message-request-rate-is-large"></a>Chybová zpráva: frekvence požadavků je velká

- **Příznaky**: při kopírování dat do Azure Cosmos DB se zobrazí následující chyba:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Příčina**: počet použitých jednotek žádostí (ru) je větší než dostupné ru nakonfigurované v Azure Cosmos DB. Pokud chcete zjistit, jak Azure Cosmos DB vypočítá ru, přečtěte si téma [jednotky žádosti v Azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations).

- **Řešení**: Vyzkoušejte jedno z následujících dvou řešení:

    - Zvyšte číslo *ru kontejneru* na větší hodnotu v Azure Cosmos DB. Toto řešení vylepší výkon aktivity kopírování, ale v Azure Cosmos DB bude účtováno větší náklady. 
    - Snižte *writeBatchSize* na menší hodnotu, třeba 1000, a snižte *parallelCopies* na menší hodnotu, třeba 1. Toto řešení sníží výkon při kopírování, ale v Azure Cosmos DB se neúčtují více nákladů.

### <a name="columns-missing-in-column-mapping"></a>V mapování sloupce chybí sloupce.

- **Příznaky**: Když importujete schéma pro Azure Cosmos DB mapování sloupce, chybí některé sloupce. 

- **Příčina**: Data Factory odvodí schéma z prvních 10 Azure Cosmos DB dokumentů. Pokud některé sloupce nebo vlastnosti dokumentu neobsahují hodnoty, schéma se nerozpozná Data Factory a následně se nezobrazí.

- **Řešení**: můžete ladit dotaz, jak je znázorněno v následujícím kódu, abyste vynutili zobrazení hodnot sloupců v sadě výsledků s prázdnými hodnotami. Předpokládá, že v prvních deseti dokumentech chybí sloupec *nemožné* . Případně můžete ručně přidat sloupec pro mapování.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Chybová zpráva: GuidRepresentation pro čtecí zařízení je CSharpLegacy

- **Příznaky**: při kopírování dat z Azure Cosmos DB MongoAPI nebo MongoDB s polem s univerzálním jedinečným identifikátorem (UUID) se zobrazí následující chyba:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Příčina**: Existují dva způsoby reprezentace identifikátoru UUID v binárním formátu JSON (bson): UuidStardard a UuidLegacy. Ve výchozím nastavení se pro čtení dat používá UuidLegacy. Pokud jsou data identifikátoru UUID v MongoDB UuidStandard, zobrazí se chyba.

- **Řešení**: v připojovacím řetězci MongoDB přidejte možnost *uuidRepresentation = Standard* . Další informace najdete v tématu [připojovací řetězec MongoDB](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>Kód chyby: CosmosDbSqlApiOperationFailed

- **Zpráva**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Příčina**: problém s operací CosmosDbSqlApi.

- **Doporučení**: Pokud chcete zjistit podrobnosti o chybě, přečtěte si téma [Azure Cosmos DB Help Document](../cosmos-db/troubleshoot-dot-net-sdk.md). Chcete-li získat další pomoc, obraťte se na tým Azure Cosmos DB.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Chybová zpráva: základní připojení bylo ukončeno: nelze vytvořit vztah důvěryhodnosti pro zabezpečený kanál SSL/TLS.

- **Příznaky**: aktivita kopírování se nezdařila s následující chybou: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Příčina**: ověření certifikátu během metody handshake TLS selhalo.

- **Řešení**: jako alternativní řešení můžete použít dvoufázové kopírování k přeskočení ověřování TLS (Transport Layer Security) pro Azure Data Lake Storage Gen1. Je nutné reprodukování tohoto problému a shromáždění trasování sítě (Netmon) a následného zapojení svého síťového týmu, aby zkontroloval konfiguraci místní sítě.

    ![Diagram připojení Azure Data Lake Storage Gen1 pro řešení problémů](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Chybová zpráva: vzdálený server vrátil chybu: (403) zakázáno

- **Příznaky**: aktivita kopírování se nezdařila s následující chybou: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Příčina**: jedinou možnou příčinou je, že instanční objekt nebo spravovaná identita nemají oprávnění pro přístup k určitým složkám nebo souborům.

- **Řešení**: Udělte patřičným oprávněním všechny složky a podsložky, které potřebujete zkopírovat. Další informace najdete v tématu [kopírování dat do nebo z Azure Data Lake Storage Gen1 pomocí Azure Data Factory](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Chybová zpráva: Nepodařilo se získat přístupový token pomocí instančního objektu. Chyba ADAL: service_unavailable

- **Příznaky**: aktivita kopírování se nezdařila s následující chybou:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Příčina**: když není k dispozici server tokenu služby (STS), který je vlastněn Azure Active Directory, znamená to, že je moc zaneprázdněný pro zpracování požadavků a vrátí chybu HTTP 503. 

- **Řešení**: po několika minutách znovu spusťte aktivitu kopírování.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Kód chyby: ADLSGen2OperationFailed

- **Zpráva**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Příčiny a doporučení**: k této chybě může dojít v různých příčinách. Níže uvedený seznam vám umožní analyzovat možnou příčinu a související doporučení.

  | Příčina analýzy                                               | Doporučení                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Pokud Azure Data Lake Storage Gen2 vyvolá chybu oznamující selhání některé operace.| Podívejte se na podrobnou chybovou zprávu vyvolanou Azure Data Lake Storage Gen2. Pokud se jedná o přechodnou chybu, zkuste operaci zopakovat. Pokud chcete získat další pomoc, obraťte se na podporu Azure Storage podpory a zadejte ID žádosti v chybové zprávě. |
  | Pokud chybová zpráva obsahuje řetězec "zakázáno", instanční objekt nebo spravovaná identita pravděpodobně nemají dostatečná oprávnění pro přístup k Azure Data Lake Storage Gen2. | Pokud chcete tuto chybu vyřešit, přečtěte si téma [kopírování a transformace dat v Azure Data Lake Storage Gen2 pomocí Azure Data Factory](./connector-azure-data-lake-storage.md#service-principal-authentication). |
  | Pokud chybová zpráva obsahuje řetězec "Nenalezeno", je vrácena chyba Azure Data Lake Storage Gen2. | Chyba může být způsobena přechodným selháním. Pokud není, zkuste operaci zopakovat. Pokud se problém nevyřeší, obraťte se na podporu Azure Storage a zadejte ID žádosti z chybové zprávy. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Požadavek na Azure Data Lake Storage Gen2 účtu způsobil chybu časového limitu.

- **Zpráva**: 
  * Kód chyby = `UserErrorFailedBlobFSOperation`
  * Chybová zpráva = `BlobFS operation failed for: A task was canceled.`

- **Příčina**: problém je způsoben chybou vypršení časového limitu jímky Azure Data Lake Storage Gen2, ke kterému obvykle dochází v počítači s místním hostováním Integration runtime (IR).

- **Doporučení**: 

    - Pokud je to možné, umístěte svůj místně hostovaný počítač IR a cílový účet Azure Data Lake Storage Gen2 ve stejné oblasti. To může zabránit náhodnému vypršení časového limitu a dosažení lepšího výkonu.

    - Zkontrolujte, jestli existuje zvláštní nastavení sítě, třeba ExpressRoute, a ujistěte se, že má síť dostatečnou šířku pásma. Doporučujeme, abyste v případě nízké šířky pásma snížili nastavení místních souběžných úloh IR na sebe. Díky tomu se můžete vyhnout konkurenci síťových prostředků napříč několika souběžnými úlohami.

    - Pokud je velikost souboru střední nebo malý, použijte menší velikost bloku pro nebinární kopírování, abyste mohli zmírnit tuto chybu časového limitu. Další informace najdete v tématu [BLOB Storage blok vložení](/rest/api/storageservices/put-block).

       Chcete-li určit velikost vlastního bloku, upravte vlastnost v editoru souborů JSON, jak je znázorněno zde:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Úložiště souborů Azure

### <a name="error-code-azurefileoperationfailed"></a>Kód chyby: AzureFileOperationFailed

- **Zpráva**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Příčina**: problém s operací úložiště Azure Files.

- **Doporučení**: informace o chybě najdete v nápovědě k [souborům Azure](/rest/api/storageservices/file-service-error-codes). Pokud chcete získat další pomoc, obraťte se na tým souborů Azure.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure synapse Analytics, Azure SQL Database a SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>Kód chyby: SqlFailedToConnect

- **Zpráva**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Příčiny a doporučení**: k této chybě může dojít v různých příčinách. Níže uvedený seznam vám umožní analyzovat možnou příčinu a související doporučení.

    | Příčina analýzy                                               | Doporučení                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | V případě Azure SQL, pokud chybová zpráva obsahuje řetězec "SqlErrorNumber = 47073", znamená to, že přístup k veřejné síti byl odepřen v nastavení připojení. | V bráně Azure SQL firewall nastavte možnost **Odepřít přístup k veřejné síti** na *ne*. Další informace najdete v tématu [nastavení připojení Azure SQL](../azure-sql/database/connectivity-settings.md#deny-public-network-access). |
    | V případě Azure SQL, pokud chybová zpráva obsahuje kód chyby SQL, například "SqlErrorNumber = [ErrorCode]", přečtěte si příručku k odstraňování potíží se službou Azure SQL. | Doporučení najdete v tématu [řešení potíží s připojením a dalších chyb pomocí Azure SQL Database a spravované instance Azure SQL](../azure-sql/database/troubleshoot-common-errors-issues.md). |
    | Zkontrolujte, jestli je na seznamu povolených bran firewall port 1433. | Další informace najdete v tématu [porty používané v SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-). |
    | Pokud chybová zpráva obsahuje řetězec "SqlException", SQL Database Chyba indikuje, že některé konkrétní operace selhaly. | Další informace najdete v kódu chyby SQL v [databázovém stroji](/sql/relational-databases/errors-events/database-engine-events-and-errors). Pokud chcete získat další pomoc, obraťte se na podporu Azure SQL. |
    | Pokud se jedná o přechodný problém (například o nestabilním síťovém připojení), přidejte do zásady aktivity opakovat, aby se zmírnil. | Další informace najdete v tématu [kanály a aktivity v Azure Data Factory](./concepts-pipelines-activities.md#activity-policy). |
    | Pokud chybová zpráva obsahuje řetězec "klient s IP adresou"... nemá povolený přístup k serveru a pokoušíte se o připojení k Azure SQL Database. příčinou této chyby je obvykle problém Azure SQL Database brány firewall. | V konfiguraci brány firewall Azure SQL Server povolte možnost **Povolit službám a prostředkům Azure přístup k tomuto serveru** . Další informace najdete v článku [pravidla brány firewall pro Azure SQL Database a Azure synapse](../azure-sql/database/firewall-configure.md). |
    
### <a name="error-code-sqloperationfailed"></a>Kód chyby: SqlOperationFailed

- **Zpráva**: `A database operation failed. Please search error to get more details.`

- **Příčiny a doporučení**: k této chybě může dojít v různých příčinách. Níže uvedený seznam vám umožní analyzovat možnou příčinu a související doporučení.

    | Příčina analýzy                                               | Doporučení                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Pokud chybová zpráva obsahuje řetězec "SqlException", SQL Database vyvolá chybu oznamující, že některá konkrétní operace se nezdařila. | Pokud není chyba SQL jasné, zkuste změnit databázi na nejnovější úroveň kompatibility 150. Může vyvolat nejnovější verzi chyb SQL. Další informace najdete v [dokumentaci](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat). <br/> Další informace o řešení problémů s SQL najdete v kódu chyby SQL v [databázovém stroji](/sql/relational-databases/errors-events/database-engine-events-and-errors). Pokud chcete získat další pomoc, obraťte se na podporu Azure SQL. |
    | Pokud chybová zpráva obsahuje řetězec "PdwManagedToNativeInteropException", je obvykle způsobena neshodou mezi velikostí zdroje a sloupce jímky. | Ověřte velikost zdroje i sloupce jímky. Pokud chcete získat další pomoc, obraťte se na podporu Azure SQL. |
    | Pokud chybová zpráva obsahuje řetězec "InvalidOperationException", je obvykle způsobena neplatnými vstupními daty. | Chcete-li zjistit, na kterém řádku došlo k problému, povolte u aktivity kopírování funkci odolnost proti chybám, která může přesměrovat problematické řádky do úložiště pro další šetření. Další informace najdete v tématu odolnost [proti chybám aktivity kopírování v Azure Data Factory](./copy-activity-fault-tolerance.md). |


### <a name="error-code-sqlunauthorizedaccess"></a>Kód chyby: SqlUnauthorizedAccess

- **Zpráva**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Příčina**: přihlašovací údaje jsou nesprávné nebo přihlašovací účet nemá přístup k databázi SQL.

- **Doporučení**: Ověřte, že přihlašovací účet má dostatečná oprávnění pro přístup k databázi SQL.


### <a name="error-code-sqlopenconnectiontimeout"></a>Kód chyby: SqlOpenConnectionTimeout

- **Zpráva**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Příčina**: problémem může být přechodná Chyba databáze SQL.

- **Doporučení**: zkuste operaci zopakovat, aby se v připojovacím řetězci propojené služby aktualizovala větší hodnota časového limitu připojení.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>Kód chyby: SqlAutoCreateTableTypeMapFailed

- **Zpráva**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Příčina**: tabulka pro vytváření dat nemůže splňovat požadavek na zdroj.

- **Doporučení**: Aktualizujte typ sloupce v *mapování* nebo ručně vytvořte tabulku jímky na cílovém serveru.


### <a name="error-code-sqldatatypenotsupported"></a>Kód chyby: SqlDataTypeNotSupported

- **Zpráva**: `A database operation failed. Check the SQL errors.`

- **Příčina**: Pokud k problému dochází ve zdroji SQL a chyba se vztahuje k přetečení Hodnota SqlDateTime, hodnota dat překračuje rozsah typu logice (1/1/1753 12:00:00 dop. 12/31/9999 11:59:59 odp.).

- **Doporučení**: přetypování typu na řetězec ve zdrojovém dotazu SQL nebo v mapování sloupce aktivity kopírování změňte typ sloupce na *String*.

- **Příčina**: Pokud k problému dojde v jímky SQL a chyba se vztahuje k přetečení Hodnota SqlDateTime, hodnota dat překračuje povolený rozsah v tabulce jímky.

- **Doporučení**: aktualizujte odpovídající typ sloupce na typ *datetime2* v tabulce jímky.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>Kód chyby: SqlInvalidDbStoredProcedure

- **Zpráva**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Příčina**: zadaná uložená procedura je neplatná. Příčinou může být to, že uložená procedura nevrátí žádná data.

- **Doporučení**: Ověřte uloženou proceduru pomocí nástrojů SQL. Ujistěte se, že uložená procedura může vracet data.


### <a name="error-code-sqlinvaliddbquerystring"></a>Kód chyby: SqlInvalidDbQueryString

- **Zpráva**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Příčina**: zadaný dotaz SQL je neplatný. Příčinou může být to, že dotaz nevrátí žádná data.

- **Doporučení**: Ověřte dotaz SQL pomocí nástrojů SQL. Ujistěte se, že dotaz může vracet data.


### <a name="error-code-sqlinvalidcolumnname"></a>Kód chyby: SqlInvalidColumnName

- **Zpráva**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Příčina**: sloupec nelze nalézt, protože konfigurace je pravděpodobně nesprávná.

- **Doporučení**: Ověřte sloupec v dotazu, *strukturu* v datové sadě a *mapování* v aktivitě.


### <a name="error-code-sqlbatchwritetimeout"></a>Kód chyby: SqlBatchWriteTimeout

- **Zpráva**: `Timeouts in SQL write operation.`

- **Příčina**: problém může být způsoben přechodným selháním databáze SQL.

- **Doporučení**: zkuste operaci zopakovat. Pokud se problém opakuje, obraťte se na podporu Azure SQL.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>Kód chyby: SqlBatchWriteTransactionFailed

- **Zpráva**: `SQL transaction commits failed.`

- **Příčina**: Pokud se v podrobnostech výjimky trvale označují časový limit transakce, latence sítě mezi integrací prostředí Integration runtime a databází je větší než výchozí prahová hodnota 30 sekund.

- **Doporučení**: aktualizujte připojovací řetězec propojené služby SQL s hodnotou *časového limitu připojení* , která je větší nebo rovna hodnotě 120, a znovu spusťte aktivitu.

- **Příčina**: Pokud informace o výjimce poobčasně signalizují, že je připojení SQL přerušeno, může jít o přechodný výpadek sítě nebo o problém na straně databáze SQL.

- **Doporučení**: zkuste tuto aktivitu znovu a zkontrolujte metriky na straně databáze SQL.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Kód chyby: SqlBulkCopyInvalidColumnLength

- **Zpráva**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Příčina**: hromadné kopírování SQL se nezdařilo, protože obdržela neplatnou délku sloupce z klienta programu pro program hromadného kopírování (BCP).

- **Doporučení**: Chcete-li zjistit, na který řádek došlo k problému, povolte u aktivity kopírování funkci odolnost proti chybám. To může přesměrovat problematické řádky do úložiště pro další šetření. Další informace najdete v tématu odolnost [proti chybám aktivity kopírování v Azure Data Factory](./copy-activity-fault-tolerance.md).


### <a name="error-code-sqlconnectionisclosed"></a>Kód chyby: SqlConnectionIsClosed

- **Zpráva**: `The connection is closed by SQL Database.`

- **Příčina**: připojení SQL je zavřené databází SQL v případě vysokého souběžného běhu a serveru ukončí připojení.

- **Doporučení**: zkuste připojení znovu. Pokud se problém opakuje, obraťte se na podporu Azure SQL.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Chybová zpráva: převod se nezdařil při převodu ze znakového řetězce na typ uniqueidentifier.

- **Příznaky**: Když zkopírujete data z tabulkového zdroje dat (například SQL Server) do služby Azure synapse Analytics pomocí připravené kopie a základu, zobrazí se následující chyba:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Příčina**: základ služby Azure synapse Analytics nemůže převést prázdný řetězec na identifikátor GUID.

- **Řešení**: v jímky aktivity kopírování v části nastavení základní hodnoty nastavte možnost **použít typ výchozí** na *hodnotu NEPRAVDA*.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Chybová zpráva: očekávaný datový typ: DECIMAL (x, x), problematická hodnota

- **Příznaky**: Když zkopírujete data z tabulkového zdroje dat (například SQL Server) do služby Azure synapse Analytics pomocí připraveného kopírování a základu, zobrazí se následující chyba:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Příčina**: základ služby Azure synapse Analytics nemůže vložit prázdný řetězec (hodnota null) do desetinného sloupce.

- **Řešení**: v jímky aktivity kopírování v části nastavení základní hodnoty nastavte možnost **použít typ výchozí** na hodnotu NEPRAVDA.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Chybová zpráva: zpráva o výjimce Java: HdfsBridge:: CreateRecordReader

- **Příznaky**: ke kopírování dat do služby Azure synapse Analytics použijte základnu a získáte tuto chybu:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Příčina**: příčinou může být to, že schéma (celková šířka sloupce) je příliš velké (větší než 1 MB). Ověřte schéma cílové tabulky Azure synapse Analytics přidáním velikosti všech sloupců:

    - Int = 4 bajty
    - Bigint = 8 bajtů
    - Varchar (n), char (n), Binary (n), varbinary (n) = n bajtů
    - Nvarchar (n), nchar (n) = n * 2 bajty
    - Datum = 6 bajtů
    - DateTime/(2), smalldatetime = 16 bajtů
    - DateTimeOffset = 20 bajtů
    - Decimal = 19 bajtů
    - Float = 8 bajtů
    - Peníze = 8 bajtů
    - Smallmoney = 4 bajty
    - Reálné = 4 bajty
    - Smallint = 2 bajty
    - Čas = 12 bajtů
    - Tinyint = 1 bajt

- **Řešení**: 
    - Zmenšete šířku sloupce na méně než 1 MB.
    - Nebo použijte přístup k hromadnému vložení zakázáním základny.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Chybová zpráva: podmínka zadaná pomocí podmíněných hlaviček HTTP není splněna.

- **Příznaky**: k vyžádání dat ze služby Azure synapse Analytics použijte dotaz SQL a obdržíte následující chybu:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Příčina**: v Azure synapse Analytics došlo k problému při dotazování na externí tabulku v Azure Storage.

- **Řešení**: Spusťte stejný dotaz v SQL Server Management Studio (SSMS) a zkontrolujte, jestli se vám vrátí stejný výsledek. Pokud tak učiníte, otevřete lístek podpory ve službě Azure synapse Analytics a zadejte svůj název databáze Azure synapse Analytics.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Úroveň výkonu je nízká a vede k selhání kopírování

- **Příznaky**: kopírujete data do Azure SQL Database a zobrazí se následující chyba: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Příčina**: Azure SQL Database S1 dosáhl omezení vstupu/výstupu (v/v).

- **Řešení**: Pokud chcete problém vyřešit, upgradujte Azure SQL Database úroveň výkonu. 


### <a name="sql-table-cant-be-found"></a>Tabulka SQL se nenašla. 

- **Příznaky**: kopírujete data z hybridu do místní SQL Server tabulky a zobrazí se následující chyba:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Příčina**: aktuální účet SQL nemá dostatečná oprávnění ke spouštění požadavků vydaných .NET SqlBulkCopy. WriteToServer.

- **Řešení**: přepněte na PRIVILEGOVANÝ účet SQL.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Chybová zpráva: řetězcová nebo binární data jsou zkrácena

- **Příznaky**: dojde k chybě při kopírování dat do místní tabulky Azure SQL Server. 

- **Příčina**: definice schématu tabulky SQL pro CX má jeden nebo více sloupců s menší délkou, než se očekávalo.

- **Řešení**: Chcete-li tento problém vyřešit, postupujte takto:

    1. Pokud chcete řešit, které řádky obsahují daný problém, použijte odolnost [proti chybám](./copy-activity-fault-tolerance.md)jímky SQL, obzvláště "redirectIncompatibleRowSettings".

        > [!NOTE]
        > Odolnost proti chybám může vyžadovat další dobu spuštění, což by mohlo vést k vyšším nákladům.

    2. Přesměrovaná data na délku sloupce schématu tabulky SQL vám umožní zjistit, které sloupce je potřeba aktualizovat.

    3. Aktualizujte schéma tabulky odpovídajícím způsobem.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Kód chyby: AzureTableDuplicateColumnsFromSource

- **Zpráva**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Příčina**: duplicitní zdrojové sloupce mohou nastat z některého z následujících důvodů:
   * Databázi používáte jako zdroj a použili jste spojení s tabulkami.
   * V řádku záhlaví máte nestrukturované soubory CSV s duplicitními názvy sloupců.

- **Doporučení**: v případě potřeby pokontrolujte a opravte zdrojové sloupce.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>Kód chyby: DB2DriverRunFailed

- **Zpráva**: `Error thrown from driver. Sql code: '%code;'`

- **Příčina**: Pokud chybová zpráva obsahuje řetězec "SQLSTATE = 51002 SQLCODE =-805", postupujte podle popisu v části [kopírování dat z DB2 pomocí Azure Data Factory](./connector-db2.md#linked-service-properties).

- **Doporučení**: zkuste nastavit "NULLID" ve `packageCollection`  Vlastnosti.


## <a name="delimited-text-format"></a>Formát textu s oddělovači

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Kód chyby: DelimitedTextColumnNameNotAllowNull

- **Zpráva**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Příčina**: Pokud je v aktivitě nastavená možnost ' firstRowAsHeader ', jako název sloupce se použije první řádek. Tato chyba znamená, že první řádek obsahuje prázdnou hodnotu (například Column a ColumnB ').

- **Doporučení**: Ověřte první řádek a opravte hodnotu, pokud je prázdná.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Kód chyby: DelimitedTextMoreColumnsThanDefined

- **Zpráva**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Příčiny a doporučení**: k této chybě může dojít v různých příčinách. Níže uvedený seznam vám umožní analyzovat možnou příčinu a související doporučení.

  | Příčina analýzy                                               | Doporučení                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Počet sloupců problematického řádku je větší než počet sloupců prvního řádku. Může to být způsobeno problémem s daty nebo nesprávným oddělovačem sloupců nebo nastavením znaků uvozovek. | Získá počet řádků z chybové zprávy, zkontroluje sloupec řádku a opraví data. |
  | Pokud je očekávaný počet sloupců "1" v chybové zprávě, možná jste zadali špatné nastavení komprese nebo formátu, což způsobilo, že Data Factory analyzovat soubory nesprávně. | Zkontrolujte nastavení formátu a ujistěte se, že se shodují se zdrojovými soubory. |
  | Pokud je zdrojem složka, můžou mít soubory v zadané složce jiné schéma. | Ujistěte se, že soubory v zadané složce mají stejné schéma. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service a Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>Kód chyby: DynamicsCreateServiceClientError

- **Zpráva**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Příčina**: problém je přechodný problém na straně Dynamics serveru.

- **Doporučení**: Spusťte kanál znovu. Pokud se znovu nepovede, zkuste omezit paralelismus. Pokud potíže potrvají, obraťte se na podporu Dynamics.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Chybějící sloupce při importu schématu nebo náhledu dat

- **Příznaky**: při importu dat schématu nebo náhledu chybí některé sloupce. Chybová zpráva: `The valid structure information (column name and type) are required for Dynamics source.`

- **Příčina**: Tento problém je záměrné, protože Data Factory nedokáže zobrazit sloupce, které v prvních deseti záznamech neobsahují žádné hodnoty. Ujistěte se, že jsou sloupce, které jste přidali, ve správném formátu. 

- **Doporučení**: ručně přidejte sloupce na kartě mapování.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Kód chyby: DynamicsMissingTargetForMultiTargetLookupField

- **Zpráva**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Příčina**: cílový sloupec neexistuje ve zdroji nebo v mapování sloupce.

- **Doporučení**:  
  1. Ujistěte se, že zdroj obsahuje cílový sloupec. 
  2. Přidejte cílový sloupec do mapování sloupce. Zajistěte, aby byl sloupec jímky ve formátu *{FieldName @EntityReference }*.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Kód chyby: DynamicsInvalidTargetForMultiTargetLookupField

- **Zpráva**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Příčina**: nesprávný název entity je zadán jako Cílová entita vyhledávacího pole s více cíli.

- **Doporučení**: Zadejte platný název entity pro vyhledávací pole s více cíli.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Kód chyby: DynamicsInvalidTypeForMultiTargetLookupField

- **Zpráva**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Příčina**: hodnota v cílovém sloupci není řetězec.

- **Doporučení**: Zadejte platný řetězec ve sloupci Cíl vyhledávání s více cíli.


### <a name="error-code-dynamicsfailedtorequetserver"></a>Kód chyby: DynamicsFailedToRequetServer

- **Zpráva**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Příčina**: Server Dynamics je nestabilní nebo nedostupný, nebo v síti dochází k problémům.

- **Doporučení**: Další podrobnosti najdete v části připojení k síti nebo v protokolu Dynamics serveru. Pokud chcete získat další pomoc, obraťte se na podporu Dynamics.
  

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Kód chyby: FtpFailedToConnectToFtpServer

- **Zpráva**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Příčina**: pro server FTP může být použit nesprávný typ propojené služby, jako je například použití propojené služby zabezpečeného FTP (SFTP) pro připojení k serveru FTP.

- **Doporučení**: Ověřte port cílového serveru. FTP používá port 21.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Kód chyby: HttpFileFailedToRead

- **Zpráva**: `Failed to read data from http server. Check the error from http server：%message;`

- **Příčina**: k této chybě dochází, když Azure Data Factory rozhovory se serverem HTTP, ale operace požadavku HTTP se nezdařila.

- **Doporučení**: Podívejte se do stavového kódu http v chybové zprávě a opravte problém vzdáleného serveru.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Kód chyby: ArgumentOutOfRangeException

- **Zpráva**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Příčina**: v Data Factory jsou hodnoty DateTime podporovány v rozsahu od 0001-01-01 00:00:00 do 9999-12-31 23:59:59. Oracle ale podporuje širší rozsah hodnot DateTime, jako je BC století nebo min/s>59, což vede k selhání v Data Factory.

- **Doporučení**: 

    Pokud chcete zjistit, jestli je hodnota v Oracle v rozsahu Data Factory, spusťte `select dump(<column name>)` . 

    Pokud chcete zjistit posloupnost bajtů ve výsledku, přečtěte si téma [jak jsou data uložená v Oracle?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle).


## <a name="orc-format"></a>Formát ORC

### <a name="error-code-orcjavainvocationexception"></a>Kód chyby: OrcJavaInvocationException

- **Zpráva**: `An error occurred when invoking Java, message: %javaException;.`
- **Příčiny a doporučení**: k této chybě může dojít v různých příčinách. Níže uvedený seznam vám umožní analyzovat možnou příčinu a související doporučení.

    | Příčina analýzy                                               | Doporučení                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Pokud chybová zpráva obsahuje řetězce "Java. lang. OutOfMemory", "prostor haldy jazyka Java" a "doubleCapacity", obvykle se jedná o problém se správou paměti ve staré verzi prostředí Integration runtime. | Pokud používáte Integration Runtime v místním prostředí, doporučujeme upgradovat na nejnovější verzi. |
    | Pokud chybová zpráva obsahuje řetězec "Java. lang. OutOfMemory", modul runtime integrace nemá dostatek prostředků ke zpracování souborů. | Omezte souběžné běhy prostředí Integration runtime. Pro prostředí IR v místním prostředí můžete škálovat až na výkonný počítač s pamětí rovnou nebo větší než 8 GB. |
    |Pokud chybová zpráva obsahuje řetězec "NullPointerReference", může být příčinou přechodná chyba. | Zkuste operaci zopakovat. Pokud se problém opakuje, obraťte se na podporu. |
    | Pokud chybová zpráva obsahuje řetězec "BufferOverflowException", může být příčinou přechodná chyba. | Zkuste operaci zopakovat. Pokud se problém opakuje, obraťte se na podporu. |
    | Pokud chybová zpráva obsahuje řetězec "Java. lang. ClassCastException:org. Apache. Hadoop. podregistr. serde2. IO. HiveCharWritable nelze přetypovat na org. Apache. Hadoop. IO. text", příčinou může být problém s převodem typu uvnitř běhového prostředí Java. Obvykle to znamená, že zdrojová data nelze v běhovém prostředí Java správně zpracovat. | Jedná se o problém s daty. Zkuste místo typu char nebo varchar použít řetězec ORC Data Format. |

### <a name="error-code-orcdatetimeexceedlimit"></a>Kód chyby: OrcDateTimeExceedLimit

- **Zpráva**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Příčina**: Pokud je hodnota datetime "0001-01-01 00:00:00", může být způsobena rozdíly mezi [juliánském kalendářem a gregoriánským kalendářem](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates).

- **Doporučení**: ověřte hodnotu taktů a vyhněte se použití hodnoty datetime "0001-01-01 00:00:00".


## <a name="parquet-format"></a>Formát Parquet

### <a name="error-code-parquetjavainvocationexception"></a>Kód chyby: ParquetJavaInvocationException

- **Zpráva**: `An error occurred when invoking java, message: %javaException;.`

- **Příčiny a doporučení**: k této chybě může dojít v různých příčinách. Níže uvedený seznam vám umožní analyzovat možnou příčinu a související doporučení.

    | Příčina analýzy                                               | Doporučení                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Pokud chybová zpráva obsahuje řetězce "Java. lang. OutOfMemory", "prostor haldy jazyka Java" a "doubleCapacity", obvykle se jedná o problém se správou paměti ve staré verzi Integration Runtime. | Pokud používáte prostředí IR v místním prostředí a verze je starší než 3.20.7159.1, doporučujeme upgradovat na nejnovější verzi. |
    | Pokud chybová zpráva obsahuje řetězec "Java. lang. OutOfMemory", modul runtime integrace nemá dostatek prostředků ke zpracování souborů. | Omezte souběžné běhy prostředí Integration runtime. Pro prostředí IR v místním prostředí můžete škálovat až na výkonný počítač s pamětí, která je větší nebo rovna 8 GB. |
    | Pokud chybová zpráva obsahuje řetězec "NullPointerReference", může se jednat o přechodnou chybu. | Zkuste operaci zopakovat. Pokud se problém opakuje, obraťte se na podporu. |

### <a name="error-code-parquetinvalidfile"></a>Kód chyby: ParquetInvalidFile

- **Zpráva**: `File is not a valid Parquet file.`

- **Příčina**: Jedná se o problém se souborem Parquet.

- **Doporučení**: Zkontrolujte, zda je vstup platným souborem Parquet.


### <a name="error-code-parquetnotsupportedtype"></a>Kód chyby: ParquetNotSupportedType

- **Zpráva**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Příčina**: formát Parquet není v Azure Data Factory podporován.

- **Doporučení**: Překontrolujte zdrojová data tak, že [v Azure Data Factory přesunete podporované formáty souborů a kompresní kodeky pomocí aktivity kopírování](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>Kód chyby: ParquetMissedDecimalPrecisionScale

- **Zpráva**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Příčina**: byla analyzována přesnost čísel a škálování, ale nebyly poskytnuty žádné informace.

- **Doporučení**: zdroj nevrací správné informace o přesnosti a měřítku. Informace najdete ve sloupci problém.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Kód chyby: ParquetInvalidDecimalPrecisionScale

- **Zpráva**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Příčina**: schéma je neplatné.

- **Doporučení**: pro přesnost a škálování se podívejte na sloupec problém.


### <a name="error-code-parquetcolumnnotfound"></a>Kód chyby: ParquetColumnNotFound

- **Zpráva**: `Column %column; does not exist in Parquet file.`

- **Příčina**: zdrojové schéma nesouhlasí se schématem jímky.

- **Doporučení**: Ověřte mapování v aktivitě. Ujistěte se, že zdrojový sloupec lze namapovat na správný sloupec jímky.


### <a name="error-code-parquetinvaliddataformat"></a>Kód chyby: ParquetInvalidDataFormat

- **Zpráva**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Příčina**: data nejde převést na typ, který je zadaný v mapování. Source.

- **Doporučení**: poklikejte na zdrojová data nebo zadejte správný datový typ pro tento sloupec v mapování sloupce aktivita kopírování. Další informace najdete v tématu [podporované formáty souborů a kompresní kodeky pomocí aktivity kopírování v Azure Data Factory](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Kód chyby: ParquetDataCountNotMatchColumnCount

- **Zpráva**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Příčina**: Neshoda mezi počtem zdrojových sloupců a počtem sloupců jímky.

- **Doporučení**: dvojitá kontrola, aby byl počet zdrojových sloupců stejný jako počet sloupců jímky v mapování


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Kód chyby: ParquetDataTypeNotMatchColumnType

- **Zpráva**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Příčina**: data ze zdroje nelze převést na typ, který je definován v jímky.

- **Doporučení**: Zadejte správný typ v mapování. jímka.


### <a name="error-code-parquetbridgeinvaliddata"></a>Kód chyby: ParquetBridgeInvalidData

- **Zpráva**: `%message;`

- **Příčina**: hodnota dat překročila limit.

- **Doporučení**: zkuste operaci zopakovat. Pokud potíže potrvají, kontaktujte nás.


### <a name="error-code-parquetunsupportedinterpretation"></a>Kód chyby: ParquetUnsupportedInterpretation

- **Zpráva**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Příčina**: Tento scénář není podporován.

- **Doporučení**: ' ParquetInterpretFor ' by neměl být ' sparkSql '.


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Kód chyby: ParquetUnsupportFileLevelCompressionOption

- **Zpráva**: `File level compression is not supported for Parquet.`

- **Příčina**: Tento scénář není podporován.

- **Doporučení**: Odeberte ' Argument ' v datové části.


### <a name="error-code-usererrorjniexception"></a>Kód chyby: UserErrorJniException

- **Zpráva**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Příčina**: prostředí Java Virtual Machine (JVM) nelze vytvořit, protože jsou nastaveny neplatné (globální) argumenty.

- **Doporučení**: Přihlaste se k počítači, který je hostitelem *všech uzlů* v místním prostředí IR. Zkontrolujte, zda je systémová proměnná nastavena správně, následovně: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Restartujte všechny uzly IR a pak znovu spusťte kanál.


### <a name="arithmetic-overflow"></a>Aritmetické přetečení

- **Příznaky**: při kopírování souborů Parquet se stala chybová zpráva: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Příčina**: Když zkopírujete soubory z Oracle do Parquet, v současné době je k dispozici pouze desetinné číslo přesnosti <= 38 a délka celočíselné části <= 20. 

- **Řešení**: jako alternativní řešení můžete převést všechny sloupce s tímto problémem na VARCHAR2.


### <a name="no-enum-constant"></a>Žádná konstanta výčtu

- **Příznaky**: při kopírování dat do formátu Parquet se stala chybová zpráva: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` nebo: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Příčina**: 

    Problém může být způsoben prázdnými znaky nebo nepodporovanými speciálními znaky (například,; {} () \n\t =) v názvu sloupce, protože Parquet nepodporuje takový formát. 

    Například název sloupce, například *Contoso (test)* , analyzuje typ v závorkách z [kódu](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . Chyba je vyvolána, protože neexistuje žádný takový "testovaný" typ.

    Pokud chcete kontrolovat podporované typy, navštivte web GitHub [Apache/Parquet-Mr](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java).

- **Řešení**: 

    Dvojitým ověřením zjistíte, zda:
    - Název sloupce jímky obsahuje prázdné znaky.
    - Jako název sloupce se použije první řádek s prázdnými znaky.
    - Typ OriginalType je podporován. Snažte se vyhnout použití těchto speciálních znaků: `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>Kód chyby: RestSinkCallFailed

- **Zpráva**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Příčina**: k této chybě dochází, když Azure Data Factory rozhovory s protokolem HTTP a operace požadavku se nezdařila.

- **Doporučení**: Projděte si stavový kód HTTP nebo zprávu v chybové zprávě a opravte problém vzdáleného serveru.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Neočekávaná odezva sítě z konektoru REST

- **Příznaky**: koncový bod někdy obdrží z KONEKTORu REST neočekávanou odpověď (400, 401, 403, 500).

- **Příčina**: zdrojový konektor REST používá při sestavování požadavku HTTP jako parametry adresu URL a metodu/hlavičku/záhlaví/text propojené služby, datové sady nebo kopie zdroje. Problém je pravděpodobně způsoben některými chybami v jednom nebo více zadaných parametrech.

- **Řešení**: 
    - V okně příkazového řádku použijte ' kudrlinkou ' k zobrazení, zda je parametr příčina (**přijmout** a hlavičky **User-Agent** by měly být vždy zahrnuty):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Pokud příkaz vrátí stejnou neočekávanou odpověď, vyřešte předchozí parametry pomocí ' kudrlinkou ', dokud nevrátí očekávanou odpověď. 

      Pro pokročilejší použití příkazu můžete také použít "oblý--Help".

    - Pokud jenom konektor Data Factory REST vrátí neočekávanou odpověď, obraťte se na podporu Microsoftu, kde najdete další řešení potíží.
    
    - Všimněte si, že výraz ' kudrlinkou ' pravděpodobně není vhodný k reprodukování problému s ověřením certifikátu protokolu SSL. V některých scénářích byl příkaz ' kudrlinkou ' úspěšně proveden, aniž by došlo k potížím s ověřováním certifikátů SSL. Když se ale v prohlížeči spustí stejná adresa URL, nevrátí se žádný certifikát SSL, aby klient navázal vztah důvěryhodnosti se serverem.

      Pro předchozí případ se doporučují nástroje jako **post** a **Fiddler** .


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Kód chyby: SftpOperationFail

- **Zpráva**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Příčina**: problém s operací SFTP.

- **Doporučení**: Projděte si podrobnosti o chybě z SFTP.


### <a name="error-code-sftprenameoperationfail"></a>Kód chyby: SftpRenameOperationFail

- **Zpráva**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Příčina**: váš server SFTP nepodporuje přejmenování dočasného souboru.

- **Doporučení**: Pokud chcete zakázat nahrávání do dočasného souboru, nastavte v jímky kopírování "useTempFileRename" jako NEPRAVDA.


### <a name="error-code-sftpinvalidsftpcredential"></a>Kód chyby: SftpInvalidSftpCredential

- **Zpráva**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **Příčina**: obsah privátního klíče je načtený z trezoru klíčů Azure nebo sady SDK, ale není správně kódovaný.

- **Doporučení**:  

    Pokud je obsah privátního klíče z vašeho trezoru klíčů, může soubor originálního klíče fungovat, když ho nahrajete přímo do propojené služby SFTP.

    Další informace najdete v tématu [kopírování dat z a na server SFTP pomocí Azure Data Factory](./connector-sftp.md#using-ssh-public-key-authentication). Obsah privátního klíče je kódovaný jako obsah privátního klíče SSH kódovaný v kódování Base64.

    Zakódovat *celý* soubor se soukromým klíčem pomocí kódování Base64 a uložit kódovaný řetězec do trezoru klíčů. Původní soubor privátního klíče je ten, který může pracovat na propojené službě SFTP, pokud vyberete **Odeslat** ze souboru.

    Tady je několik ukázek, které můžete použít k vygenerování řetězce:

    - Použít kód jazyka C#:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Použít kód Pythonu:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - Použijte nástroj pro převod Base64 třetí strany. Doporučujeme použít nástroj [kódování na Formát Base64](https://www.base64encode.org/) .

- **Příčina**: byl vybrán špatný formát obsahu klíče.

- **Doporučení**:  

    Privátní klíč SSH formátu PKCS # 8 (začíná na-----začínat ŠIFROVANÝm PRIVÁTNÍm klíčem-----) se v současné době nepodporuje pro přístup k serveru SFTP v Data Factory. 

    Chcete-li převést klíč na tradiční formát klíče SSH, začněte pomocí příkazu "-----zahájit používání PRIVÁTNÍho klíče RSA-----", spusťte následující příkazy:

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Příčina**: neplatné přihlašovací údaje nebo obsah privátního klíče.

- **Doporučení**: Pokud chcete zjistit, jestli je soubor klíče nebo heslo správný, dvakrát Zkontrolujte nástroje, jako je WinSCP.

### <a name="sftp-copy-activity-failed"></a>Aktivita kopírování SFTP se nezdařila.

- **Příznaky**: 
  * Kód chyby: UserErrorInvalidColumnMappingColumnNotFound 
  * Chybová zpráva: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Příčina**: zdroj neobsahuje sloupec s názvem "AccMngr".

- **Řešení**: Chcete-li zjistit, zda sloupec "AccMngr" existuje, zkontrolujte konfiguraci datové sady pomocí mapování sloupce cílové datové sady.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Kód chyby: SftpFailedToConnectToSftpServer

- **Zpráva**: `Failed to connect to SFTP server '%server;'.`

- **Příčina**: Pokud chybová zpráva obsahuje řetězec "operace čtení soketu vypršela po 30 000 milisekundách, může to být tím, že se pro server SFTP používá nesprávný typ propojené služby. Můžete například použít propojenou službu FTP k připojení k serveru SFTP.

- **Doporučení**: Ověřte port cílového serveru. Protokol SFTP ve výchozím nastavení používá port 22.

- **Příčina**: Pokud chybová zpráva obsahuje řetězec "odpověď serveru neobsahuje identifikaci protokolu SSH", může to být způsobeno tím, že server SFTP omezení připojení omezil. Data Factory vytvoří Paralelní stažení více připojení ze serveru SFTP a v některých případech dojde k omezení serveru SFTP. V případě, že různé servery nastanou omezení, jsou obvykle jiné chyby.

- **Doporučení**:  

    Zadejte maximální počet souběžných připojení datové sady SFTP jako 1 a znovu spusťte aktivitu kopírování. Pokud je aktivita úspěšná, můžete si být jisti, že je to příčina omezování.

    Pokud chcete zvýšit propustnost, obraťte se na správce SFTP, aby zvýšil limit počtu souběžných připojení, nebo můžete provést jednu z následujících akcí:

    * Pokud používáte prostředí IR v místním prostředí, přidejte do seznamu povolených počítačů místní počítač s INFRAČERVENým hostováním.
    * Pokud používáte Azure IR, přidejte [Azure Integration runtime IP adresy](./azure-integration-runtime-ip-addresses.md). Pokud nechcete do seznamu povolených serverů SFTP přidat rozsah IP adres, použijte místo toho místní prostředí IR.

## <a name="sharepoint-online-list"></a>Seznam SharePointu Online

### <a name="error-code-sharepointonlineauthfailed"></a>Kód chyby: SharePointOnlineAuthFailed

- **Zpráva**: `The access token generated failed, status code: %code;, error message: %message;.`

- **Příčina**: ID instančního objektu a klíč nemusí být nastavené správně.

- **Doporučení**: Zkontrolujte zaregistrovanou aplikaci (ID objektu služby) a klíč, abyste viděli, jestli jsou správně nastavené.


## <a name="xml-format"></a>Formát XML

### <a name="error-code-xmlsinknotsupported"></a>Kód chyby: XmlSinkNotSupported

- **Zpráva**: `Write data in XML format is not supported yet, choose a different format!`

- **Příčina**: datová sada XML byla použita jako datová sada jímky v aktivitě kopírování.

- **Doporučení**: použijte datovou sadu v jiném formátu než datovou sadu jímky.


### <a name="error-code-xmlattributecolumnnameconflict"></a>Kód chyby: XmlAttributeColumnNameConflict

- **Zpráva**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Příčina**: byla použita předpona atributu, což způsobilo konflikt.

- **Doporučení**: nastavte pro vlastnost "attributePrefix" jinou hodnotu.


### <a name="error-code-xmlvaluecolumnnameconflict"></a>Kód chyby: XmlValueColumnNameConflict

- **Zpráva**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Příčina**: jako název sloupce pro hodnotu elementu byl použit jeden z názvů podřízených elementů.

- **Doporučení**: nastavte pro vlastnost "valueColumn" jinou hodnotu.


### <a name="error-code-xmlinvalid"></a>Kód chyby: XmlInvalid

- **Zpráva**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Příčina**: vstupní soubor XML nemá správný formát.

- **Doporučení**: opravte soubor XML, aby byl správně vytvořen.


## <a name="general-copy-activity-error"></a>Obecná chyba aktivity kopírování

### <a name="error-code-jrenotfound"></a>Kód chyby: JreNotFound

- **Zpráva**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Příčina**: Modul IR pro místní hostování nemůže najít běhový modul Java. Pro čtení konkrétního zdroje je vyžadován běhový modul Java.

- **Doporučení**: Projděte si prostředí Integration runtime, přečtěte si téma [použití Integration runtime](./format-parquet.md#using-self-hosted-integration-runtime)v místním prostředí.


### <a name="error-code-wildcardpathsinknotsupported"></a>Kód chyby: WildcardPathSinkNotSupported

- **Zpráva**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Příčina**: datová sada jímky nepodporuje zástupné hodnoty.

- **Doporučení**: Ověřte datovou sadu jímky a přepište cestu bez použití zástupné hodnoty.


### <a name="fips-issue"></a>Problém FIPS

- **Příznaky**: aktivita kopírování se v místním počítači s podporou standardu FIPS nezdařila s následující chybovou zprávou: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Příčina**: k této chybě může dojít při kopírování dat pomocí konektorů, jako je Azure Blob, SFTP atd. Standard FIPS (Federal Information Processing Standards) definuje určitou sadu kryptografických algoritmů, které je povoleno použít. Když je v počítači povolený režim FIPS, některé kryptografické třídy, na kterých aktivita kopírování závisí, jsou v některých scénářích blokované.

- **Řešení**: Zjistěte, [Proč už nedoporučujeme "režim FIPS"](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037), a vyhodnoťte, jestli můžete Standard FIPS zakázat na vašem místním počítači s infračerveným hostováním.

    Případně, pokud chcete povolit Azure Data Factory obejít FIPS a spustit aktivitu úspěšně, udělejte toto:

    1. Otevřete složku, ve které je nainstalováno prostředí IR pro místní hostování. Cesta je obvykle *C:\Program Files\Microsoft Integration runtime \<IR version> \Shared*.

    2. Otevřete soubor *diawp.exe.config* a pak na konci `<runtime>` oddílu přidejte `<enforceFIPSPolicy enabled="false"/>` , jak je znázorněno zde:

        ![Snímek obrazovky s oddílem diawp.exe.config souboru, který zobrazuje FIPS zakázaný](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Uložte soubor a pak znovu spusťte počítač IR s místním hostováním.


## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka Microsoft Q&](/answers/topics/azure-data-factory.html)
*  [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)