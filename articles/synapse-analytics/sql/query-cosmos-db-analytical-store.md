---
title: Dotazování na data Azure Cosmos DB pomocí SQL bez serveru v propojení Azure synapse (Preview)
description: V tomto článku se naučíte, jak zadat dotaz na Azure Cosmos DB pomocí SQL na vyžádání v tématu připojení Azure synapse (Preview).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 9d36cee1ebf9f58991db6fd40fd6aa9422546b61
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710749"
---
# <a name="query-azure-cosmos-db-data-using-sql-serverless-in-azure-synapse-link-preview"></a>Dotazování na data Azure Cosmos DB pomocí SQL bez serveru v propojení Azure synapse (Preview)

Synapse SQL bez serveru (dříve SQL na vyžádání) umožňuje analyzovat data v kontejnerech Azure Cosmos DB, která jsou povolená pomocí [Azure synapse Link](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) v téměř reálném čase, aniž by to ovlivnilo výkon transakčních úloh. Nabízí známou syntaxi T-SQL pro dotazování dat z [analytického úložiště](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) a integrovaného připojení k široké škále nástrojů pro dotazování BI a ad-hoc, a to prostřednictvím rozhraní T-SQL.

> [!NOTE]
> Podpora pro dotazování Azure Cosmos DB analytického úložiště bez SQL serveru je v současné době ve verzi ověřované verze Preview. 

