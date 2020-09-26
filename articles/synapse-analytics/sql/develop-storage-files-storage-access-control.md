---
title: Řízení přístupu účtu úložiště pro SQL na vyžádání (Preview)
description: Popisuje, jak SQL na vyžádání (Preview) přistupuje k Azure Storage a jak můžete řídit přístup k úložišti pro SQL na vyžádání v Azure synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: b3df83bc68cfa1952238b792fceffe57c0dc0ce7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288932"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview"></a>Řízení přístupu účtu úložiště pro SQL na vyžádání (Preview)

Dotaz na vyžádání SQL čte soubory přímo z Azure Storage. Oprávnění pro přístup k souborům v Azure Storage se řídí na dvou úrovních:
- **Úroveň úložiště** – uživatel by měl mít oprávnění k přístupu k základním souborům úložiště. Správce úložiště by měl objektu zabezpečení služby Azure AD umožňovat čtení a zápis souborů nebo generování klíče SAS, který se bude používat pro přístup k úložišti.
- **Úroveň služby SQL** – uživatel by měl mít `SELECT` oprávnění ke čtení dat z [externí tabulky](develop-tables-external-tables.md) nebo `ADMINISTER BULK ADMIN` oprávnění ke spuštění `OPENROWSET` a také oprávnění k použití přihlašovacích údajů, které se použijí pro přístup k úložišti.

Tento článek popisuje typy přihlašovacích údajů, které můžete použít, a informace o tom, jak jsou pro uživatele SQL a Azure AD vyhledány přihlašovací údaje.

## <a name="supported-storage-authorization-types"></a>Podporované typy autorizace úložiště

Uživatel, který se přihlásil k prostředku na vyžádání SQL, musí mít oprávnění k přístupu k souborům v Azure Storage, pokud nejsou veřejně dostupné, a dotazování na ně. Pro přístup k neveřejné [identitě uživatelů](?tabs=user-identity)úložiště, [sdílenému přístupovému podpisu](?tabs=shared-access-signature)a [spravované identitě](?tabs=managed-identity)můžete použít tři typy autorizace.

> [!NOTE]
> **Předávací služba Azure AD** je výchozím chováním při vytváření pracovního prostoru.

### <a name="user-identity"></a>[Identita uživatele](#tab/user-identity)

**Identita uživatele**, známá taky jako "předávací služba Azure AD", je autorizační typ, kde se k autorizaci přístupu k datům používá identita uživatele Azure AD, který je přihlášený k SQL na vyžádání. Před přístupem k datům musí správce Azure Storage udělit oprávnění k uživateli Azure AD. Jak je uvedeno v následující tabulce, není podporováno pro typ uživatele SQL.

> [!IMPORTANT]
> Abyste mohli používat vaši identitu pro přístup k datům, musíte mít roli vlastníka dat objektu BLOB úložiště/Přispěvatel/čtenář.
> I v případě, že jste vlastníkem účtu úložiště, je stále nutné přidat sami sebe do jedné z rolí dat objektu BLOB úložiště.
>
> Další informace o řízení přístupu v Azure Data Lake Store Gen2 naleznete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md) článku.
>

### <a name="shared-access-signature"></a>[Sdílený přístupový podpis](#tab/shared-access-signature)

**Sdílený přístupový podpis (SAS)** poskytuje delegovaný přístup k prostředkům v účtu úložiště. Pomocí SAS může zákazník udělit klientům přístup k prostředkům v účtu úložiště bez použití klíčů účtu pro sdílení. SAS vám poskytuje podrobnější kontrolu nad typem přístupu, který udělíte klientům, kteří mají SAS, včetně intervalu platnosti, udělených oprávnění, přijatelného rozsahu IP adres a přijatelného protokolu (HTTPS/HTTP).

Token SAS můžete získat tak, že přejdete na **účet úložiště > Azure Portal – > sdílený přístup – > konfigurace oprávnění – > generovat SAS a připojovací řetězec.**

