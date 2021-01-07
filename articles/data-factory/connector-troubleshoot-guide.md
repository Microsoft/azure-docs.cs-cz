---
title: Řešení potíží s konektory služby Azure Data Factory
description: Naučte se řešit potíže s konektorem v Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: 68547b8fb673cd54b7c21963ede122553bbbc390
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97967119"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Řešení potíží s konektory služby Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží pro konektory v Azure Data Factory.
  
## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>Kód chyby: AzureBlobOperationFailed

- **Zpráva**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Příčina**: došlo k potížím s voláním operace BLOB Storage.

- **Doporučení**: Podrobnosti najdete v podrobnostech o chybě. Podívejte se na dokument s nápovědě k objektu BLOB: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes . Pokud potřebujete, obraťte se na tým úložiště.


### <a name="invalid-property-during-copy-activity"></a>Neplatná vlastnost během aktivity kopírování

- **Zpráva**: `Copy activity <Activity Name> has an invalid "source" property. The source type is not compatible with the dataset <Dataset Name> and its linked service <Linked Service Name>. Please verify your input against.`

- **Příčina**: typ definovaný v datové sadě je nekonzistentní s typem zdroje nebo jímky definovaným v aktivitě kopírování.

- **Řešení**: Upravte definici JSON datové sady nebo kanálu, aby se typy shodovaly, a spusťte nasazení znovu.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Chybová zpráva: velikost požadavku je příliš velká.

- **Příznaky**: kopírujete data do Azure Cosmos DB s výchozí velikostí dávky pro zápis a chyba volání *"**Velikost požadavku je příliš velká**"*.

- **Příčina**: Cosmos DB omezuje velikost jednoho požadavku na 2 MB. Vzorec je, velikost požadavku = jedna velikost dokumentu * velikost dávky zápisu. Pokud je velikost dokumentu velká, výchozí chování bude mít za následek příliš velkou velikost požadavku. Můžete vyladit velikost dávky zápisu.

