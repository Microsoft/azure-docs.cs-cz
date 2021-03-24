---
title: Dotazování na data Azure Cosmos DB pomocí neserverového fondu SQL ve službě Azure synapse Link
description: V tomto článku se naučíte, jak zadávat dotazy na Azure Cosmos DB pomocí neserverového fondu SQL ve službě Azure synapse Link.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 03/02/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 10262b168b91370956c9559ba688c72213ba7618
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870989"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link"></a>Dotazování na data Azure Cosmos DB pomocí neserverového fondu SQL ve službě Azure synapse Link

Fond SQL bez serveru umožňuje analyzovat data ve vašich Azure Cosmos DBch kontejnerech, které jsou povolené pomocí [Azure synapse s odkazem](../../cosmos-db/synapse-link.md) téměř v reálném čase, aniž by to ovlivnilo výkon transakčních úloh. Nabízí známou syntaxi T-SQL pro dotazování dat z [analytického úložiště](../../cosmos-db/analytical-store-introduction.md) a integrovaného připojení k široké škále nástrojů pro Business Intelligence (BI) a ad hoc pro dotazování prostřednictvím rozhraní T-SQL.

Pro dotazování Azure Cosmos DB je podpora celé oblasti [výběru](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) plochy podporována prostřednictvím funkce [OpenRowset](develop-openrowset.md) , která zahrnuje většinu [funkcí a operátorů SQL](overview-features.md). Můžete také uložit výsledky dotazu, který čte data z Azure Cosmos DB společně s daty v Azure Blob Storage nebo Azure Data Lake Storage pomocí příkazu [vytvořit externí tabulku jako SELECT](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS). V současné době nemůžete ukládat výsledky dotazu fondu SQL bez serveru do Azure Cosmos DB pomocí CETAS.

