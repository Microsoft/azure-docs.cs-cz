---
title: 'Rychlý Start: použití fondu SQL bez serveru'
description: V tomto rychlém startu se dozvíte, jak snadné je dotazování různých typů souborů pomocí fondu SQL bez serveru.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 8607355069bbae5983239ddbd3e8752143f31497
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676344"
---
# <a name="quickstart-use-serverless-sql-pool"></a>Rychlý Start: použití fondu SQL bez serveru

Synapse fond SQL bez serveru je služba dotazů bez serveru, která umožňuje spouštět dotazy SQL na souborech umístěných v Azure Storage. V tomto rychlém startu se dozvíte, jak zadávat dotazy na různé typy souborů pomocí SQL fondu bez serveru. Podporované formáty jsou uvedeny ve funkci [OpenRowset](sql/develop-openrowset.md).

V tomto rychlém startu se zobrazí dotazování: CSV, Apache Parquet a soubory JSON.

## <a name="prerequisites"></a>Požadavky

Vyberte si klienta SQL pro vydávání dotazů:

- [Azure synapse Studio](./get-started-create-workspace.md) je webový nástroj, který můžete použít k procházení souborů v úložišti a vytváření dotazů SQL.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) je klientský nástroj, který umožňuje spouštět dotazy a poznámkové bloky SQL v databázi na vyžádání.
- [SQL Server Management Studio](sql/get-started-ssms.md) je klientský nástroj, který umožňuje spouštět dotazy SQL v databázi na vyžádání.

Parametry pro tento rychlý Start:

| Parametr                                 | Popis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adresa koncového bodu služby fondu SQL bez serveru    | Používá se jako název serveru.                                   |
| oblast koncového bodu služby fondu SQL bez serveru     | Slouží k určení, jaké úložiště se bude používat v ukázkách. |
| Uživatelské jméno a heslo pro přístup ke koncovému bodu | Používá se pro přístup ke koncovému bodu.                               |
| Databáze používaná k vytváření zobrazení         | Databáze použitá jako počáteční bod v ukázkách       |

## <a name="first-time-setup"></a>Nastavení při prvním spuštění

Než použijete ukázky:

- Vytvoření databáze pro zobrazení (pro případ, že chcete použít zobrazení)
- Vytvoření přihlašovacích údajů, které bude používat fond SQL bez serveru pro přístup k souborům v úložišti

### <a name="create-database"></a>Vytvoření databáze

Vytvořte si vlastní databázi pro demonstrační účely. Tuto databázi použijete k vytvoření zobrazení a k ukázkovým dotazům v tomto článku.

> [!NOTE]
> Databáze se používají jenom pro metadata zobrazení, ne pro skutečná data.
>Poznamenejte si název databáze, který použijete pro pozdější použití v rychlém startu.

Použijte následující dotaz, který se změní `mydbname` na libovolný název:

```sql
CREATE DATABASE mydbname
```

### <a name="create-data-source"></a>Vytvořit zdroj dat

Pokud chcete spouštět dotazy pomocí neserverového fondu SQL, vytvořte zdroj dat, který fond SQL bez serveru může použít k přístupu k souborům v úložišti.
Spusťte následující fragment kódu pro vytvoření zdroje dat používaného v ukázkách v této části:

```sql
-- create master key that will protect the credentials:
CREATE MASTER KEY ENCRYPTION BY PASSWORD = <enter very strong password here>

-- create credentials for containers in our demo storage account
CREATE DATABASE SCOPED CREDENTIAL sqlondemand
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

## <a name="query-csv-files"></a>Dotazování souborů CSV

Následující obrázek je náhled souboru, který se má dotazovat:

![Prvních 10 řádků souboru CSV bez záhlaví, nový řádek stylu Windows](./sql/media/query-single-csv-file/population.png)

Následující dotaz ukazuje, jak číst soubor CSV, který neobsahuje řádek záhlaví, s novým řádkem ve stylu Windows a s oddělovači sloupců oddělenými čárkami:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'csv/population/*.csv',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT = 'CSV', PARSER_VERSION = '2.0'
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

## <a name="query-parquet-files"></a>Dotazování souborů Parquet

Následující příklad ukazuje schopnosti automatického odvození schématu pro dotazování souborů Parquet. Vrátí počet řádků v září 2017 bez zadání schématu.

> [!NOTE]
> Při čtení souborů Parquet není nutné zadávat sloupce v `OPENROWSET WITH` klauzuli. V takovém případě fond SQL bez serveru používá metadata v souboru Parquet a váže sloupce podle názvu.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'parquet/taxi/year=2017/month=9/*.parquet',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT='PARQUET'
  ) AS nyc
```

Přečtěte si další informace o [dotazování souborů Parquet](sql/query-parquet-files.md).

## <a name="query-json-files"></a>Dotazování souborů JSON

### <a name="json-sample-file"></a>Ukázkový soubor JSON

Soubory jsou uloženy v kontejneru *JSON* , v *knihách* složek a obsahují jednu položku knihy s následující strukturou:

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

### <a name="query-json-files"></a>Dotazování souborů JSON

Následující dotaz ukazuje, jak použít [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) k načtení skalárních hodnot (title, Publisher) z knihy s názvem *pravděpodobnostní a statistickými metodami v Cryptology, Úvod do vybraných článků*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'json/books/*.json',
      DATA_SOURCE = 'SqlOnDemandDemo'
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