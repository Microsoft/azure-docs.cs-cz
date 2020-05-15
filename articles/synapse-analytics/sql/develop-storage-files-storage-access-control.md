---
title: Řízení přístupu účtu úložiště pro SQL na vyžádání (Preview)
description: Popisuje, jak SQL na vyžádání (Preview) přistupuje k Azure Storage a jak můžete řídit přístup k úložišti pro SQL na vyžádání v Azure synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 7d73b3a1a7c3b2ab290d85d88aa24108d9e7a605
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2020
ms.locfileid: "83401984"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview"></a>Řízení přístupu účtu úložiště pro SQL na vyžádání (Preview)

Dotaz na vyžádání SQL čte soubory přímo z Azure Storage. Oprávnění pro přístup k souborům v Azure Storage se řídí na dvou úrovních:
- **Úroveň úložiště** – uživatel by měl mít oprávnění k přístupu k základním souborům úložiště. Správce úložiště by měl objektu zabezpečení služby Azure AD umožňovat čtení a zápis souborů nebo generování klíče SAS, který se bude používat pro přístup k úložišti.
- **Úroveň služby SQL** – uživatel by měl mít `SELECT` oprávnění ke čtení dat z [externí tabulky](develop-tables-external-tables.md) nebo `ADMINISTER BULK ADMIN` oprávnění ke spuštění `OPENROWSET` a také oprávnění k použití přihlašovacích údajů, které se použijí pro přístup k úložišti.

Tento článek popisuje typy přihlašovacích údajů, které můžete použít, a informace o tom, jak jsou pro uživatele SQL a Azure AD vyhledány přihlašovací údaje.

## <a name="supported-storage-authorization-types"></a>Podporované typy autorizace úložiště

Uživatel, který byl přihlášen k prostředku na vyžádání SQL, musí mít autorizaci pro přístup k souborům v Azure Storage, pokud nejsou soubory veřejně dostupné. Podporují se tři typy autorizace:

- [Identita uživatele](?tabs=user-identity)
- [Sdílený přístupový podpis](?tabs=shared-access-signature)
- [Spravovaná identita](?tabs=managed-identity)

> [!NOTE]
> [Předávací služba Azure AD](#force-azure-ad-pass-through) je výchozím chováním při vytváření pracovního prostoru. Pokud ho použijete, nemusíte vytvářet přihlašovací údaje pro každý účet úložiště, ke kterému jste přistupovali pomocí přihlašovacích údajů Azure AD. [Toto chování můžete zakázat](#disable-forcing-azure-ad-pass-through).

### <a name="shared-access-signature"></a>[Sdílený přístupový podpis](#tab/shared-access-signature)

**Sdílený přístupový podpis (SAS)** poskytuje delegovaný přístup k prostředkům v účtu úložiště. Pomocí SAS může zákazník udělit klientům přístup k prostředkům v účtu úložiště bez použití klíčů účtu pro sdílení. SAS vám poskytuje podrobnější kontrolu nad typem přístupu, který udělíte klientům, kteří mají SAS, včetně intervalu platnosti, udělených oprávnění, přijatelného rozsahu IP adres a přijatelného protokolu (HTTPS/HTTP).

Token SAS můžete získat tak, že přejdete na **účet úložiště > Azure Portal – > sdílený přístup – > konfigurace oprávnění – > generovat SAS a připojovací řetězec.**

> [!IMPORTANT]
> Při vygenerování tokenu SAS obsahuje znak otazníku (?) na začátku tokenu. Pokud chcete použít token v SQL na vyžádání, musíte při vytváření přihlašovacích údajů odebrat otazník (?). Například:
>
> Token SAS:? sv = 2018-03-28&SS = bfqt&SRT aplikace = SCO&SP = rwdlacup&se = 2019-04-18T20:42:12Z&St = 2019-04-18T12:42:12Z&spr = https&SIG = lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78% 3D

Aby bylo možné povolit přístup pomocí tokenu SAS, je nutné vytvořit pověření v oboru databáze nebo na serveru.

### <a name="user-identity"></a>Identita uživatele

**Identita uživatele**, označovaná také jako "průchozí", je autorizační typ, ve kterém se k autorizaci přístupu k datům používá identita uživatele služby Azure AD, který se přihlásil k SQL na vyžádání. Před přístupem k datům musí správce Azure Storage udělit oprávnění k uživateli Azure AD. Jak je uvedeno v tabulce výše, není podporováno pro typ uživatele SQL.

> [!IMPORTANT]
> Abyste mohli používat vaši identitu pro přístup k datům, musíte mít roli vlastníka dat objektu BLOB úložiště/Přispěvatel/čtenář.
> I v případě, že jste vlastníkem účtu úložiště, je stále nutné přidat sami sebe do jedné z rolí dat objektu BLOB úložiště.
>
> Další informace o řízení přístupu v Azure Data Lake Store Gen2 naleznete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md) článku.
>