V tomto článku se dozvíte, jak napsat dotaz s neserverovým fondem SQL, který bude dotazovat data z Azure Cosmos DB kontejnerů, které jsou povolené pomocí odkazu Azure synapse. Pak si můžete přečíst další informace o vytváření zobrazení fondu SQL bez serveru nad kontejnery Azure Cosmos DB a jejich propojením s Power BI modely v [tomto kurzu](./tutorial-data-analyst.md). V tomto kurzu se používá kontejner s [Azure Cosmos DB dobře definovaným schématem](../../cosmos-db/analytical-store-introduction.md#schema-representation).

## <a name="overview"></a>Přehled

Fond SQL bez serveru umožňuje dotazovat se na Azure Cosmos DB analytické úložiště pomocí `OPENROWSET` funkce. 
- `OPENROWSET` s vloženým klíčem Tato syntaxe se dá použít k dotazování kolekce Azure Cosmos DB bez nutnosti připravovat přihlašovací údaje.
- `OPENROWSET` které odkazovalo na přihlašovací údaje obsahující Cosmos DB klíč účtu. Tato syntaxe se dá použít k vytvoření zobrazení Azure Cosmos DB kolekcí.

### <a name="openrowset-with-key"></a>[OPENROWSET s klíčem](#tab/openrowset-key)

Aby bylo možné podporovat dotazování a analýzu dat ve službě Azure Cosmos DB Analytical Store, fond SQL bez serveru používá následující `OPENROWSET` syntaxi:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Připojovací řetězec Azure Cosmos DB určuje Azure Cosmos DB název účtu, název databáze, hlavní klíč databázového účtu a nepovinný název oblasti `OPENROWSET` funkce.

Připojovací řetězec má následující formát:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Název kontejneru Azure Cosmos DB je zadán bez uvozovek v `OPENROWSET` syntaxi. Pokud má název kontejneru nějaké speciální znaky, například pomlčkou (-), měl by se název v syntaxi uzavřít do hranatých závorek ( `[]` ) `OPENROWSET` .

### <a name="openrowset-with-credential"></a>[OPENROWSET s přihlašovacími údaji](#tab/openrowset-credential)

Můžete použít `OPENROWSET` syntaxi, která odkazuje na přihlašovací údaje:

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<Azure Cosmos DB connection string without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

Připojovací řetězec Azure Cosmos DB v tomto případě neobsahuje klíč. Připojovací řetězec má následující formát:
```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

Hlavní klíč databázového účtu je umístěný v přihlašovacích údajích na úrovni serveru nebo v rámci pověření v oboru databáze. 

---

> [!IMPORTANT]
> Ujistěte se, že používáte určitou databázovou kolaci UTF-8, například `Latin1_General_100_CI_AS_SC_UTF8` proto, že řetězcové hodnoty v Azure Cosmos DB analytických obchodech jsou kódované jako text v kódování UTF-8.
> Neshoda mezi kódováním textu v souboru a kolaci může způsobit neočekávané chyby převodu textu.
> Výchozí kolaci aktuální databáze můžete snadno změnit pomocí příkazu T-SQL `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` .

> [!NOTE]
> Fond SQL bez serveru nepodporuje dotazování na Azure Cosmos DB transakční úložiště.

## <a name="sample-dataset"></a>Ukázková datová sada

Příklady v tomto článku jsou založené na datech z [Evropského centra pro prevenci a řízení nemocí (ECDC) COVID – 19 případů](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) a [COVID-19 Open Research DataSet (šňůra 19), Doi: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

Na těchto stránkách můžete zobrazit licenci a strukturu dat. Můžete si také stáhnout ukázková data pro datové sady [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) a [šňůr-19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) .

Pokud chcete postupovat podle tohoto článku předvádí, jak zadávat dotazy na data Azure Cosmos DB s neserverovým fondem SQL, ujistěte se, že jste vytvořili tyto prostředky:

* Účet databáze Azure Cosmos DB s [povoleným odkazem na Azure synapse](../../cosmos-db/configure-synapse-link.md).
* Azure Cosmos DB databáze s názvem `covid` .
* Dva kontejnery Azure Cosmos DB s názvem `Ecdc` a `Cord19` načteny s předchozími ukázkovými datovými sadami.

Pro účely testování můžete použít následující připojovací řetězec: `Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==` . Všimněte si, že toto připojení nezaručuje výkon, protože tento účet se v porovnání s vaším koncovým bodem SQL synapse může nacházet ve vzdálené oblasti.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Zkoumání Azure Cosmos DB dat pomocí automatického odvození schématu

Nejjednodušší způsob, jak prozkoumat data v Azure Cosmos DB, je použití možnosti automatického odvození schématu. Vyvoláním `WITH` klauzule z `OPENROWSET` příkazu můžete instruovat fond SQL bez serveru na automatické rozpoznávání (odvodit) schéma analytického úložiště kontejneru Azure Cosmos DB.

### <a name="openrowset-with-key"></a>[OPENROWSET s klíčem](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### <a name="openrowset-with-credential"></a>[OPENROWSET s přihlašovacími údaji](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

---

V předchozím příkladu jsme pověřili fond SQL bez serveru pro připojení k `covid` databázi v Azure Cosmos DB účet `MyCosmosDbAccount` ověřený pomocí klíče Azure Cosmos dB (fiktivního v předchozím příkladu). Pak jsme k `Ecdc` analytickému úložišti kontejneru přistupovali v `West US 2` oblasti. Vzhledem k tomu, že neexistuje žádná projekce specifických vlastností, `OPENROWSET` funkce vrátí všechny vlastnosti z Azure Cosmos DBch položek.

Za předpokladu, že položky v kontejneru Azure Cosmos DB `date_rep` mají `cases` vlastnosti, a `geo_id` , výsledky tohoto dotazu jsou uvedeny v následující tabulce:

| date_rep | věcech | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Pokud potřebujete prozkoumat data z druhého kontejneru ve stejné Azure Cosmos DB databázi, můžete použít stejný připojovací řetězec a odkazovat na požadovaný kontejner jako na třetí parametr:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Explicitně zadat schéma

I když funkce automatického odvození schématu v nástroji `OPENROWSET` poskytuje jednoduché, snadno použitelné querience, vaše firemní scénáře můžou vyžadovat, abyste v Azure Cosmos DBch datech explicitně určili schéma pro příslušné vlastnosti jen pro čtení.

`OPENROWSET`Funkce umožňuje explicitně určit, které vlastnosti chcete číst z dat v kontejneru a zadat jejich datové typy.

Řekněme, že jsme naimportovali některá data z [datové sady ECDC COVID](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) s následující strukturou do Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Tyto ploché dokumenty JSON ve Azure Cosmos DB můžou být reprezentované jako sada řádků a sloupců v synapse SQL. `OPENROWSET`Funkce umožňuje určit podmnožinu vlastností, které chcete číst, a přesné typy sloupců v `WITH` klauzuli:

### <a name="openrowset-with-key"></a>[OPENROWSET s klíčem](#tab/openrowset-key)
```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
### <a name="openrowset-with-credential"></a>[OPENROWSET s přihlašovacími údaji](#tab/openrowset-credential)
```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
---
Výsledek tohoto dotazu může vypadat podobně jako v následující tabulce:

| date_rep | věcech | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Další informace o typech SQL, které by se měly používat pro Azure Cosmos DB hodnoty, najdete na stránce [pravidla pro mapování typů SQL](#azure-cosmos-db-to-sql-type-mappings) na konci článku.

## <a name="create-view"></a>Vytvořit zobrazení

Vytváření zobrazení v hlavních nebo výchozích databázích se nedoporučuje ani nepodporuje. Proto potřebujete vytvořit uživatelskou databázi pro zobrazení.

Po identifikaci schématu můžete zobrazit zobrazení dat Azure Cosmos DB. Klíč účtu Azure Cosmos DB byste měli umístit do samostatného přihlašovacího údaje a odkazovat na toto pověření z `OPENROWSET` funkce. Neudržujte klíč účtu v definici zobrazení.

```sql
CREATE CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Nepoužívejte `OPENROWSET` bez explicitně definovaného schématu, protože by to mohlo mít vliv na výkon. Ujistěte se, že používáte nejmenší možné velikosti pro sloupce (například VARCHAR (100) namísto default VARCHAR (8000)). Měli byste použít určitou kolaci UTF-8 jako výchozí kolaci databáze nebo ji nastavit jako explicitní kolaci sloupců, aby nedocházelo k [problémům s převodem ve formátu UTF-8](../troubleshoot/reading-utf8-text.md). Kolace `Latin1_General_100_BIN2_UTF8` poskytuje nejlepší výkon, když Yu filtruje data pomocí některých řetězcových sloupců.

## <a name="query-nested-objects-and-arrays"></a>Dotazování vnořených objektů a polí

Pomocí Azure Cosmos DB můžete reprezentovat složitější datové modely jejich sestavou jako vnořené objekty nebo pole. Funkce AutoSync odkazu Azure synapse pro Azure Cosmos DB spravuje reprezentaci schématu v analytickém úložišti, která zahrnuje zpracování vnořených datových typů, které umožňují vytvářet dotazy z fondu SQL bez serveru.

Například datová sada [šňůr-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) obsahuje dokumenty JSON, které následují tuto strukturu:

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

Vnořené objekty a pole v Azure Cosmos DB jsou reprezentovány jako řetězce JSON ve výsledku dotazu, pokud `OPENROWSET` je funkce čte. Můžete zadat cesty k vnořeným hodnotám v objektech při použití `WITH` klauzule:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19)
WITH (  paper_id    varchar(8000),
        title        varchar(1000) '$.metadata.title',
        metadata     varchar(max),
        authors      varchar(max) '$.metadata.authors'
) AS docs;
```

Výsledek tohoto dotazu může vypadat podobně jako v následující tabulce:

| paper_id | title | zprostředkovatele identity | Autoři |
| --- | --- | --- |
| bb11206963e831f... | Doplňující informace epidemi... | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1... | Použití Convalescent séra v imunní-E... | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649... | Tylosema esculentum (Marama) hlízy a B... | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

Přečtěte si další informace o analýze [složitých datových typů v odkazech Azure synapse](../how-to-analyze-complex-schema.md) a [ve vnořených strukturách v neserverovém fondu SQL](query-parquet-nested-types.md).

> [!IMPORTANT]
> Pokud se v textu jako místo, zobrazí neočekávané znaky `MÃƒÂ©lade` `Mélade` , není kolace databáze nastavená na rekolaci [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) .
> [Změňte kolaci databáze](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) na kolaci UTF-8 pomocí příkazu SQL, jako je `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="flatten-nested-arrays"></a>Sloučit vnořená pole