> [!IMPORTANT]
> Při vygenerování tokenu SAS obsahuje znak otazníku (?) na začátku tokenu. Pokud chcete použít token v SQL na vyžádání, musíte při vytváření přihlašovacích údajů odebrat otazník (?). Příklad:
>
> Token SAS:? sv = 2018-03-28&SS = bfqt&SRT aplikace = SCO&SP = rwdlacup&se = 2019-04-18T20:42:12Z&St = 2019-04-18T12:42:12Z&spr = https&SIG = lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78% 3D

Pokud chcete povolit přístup pomocí tokenu SAS, musíte vytvořit přihlašovací údaje v oboru databáze nebo na serveru. 

### <a name="managed-identity"></a>[Spravovaná identita](#tab/managed-identity)

**Spravovaná identita** se také označuje jako MSI. Je to funkce Azure Active Directory (Azure AD), která poskytuje služby Azure pro SQL na vyžádání. Také nasadí automaticky spravovanou identitu ve službě Azure AD. Tato identita se dá použít k autorizaci žádosti o přístup k datům v Azure Storage.

Před přístupem k datům musí správce Azure Storage udělit oprávnění ke spravované identitě pro přístup k datům. Udělení oprávnění pro spravovanou identitu se provádí stejným způsobem jako udělení oprávnění jinému uživateli Azure AD.

### <a name="anonymous-access"></a>[Anonymní přístup](#tab/public-access)

Můžete přistupovat k veřejně dostupným souborům umístěným na účtech Azure Storage, které [povolují anonymní přístup](/azure/storage/blobs/storage-manage-access-to-resources).

---

### <a name="supported-authorization-types-for-databases-users"></a>Podporované typy autorizace pro databáze uživatelů

V následující tabulce najdete dostupné typy autorizace:

