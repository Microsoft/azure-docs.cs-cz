---
title: Používání SQL na vyžádání (Preview)
description: V tomto rychlém startu uvidíte a naučíte se, jak se snadno dotazovat na různé typy souborů pomocí SQL na vyžádání (Preview).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 43f361fbaf4ab0462af0a720d7711f219134a165
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692173"
---
# <a name="quickstart-using-sql-on-demand"></a>Rychlý Start: používání SQL na vyžádání

Synapse SQL na vyžádání (Preview) je služba pro dotazování bez serveru, která umožňuje spouštět dotazy SQL na souborech umístěných v Azure Storage. V tomto rychlém startu se dozvíte, jak zadávat dotazy na různé typy souborů pomocí SQL na vyžádání.

Podporovány jsou následující typy souborů: JSON, CSV, Apache Parquet

## <a name="prerequisites"></a>Požadavky

Zvolit klienta SQL pro vydávání dotazů:

- [Azure synapse Studio](quickstart-synapse-studio.md) je webový nástroj, který můžete použít k procházení souborů v úložišti a vytváření dotazů SQL.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) je klientský nástroj, který umožňuje spouštět dotazy a poznámkové bloky SQL v databázi na vyžádání.
- [SQL Server Management Studio](sql/get-started-ssms.md) je klientský nástroj, který umožňuje spouštět dotazy SQL v databázi na vyžádání.

Parametry pro rychlý Start:

| Parametr                                 | Popis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adresa koncového bodu služby SQL na vyžádání    | Používá se jako název serveru.                                   |
| Oblast koncového bodu služby SQL na vyžádání     | Slouží k určení, jaké úložiště se bude používat v ukázkách. |
| Uživatelské jméno a heslo pro přístup ke koncovému bodu | Používá se pro přístup ke koncovému bodu.                               |
| Databáze používaná k vytváření zobrazení         | Databáze použitá jako počáteční bod v ukázkách       |

## <a name="first-time-setup"></a>Nastavení při prvním spuštění

Než použijete ukázky:

- Vytvoření databáze pro zobrazení (pro případ, že chcete použít zobrazení)
- Vytvoření přihlašovacích údajů, které budou používat SQL na vyžádání pro přístup k souborům v úložišti

### <a name="create-database"></a>Vytvoření databáze

Vytvořte si vlastní databázi pro demonstrační účely. Tuto databázi použijete k vytvoření zobrazení a k ukázkovým dotazům v tomto článku.

> [!NOTE]
> Databáze se používají jenom pro metadata zobrazení, ne pro skutečná data.
>Poznamenejte si název databáze, který použijete pro pozdější použití v rychlém startu.

Použijte následující dotaz, který se `mydbname` změní na libovolný název:

```sql
CREATE DATABASE mydbname
```

### <a name="create-credentials"></a>Vytvořit pověření

Pokud chcete spouštět dotazy pomocí SQL na vyžádání, vytvořte přihlašovací údaje pro SQL na vyžádání, abyste je mohli použít pro přístup k souborům v úložišti.

> [!NOTE]
> Aby bylo možné úspěšně spustit ukázky v této části, je nutné použít token SAS.
>
> Chcete-li začít používat tokeny SAS, je třeba vyřadit UserIdentity, který je vysvětlen v následujícím [článku](sql/develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> SQL na vyžádání ve výchozím nastavení vždy používá předávací průchozí služba AAD.

Další informace o tom, jak spravovat řízení přístupu k úložišti, najdete v článku[řízení přístupu k účtu úložiště pro SQL na vyžádání](sql/develop-storage-files-storage-access-control.md) .

Spusťte následující fragment kódu pro vytvoření přihlašovacích údajů použitých v ukázkách v této části:

```sql
-- create credentials for containers in our demo storage account
IF EXISTS
   (SELECT * FROM sys.credentials
   WHERE name = 'https://sqlondemandstorage.blob.core.windows.net')
   DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
```

## <a name="querying-csv-files"></a>Dotazování na soubory CSV

Následující obrázek je náhled souboru, který se má dotazovat:

![Prvních 10 řádků souboru CSV bez záhlaví, nový řádek stylu Windows](./sql/media/query-single-csv-file/population.png)

Následující dotaz ukazuje, jak číst soubor CSV, který neobsahuje řádek záhlaví, s novým řádkem ve stylu Windows a s oddělovači sloupců oddělenými čárkami:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/*.csv'
    , FORMAT = 'CSV'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

V době kompilace dotazu můžete zadat schéma.
Další příklady najdete v tématu Postup [dotazování souboru CSV](sql/query-single-csv-file.md).

## <a name="querying-parquet-files"></a>Dotazování na soubory Parquet

Následující příklad ukazuje schopnosti automatického odvození schématu pro dotazování souborů Parquet. Vrátí počet řádků v září 2017 bez zadání schématu.

> [!NOTE]
> Při čtení souborů Parquet není nutné zadávat sloupce `OPENROWSET WITH` v klauzuli. V takovém případě SQL na vyžádání využívá metadata v souboru Parquet a váže sloupce podle názvu.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

Přečtěte si další informace o [dotazování souborů Parquet](sql/query-parquet-files.md).

## <a name="querying-json-files"></a>Dotazování na soubory JSON

### <a name="json-sample-file"></a>Ukázkový soubor JSON

Soubory jsou uloženy v kontejneru *JSON* , v *knihách*složek a obsahují jednu položku knihy s následující strukturou:

```json
{  
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[  
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

### <a name="querying-json-files"></a>Dotazování na soubory JSON

Následující dotaz ukazuje, jak použít [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) k načtení skalárních hodnot (title, Publisher) z knihy s názvem *pravděpodobnostní a statistickými metodami v Cryptology, Úvod do vybraných článků*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> Načítáme celý soubor JSON jako jeden řádek nebo sloupec. FIELDTERMINATOR, FIELDQUOTE a ROWTERMINATOR jsou nastaveny na 0x0B, protože neočekáváme, že ji v souboru nenajdeme.

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni pokračovat v následujících článcích:

- [Dotaz na jeden soubor CSV](sql/query-single-csv-file.md)
- [Složky dotazů a více souborů CSV](sql/query-folders-multiple-csv-files.md)
- [Dotazování konkrétních souborů](sql/query-specific-files.md)
- [Dotazování souborů Parquet](sql/query-parquet-files.md)
- [Dotazování vnořených typů Parquet](sql/query-parquet-nested-types.md)
- [Dotazování souborů JSON](sql/query-json-files.md)
- [Vytváření a používání zobrazení](sql/create-use-views.md)
- [Vytváření a používání externích tabulek](sql/create-use-external-tables.md)
- [Uchovat výsledek dotazu do Azure Storage](sql/create-external-table-as-select.md)
- [Dotaz na jeden soubor CSV](sql/query-single-csv-file.md)
