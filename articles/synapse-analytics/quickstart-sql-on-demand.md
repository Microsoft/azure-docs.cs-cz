---
title: Použití SQL na vyžádání (náhled)
description: V tomto rychlém startu uvidíte a dozvíte se, jak snadné je dotazovat různé typy souborů pomocí SQL na vyžádání (náhled).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 0d543abc88c1e45f2c1f5503473d8e92566fc582
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457378"
---
# <a name="quickstart-using-sql-on-demand"></a>Úvodní příručka: Použití sql na vyžádání

Synapse SQL na vyžádání (preview) je bezserverová dotazovací služba, která umožňuje spouštět dotazy SQL na vaše soubory umístěné v Azure Storage. V tomto rychlém startu se dozvíte, jak dotazovat různé typy souborů pomocí SQL na vyžádání.

Podporovány jsou následující typy souborů: JSON, CSV, Apache Parquet

## <a name="prerequisites"></a>Požadavky

Zvolte klienta SQL pro vydávání dotazů:

- [Azure Synapse Studio](quickstart-synapse-studio.md) je webový nástroj, který můžete použít k procházení souborů v úložišti a vytváření dotazu SQL.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) je klientský nástroj, který umožňuje spouštět dotazy sql a poznámkové bloky v databázi na vyžádání.
- [SQL Server Management Studio](sql/get-started-ssms.md) je klientský nástroj, který umožňuje spouštět dotazy SQL v databázi na vyžádání.

Parametry pro rychlý start:

| Parametr                                 | Popis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adresa koncového bodu služby SQL na vyžádání    | Používá se jako název serveru                                   |
| Oblast koncového bodu služby SQL na vyžádání     | Používá se k určení, jaké úložiště budeme používat ve vzorcích |
| Uživatelské jméno a heslo pro přístup ke koncovému bodu | Slouží k přístupu ke koncovému bodu.                               |
| Databáze použitá k vytvoření zobrazení         | Databáze použitá jako výchozí bod ve vzorcích       |

## <a name="first-time-setup"></a>První nastavení

Před použitím vzorků:

- Vytvoření databáze pro vaše zobrazení (v případě, že chcete zobrazení použít)
- Vytvoření přihlašovacích údajů pro přístup k souborům v úložišti pomocí sql na vyžádání

### <a name="create-database"></a>Vytvoření databáze

Vytvořte si vlastní databázi pro ukázkové účely. Toto je databáze, ve které vytvoříte zobrazení. Tuto databázi použijte v ukázkových dotazech v tomto článku.

> [!NOTE]
> Databáze se používají pouze pro metadata zobrazení, nikoli pro skutečná data.
>
> Poznamenejte si název databáze, který použijete pro pozdější použití v rychlém startu.

Použijte následující dotaz `mydbname` a přecházejte na název podle vašeho výběru:

```sql
CREATE DATABASE mydbname
```

### <a name="create-credentials"></a>Vytvoření přihlašovacích údajů

Chcete-li spouštět dotazy pomocí sql na vyžádání, vytvořte pověření pro SQL na vyžádání pro přístup k souborům v úložišti.

> [!NOTE]
> Všimněte si, že je třeba vytvořit přihlašovací údaje pro přístup k účtu úložiště. Přestože SQL na vyžádání přístup k úložištím z různých oblastí, s úložiště a Azure Synapse pracovní ho prostoru ve stejné oblasti bude poskytovat lepší výkon prostředí.

Upravte následující fragment kódu a vytvořte pověření pro kontejnery CSV, JSON a Parquet:

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

## <a name="querying-csv-files"></a>Dotazování souborů CSV

Následující obrázek je náhled souboru, který má být dotazován:

![Prvních 10 řádků souboru CSV bez záhlaví, Windows styl nový řádek.](./sql/media/query-single-csv-file/population.png)

Následující dotaz ukazuje, jak číst soubor CSV, který neobsahuje řádek záhlaví, s novým řádkem ve stylu windows a sloupci oddělenými čárkami:

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

Schéma můžete zadat v době kompilace dotazu.
Další příklady naleznete v tématu, jak zadat [dotaz na soubor CSV](sql/query-single-csv-file.md).

## <a name="querying-parquet-files"></a>Dotazování na parketové soubory

Následující ukázka ukazuje možnosti odvození automatického schématu pro dotazování parketových souborů. Vrátí počet řádků v září 2017 bez zadání schématu.

> [!NOTE]
> Při čtení parketových souborů `OPENROWSET WITH` není nutné zadávat sloupce v klauzuli. V takovém případě SQL na vyžádání využívá metadata v souboru parket a vázat sloupce podle názvu.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

Další informace o [dotazování na parketové soubory](sql/query-parquet-files.md).

## <a name="querying-json-files"></a>Dotazování na soubory JSON

### <a name="json-sample-file"></a>Ukázkový soubor JSON

Soubory jsou *uloženy v kontejneru json,* *knihy*složek a obsahují jednu položku knihy s následující strukturou:

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

Následující dotaz ukazuje, jak používat [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) k načtení skalárních hodnot (název, vydavatel) z knihy s názvem *Pravděpodobnostní a statistické metody v kryptologii, Úvod vybranými články*:

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
> Čteme celý soubor JSON jako jeden řádek/sloupec, takže FIELDTERMINATOR, FIELDQUOTE a ROWTERMINATOR jsou nastaveny na 0x0b, protože neočekáváme, že jej v souboru najdeme.

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni začít s následujícími články quickstart:

- [Dotaz na jeden soubor CSV](sql/query-single-csv-file.md)
- [Složky dotazů a více souborů CSV](sql/query-folders-multiple-csv-files.md)
- [Soubory specifické pro dotaz](sql/query-specific-files.md)
- [Soubory parket dotazu](sql/query-parquet-files.md)
- [Typy vnořených par dotazů](sql/query-parquet-nested-types.md)
- [Dotaz na soubory JSON](sql/query-json-files.md)
- [Vytváření a používání zobrazení](sql/create-use-views.md)
- [Vytváření a používání externích tabulek](sql/create-use-external-tables.md)
- [Zachovat výsledek dotazu do úložiště Azure](sql/create-external-table-as-select.md)

Přejdete k dalšímu článku a dozvíte se, jak zadat dotaz na jeden soubor CSV.
> [!div class="nextstepaction"]
> [Dotaz na jeden soubor CSV](sql/query-single-csv-file.md)