Data Azure Cosmos DB mohou mít vnořená podpole, jako je pole autora, z datové sady [šňůr-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) :

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

V některých případech se může stát, že budete muset "spojit" vlastnosti z horní položky (metadata) se všemi prvky pole (autoři). Fond SQL bez serveru umožňuje sloučit vnořené struktury s použitím `OPENJSON` funkce ve vnořeném poli:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
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

Výsledek tohoto dotazu může vypadat podobně jako v následující tabulce:

| title | Autoři | první | poslední | přidružení |
| --- | --- | --- | --- | --- |
| Doplňující informace epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Doplňující informace epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4 # |`{"laboratory":"","institution":"U…` | 
| Doplňující informace epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Doplňující informace epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Pokud se v textu jako místo, zobrazí neočekávané znaky `MÃƒÂ©lade` `Mélade` , není kolace databáze nastavená na rekolaci [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) . [Změňte kolaci databáze](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) na kolaci UTF-8 pomocí příkazu SQL, jako je `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Mapování typů Azure Cosmos DB na SQL

I když Azure Cosmos DB transakční úložiště je Schema-nezávislá, je analytické úložiště schematized na optimalizaci pro výkon analytických dotazů. Díky možnosti automatické synchronizace v rámci odkazu na Azure synapse Azure Cosmos DB spravuje reprezentaci schématu v analytickém úložišti, která zahrnuje zpracování vnořených datových typů. Vzhledem k tomu, že fond SQL bez serveru se dotazuje na analytické úložiště, je důležité pochopit, jak namapovat Azure Cosmos DB vstupní datové typy na datové typy SQL.

Azure Cosmos DB účty rozhraní API SQL (Core) API podporují typy vlastností JSON číslo, řetězec, logická hodnota, null, vnořený objekt nebo pole. Pokud používáte klauzuli v, musíte zvolit typy SQL, které odpovídají těmto typům JSON `WITH` `OPENROWSET` . V následující tabulce jsou uvedeny typy sloupců SQL, které by měly být použity pro různé typy vlastností v Azure Cosmos DB.

| Typ vlastnosti Azure Cosmos DB | Typ sloupce SQL |
| --- | --- |
| Logická hodnota | bit |
| Integer | bigint |
| Decimal | float |
| Řetězec | varchar (databázová kola UTF-8) |
| Datum a čas (řetězec ve formátu ISO) | varchar (30) |
| Datum a čas (časové razítko systému UNIX) | bigint |
| Null | `any SQL type` 
| Vnořený objekt nebo pole | varchar (max) (kolace databáze UTF-8), serializovaná jako text JSON |

## <a name="full-fidelity-schema"></a>Schéma s úplnými kvalitami

Azure Cosmos DB plné schéma přesnosti zaznamenává obě hodnoty a jejich nejlepší typy shod pro každou vlastnost v kontejneru. `OPENROWSET`Funkce na kontejneru s schématem s úplnými věrností poskytuje jak typ, tak skutečnou hodnotu v každé buňce. Řekněme, že následující dotaz čte položky z kontejneru se schématem s úplnými věrností:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) as rows
```