| Typ autorizace                    | *Uživatel SQL*    | *Uživatel služby Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [Identita uživatele](?tabs=user-identity#supported-storage-authorization-types)       | Nepodporováno | Podporováno      |
| [VEDE](?tabs=shared-access-signature#supported-storage-authorization-types)       | Podporováno     | Podporováno      |
| [Spravovaná identita](?tabs=managed-identity#supported-storage-authorization-types) | Nepodporováno | Podporováno      |

### <a name="supported-storages-and-authorization-types"></a>Podporované typy úložišť a autorizace

Můžete použít následující kombinace autorizačních a Azure Storagech typů:

| Typ autorizace  | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| [VEDE](?tabs=shared-access-signature#supported-storage-authorization-types)    | Podporováno\*      | Nepodporováno   | Podporováno\*     |
| [Spravovaná identita](?tabs=managed-identity#supported-storage-authorization-types) | Podporováno      | Podporováno        | Podporováno     |
| [Identita uživatele](?tabs=user-identity#supported-storage-authorization-types)    | Podporováno\*      | Podporováno\*        | Podporováno\*     |

\* Token SAS a identita Azure AD lze použít pro přístup k úložišti, které není chráněno bránou firewall.

> [!IMPORTANT]
> Při přístupu k úložišti chráněnému bránou firewall se dá použít jenom spravovaná identita. Je nutné, aby byly [povoleny důvěryhodné služby společnosti Microsoft... nastavení](../../storage/common/storage-network-security.md#trusted-microsoft-services) a explicitně [přiřadíte roli Azure](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights) pro [spravovanou identitu přiřazenou systémem](../../active-directory/managed-identities-azure-resources/overview.md) pro danou instanci prostředku. V takovém případě rozsah přístupu pro instanci odpovídá roli Azure přiřazené spravované identitě.
>

## <a name="credentials"></a>Přihlašovací údaje

Pokud chcete zadat dotaz na soubor umístěný v Azure Storage, vyžaduje koncový bod SQL na vyžádání přihlašovací údaje, které obsahují ověřovací údaje. Používají se dva typy přihlašovacích údajů:
- PŘIHLAŠOVACÍ údaje na úrovni serveru se používají pro dotazy ad-hoc prováděné pomocí `OPENROWSET` funkce. Název přihlašovacích údajů se musí shodovat s adresou URL úložiště.
- Pro externí tabulky se používá pověření s ROZSAHem databáze. Externí tabulka odkazuje na `DATA SOURCE` přihlašovací údaje, které by měly být použity pro přístup k úložišti.

Pokud chcete uživateli povolit vytvoření nebo vyřazení přihlašovacích údajů, může správce udělit nebo odepřít změnu všech oprávnění k pověření uživateli:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Uživatelé databáze, kteří přistupují k externímu úložišti, musí mít oprávnění k používání přihlašovacích údajů.

### <a name="grant-permissions-to-use-credential"></a>Udělení oprávnění k použití přihlašovacích údajů

Aby uživatel mohl používat přihlašovací údaje, musí mít `REFERENCES` oprávnění pro konkrétní přihlašovací údaje. Chcete-li udělit `REFERENCES` oprávnění pro storage_credential specific_user, proveďte následující:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Aby bylo zajištěno bezproblémové předávací prostředí Azure AD, budou mít všichni uživatelé ve výchozím nastavení právo používat `UserIdentity` přihlašovací údaje.

## <a name="server-scoped-credential"></a>Přihlašovací údaje v oboru serveru

Přihlašovací údaje v oboru serveru se používají, když funkce přihlášení `OPENROWSET` k SQL funguje bez `DATA_SOURCE` čtení souborů v některém účtu úložiště. Název přihlašovacích údajů v oboru serveru se **musí** shodovat s adresou URL úložiště Azure. Přihlašovací údaje se přidají spuštěním [Vytvoření přihlašovacích údajů](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Budete muset zadat argument název PŘIHLAŠOVACÍch údajů. Musí odpovídat buď části cesty, nebo celé cestě k datům v úložišti (viz níže).

> [!NOTE]
> `FOR CRYPTOGRAPHIC PROVIDER`Argument není podporován.

PŘIHLAŠOVACÍ jméno na úrovni serveru musí odpovídat celé cestě k účtu úložiště (a volitelně kontejneru) v následujícím formátu: `<prefix>://<storage_account_path>/<storage_path>` . Cesty k účtu úložiště jsou popsané v následující tabulce:

| Externí zdroj dat       | Předpona | Cesta k účtu úložiště                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | HTTPS  | <storage_account>. blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | HTTPS  | <storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | HTTPS  | <storage_account>. dfs.core.windows.net              |

Přihlašovací údaje v oboru serveru umožňují přístup k úložišti Azure pomocí následujících typů ověřování:

### <a name="user-identity"></a>[Identita uživatele](#tab/user-identity)

Uživatelé Azure AD mají přístup ke všem souborům ve službě Azure Storage, pokud mají `Storage Blob Data Owner` `Storage Blob Data Contributor` roli, nebo `Storage Blob Data Reader` . Uživatelé Azure AD nepotřebují přihlašovací údaje pro přístup k úložišti. 

Uživatelé SQL nemůžou k přístupu k úložišti používat ověřování Azure AD.

### <a name="shared-access-signature"></a>[Sdílený přístupový podpis](#tab/shared-access-signature)

Následující skript vytvoří přihlašovací údaje na úrovni serveru, které můžou používat `OPENROWSET` funkce pro přístup k jakémukoli souboru v úložišti Azure pomocí tokenu SAS. Vytvořte toto přihlašovací údaje, aby se povolil objekt zabezpečení SQL, který spustí `OPENROWSET` funkci pro čtení souborů chráněných pomocí klíče SAS v úložišti Azure, které odpovídají adrese URL v názvu přihlašovacích údajů.

Exchange <*mystorageaccountname*> s vaším skutečným názvem účtu úložiště a> <*mystorageaccountcontainername* s aktuálním názvem kontejneru:

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="managed-identity"></a>[Spravovaná identita](#tab/managed-identity)

Následující skript vytvoří přihlašovací údaje na úrovni serveru, které můžou používat `OPENROWSET` funkce pro přístup k jakémukoli souboru v úložišti Azure pomocí identity spravované v pracovním prostoru.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

### <a name="public-access"></a>[Veřejný přístup](#tab/public-access)

Přihlašovací údaje v oboru databáze nejsou nutné k povolení přístupu k veřejně dostupným souborům. Vytvoří [zdroj dat bez pověření oboru databáze](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) pro přístup k veřejně dostupným souborům v Azure Storage.

---

## <a name="database-scoped-credential"></a>Přihlašovací údaje v rámci databáze

Přihlašovací údaje v oboru databáze se používají, když jakákoli instanční `OPENROWSET` funkce volá funkci `DATA_SOURCE` nebo vybere data z [externí tabulky](develop-tables-external-tables.md) , která nepřistupuje k veřejným souborům. Přihlašovací údaje v oboru databáze nemusí odpovídat názvu účtu úložiště. Použije se explicitně ve zdroji dat, který definuje umístění úložiště.

Přihlašovací údaje v rámci databáze umožňují přístup k úložišti Azure pomocí následujících typů ověřování:

### <a name="azure-ad-identity"></a>[Identita Azure AD](#tab/user-identity)

Uživatelé Azure AD mají přístup k jakémukoli souboru v úložišti Azure, pokud mají `Storage Blob Data Owner` roli aspoň, `Storage Blob Data Contributor` nebo `Storage Blob Data Reader` . Uživatelé Azure AD nepotřebují přihlašovací údaje pro přístup k úložišti.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

Uživatelé SQL nemůžou k přístupu k úložišti používat ověřování Azure AD.

### <a name="shared-access-signature"></a>[Sdílený přístupový podpis](#tab/shared-access-signature)

Následující skript vytvoří přihlašovací údaje, které se používají pro přístup k souborům v úložišti pomocí tokenu SAS zadaného v přihlašovacích údajích. Skript vytvoří ukázkový externí zdroj dat, který pomocí tohoto tokenu SAS přistupuje k úložišti.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```

### <a name="managed-identity"></a>[Spravovaná identita](#tab/managed-identity)

Následující skript vytvoří přihlašovací údaje v rámci databáze, které je možné použít k zosobnění aktuálního uživatele Azure AD jako spravované identity služby. Skript vytvoří ukázkový externí zdroj dat, který bude k přístupu k úložišti používat identitu pracovního prostoru.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

Přihlašovací údaje v oboru databáze se nemusí shodovat s názvem účtu úložiště, protože se explicitně použijí ve zdroji dat, který definuje umístění úložiště.

### <a name="public-access"></a>[Veřejný přístup](#tab/public-access)

Přihlašovací údaje v oboru databáze nejsou nutné k povolení přístupu k veřejně dostupným souborům. Vytvoří [zdroj dat bez pověření oboru databáze](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) pro přístup k veřejně dostupným souborům v Azure Storage.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

Přihlašovací údaje v oboru databáze se používají v externích zdrojích dat k určení, která metoda ověřování se použije pro přístup k tomuto úložišti:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Příklady

### <a name="access-a-publicly-available-data-source"></a>**Přístup k veřejně dostupnému zdroji dat**

Pomocí následujícího skriptu vytvořte tabulku, která přistupuje k veřejně dostupnému zdroji dat.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

Uživatel databáze může číst obsah souborů ze zdroje dat pomocí externí tabulky nebo funkce [OpenRowset](develop-openrowset.md) , která odkazuje na zdroj dat:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = [mysample],
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**Přístup ke zdroji dat pomocí přihlašovacích údajů**

Úpravou následujícího skriptu vytvořte externí tabulku, která přistupuje k Azure Storage pomocí tokenu SAS, identity uživatele Azure AD nebo spravované identity pracovního prostoru.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity or SAS token. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

Uživatel databáze může číst obsah souborů ze zdroje dat pomocí [externí tabulky](develop-tables-external-tables.md) nebo funkce [OpenRowset](develop-openrowset.md)  , která odkazuje na zdroj dat:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>Další kroky

Níže uvedené články vám pomůžou zjistit, jak zadávat dotazy na různé typy složek, typy souborů a vytváření a používání zobrazení:

- [Dotaz na jeden soubor CSV](query-single-csv-file.md)
- [Složky dotazů a více souborů CSV](query-folders-multiple-csv-files.md)
- [Dotazování konkrétních souborů](query-specific-files.md)
- [Dotazování souborů Parquet](query-parquet-files.md)
- [Vytváření a používání zobrazení](create-use-views.md)
- [Dotazování souborů JSON](query-json-files.md)
- [Dotazování vnořených typů Parquet](query-parquet-nested-types.md)
