---
title: Řešení potíží s konektory Azure Data Factory | Microsoft Docs
description: Naučte se řešit potíže s konektorem v Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: bea5191063cf673f6b1395d46a15536e80b0aa30
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143502"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Řešení potíží s konektory Azure Data Factory

Tento článek popisuje běžné metody řešení potíží pro konektory v Azure Data Factory.

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Chybová zpráva: Vzdálený server vrátil chybu: (403) zakázáno

- **Příznaky**: Aktivita kopírování se nezdařila s následující chybou: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Příčina:** Jednou z možných příčin je, že instanční objekt nebo spravovaná identita nemají oprávnění pro přístup k určité složce nebo souboru.

- **Rozlišení**: Udělte odpovídající oprávnění ke všem složkám a podsložkám, které potřebujete zkopírovat. Přečtěte si [Tento dokument](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Chybová zpráva: Nepovedlo se získat přístupový token pomocí instančního objektu. Chyba ADAL: service_unavailable

- **Příznaky**: aktivita kopírování se nezdařila s následující chybou:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Příčina:** Pokud server tokenu služby (STS), který je vlastněn Azure Active Directory, není k dispozici, tedy příliš zaneprázdněný zpracování požadavků, vrátí chybu HTTP 503. 

- **Rozlišení**: Po několika minutách znovu spusťte aktivitu kopírování.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Chybová zpráva: Převod se nezdařil při převodu ze znakového řetězce na typ uniqueidentifier.

- **Příznaky**: Když kopírujete data z tabulkového zdroje dat (například SQL Server) do Azure SQL Data Warehouse pomocí připravené kopie a základu, zobrazí se následující chyba:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Příčina:** Základ Azure SQL Data Warehouse nemůže převést prázdný řetězec na identifikátor GUID.

- **Rozlišení**: V poli jímka aktivity kopírování v části nastavení základní hodnoty nastavte možnost**použít výchozí typ**na hodnotu NEPRAVDA.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Chybová zpráva: Očekávaný datový typ: DECIMAL (x, x), problematická hodnota

- **Příznaky**: Když zkopírujete data z tabulkového zdroje dat (například SQL Server) do SQL DW pomocí připravené kopie a základu, zobrazí se následující chyba:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Příčina:** Základ Azure SQL Data Warehouse nemůže vložit prázdný řetězec (hodnota null) do desetinného sloupce.

- **Rozlišení**: V poli jímka aktivity kopírování v části nastavení základní hodnoty nastavte možnost**použít výchozí typ**na hodnotu NEPRAVDA.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Chybová zpráva: Zpráva o výjimce Java: HdfsBridge:: CreateRecordReader

- **Příznaky**: Data kopírujete do Azure SQL Data Warehouse s použitím základny a dojde k následující chybě:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Příčina:** Možnou příčinou je, že schéma (celková šířka sloupce) je příliš velké (větší než 1 MB). Ověřte schéma cílové tabulky SQL DW přidáním velikosti všech sloupců:

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

- **Rozlišení**: Zmenšit šířku sloupce tak, aby byla menší než 1 MB

- Nebo použijte přístup k hromadnému vložení zakázáním základny

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Chybová zpráva: Podmínka zadaná pomocí podmíněných hlaviček HTTP není splněná.

- **Příznaky**: Použijete dotaz SQL k vyžádání dat z Azure SQL Data Warehouse a dojde k následující chybě:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Příčina:** Azure SQL Data Warehouse problému při dotazování externí tabulky v Azure Storage.

- **Rozlišení**: Spusťte stejný dotaz v SSMS a zkontrolujte, jestli vidíte stejný výsledek. Pokud ano, otevřete lístek podpory pro Azure SQL Data Warehouse a zadejte svůj server SQL DW a název databáze pro další odstraňování potíží.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Chybová zpráva: Velikost požadavku je příliš velká.

- **Příznaky**: Data kopírujete do Azure Cosmos DB s výchozí velikostí dávky pro zápis a chyba volání *"**Velikost požadavku je příliš velká**"* .

- **Příčina:** Cosmos DB omezuje velikost jednoho požadavku na 2 MB. Vzorec je, velikost požadavku = jedna velikost dokumentu * velikost dávky zápisu. Pokud je velikost dokumentu velká, výchozí chování bude mít za následek příliš velkou velikost požadavku. Můžete vyladit velikost dávky zápisu.

- **Rozlišení**: V části jímka aktivity kopírování snižte hodnotu "zapsat velikost dávky" (výchozí hodnota je 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Chybová zpráva: Porušení omezení jedinečného indexu

- **Příznaky**: Při kopírování dat do Cosmos DB se zobrazí následující chyba:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception &#61; Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Příčina:** K dispozici jsou dvě možné příčiny:

    - Pokud použijete funkci **Vložit** jako zápis, tato chyba znamená, že zdrojová data mají řádky nebo objekty se stejným ID.

    - Pokud použijete **Upsert** jako chování zápisu a nastavíte do kontejneru jiný jedinečný klíč, tato chyba znamená, že zdrojová data budou mít řádky nebo objekty s odlišnými identifikátory, ale stejnou hodnotu pro definovaný jedinečný klíč.

- **Rozlišení**: 

    - V případě cause1 nastavte **Upsert** jako chování zápisu.
    - V případě příčiny 2 se ujistěte, že každý dokument má odlišnou hodnotu pro definovaný jedinečný klíč.

### <a name="error-message-request-rate-is-large"></a>Chybová zpráva: Rychlost požadavků je velká.

- **Příznaky**: Při kopírování dat do Cosmos DB se zobrazí následující chyba:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Příčina:** Využité jednotky žádosti jsou vyšší než dostupný RU nakonfigurovaná v Cosmos DB. Přečtěte si, jak Cosmos DB [](../cosmos-db/request-units.md#request-unit-considerations)počítá ru.

- **Rozlišení**: Tady jsou dvě řešení:

    1. Zvyšte hodnotu v **kontejneru ru** na větší hodnotu v Cosmos DB, což vylepší výkon aktivity kopírování, i když se v Cosmos DB účtuje větší náklady. 

    2. Zmenšete **writeBatchSize** na menší hodnotu (například 1000) a nastavte **parallelCopies** na menší hodnotu, jako je například 1, což způsobí, že kopírování bude mít horší výkon, než je aktuální, ale nebudou se vám účtovat žádné náklady Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>V mapování sloupce chybí sloupec.

- **Příznaky**: Při importu schématu pro Cosmos DB pro mapování sloupce chybí některé sloupce. 

- **Příčina:** ADF odvodí schéma z prvních 10 Cosmos DB dokumentů. Pokud některé sloupce nebo vlastnosti v těchto dokumentech nemají hodnotu, nebudou zjištěny pomocí ADF, takže se nebudou zobrazovat.

- **Rozlišení**: Můžete vyladit dotaz tak, aby se sloupec vynutil, aby se zobrazil v sadě výsledků s prázdnou hodnotou: (Předpokládejme, že ve prvních 10 dokumentech chybí sloupec nemožný). Případně můžete ručně přidat sloupec pro mapování.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Chybová zpráva: GuidRepresentation pro čtecí zařízení je CSharpLegacy

- **Příznaky**: Při kopírování dat z Cosmos DB MongoAPI/MongoDB s polem UUID se zobrazí následující chyba:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Příčina:** Existují dva způsoby, jak vyjádřit UUID v BSON-UuidStardard a UuidLegacy. Ve výchozím nastavení se pro čtení dat používá UuidLegacy. Pokud jsou vaše data UUID v MongoDB UuidStandard, bude se vám vyrazit chyba.

- **Rozlišení**: V připojovacím řetězci MongoDB přidejte možnost "**uuidRepresentation = Standard**". Další informace najdete v tématu [připojovací řetězec MongoDB](connector-mongodb.md#linked-service-properties).

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Chybová zpráva: Pro typ ověřování SshPublicKey se zadaly neplatné přihlašovací údaje SFTP.

- **Příznaky**: Použijete `SshPublicKey` ověřování a narazíte na následující chybu:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **Příčina:** Existují 3 možné příčiny:

    1. Pokud k vytvoření propojené služby SFTP použijete uživatelské rozhraní pro vytváření ADF, znamená to, že privátní klíč, který se rozhodnete použít, nemá správný formát. Můžete použít formát PKCS # 8 privátního klíče SSH, zatímco ADF podporuje jenom tradiční formát klíče SSH. Konkrétně rozdíl mezi formátem PKCS # 8 a tradičním klíčem je obsah klíče PKCS # 8 začíná řetězcem " *-----zahájit šifrování privátního klíče-----* ", zatímco formát tradičního klíče začíná na " *-----zahájit-----privátního klíče RSA*".
    2. Pokud používáte Azure Key Vault k uložení obsahu privátního klíče nebo k vytvoření propojené služby SFTP pomocí programového způsobu, tato chyba znamená, že obsah privátního klíče je nesprávný, pravděpodobně není kódovaný v kódování Base64.
    3. Neplatný obsah přihlašovacích údajů nebo privátního klíče.

- **Rozlišení**: 

    - V případě #1 Spusťte následující příkazy, abyste klíč převedli na formát tradičního klíče, a pak ho použijete v uživatelském rozhraní pro vytváření ADF.

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - V případě příčiny #2 pro generování takového řetězce může zákazník použít méně než 2 způsoby:
    - Pomocí nástroje pro převod Base64 třetí strany: vložte obsah celého privátního klíče do nástrojů, jako je kódování [Base64 a dekódování](https://www.base64encode.org/), zakódovat ho do řetězce formátu Base64 a pak tento řetězec vložte do trezoru klíčů, případně tuto hodnotu použijte pro programové vytváření propojené služby SFTP.
    - Pomocí C# kódu:

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Pro příčinu #3 dvakrát zkontrolujte, jestli je soubor klíče nebo heslo správné, pomocí jiných nástrojů, abyste ověřili, jestli ho můžete správně použít pro přístup k serveru SFTP.

## <a name="next-steps"></a>Další postup

Pro další nápovědu k řešení potíží zkuste tyto prostředky:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa k Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)