Výsledek tohoto dotazu bude vracet typy a hodnoty formátované jako text JSON:

| date_rep | věcech | geo_id |
| --- | --- | --- |
| {"date": "2020-08-13"} | {"Int32": "254"} | {"String": "RS"} |
| {"date": "2020-08-12"} | {"Int32": "235"}| {"String": "RS"} |
| {"date": "2020-08-11"} | {"Int32": "316"} | {"String": "RS"} |
| {"date": "2020-08-10"} | {"Int32": "281"} | {"String": "RS"} |
| {"date": "2020-08-09"} | {"Int32": "295"} | {"String": "RS"} |
| {"String": "2020/08/08"} | {"Int32": "312"} | {"String": "RS"} |
| {"date": "2020-08-07"} | {"float64": "339.0"} | {"String": "RS"} |

U každé hodnoty vidíte typ identifikovaný v Azure Cosmos DB položce kontejneru. Většina hodnot `date_rep` vlastnosti obsahuje `date` hodnoty, ale některé z nich jsou nesprávně uloženy jako řetězce v Azure Cosmos DB. Schéma s úplnou věrností vrátí správně zadané `date` hodnoty a nesprávně naformátované `string` hodnoty.
Počet případů je informace uložená jako `int32` hodnota, ale jedna hodnota je zadaná jako desetinné číslo. Tato hodnota má `float64` typ. Pokud existují některé hodnoty, které překračují největší `int32` číslo, budou uloženy jako `int64` typ. Všechny `geo_id` hodnoty v tomto příkladu jsou uloženy jako `string` typy.