- **Řešení**: v jímky aktivity kopírování snižte hodnotu "zapsat velikost dávky" (výchozí hodnota je 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Chybová zpráva: porušení omezení jedinečného indexu

- **Příznaky**: při kopírování dat do Cosmos DB se zobrazí následující chyba:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Příčina**: Existují dvě možné příčiny:

    - Pokud použijete funkci **Vložit** jako zápis, tato chyba znamená, že zdrojová data mají řádky nebo objekty se stejným ID.
    - Pokud použijete **Upsert** jako chování zápisu a nastavíte do kontejneru jiný jedinečný klíč, tato chyba znamená, že zdrojová data budou mít řádky nebo objekty s odlišnými identifikátory, ale stejnou hodnotu pro definovaný jedinečný klíč.

- **Řešení**: 

    - V případě cause1 nastavte **Upsert** jako chování zápisu.
    - V případě příčiny 2 se ujistěte, že každý dokument má odlišnou hodnotu pro definovaný jedinečný klíč.

### <a name="error-message-request-rate-is-large"></a>Chybová zpráva: frekvence požadavků je velká

- **Příznaky**: při kopírování dat do Cosmos DB se zobrazí následující chyba:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Příčina**: použité jednotky žádostí jsou větší než dostupný ru nakonfigurovaný v Cosmos DB. Přečtěte [si, jak](../cosmos-db/request-units.md#request-unit-considerations)Cosmos DB počítá ru.

- **Řešení**: tady jsou dvě řešení:

    - **Zvyšte hodnotu v kontejneru ru** na větší hodnotu v Cosmos DB, což vylepší výkon aktivity kopírování, i když se v Cosmos DB účtuje větší náklady. 
    - Zmenšete **writeBatchSize** na menší hodnotu (například 1000) a nastavte **parallelCopies** na menší hodnotu, jako je například 1, což způsobí, že kopírování bude mít horší výkon, než je aktuální, ale nebudou se vám účtovat žádné náklady Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>V mapování sloupce chybí sloupec.

- **Příznaky**: Když importujete schéma pro Cosmos DB mapování sloupců, chybí některé sloupce. 

- **Příčina**: ADF odvodí schéma z prvních 10 Cosmos DB dokumentů. Pokud některé sloupce nebo vlastnosti v těchto dokumentech nemají hodnotu, nebudou zjištěny pomocí ADF, takže se nebudou zobrazovat.

- **Řešení**: Tento dotaz můžete vyladit tak, aby se sloupec vynutil, aby se zobrazil v sadě výsledků s prázdnou hodnotou: (Předpokládejme, že ve prvních 10 dokumentech chybí sloupec nemožné). Případně můžete ručně přidat sloupec pro mapování.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Chybová zpráva: GuidRepresentation pro čtecí zařízení je CSharpLegacy

- **Příznaky**: při kopírování dat z Cosmos DB MongoAPI/MongoDB s polem UUID se zobrazí následující chyba:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Příčina**: Existují dva způsoby, jak vyjádřit UUID v bson-UuidStardard a UuidLegacy. Ve výchozím nastavení se pro čtení dat používá UuidLegacy. Pokud jsou vaše data UUID v MongoDB UuidStandard, bude se vám vyrazit chyba.

- **Řešení**: v připojovacím řetězci MongoDB přidejte možnost "**uuidRepresentation = Standard**". Další informace najdete v tématu [připojovací řetězec MongoDB](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code--cosmosdbsqlapioperationfailed"></a>Kód chyby: CosmosDbSqlApiOperationFailed

- **Zpráva**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Příčina**: problém CosmosDbSqlApi operace.

- **Doporučení**: Podrobnosti najdete v podrobnostech o chybě. Podívejte se na [dokument s CosmosDb Help](https://docs.microsoft.com/azure/cosmos-db/troubleshoot-dot-net-sdk). Pokud potřebujete, obraťte se na tým CosmosDb.


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Chybová zpráva: základní připojení bylo ukončeno: nelze vytvořit vztah důvěryhodnosti pro zabezpečený kanál SSL/TLS.

- **Příznaky**: aktivita kopírování se nezdařila s následující chybou: 

    ```
    Message: ErrorCode = `UserErrorFailedFileOperation`, Error Message = `The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`.
    ```

- **Příčina**: ověření certifikátu během TLS handshake selhalo.

- **Řešení**: alternativní řešení: k přeskočení ověřování TLS pro adls Gen1 použijte dvoufázové kopírování. Je nutné reprodukování tohoto problému a shromáždění trasování Netmon a poté zapojením svého síťového týmu ke kontrole konfigurace místní sítě.

    ![Řešení potíží s ADLS Gen1](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Chybová zpráva: vzdálený server vrátil chybu: (403) zakázáno

- **Příznaky**: aktivita kopírování se nezdařila s následující chybou: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Příčina**: jedinou možnou příčinou je, že instanční objekt nebo spravovaná identita nemají oprávnění pro přístup k určité složce nebo souboru.

- **Řešení**: Udělte odpovídající oprávnění pro všechny složky a podsložky, které potřebujete zkopírovat. Přečtěte si [Tento dokument](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Chybová zpráva: Nepodařilo se získat přístupový token pomocí instančního objektu. Chyba ADAL: service_unavailable

- **Příznaky**: aktivita kopírování se nezdařila s následující chybou:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Příčina**: Pokud server tokenu služby (STS), který je vlastněn Azure Active Directory, není k dispozici, například příliš zaneprázdněný pro zpracování požadavků, vrátí chybu HTTP 503. 

- **Řešení**: po několika minutách znovu spusťte aktivitu kopírování.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Kód chyby: ADLSGen2OperationFailed

- **Zpráva**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Příčina**: adls Gen2 vyvolá chybu oznamující, že operace se nezdařila.

- **Doporučení**: Přečtěte si podrobnou chybovou zprávu vyvolanou adls Gen2. Pokud je to způsobeno přechodným selháním, zkuste to prosím znovu. Pokud potřebujete další pomoc, obraťte se prosím na podporu Azure Storage a poskytněte ID žádosti v chybové zprávě.

- **Příčina**: Pokud chybová zpráva obsahuje zakázané, instanční objekt nebo spravovaná identita nemusí mít k dispozici dostatečná oprávnění pro přístup k adls Gen2.

- **Doporučení**: informace naleznete v dokumentu Help: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication .

- **Příčina**: Pokud chybová zpráva obsahuje hodnotu ' Nenalezeno ', je vrácena chyba adls Gen2.

- **Doporučení**: může to být způsobeno přechodným selháním. zkuste to prosím znovu. Pokud se problém nevyřeší, obraťte se prosím na podporu Azure Storage a poskytněte ID žádosti v chybové zprávě.

### <a name="request-to-adls-gen2-account-met-timeout-error"></a>Požadavek na ADLS Gen2 účtu při vypršení časového limitu

- **Zpráva**: kód chyby = `UserErrorFailedBlobFSOperation` , chybová zpráva = `BlobFS operation failed for: A task was canceled` .

- **Příčina**: problém je způsoben chybou vypršení časového limitu jímky adls Gen2, k němuž většinou dochází v místním prostředí IR.

- **Doporučení**: 

    - Pokud je to možné, umístěte svůj místně hostovaný počítač IR a cílový účet ADLS Gen2 ve stejné oblasti. To se může vyhnout náhodnému vypršení časového limitu a mít lepší výkon.

    - Zkontrolujte, jestli existuje nějaké zvláštní nastavení sítě, jako je třeba ExpressRoute, a ujistěte se, že má síť dostatečnou šířku pásma. Doporučujeme, abyste snížili nastavení místních souběžných úloh IR v místním prostředí, když je celková šířka pásma nízká, což se může vyhnout konkurenci síťových prostředků napříč několika souběžnými úlohami.

    - Pro nebinární kopírování použijte menší velikost bloku pro zmírnění takové chyby vypršení časového limitu, pokud je velikost souboru střední nebo malá. Přečtěte si [BLOB Storage blok vložení](https://docs.microsoft.com/rest/api/storageservices/put-block).

       Chcete-li určit velikost vlastního bloku, můžete upravit vlastnost v editoru. JSON:
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-file-storage"></a>Azure File Storage

### <a name="error-code--azurefileoperationfailed"></a>Kód chyby: AzureFileOperationFailed

- **Zpráva**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Příčina**: došlo k potížím s operací služby Azure File Storage.

- **Doporučení**: Podrobnosti najdete v podrobnostech o chybě. Další informace najdete v dokumentu o nápovědě ke službě Azure File: https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes . Pokud potřebujete podporu, obraťte se na tým úložiště.


## <a name="azure-synapse-analyticsazure-sql-databasesql-server"></a>Azure synapse Analytics/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Kód chyby: SqlFailedToConnect

- **Zpráva**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Příčina**: Azure SQL: Pokud chybová zpráva obsahuje "SqlErrorNumber = 47073", znamená to, že v nastavení připojení se odepře přístup k veřejné síti.

- **Doporučení**: v bráně Azure SQL firewall nastavte možnost Odepřít přístup k veřejné síti na ne. Další informace najdete v https://docs.microsoft.com/azure/azure-sql/database/connectivity-settings#deny-public-network-access .

- **Příčina**: Azure SQL: Pokud chybová zpráva obsahuje kód chyby SQL, například "SqlErrorNumber = [ErrorCode]", přečtěte si prosím Průvodce odstraňováním potíží Azure SQL.

- **Doporučení**: Další informace najdete v https://docs.microsoft.com/azure/azure-sql/database/troubleshoot-common-errors-issues .

- **Příčina**: Ověřte, jestli je v seznamu povolených bran firewall port 1433.

- **Doporučení**: postupujte podle tohoto referenčního dokumentu: https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by- .

- **Příčina**: Pokud chybová zpráva obsahuje "SqlException", SQL Database vyvolá chybu oznamující, že některá konkrétní operace selhala.

- **Doporučení**: Další podrobnosti najdete v kódu chyby SQL v tomto referenčním dokumentu: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Pokud potřebujete další pomoc, obraťte se na podporu Azure SQL.

- **Příčina**: Pokud jde o přechodný problém (například nestabilní síťové připojení), přidejte prosím do zásad aktivity opakovat, aby se zmírnila.

- **Doporučení**: postupujte podle tohoto referenčního dokumentu: https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities#activity-policy .

- **Příčina**: Pokud chybová zpráva obsahuje "klient s IP adresou"... nemá povolený přístup k serveru a pokoušíte se připojit k Azure SQL Database, obvykle to způsobuje Azure SQL Database potížích s bránou firewall.

- **Doporučení**: v konfiguraci brány firewall Azure SQL Server povolte možnost Povolit službám a prostředkům Azure přístup k tomuto serveru. Referenční doc: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure .


### <a name="error-code--sqloperationfailed"></a>Kód chyby: SqlOperationFailed

- **Zpráva**: `A database operation failed. Please search error to get more details.`

- **Příčina**: Pokud chybová zpráva obsahuje "SqlException", SQL Database vyvolá chybu oznamující, že některá konkrétní operace selhala.

- **Doporučení**: Pokud chyba SQL není jasná, zkuste prosím změnit databázi na nejnovější úroveň kompatibility 150. Může vyvolat nejnovější verzi chyb SQL. Přečtěte si [dokument s podrobnostmi](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat).

    Pokud chcete řešit potíže s SQL serverem, vyhledejte v tomto referenčním dokumentu kód chyby SQL, kde najdete další podrobnosti: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Pokud potřebujete další pomoc, obraťte se na podporu Azure SQL.

- **Příčina**: Pokud chybová zpráva obsahuje "PdwManagedToNativeInteropException", obvykle se jedná o neshodu mezi zdrojem a velikostmi sloupců jímky.

- **Doporučení**: Ověřte velikost zdroje i sloupce jímky. Pokud potřebujete další pomoc, obraťte se na podporu Azure SQL.

- **Příčina**: Pokud chybová zpráva obsahuje "InvalidOperationException", obvykle je způsobena neplatnými vstupními daty.

- **Doporučení**: Chcete-li zjistit, který řádek narazí na problém, povolte funkci odolnost proti chybám u aktivity kopírování, která může přesměrovat problematické řádky do úložiště pro další šetření. Referenční doc: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlunauthorizedaccess"></a>Kód chyby: SqlUnauthorizedAccess

- **Zpráva**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Příčina**: přihlašovací údaje jsou nesprávné nebo přihlašovací účet nemá přístup k SQL Database.

- **Doporučení**: Ověřte, že přihlašovací účet má dostatečná oprávnění pro přístup k SQL Database.


### <a name="error-code--sqlopenconnectiontimeout"></a>Kód chyby: SqlOpenConnectionTimeout

- **Zpráva**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Příčina**: SQL Database přechodného selhání.

- **Doporučení**: zkuste aktualizovat připojovací řetězec propojené služby s větší hodnotou časového limitu připojení.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Kód chyby: SqlAutoCreateTableTypeMapFailed

- **Zpráva**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Příčina**: tabulka automatického vytváření nemůže splňovat požadavek na zdroj.

- **Doporučení**: Aktualizujte typ sloupce v mapování nebo na cílovém serveru vytvořte tabulku jímky ručně.


### <a name="error-code--sqldatatypenotsupported"></a>Kód chyby: SqlDataTypeNotSupported

- **Zpráva**: `A database operation failed. Check the SQL errors.`

- **Příčina**: Pokud k problému dojde ve zdroji SQL a chyba se týká přetečení Hodnota SqlDateTime, hodnota dat je nad rozsahem typu logic (1/1/1753 12:00:00 dop. 12/31/9999 11:59:59 odp.).

- **Doporučení**: přetypování typu na řetězec ve zdrojovém dotazu SQL nebo v mapování sloupce aktivity kopírování změňte typ sloupce na String.

- **Příčina**: Pokud k problému dojde v jímky SQL a chyba se vztahuje k přetečení Hodnota SqlDateTime, hodnota dat je nad povoleným rozsahem v tabulce jímky.

- **Doporučení**: aktualizujte odpovídající typ sloupce na typ datetime2 v tabulce jímky.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Kód chyby: SqlInvalidDbStoredProcedure

- **Zpráva**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Příčina**: zadaná uložená procedura není platná. Příčinou může být to, že uložená procedura nevrací žádná data.

- **Doporučení**: Ověřte uloženou proceduru pomocí nástrojů SQL. Ujistěte se, že uložená procedura může vracet data.


### <a name="error-code--sqlinvaliddbquerystring"></a>Kód chyby: SqlInvalidDbQueryString

- **Zpráva**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Příčina**: zadaný dotaz SQL není platný. To může být způsobeno tím, že dotaz nevrátí žádná data.

- **Doporučení**: Ověřte dotaz SQL pomocí nástrojů SQL. Ujistěte se, že dotaz může vracet data.


### <a name="error-code--sqlinvalidcolumnname"></a>Kód chyby: SqlInvalidColumnName

- **Zpráva**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Příčina**: sloupec nelze najít. Možná konfigurace je chybná.

- **Doporučení**: Ověřte sloupec v dotazu, "Structure" v datové sadě a "Mappings" v aktivitě.


### <a name="error-code--sqlbatchwritetimeout"></a>Kód chyby: SqlBatchWriteTimeout

- **Zpráva**: `Timeouts in SQL write operation.`

- **Příčina**: SQL Database přechodného selhání.

- **Doporučení**: zkuste to znovu. Pokud se problém reprodukci, obraťte se na podporu Azure SQL.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Kód chyby: SqlBatchWriteTransactionFailed

- **Zpráva**: `SQL transaction commits failed`

- **Příčina**: Pokud detaily výjimky neustále oznamují časový limit transakce, latence sítě mezi integrací modulem runtime a databází je vyšší než výchozí prahová hodnota 30 sekund.

- **Doporučení**: aktualizujte připojovací řetězec propojené služby SQL s hodnotou časový limit připojení se rovná 120 nebo vyšší a znovu spusťte aktivitu.

- **Příčina**: Pokud se v podrobnostech výjimky občas říká SqlConnection, může se jednat o přechodný výpadek sítě nebo problém na straně SQL Database.

- **Doporučení**: zkuste tuto aktivitu znovu a zkontrolujte SQL Database metriky na straně.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Kód chyby: SqlBulkCopyInvalidColumnLength

- **Zpráva**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Příčina**: hromadné kopírování SQL se nezdařilo, protože od klienta BCP byla přijata neplatná délka sloupce.

- **Doporučení**: Chcete-li zjistit, který řádek narazí na problém, povolte funkci odolnost proti chybám u aktivity kopírování, která může přesměrovat problematické řádky do úložiště pro další šetření. Referenční doc: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlconnectionisclosed"></a>Kód chyby: SqlConnectionIsClosed

- **Zpráva**: `The connection is closed by SQL Database.`

- **Příčina**: připojení SQL je ukončeno SQL Database při vysokém souběžném spuštění a ukončení připojení serveru.

- **Doporučení**: vzdálený server zavřel připojení SQL. Opakujte. Pokud se problém reprodukci, obraťte se na podporu Azure SQL.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Chybová zpráva: převod se nezdařil při převodu ze znakového řetězce na typ uniqueidentifier.

- **Příznaky**: Když zkopírujete data z tabulkového zdroje dat (například SQL Server) do služby Azure synapse Analytics pomocí připravené kopie a základu, zobrazí se následující chyba:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Příčina**: základní databáze Azure synapse Analytics nemůže převést prázdný řetězec na GUID.

- **Řešení**: v jímky aktivity kopírování v části nastavení základní hodnoty nastavte možnost **použít výchozí typ** na hodnotu NEPRAVDA.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Chybová zpráva: očekávaný datový typ: DECIMAL (x, x), problematická hodnota

- **Příznaky**: Když zkopírujete data z tabulkového zdroje dat (například SQL Server) do služby Azure synapse Analytics pomocí připravené kopie a základu, zobrazí se následující chyba:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Příčina**: základ analýzy Azure synapse Analytics nemůže vložit prázdný řetězec (hodnota null) do desetinného sloupce.

- **Řešení**: v jímky aktivity kopírování v části nastavení základní hodnoty nastavte možnost **použít výchozí typ** na hodnotu NEPRAVDA.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Chybová zpráva: zpráva o výjimce Java: HdfsBridge:: CreateRecordReader

- **Příznaky**: kopírujete data do služby Azure synapse Analytics pomocí základu dat a zaškrtnete tuto chybu:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Příčina**: možnou příčinou je, že schéma (celková šířka sloupce) je příliš velké (větší než 1 MB). Ověřte schéma cílové tabulky Azure synapse Analytics přidáním velikosti všech sloupců:

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

- **Řešení**: 
    - Zmenšete šířku sloupce tak, aby byla menší než 1 MB.
    - Nebo zakažte PolyBase a použijte přístup s hromadným vkládáním.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Chybová zpráva: podmínka zadaná pomocí podmíněných hlaviček HTTP není splněna.

- **Příznaky**: k vyžádání dat ze služby Azure synapse Analytics použijete dotaz SQL a zobrazí se tato chyba:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Příčina**: při dotazování na externí tabulku v Azure Storage došlo k potížím se službou Azure synapse Analytics.

- **Řešení**: Spusťte stejný dotaz v SSMS a zkontrolujte, jestli vidíte stejný výsledek. Pokud ano, otevřete lístek podpory pro Azure synapse Analytics a poskytněte vašemu serveru Azure synapse Analytics Server a název databáze, abyste mohli dále řešit problémy.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Úroveň výkonu je nízká a vede k selhání kopírování

- **Příznaky**: při kopírování dat do Azure SQL Database se stala chybová zpráva: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Příčina**: používá se Azure SQL Database S1, která v takovém případě dosáhla limitu v/v.

- **Řešení**: Pokud chcete problém vyřešit, upgradujte Azure SQL Database úroveň výkonu. 


### <a name="sql-table-cannot-be-found"></a>Tabulka SQL se nenašla. 

- **Příznaky**: při kopírování dat z Hybrid do Prem SQL Server tabulky došlo k chybě:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Příčina**: aktuální účet SQL nemá dostatečná oprávnění ke spouštění požadavků vydaných .NET SqlBulkCopy. WriteToServer.

- **Řešení**: přepněte na PRIVILEGOVANÝ účet SQL.


### <a name="error-message-string-or-binary-data-would-be-truncated"></a>Chybová zpráva: řetězcová nebo binární data by byla zkrácena.

- **Příznaky**: došlo k chybě při kopírování dat do tabulky Prem/Azure SQL Server: 

- **Příčina**: definice schématu tabulky SQL CX má jeden nebo více sloupců s menší délkou, než je očekáváno.

- **Řešení**: zkuste problém vyřešit podle následujících kroků:

    1. Použijte odolnost [proti chybám](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance)jímky SQL, obzvláště "redirectIncompatibleRowSettings", abyste mohli řešit, které řádky mají problém.

        > [!NOTE]
        > Upozorňujeme, že odolnost proti chybám může zavést další dobu spuštění, což by mohlo vést k vyšším nákladům.

    2. Pokud chcete zjistit, které sloupce je třeba aktualizovat, poklikejte na přesměrovaná data pomocí sloupce schématu tabulky SQL.

    3. Aktualizujte schéma tabulky odpovídajícím způsobem.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code--azuretableduplicatecolumnsfromsource"></a>Kód chyby: AzureTableDuplicateColumnsFromSource

- **Zpráva**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink`

- **Příčina**: může být běžné pro dotaz SQL s připojením nebo nestrukturovanými soubory CSV.

- **Doporučení**: poklikejte na zdrojové sloupce a opravte je odpovídajícím způsobem.


## <a name="db2"></a>DB2

### <a name="error-code--db2driverrunfailed"></a>Kód chyby: DB2DriverRunFailed

- **Zpráva**: `Error thrown from driver. Sql code: '%code;'`

- **Příčina**: Pokud chybová zpráva obsahuje "SQLSTATE = 51002 SQLCODE =-805", přečtěte si prosím Tip v tomto dokumentu: https://docs.microsoft.com/azure/data-factory/connector-db2#linked-service-properties

- **Doporučení**: zkuste nastavit "NULLID" ve vlastnosti "balíčekcollection".


## <a name="delimited-text-format"></a>Textový formát s oddělovači

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Kód chyby: DelimitedTextColumnNameNotAllowNull

- **Zpráva**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Příčina**: když se v aktivitě nastaví ' firstRowAsHeader ', jako název sloupce se použije první řádek. Tato chyba znamená, že první řádek obsahuje prázdnou hodnotu. Například: "Columns, ColumnB".

- **Doporučení**: Ověřte první řádek a opravte hodnotu, pokud existuje prázdná hodnota.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Kód chyby: DelimitedTextMoreColumnsThanDefined

- **Zpráva**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Příčina**: problematický počet sloupců řádku je větší než počet sloupců prvního řádku. Může to být způsobeno problémem s daty nebo nesprávným nastavením oddělovače sloupců nebo uvozovek.

- **Doporučení**: Získá počet řádků v chybové zprávě, zkontroluje sloupec řádku a opraví data.

- **Příčina**: Pokud je očekávaný počet sloupců "1" v chybové zprávě, možná jste zadali špatné nastavení komprese nebo formátu. Proto ADF správně analyzuje vaše soubory.

- **Doporučení**: Zkontrolujte nastavení formátu a ujistěte se, že se shoduje se zdrojovými soubory.

- **Příčina**: Pokud je zdrojem složka, je možné, že soubory v zadané složce mají jiné schéma.

- **Doporučení**: Ujistěte se, že soubory v dané složce mají stejné schéma.


## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Kód chyby: DynamicsCreateServiceClientError

- **Zpráva**: `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Příčina**: Jedná se o přechodný problém na straně Dynamics serveru.

- **Doporučení**: Spusťte kanál znovu. Pokud budete pokračovat, zkuste omezit paralelismus. Pokud pořád selže, obraťte se prosím na podporu Dynamics.


### <a name="columns-are-missing-when-previewingimporting-schema"></a>Při náhledu nebo importu schématu chybí sloupce.

- **Příznaky**: některé sloupce se při importu schématu nebo náhledu dat vypínají. Chybová zpráva: `The valid structure information (column name and type) are required for Dynamics source.`

- **Příčina**: Tento problém je v podstatě záměrné podle návrhu, protože ADF nemůže zobrazit sloupce, které nemají žádnou hodnotu v prvních 10 záznamech. Ujistěte se, že jsou sloupce, které jste přidali, ve správném formátu. 

- **Doporučení**: ručně přidejte sloupce na kartě mapování.


### <a name="error-code--dynamicsmissingtargetformultitargetlookupfield"></a>Kód chyby: DynamicsMissingTargetForMultiTargetLookupField

- **Zpráva**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Příčina**: cílový sloupec neexistuje ve zdroji nebo v mapování sloupce.

- **Doporučení**: 1. Ujistěte se, že zdroj obsahuje cílový sloupec. 2. Přidejte cílový sloupec do mapování sloupce. Zajistěte, aby byl sloupec jímky ve vzoru {fieldName} @EntityReference .


### <a name="error-code--dynamicsinvalidtargetformultitargetlookupfield"></a>Kód chyby: DynamicsInvalidTargetForMultiTargetLookupField

- **Zpráva**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;"`

- **Příčina**: nesprávný název entity je zadán jako Cílová entita vyhledávacího pole s více cíli.

- **Doporučení**: Zadejte platný název entity pro vyhledávací pole s více cíli.


### <a name="error-code--dynamicsinvalidtypeformultitargetlookupfield"></a>Kód chyby: DynamicsInvalidTypeForMultiTargetLookupField

- **Zpráva**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Příčina**: hodnota v cílovém sloupci není řetězec.

- **Doporučení**: Zadejte platný řetězec ve sloupci Cíl vyhledávání s více cíli.


### <a name="error-code--dynamicsfailedtorequetserver"></a>Kód chyby: DynamicsFailedToRequetServer

- **Zpráva**: `The dynamics server or the network is experiencing issues. Check network connectivity or check dynamics server log for more details.`

- **Příčina**: Server Dynamics je nestabilní nebo nedostupný nebo v síti dochází k problémům.

- **Doporučení**: Prohlédněte si připojení k síti nebo vyhledejte další podrobnosti v protokolu serveru Dynamics Server. Další nápovědu získáte od podpory Dynamics.
    

## <a name="ftp"></a>FTP

### <a name="error-code--ftpfailedtoconnecttoftpserver"></a>Kód chyby: FtpFailedToConnectToFtpServer

- **Zpráva**: `Failed to connect to FTP server. Please make sure the provided server informantion is correct, and try again.`

- **Příčina**: pro server FTP může být použit nesprávný typ propojené služby, například použití propojené služby SFTP pro připojení k serveru FTP.

- **Doporučení**: Ověřte port cílového serveru. Ve výchozím nastavení FTP používá port 21.


## <a name="http"></a>HTTP

### <a name="error-code--httpfilefailedtoread"></a>Kód chyby: HttpFileFailedToRead

- **Zpráva**: `Failed to read data from http server. Check the error from http server：%message;`

- **Příčina**: k této chybě dochází, když Azure Data Factory komunikovat se serverem HTTP, ale operace požadavku HTTP selže.

- **Doporučení**: Podívejte se na stavový kód HTTP \ zpráva v chybové zprávě a opravte problém vzdáleného serveru.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Kód chyby: ArgumentOutOfRangeException

- **Zpráva**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Příčina**: v ADF jsou hodnoty DateTime podporované v rozsahu od 0001-01-01 00:00:00 do 9999-12-31 23:59:59. Oracle ale podporuje širší rozsah hodnoty DateTime (například BC století nebo min/s>59), což vede k selhání v ADF.

- **Doporučení**: 

    Spusťte `select dump(<column name>)` a ověřte, zda je hodnota v poli Oracle v rozsahu ADF. 

    Pokud chcete ve výsledku znát sekvenci bajtů, zkontrolujte prosím https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle .


## <a name="orc-format"></a>Formát ORC

### <a name="error-code--orcjavainvocationexception"></a>Kód chyby: OrcJavaInvocationException

- **Zpráva**: `An error occurred when invoking java, message: %javaException;.`

- **Příčina**: Pokud chybová zpráva obsahuje "Java. lang. OutOfMemory", "prostor haldy jazyka Java" a "doubleCapacity", obvykle se jedná o problém se správou paměti ve staré verzi prostředí Integration runtime.

- **Doporučení**: pokud používáte Integration runtime pro místní hostování, navrhněte upgrade na nejnovější verzi.

- **Příčina**: Pokud chybová zpráva obsahuje "Java. lang. OutOfMemory", modul runtime integrace nemá dostatek prostředků ke zpracování těchto souborů.

- **Doporučení**: Omezte souběžné běhy prostředí Integration runtime. Pro Integration Runtime v místním prostředí, Škálujte až do výkonného počítače s pamětí rovnou nebo větší než 8 GB.

- **Příčina**: Pokud chybová zpráva obsahuje ' NullPointerReference ', je možné, že se jedná o přechodnou chybu.

- **Doporučení**: zkuste to znovu. Pokud se problém opakuje, obraťte se prosím na podporu.

- **Příčina**: Pokud chybová zpráva obsahuje ' BufferOverflowException ', je možné, že se jedná o přechodnou chybu.

- **Doporučení**: zkuste to znovu. Pokud se problém opakuje, obraťte se prosím na podporu.

- **Příčina**: Pokud chybová zpráva obsahuje text "Java. lang. ClassCastException:org. Apache. Hadoop. podregistr. serde2. IO. HiveCharWritable nelze přetypovat na org. Apache. Hadoop. IO. text", jedná se o problém s převodem typu uvnitř Java Runtime. Obvykle to způsobuje, že zdrojová data nemůžou být v běhovém prostředí Java správně zpracována.

- **Doporučení**: Jedná se o problém s daty. Zkuste použít řetězec místo typu char/varchar v ORC formátu dat.

### <a name="error-code--orcdatetimeexceedlimit"></a>Kód chyby: OrcDateTimeExceedLimit

- **Zpráva**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Příčina**: Pokud je hodnota datetime "0001-01-01 00:00:00", může to být způsobeno rozdílem mezi juliánském kalendářem a gregoriánským kalendářem. https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates.

- **Doporučení**: ověřte hodnotu taktů a vyhněte se použití hodnoty datetime "0001-01-01 00:00:00".


## <a name="parquet-format"></a>Formát Parquet

### <a name="error-code--parquetjavainvocationexception"></a>Kód chyby: ParquetJavaInvocationException

- **Zpráva**: `An error occurred when invoking java, message: %javaException;.`

- **Příčina**: Pokud chybová zpráva obsahuje "Java. lang. OutOfMemory", "prostor haldy jazyka Java" a "doubleCapacity", obvykle se jedná o problém se správou paměti ve staré verzi prostředí Integration runtime.

- **Doporučení**: pokud používáte Integration runtime v místním prostředí a verze je starší než 3.20.7159.1, navrhuje se upgradovat na nejnovější verzi.

- **Příčina**: Pokud chybová zpráva obsahuje "Java. lang. OutOfMemory", modul runtime integrace nemá dostatek prostředků ke zpracování těchto souborů.

- **Doporučení**: Omezte souběžné běhy prostředí Integration runtime. Pro Integration Runtime v místním prostředí, Škálujte až do výkonného počítače s pamětí rovnou nebo větší než 8 GB.

- **Příčina**: Pokud chybová zpráva obsahuje ' NullPointerReference ', je možné, že se jedná o přechodnou chybu.

- **Doporučení**: zkuste to znovu. Pokud se problém opakuje, obraťte se prosím na podporu.


### <a name="error-code--parquetinvalidfile"></a>Kód chyby: ParquetInvalidFile

- **Zpráva**: `File is not a valid Parquet file.`

- **Příčina**: problém Parquet souboru.

- **Doporučení**: Ověřte, zda je vstup platným souborem Parquet.


### <a name="error-code--parquetnotsupportedtype"></a>Kód chyby: ParquetNotSupportedType

- **Zpráva**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Příčina**: formát Parquet není v Azure Data Factory podporován.

- **Doporučení**: poklikejte na zdrojová data. Podívejte se na dokument: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Kód chyby: ParquetMissedDecimalPrecisionScale

- **Zpráva**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Příčina**: zkuste analyzovat přesnost čísel a škálování, ale nejsou k dispozici žádné informace.

- **Doporučení**: ' source ' nevrací správnou přesnost a škálování. Podívejte se na přesnost a měřítko sloupce problému.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Kód chyby: ParquetInvalidDecimalPrecisionScale

- **Zpráva**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Příčina**: schéma je neplatné.

- **Doporučení**: Ověřte přesnost a měřítko sloupce problému.


### <a name="error-code--parquetcolumnnotfound"></a>Kód chyby: ParquetColumnNotFound

- **Zpráva**: `Column %column; does not exist in Parquet file.`

- **Příčina**: zdrojové schéma se neshoduje se schématem jímky.

- **Doporučení**: Ověřte the'mappings v aktivitě. Ujistěte se, že zdrojový sloupec lze namapovat do pravého sloupce jímky.


### <a name="error-code--parquetinvaliddataformat"></a>Kód chyby: ParquetInvalidDataFormat

- **Zpráva**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Příčina**: data nejde převést na typ zadaný v mapování. Source.

- **Doporučení**: poklikejte na zdrojová data nebo zadejte správný datový typ pro tento sloupec v mapování sloupců aktivity kopírování. Podívejte se na dokument: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Kód chyby: ParquetDataCountNotMatchColumnCount

- **Zpráva**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Příčina**: Neshoda počtu zdrojových sloupců a sloupců jímky

- **Doporučení**: typ zdrojového sloupce dvojité kontroly je stejný jako počet sloupců jímky v mapování.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Kód chyby: ParquetDataTypeNotMatchColumnType

- **Zpráva**: datový typ% srcType; se neshoduje s daným typem sloupce% dstType;. ve sloupci% columnIndex;.

- **Příčina**: data ze zdroje nejde převést na typované definované v jímky.

- **Doporučení**: Zadejte správný typ v mapování. jímka.


### <a name="error-code--parquetbridgeinvaliddata"></a>Kód chyby: ParquetBridgeInvalidData

- **Zpráva**: `%message;`

- **Příčina**: hodnota dat nad omezením

- **Doporučení**: zkuste to znovu. Pokud se problém opakuje, kontaktujte nás prosím.


### <a name="error-code--parquetunsupportedinterpretation"></a>Kód chyby: ParquetUnsupportedInterpretation

- **Zpráva**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Příčina**: nepodporováný scénář

- **Doporučení**: ' ParquetInterpretFor ' by neměl být ' sparkSql '.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Kód chyby: ParquetUnsupportFileLevelCompressionOption

- **Zpráva**: `File level compression is not supported for Parquet.`

- **Příčina**: nepodporováný scénář

- **Doporučení**: Odeberte ' Argument ' v datové části.


### <a name="error-code--usererrorjniexception"></a>Kód chyby: UserErrorJniException

- **Zpráva**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Příčina**: JVM nejde vytvořit, protože jsou nastavené neplatné (globální) argumenty.

- **Doporučení**: Přihlaste se k počítači, který je hostitelem **všech uzlů** v místním prostředí IR. Ověřte, zda je systémová proměnná správně nastavena takto: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Restartujte všechny uzly IR a pak znovu spusťte kanál.


### <a name="arithmetic-overflow"></a>Aritmetické přetečení

- **Příznaky**: při kopírování souborů Parquet se stala chybová zpráva: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Příčina**: při kopírování souborů z Oracle do Parquet je v současné době k dispozici pouze desítková hodnota přesnosti <= 38 a délka celé části <= 20. 

- **Řešení**: jako alternativní řešení můžete převést sloupce s takovým problémem na VARCHAR2.


### <a name="no-enum-constant"></a>Žádná konstanta výčtu

- **Příznaky**: při kopírování dat do formátu Parquet došlo k chybové zprávě: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` nebo `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Příčina**: 

    Příčinou může být prázdný znak nebo nepodporované znaky (například,; {} () \n\t =) v názvu sloupce, protože Parquet nepodporuje takový formát. 

    Například název sloupce, jako je *Contoso (test)* , analyzuje typ v závorkách z [kódu](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . Tato chyba se vyvolá, protože neexistuje žádný takový typ testu.

    Pokud chcete kontrolovat podporované typy, můžete je [tady](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)zaškrtnout.

- **Řešení**: 

    - Pokud název sloupce jímky obsahuje prázdné znaky, poklikejte na něj.

    - Pokud je jako název sloupce použit první řádek s prázdnými znaky, poklepejte na něj.

    - Dvakrát ověřte, zda je typ OriginalType podporován nebo ne. Zkuste vyhnout těmto speciálním symbolům `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code--restsinkcallfailed"></a>Kód chyby: RestSinkCallFailed

- **Zpráva**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Příčina**: k této chybě dochází, když Azure Data Factory komunikovat s koncovým bodem REST přes protokol HTTP a operace požadavku selže.

- **Doporučení**: Podívejte se na stavový kód HTTP \ zpráva v chybové zprávě a opravte problém vzdáleného serveru.

### <a name="unexpected-network-response-from-rest-connector"></a>Neočekávaná odezva sítě z konektoru REST

- **Příznaky**: koncový bod někdy obdrží neočekávanou odpověď (400/401/403/500) z konektoru REST.

- **Příčina**: zdrojový konektor REST při vytváření požadavku HTTP používá jako parametry adresu URL a metodu/hlavičku/záhlaví/tělo protokolu HTTP z propojené služby, datové sady nebo zdroje kopie. Problém je pravděpodobně způsoben některými chybami v jednom nebo více zadaných parametrech.

- **Řešení**: 
    - V příkazovém okně použijte ' kudrlinkou ' a ověřte, zda je parametr příčinou nebo ne (**přijmout** a zda mají být hlavičky **uživatelských agentů** vždy zahrnuty):
        ```
        curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>
        ```
      Pokud příkaz vrátí stejnou neočekávanou odpověď, opravte prosím výše uvedené parametry pomocí ' kudrlinkou ', dokud nevrátí očekávanou odpověď. 

      K pokročilejšímu využití příkazu můžete také použít "kudrlinkou--Help".

    - Pokud jenom konektor ADF REST vrátí neočekávanou odpověď, obraťte se prosím na podporu Microsoftu, kde najdete další řešení potíží.
    
    - Upozorňujeme, že ' kudrlinkou ' nemusí být vhodný k reprodukování problému s ověřením certifikátu SSL. V některých scénářích byl příkaz ' kudrlinkou ' proveden úspěšně, aniž by došlo k potížím s ověřením certifikátu SSL. Pokud se ale v prohlížeči spustí stejná adresa URL, nevrátí se ve skutečnosti žádný certifikát SSL, který by klient navázal na vytvoření vztahu důvěryhodnosti se serverem.

      U výše uvedeného případu doporučujeme používat nástroje jako **post** a **Fiddler** .


## <a name="sftp"></a>SFTP

#### <a name="error-code--sftpoperationfail"></a>Kód chyby: SftpOperationFail

- **Zpráva**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Příčina**: problém s voláním operace SFTP.

- **Doporučení**: Projděte si podrobnou chybu z SFTP.


### <a name="error-code--sftprenameoperationfail"></a>Kód chyby: SftpRenameOperationFail

- **Zpráva**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, please set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Příčina**: váš server SFTP nepodporuje přejmenování dočasného souboru.

- **Doporučení**: Pokud chcete zakázat nahrávání do dočasného souboru, nastavte v jímky kopírování useTempFileRename na hodnotu false.


### <a name="error-code--sftpinvalidsftpcredential"></a>Kód chyby: SftpInvalidSftpCredential

- **Zpráva**: `Invalid Sftp credential provided for '%type;' authentication type.`

- **Příčina**: obsah privátního klíče je načtený z integrace/SDK, ale není správně kódovaný.

- **Doporučení**:  

    Pokud je obsah privátního klíče z integrace a původní soubor klíče může fungovat, když ho zákazník nahraje přímo do propojené služby SFTP

    Viz https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication , obsah PrivateKey je obsah privátního klíče SSH kódovaný v kódování Base64.

    Zakódovat **celý obsah původního souboru privátního klíče** pomocí kódování Base64 a uložit kódovaný řetězec do integrace. Původní soubor privátního klíče je ten, který může fungovat na propojené službě SFTP, pokud kliknete na Odeslat ze souboru.

    Tady je několik ukázek používaných k vygenerování řetězce:

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

    - Použít nástroj pro převod Base64 třetí strany

        https://www.base64encode.org/Doporučuje se používat nástroje, jako jsou doporučené.

- **Příčina**: je vybraný špatný formát obsahu klíče

- **Doporučení**:  

    Privátní klíč SSH formátu PKCS # 8 (začíná na-----začínat ŠIFROVANÝm PRIVÁTNÍm klíčem-----) se v současné době nepodporuje pro přístup k serveru SFTP v ADF. 

    Spusťte následující příkazy, abyste klíč převedli do tradičního formátu klíče SSH (začněte textem "-----začít používat privátní klíč RSA-----"):

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Příčina**: neplatné přihlašovací údaje nebo obsah privátního klíče

- **Doporučení**: poklikejte na nástroje, jako je WinSCP, a zkontrolujte, jestli je soubor klíče nebo heslo správné.

### <a name="sftp-copy-activity-failed"></a>Aktivita kopírování SFTP se nezdařila.

- **Příznaky**: kód chyby: UserErrorInvalidColumnMappingColumnNotFound. Chybová zpráva: `Column &apos;AccMngr&apos; specified in column mapping cannot be found in source data.`

- **Příčina**: zdroj neobsahuje sloupec s názvem "AccMngr".

- **Řešení**: poklikejte na to, jak datovou sadu nakonfiguroval, pomocí mapování sloupce cílové datové sady, aby se ověřilo, jestli existuje takový sloupec "AccMngr".


### <a name="error-code--sftpfailedtoconnecttosftpserver"></a>Kód chyby: SftpFailedToConnectToSftpServer

- **Zpráva**: `Failed to connect to Sftp server '%server;'.`

- **Příčina**: Pokud chybová zpráva obsahuje hodnotu vypršel časový limit operace čtení soketu po 30000 milisekundách, může to být, že pro server SFTP se používá nesprávný typ propojené služby, jako je například použití propojené služby FTP pro připojení k serveru SFTP.

- **Doporučení**: Ověřte port cílového serveru. Protokol SFTP používá ve výchozím nastavení port 22.

- **Příčina**: Pokud chybová zpráva obsahuje zprávu "odpověď serveru neobsahuje identifikaci protokolu SSH", může to být způsobeno tím, že server SFTP omezuje připojení. ADF vytvoří více připojení, která se budou stahovat ze serveru SFTP paralelně, a v některých případech bude zasáhnout omezení serveru SFTP. V podstatě jiný server vrátí jinou chybu, když dojde k omezení úspěšnosti.

- **Doporučení**:  

    Zadejte maximální souběžné připojení datové sady SFTP k 1 a znovu spusťte kopii. Pokud je to úspěšné, můžeme se ujistit, že je to příčina omezování.

    Pokud chcete zvýšit propustnost, obraťte se prosím na správce protokolu SFTP, aby zvýšil limit počtu souběžných připojení, nebo přidejte pod seznamem povolených IP adres seznam povolených IP adres:

    - Pokud používáte spravované INFRAČERVENé prostředí, přidejte prosím [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653).
      Pokud nechcete do seznamu povolených serverů SFTP přidat velký seznam rozsahů IP adres, můžete nainstalovat místní prostředí IR.

    - Pokud používáte prostředí IR v místním prostředí, přidejte prosím do seznamu povolených SHIR IP adresu počítače, která je nainstalovaná.


## <a name="sharepoint-online-list"></a>Seznam služby SharePoint Online

### <a name="error-code--sharepointonlineauthfailed"></a>Kód chyby: SharePointOnlineAuthFailed

- **Zpráva**: `The access token generated failed, status code: %code;, error message: %message;.`

- **Příčina**: ID instančního objektu a klíč nemusí být správně nastavené.

- **Doporučení**: Ověřte registrovanou aplikaci (ID instančního objektu) a klíč, jestli je správně nastavená.


## <a name="xml-format"></a>Formát XML

### <a name="error-code--xmlsinknotsupported"></a>Kód chyby: XmlSinkNotSupported

- **Zpráva**: `Write data in xml format is not supported yet, please choose a different format!`

- **Příčina**: používala datovou sadu XML jako datovou sadu jímky v aktivitě kopírování

- **Doporučení**: použijte datovou sadu v jiném formátu jako jímku kopírování.


### <a name="error-code--xmlattributecolumnnameconflict"></a>Kód chyby: XmlAttributeColumnNameConflict

- **Zpráva**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Příčina**: byla použita předpona atributu, která způsobila konflikt.

- **Doporučení**: nastavte jinou hodnotu vlastnosti "attributePrefix".


### <a name="error-code--xmlvaluecolumnnameconflict"></a>Kód chyby: XmlValueColumnNameConflict

- **Zpráva**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Příčina**: jako název sloupce pro hodnotu elementu byl použit jeden z názvů podřízených prvků.

- **Doporučení**: nastavte jinou hodnotu vlastnosti "valueColumn".


### <a name="error-code--xmlinvalid"></a>Kód chyby: XmlInvalid

- **Zpráva**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Příčina**: vstupní soubor XML nemá správný formát.

- **Doporučení**: opravte soubor XML, aby byl správně vytvořen.


## <a name="general-copy-activity-error"></a>Obecná chyba aktivity kopírování

### <a name="error-code--jrenotfound"></a>Kód chyby: JreNotFound

- **Zpráva**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Příčina**: modul runtime integrace v místním prostředí nemůže najít modul runtime Java. Modul Java Runtime je vyžadován pro čtení konkrétního zdroje.

- **Doporučení**: Podívejte se do prostředí Integration runtime, referenční dokument: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Kód chyby: WildcardPathSinkNotSupported

- **Zpráva**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Příčina**: datová sada jímky nepodporuje zástupný znak.

- **Doporučení**: Ověřte datovou sadu jímky a opravte cestu bez zástupných hodnot.


### <a name="fips-issue"></a>Problém FIPS

- **Příznaky**: aktivita kopírování se na počítači Integration runtime v místním prostředí FIPS nezdařila s chybovou zprávou `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` . K tomu může dojít při kopírování dat pomocí konektorů, jako je Azure Blob, SFTP atd.

- **Příčina**: FIPS (Federal Information Processing Standards) definuje určitou sadu kryptografických algoritmů, které je povoleno použít. Když je v počítači povolený režim FIPS, některé kryptografické třídy, na kterých aktivita kopírování závisí, jsou v některých scénářích blokované.

- **Řešení**: z [tohoto článku](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)můžete získat informace o aktuální situaci režimu FIPS v systému Windows a vyhodnotit, jestli můžete FIPS zakázat na počítači Integration runtime v místním prostředí.

    Na druhé straně, pokud chcete nechat Azure Data Factory obejít FIPS a spustit aktivitu úspěšně, můžete postupovat takto:

    1. Otevřete složku, ve které je místně hostovaný Integration Runtime, obvykle v části `C:\Program Files\Microsoft Integration Runtime\<IR version>\Shared` .

    2. Otevřete diawp.exe.config a přidejte `<enforceFIPSPolicy enabled="false"/>` do `<runtime>` části podobné následujícímu.

        ![Zákaz FIPS](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Restartujte počítač Integration Runtime v místním prostředí.


## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka s otázkou Microsoft Q&](/answers/topics/azure-data-factory.html)
*  [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