Pro dotazování Azure Cosmos DB je dostupná plocha kompletního [výběru](/sql/t-sql/queries/select-transact-sql.md?view=sql-server-ver15&preserve-view=true) plochy prostřednictvím funkce [OpenRowset](develop-openrowset.md) , včetně většiny [funkcí a operátorů SQL](overview-features.md). Můžete také uložit výsledky dotazu, který čte data z Azure Cosmos DB společně s daty v Azure Blob Storage nebo Azure Data Lake Storage pomocí příkazu [vytvořit externí tabulku jako SELECT](develop-tables-cetas.md#cetas-in-sql-on-demand). V tuto chvíli nemůžete ukládat výsledky dotazů bez SQL serveru, abyste je Azure Cosmos DB pomocí [CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand).

V tomto článku se dozvíte, jak napsat dotaz pomocí SQL bez serveru, který bude dotazovat data z Azure Cosmos DB kontejnerů s povoleným odkazem synapse. Pak si můžete přečíst další informace o vytváření zobrazení bez SQL serveru nad kontejnery Azure Cosmos DB a jejich propojením s Power BI modely v [tomto](./tutorial-data-analyst.md) kurzu. 

## <a name="overview"></a>Přehled

K podpoře dotazování a analýze dat v Azure Cosmos DB analytickém úložišti bez serveru SQL Server se používá následující `OPENROWSET` syntaxe:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ]
```

Připojovací řetězec Azure Cosmos DB určuje Azure Cosmos DB název účtu, název databáze, hlavní klíč databázového účtu a nepovinný název oblasti, který má `OPENROWSET` fungovat. Připojovací řetězec má následující formát:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Název kontejneru Azure Cosmos DB je zadán bez uvozovek v `OPENROWSET` syntaxi. Pokud má název kontejneru nějaké speciální znaky (například pomlčku '-'), název by měl být zabalen do `[]` (hranaté závorky) v `OPENROWSET` syntaxi.

> [!NOTE]
> SQL bez serveru nepodporuje dotazování Azure Cosmos DB transakčního úložiště.

## <a name="sample-data-set"></a>Ukázková datová sada

Příklady v tomto článku jsou založené na datech z [Evropského centra pro prevenci a řízení nemocí (ECDC) COVID – 19 případů](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) a [COVID-19 Open Research DataSet (šňůra 19), Doi: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

Můžete si Zobrazit licenci a strukturu dat na těchto stránkách a stáhnout ukázková data pro sady dat [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) a [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) .

Pokud chcete postupovat podle tohoto článku předvádí, jak zadávat dotazy na data Cosmos DB bez SQL serveru, je potřeba vytvořit tyto prostředky:
* Účet databáze Azure Cosmos DB s [povoleným odkazem synapse](../../cosmos-db/configure-synapse-link.md)
* Azure Cosmos DB databáze s názvem `covid`
* Byly načteny dva kontejnery Azure Cosmos DB s názvem `EcdcCases` a `Cord19` nad sadou vzorových datových sad.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Zkoumání Azure Cosmos DB dat pomocí automatického odvození schématu

Nejjednodušší způsob, jak prozkoumat data v Azure Cosmos DB, je využití možnosti automatického odvození schématu. Vyvoláním `WITH` klauzule z `OPENROWSET` příkazu můžete dát SQL serveru možnost automaticky detekovat (odvodit) schéma analytického úložiště kontejneru Azure Cosmos DB.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
V předchozím příkladu dáváme pokyn k tomu, aby SQL Server bez serveru se připojil k `covid` databázi v Azure Cosmos DB účet `MyCosmosDbAccount` ověřený pomocí Azure Cosmos DB Key (fiktivní v předchozím příkladu). Pak přistupujeme k `EcdcCases` analytickému úložišti kontejneru v `West US 2` oblasti. Vzhledem k tomu, že neexistuje žádná projekce specifických vlastností, `OPENROWSET` funkce vrátí všechny vlastnosti z Azure Cosmos DBch položek.

Pokud potřebujete prozkoumat data z druhého kontejneru ve stejné Azure Cosmos DB databázi, můžete použít stejný připojovací řetězec a odkaz požadovaný kontejner jako třetí parametr:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Explicitně zadat schéma

I když funkce automatického odvození schématu v nástroji `OPENROWSET` poskytuje jednoduché a snadno použitelné querience, vaše firemní scénáře můžou vyžadovat, abyste v Azure Cosmos DBch datech explicitně určili schéma pro konkrétní vlastnosti jen pro čtení.

`OPENROWSET` umožňuje explicitně určit, které vlastnosti chcete číst z dat v kontejneru a zadat jejich datové typy. Představme si, že jsme naimportovali data z [ECDC sady dat COVID](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) s následující strukturou do Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Tyto ploché dokumenty JSON ve Azure Cosmos DB můžou být reprezentované jako sada řádků a sloupců v synapse SQL. `OPENROWSET` funkce umožňuje určit podmnožinu vlastností, které chcete číst, a přesné typy sloupců v `WITH` klauzuli:

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Výsledek tohoto dotazu může vypadat takto:

| date_rep | věcech | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Další informace o typech SQL, které by se měly použít pro Azure Cosmos DB hodnotu, najdete v článku [pravidla pro mapování typů SQL](#azure-cosmos-db-to-sql-type-mappings) na konci článku.

## <a name="querying-nested-objects-and-arrays"></a>Dotazování na vnořené objekty a pole

Azure Cosmos DB umožňuje znázornit složitější datové modely jejich sestavou jako vnořené objekty nebo pole. Schopnost AutoSync odkazu synapse pro Azure Cosmos DB spravuje reprezentaci schématu v analytickém úložišti, která zahrnuje zpracování vnořených datových typů, což umožňuje pracovat s bohatou dotazováním z SQL serveru.

Například datová sada [šňůr-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) obsahuje dokumenty JSON, které následují následující strukturu:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Vnořené objekty a pole v Azure Cosmos DB jsou reprezentovány jako řetězce JSON ve výsledku dotazu, pokud `OPENROWSET` je funkce čte. Jedna z možností, jak číst hodnoty z těchto komplexních typů, protože sloupce SQL používá funkce SQL JSON:

```sql
SELECT
    title = JSON_VALUE(metadata, '$.title'),
    authors = JSON_QUERY(metadata, '$.authors'),
    first_author_name = JSON_VALUE(metadata, '$.authors[0].first')
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( metadata varchar(MAX) ) AS docs;
```

Výsledek tohoto dotazu může vypadat takto:

| title | Autoři | first_autor_name |
| --- | --- | --- |
| Doplňující informace epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien |  

Jako alternativní možnost můžete také zadat cesty k vnořeným hodnotám v objektech při použití `WITH` klauzule:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( title varchar(1000) '$.metadata.title',
           authors varchar(max) '$.metadata.authors'
    ) AS docs;
```