> [!IMPORTANT]
> `OPENROWSET`Funkce bez `WITH` klauzule zveřejňuje obě hodnoty s očekávanými typy a hodnotami s nesprávně zadanými typy. Tato funkce je určená pro zkoumání dat, nikoli pro vytváření sestav. Neanalyzujte hodnoty JSON vrácené z této funkce pro vytváření sestav. K vytvoření sestav použijte explicitní [klauzuli WITH](#query-items-with-full-fidelity-schema) . V kontejneru Azure Cosmos DB byste měli vyčistit hodnoty, které mají nesprávné typy pro aplikování oprav v plném analytickém úložišti věrnosti.

Pokud potřebujete zadat dotaz na účty Azure Cosmos DB typu rozhraní API Mongo DB, můžete získat další informace o úplném vyjádření schématu přesnosti v analytickém úložišti a názvu rozšířených vlastností, které se mají použít v [Azure Cosmos DB analytickém úložišti?](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

### <a name="query-items-with-full-fidelity-schema"></a>Dotazování na položky s úplným schématem přesnosti

Při dotazování na schéma s úplnými přesnostmi musíte explicitně zadat typ SQL a očekávaný typ vlastnosti Azure Cosmos DB v `WITH` klauzuli.

V následujícím příkladu předpokládáme, že `string` je správný typ pro `geo_id` vlastnost a `int32` je správného typu pro `cases` vlastnost:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

Hodnoty pro `geo_id` a `cases` , které mají jiné typy, budou vráceny jako `NULL` hodnoty. Tento dotaz bude odkazovat pouze na `cases` zadaný typ ve výrazu ( `cases.int32` ).

Pokud máte hodnoty s jinými typy ( `cases.int64` , `cases.float64` ), které nelze vyčistit v kontejneru Azure Cosmos DB, je nutné je explicitně odkazovat v `WITH` klauzuli a kombinovat výsledky. Následující dotaz agreguje a `int32` `int64` `float64` ukládá do `cases` sloupce:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

V tomto příkladu je počet případů uložen buď jako `int32` , `int64` nebo jako `float64` hodnoty. Pro výpočet počtu případů na zemi musí být extrahovány všechny hodnoty.

## <a name="known-issues"></a>Známé problémy

- Fond SQL bez serveru vrátí upozornění v době kompilace, pokud `OPENROWSET` řazení sloupce nemá kódování UTF-8. Můžete snadno změnit výchozí kolaci pro všechny `OPENROWSET` funkce běžící v aktuální databázi pomocí příkazu T-SQL `alter database current collate Latin1_General_100_CI_AS_SC_UTF8` .

Možné chyby a akce při řešení potíží jsou uvedené v následující tabulce.

| Chyba | Původní příčina |
| --- | --- |
| Chyby syntaxe:<br/> – Nesprávná syntaxe poblíž textu `Openrowset`<br/> - `...` není rozpoznanou `BULK OPENROWSET` možností poskytovatele.<br/> – Nesprávná syntaxe poblíž textu `...` | Možné hlavní příčiny:<br/> – Nepoužívá CosmosDB jako první parametr.<br/> – Použití řetězcového literálu místo identifikátoru ve třetím parametru.<br/> -Nelze zadat třetí parametr (název kontejneru). |
| V připojovacím řetězci CosmosDB došlo k chybě. | – Účet, databáze nebo klíč není zadaný. <br/> – V připojovacím řetězci je nějaká možnost, která není rozpoznaná.<br/> – Střední ( `;` ) je umístěn na konci připojovacího řetězce. |
| Překlad cesty CosmosDB se nezdařil s chybou "nesprávný název účtu" nebo "nesprávný název databáze". | Zadaný název účtu, název databáze nebo kontejner se nepodařilo najít, nebo nebylo povoleno analytické úložiště do zadané kolekce.|
| Překlad cesty CosmosDB se nezdařil s chybou "nesprávná tajná hodnota" nebo "tajný klíč má hodnotu null nebo je prázdný." | Klíč účtu není platný nebo chybí. |
| Sloupec `column name` typu `type name` není kompatibilní s externím datovým typem `type name` . | Zadaný typ sloupce v klauzuli se `WITH` neshoduje s typem v kontejneru Azure Cosmos DB. Zkuste změnit typ sloupce tak, jak je popsán v části [Azure Cosmos DB mapování typu SQL](#azure-cosmos-db-to-sql-type-mappings), nebo použijte `VARCHAR` typ. |
| Sloupec obsahuje `NULL` hodnoty ve všech buňkách. | Pravděpodobnou příčinou je nesprávný název sloupce nebo výraz cesty v `WITH` klauzuli. Název sloupce (nebo výraz cesty za typem sloupce) v `WITH` klauzuli se musí shodovat s názvem některé vlastnosti v kolekci Azure Cosmos DB. U porovnávání se rozlišují *malá a velká písmena*. Například `productCode` a `ProductCode` jsou různé vlastnosti. |

Návrhy a problémy můžete nahlásit na [stránce pro zpětnou vazbu ke službě Azure synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Použití Power BI a neserverového fondu SQL s propojením Azure synapse](../../cosmos-db/synapse-link-power-bi.md)
- [Vytváření a používání zobrazení ve fondu SQL bez serveru](create-use-views.md)
- [Kurz týkající se vytváření zobrazení fondu SQL bez serveru přes Azure Cosmos DB a jejich propojení s Power BI modely prostřednictvím DirectQuery](./tutorial-data-analyst.md)