Musíte explicitně povolit předávací ověřování Azure AD, aby uživatelé Azure AD mohli získat přístup k úložišti pomocí svých identit.

#### <a name="force-azure-ad-pass-through"></a>Vynutit předávací službu Azure AD

Vynucení předávacího průchodu Azure AD je výchozím chováním, které dosáhlo speciálního názvu PŘIHLAŠOVACÍho údaje, `UserIdentity` který se vytvoří automaticky během zřizování pracovních prostorů Azure synapse. Vynucuje použití předávacího průchodu Azure AD pro každý dotaz každého přihlášení služby Azure AD, ke kterému dojde bez ohledu na existenci jiných přihlašovacích údajů.

> [!NOTE]
> Předávací služba Azure AD je výchozí chování. Nemusíte vytvářet přihlašovací údaje pro každý účet úložiště, ke kterému přistupovali prostřednictvím přihlášení AD.

Pokud jste [zakázali vynucení předávacího procesu Azure AD pro každý dotaz](#disable-forcing-azure-ad-pass-through)a chcete ho znovu povolit, spusťte:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Pokud chcete povolit vynucení předávacího průchodu Azure AD pro konkrétního uživatele, můžete tomuto konkrétnímu uživateli udělit referenční oprávnění k přihlašovacím údajům `UserIdentity` . Následující příklad umožňuje vynucení předávacího průchodu Azure AD pro user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

#### <a name="disable-forcing-azure-ad-pass-through"></a>Zakázat vynucení předávacího Azure AD

[Pro každý dotaz můžete zakázat vynucení předávacího Azure AD](#force-azure-ad-pass-through). Pokud ho chcete zakázat, vyřaďte `Userdentity` přihlašovací údaje pomocí:

```sql
DROP CREDENTIAL [UserIdentity];
```

Pokud ho chcete znovu povolit, přečtěte si část [vynucení předávacího rozhraní Azure AD](#force-azure-ad-pass-through) .

### <a name="managed-identity"></a>Spravovaná identita

**Spravovaná identita** se také označuje jako MSI. Je to funkce Azure Active Directory (Azure AD), která poskytuje služby Azure pro SQL na vyžádání. Také nasadí automaticky spravovanou identitu ve službě Azure AD. Tato identita se dá použít k autorizaci žádosti o přístup k datům v Azure Storage.

Před přístupem k datům musí správce Azure Storage udělit oprávnění ke spravované identitě pro přístup k datům. Udělení oprávnění pro spravovanou identitu se provádí stejným způsobem jako udělení oprávnění jinému uživateli Azure AD.

### <a name="anonymous-access"></a>Anonymní přístup

Můžete přistupovat k veřejně dostupným souborům umístěným na účtech Azure Storage, které [povolují anonymní přístup](/azure/storage/blobs/storage-manage-access-to-resources.md).

---

### <a name="supported-authorization-types-for-databases-users"></a>Podporované typy autorizace pro databáze uživatelů

V následující tabulce najdete dostupné typy autorizace:

| Typ autorizace                    | *Uživatel SQL*    | *Uživatel Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [Identita uživatele](?tabs=user-identity#supported-storage-authorization-types)       | Nepodporuje se | Podporuje se      |
| [VEDE](?tabs=shared-access-signature#supported-storage-authorization-types)       | Podporuje se     | Podporuje se      |
| [Spravovaná identita](?tabs=managed-identity#supported-storage-authorization-types) | Nepodporuje se | Podporuje se      |

### <a name="supported-storages-and-authorization-types"></a>Podporované typy úložišť a autorizace

Můžete použít následující kombinace autorizačních a Azure Storagech typů:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *VEDE*               | Podporuje se      | Nepodporováno   | Podporuje se     |
| *Spravovaná identita* | Podporuje se      | Podporuje se        | Podporuje se     |
| *Identita uživatele*    | Podporuje se      | Podporuje se        | Podporuje se     |

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

Aby bylo zajištěno bezproblémové předávací prostředí Azure AD, budou mít všichni uživatelé ve výchozím nastavení právo používat `UserIdentity` přihlašovací údaje. Toho je dosaženo automatickým provedením následujícího příkazu při zřizování pracovního prostoru Azure synapse:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="server-scoped-credential"></a>Přihlašovací údaje v oboru serveru

Přihlašovací údaje v oboru serveru se používají, když funkce přihlášení `OPENROWSET` k SQL funguje bez `DATA_SOURCE` čtení souborů v některém účtu úložiště. Název přihlašovacích údajů v oboru serveru se **musí** shodovat s adresou URL úložiště Azure. Přihlašovací údaje se přidají spuštěním [Vytvoření přihlašovacích údajů](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Budete muset zadat argument název PŘIHLAŠOVACÍch údajů. Musí odpovídat buď části cesty, nebo celé cestě k datům v úložišti (viz níže).

> [!NOTE]
> Argument pro zprostředkovatele KRYPTOGRAFICKÝch služeb není podporován.

PŘIHLAŠOVACÍ jméno na úrovni serveru musí odpovídat celé cestě k účtu úložiště (a volitelně kontejneru) v následujícím formátu: `<prefix>://<storage_account_path>/<storage_path>` . Cesty k účtu úložiště jsou popsané v následující tabulce:

| Externí zdroj dat       | Předpona | Cesta k účtu úložiště                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | HTTPS  | <storage_account>. blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | HTTPS  | <storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | HTTPS  | <storage_account>. dfs.core.windows.net              |

> [!NOTE]
> K dispozici jsou speciální přihlašovací údaje na úrovni serveru `UserIdentity` , které [vynutí předávací službu Azure AD](?tabs=user-identity#force-azure-ad-pass-through).

Přihlašovací údaje v oboru serveru umožňují přístup k úložišti Azure pomocí následujících typů ověřování:

### <a name="shared-access-signature"></a>Sdílený přístupový podpis

Následující skript vytvoří přihlašovací údaje na úrovni serveru, které můžou používat `OPENROWSET` funkce pro přístup k jakémukoli souboru v úložišti Azure pomocí tokenu SAS. Vytvořte toto přihlašovací údaje, aby se povolil objekt zabezpečení SQL, který spustí `OPENROWSET` funkci pro čtení souborů chráněných pomocí klíče SAS v úložišti Azure, které odpovídají adrese URL v názvu přihlašovacích údajů.

Exchange <*mystorageaccountname*> s vaším skutečným názvem účtu úložiště a> <*mystorageaccountcontainername* s aktuálním názvem kontejneru:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="user-identity"></a>Identita uživatele

Následující skript vytvoří přihlašovací údaje na úrovni serveru, které uživateli umožňují zosobnit pomocí identity Azure AD.

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

### <a name="managed-identity"></a>Spravovaná identita

Následující skript vytvoří přihlašovací údaje na úrovni serveru, které můžou používat `OPENROWSET` funkce pro přístup k jakémukoli souboru v úložišti Azure pomocí spravované identity pracovního prostoru.

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='Managed Identity'
```

### <a name="public-access"></a>Veřejný přístup

Následující skript vytvoří přihlašovací údaje na úrovni serveru, které můžou používat `OPENROWSET` funkce pro přístup k jakémukoli souboru na veřejně dostupném úložišti Azure. Vytvořte toto přihlašovací údaje, aby se povolil objekt zabezpečení SQL, který spustí `OPENROWSET` funkci pro čtení veřejně dostupných souborů v Azure Storage, které odpovídají adrese URL v názvu přihlašovacích údajů.

Musíte mít> Exchange <*mystorageaccountname* s vaším skutečným názvem účtu úložiště a <*mystorageaccountcontainername*> s aktuálním názvem kontejneru:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = '';
GO
```
---

## <a name="database-scoped-credential"></a>Přihlašovací údaje v rámci databáze

Přihlašovací údaje v oboru databáze se používají, když jakákoli instanční `OPENROWSET` funkce volá funkci `DATA_SOURCE` nebo vybere data z [externí tabulky](develop-tables-external-tables.md) , která nepřistupuje k veřejným souborům. Přihlašovací údaje v oboru databáze se nemusí shodovat s názvem účtu úložiště, protože se explicitně použijí ve zdroji dat, který definuje umístění úložiště.

Přihlašovací údaje v rámci databáze umožňují přístup k úložišti Azure pomocí následujících typů ověřování:

### <a name="shared-access-signature"></a>Sdílený přístupový podpis

Následující skript vytvoří přihlašovací údaje, které se používají pro přístup k souborům v úložišti pomocí tokenu SAS zadaného v přihlašovacích údajích.

```sql
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="azure-ad-identity"></a>Identita Azure AD

Následující skript vytvoří přihlašovací údaje v rámci databáze používané [externí tabulkou](develop-tables-external-tables.md) a `OPENROWSET` funkcemi, které používají zdroj dat s přihlašovacími údaji pro přístup k souborům úložiště pomocí vlastní identity Azure AD.

```sql
CREATE DATABASE SCOPED CREDENTIAL [AzureAD]
WITH IDENTITY = 'User Identity';
GO
```

### <a name="managed-identity"></a>Spravovaná identita

Následující skript vytvoří přihlašovací údaje v rámci databáze, které je možné použít k zosobnění aktuálního uživatele Azure AD jako spravované identity služby. 

```sql
CREATE DATABASE SCOPED CREDENTIAL [SynapseIdentity]
WITH IDENTITY = 'Managed Identity';
GO
```

Přihlašovací údaje v oboru databáze se nemusí shodovat s názvem účtu úložiště, protože se explicitně použijí ve zdroji dat, který definuje umístění úložiště.

### <a name="public-access"></a>[Veřejný přístup](#tab/public-access)

Přihlašovací údaje v oboru databáze nejsou nutné k povolení přístupu k veřejně dostupným souborům. Vytvoří [zdroj dat bez pověření oboru databáze](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) pro přístup k veřejně dostupným souborům v Azure Storage.

---

Přihlašovací údaje v oboru databáze se používají v externích zdrojích dat k určení, která metoda ověřování se použije pro přístup k tomuto úložišti:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://*******.blob.core.windows.net/samples',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Příklady

**Přístup k veřejně dostupnému zdroji dat**

Pomocí následujícího skriptu vytvořte tabulku, která přistupuje k veřejně dostupnému zdroji dat.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://****.blob.core.windows.net/public-access' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet', DATA_SOURCE = [publicData], FILE_FORMAT = [SynapseParquetFormat] )
```

Uživatel databáze může číst obsah souborů ze zdroje dat pomocí externí tabulky nebo funkce [OpenRowset](develop-openrowset.md) , která odkazuje na zdroj dat:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FORMAT=PARQUET) as rows;
GO
```

**Přístup ke zdroji dat pomocí přihlašovacích údajů**

Úpravou následujícího skriptu vytvořte externí tabulku, která přistupuje k Azure Storage pomocí tokenu SAS, identity uživatele Azure AD nebo spravované identity pracovního prostoru.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use User Identity, Managed Identity, or SAS. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL MyIdentity WITH IDENTITY = 'User Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://*******.blob.core.windows.net/samples'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = MyIdentity 
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FILE_FORMAT = [SynapseParquetFormat] )

```

Uživatel databáze může číst obsah souborů ze zdroje dat pomocí [externí tabulky](develop-tables-external-tables.md) nebo funkce [OpenRowset](develop-openrowset.md) , která odkazuje na zdroj dat:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FORMAT=PARQUET) as rows;
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