Přečtěte si další informace o analýze [složitých datových typů v odkazech na synapse](../how-to-analyze-complex-schema.md) a [ve vnořených strukturách bez SQL serveru](query-parquet-nested-types.md).

> [!IMPORTANT]
> Pokud se v textu zobrazí neočekávané znaky jako `MÃƒÂ©lade` místo, není `Mélade` kolace databáze nastavena na kolaci [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . 
> [Změňte kolaci databáze](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) na určitou kolaci UTF8 pomocí nějakého příkazu SQL `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` , jako je.


## <a name="flattening-nested-arrays"></a>Sloučení vnořených polí

Data Azure Cosmos DB můžou mít vnořená dílčí pole, jako je pole autora ze sady dat [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) :

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

V některých případech se může stát, že budete muset "spojit" vlastnosti z horní položky (metadata) se všemi prvky pole (autoři). Bez SQL serveru můžete sloučit vnořené struktury s použitím `OPENJSON` funkce ve vnořeném poli:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

Výsledek tohoto dotazu může vypadat takto:

| title | Autoři | první | poslední | přidružení |
| --- | --- | --- | --- | --- |
| Doplňující informace epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Doplňující informace epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4 # |`{"laboratory":"","institution":"U…` | 
| Doplňující informace epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Doplňující informace epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Pokud se v textu zobrazí neočekávané znaky jako `MÃƒÂ©lade` místo, není `Mélade` kolace databáze nastavena na kolaci [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . 
> [Změňte kolaci databáze](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) na určitou kolaci UTF8 pomocí nějakého příkazu SQL `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` , jako je.

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Mapování typů Azure Cosmos DB na SQL

Je důležité nejdřív si uvědomit, že zatímco Azure Cosmos DB transakčního úložiště je Schema-nezávislá, je analytické úložiště schematized na optimalizaci pro výkon analytických dotazů. Díky schopnosti autosync v synapse Azure Cosmos DB propojení se v analytickém úložišti nástroje spravují reprezentace schématu, která zahrnuje zpracování vnořených datových typů. Vzhledem k tomu, že dotazy bez SQL serveru jsou analytické úložiště, je důležité pochopit, jak namapovat Azure Cosmos DB vstupní datové typy k datovým typům SQL.

Azure Cosmos DB účty rozhraní API SQL (Core) API podporují typy vlastností JSON číslo, řetězec, logická hodnota, null, vnořený objekt nebo pole. Pokud používáte klauzuli v, musíte zvolit typy SQL, které odpovídají těmto typům JSON `WITH` `OPENROWSET` . Viz níže typy sloupců SQL, které by měly být použity pro různé typy vlastností v Azure Cosmos DB.

| Typ vlastnosti Azure Cosmos DB | Typ sloupce SQL |
| --- | --- |
| Logická hodnota | bit |
| Integer | bigint |
| Decimal | float |
| Řetězec | varchar (kolace databáze UTF8) |
| Datum a čas (formátovaný řetězec ISO) | varchar (30) |
| Datum a čas (časové razítko systému UNIX) | bigint |
| Null | `any SQL type` 
| Vnořený objekt nebo pole | varchar (max) (kolace databáze UTF8), serializovaná jako text JSON |

Pro dotazování Azure Cosmos DBch účtů typu rozhraní API služby Mongo DB můžete získat další informace o úplném vyjádření schématu přesnosti v analytickém úložišti a o rozšířených názvech vlastností, které se [tady](../../cosmos-db/analytical-store-introduction.md#analytical-schema)mají použít.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Postup vytvoření a používání zobrazení v SQL na vyžádání](create-use-views.md) 
- [Kurz k sestavování zobrazení SQL na vyžádání přes Azure Cosmos DB a jejich propojení s Power BI modely prostřednictvím DirectQuery](./tutorial-data-analyst.md)
