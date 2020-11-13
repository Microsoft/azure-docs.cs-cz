---
title: Dotazování na data Azure Cosmos DB pomocí SQL fondu bez serveru v odkazu Azure synapse (Preview)
description: V tomto článku se naučíte, jak zadávat dotazy na Azure Cosmos DB s využitím fondu SQL bez serveru ve službě Azure synapse Link (Preview).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 087ee796fbd3c0563b8019a062acab9c7ad80bb1
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579381"
---
# <a name="query-azure-cosmos-db-data-with-serverless-sql-pool-in-azure-synapse-link-preview"></a>Dotazování na data Azure Cosmos DB ve fondu SQL bez serveru v odkazu Azure synapse (Preview)

Synapse fond SQL bez serveru umožňuje analyzovat data v kontejnerech Azure Cosmos DB, které jsou povolené pomocí [Azure synapse Link](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) v téměř reálném čase, aniž by to ovlivnilo výkon transakčních úloh. Nabízí známou syntaxi T-SQL pro dotazování dat z [analytického úložiště](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) a integrovaného připojení k široké škále nástrojů pro dotazování BI a ad-hoc, a to prostřednictvím rozhraní T-SQL.

Pro dotazování Azure Cosmos DB je dostupná plocha kompletního [výběru](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) plochy prostřednictvím funkce [OpenRowset](develop-openrowset.md) , včetně většiny [funkcí a operátorů SQL](overview-features.md). Můžete také uložit výsledky dotazu, který čte data z Azure Cosmos DB společně s daty v Azure Blob Storage nebo Azure Data Lake Storage pomocí příkazu [vytvořit externí tabulku jako SELECT](develop-tables-cetas.md#cetas-in-serverless-sql-pool). V současné době nemůžete ukládat výsledky dotazu fondu SQL bez serveru do Azure Cosmos DB pomocí CETAS. 

V tomto článku se dozvíte, jak napsat dotaz s neserverovým fondem SQL, který bude dotazovat data z Azure Cosmos DB kontejnerů s povoleným odkazem synapse. Pak si můžete přečíst další informace o vytváření zobrazení fondu SQL bez serveru nad kontejnery Azure Cosmos DB a jejich propojením s Power BI modely v [tomto](./tutorial-data-analyst.md) kurzu. 

> [!IMPORTANT]
> V tomto kurzu se používá kontejner s [Azure Cosmos DB dobře definovaným schématem](../../cosmos-db/analytical-store-introduction.md#schema-representation). Dotazování fondu SQL bez serveru poskytuje [Azure Cosmos DB úplných schémat přesnosti](#full-fidelity-schema) je dočasné chování, které se změní v závislosti na zpětné vazbě ve verzi Preview. Nespoléhá se na schéma sady výsledků `OPENROWSET` funkce bez `WITH` klauzule, která čte data z kontejneru se schématem s úplnými zobrazeními, protože prostředí dotazů může být změněno a zarovnané pomocí dobře definovaného schématu. Pošlete nám svůj názor na [Fórum o zpětné vazbě Azure synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics) nebo kontaktujte [produktový tým synapse Link](mailto:cosmosdbsynapselink@microsoft.com) , abyste mohli poskytnout zpětnou vazbu.

## <a name="overview"></a>Přehled

Aby bylo možné podporovat dotazování a analýzu dat ve službě Azure Cosmos DB analytické úložiště, fond SQL bez serveru používá následující `OPENROWSET` syntaxi:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Připojovací řetězec Azure Cosmos DB určuje Azure Cosmos DB název účtu, název databáze, hlavní klíč databázového účtu a nepovinný název oblasti, který má `OPENROWSET` fungovat. 

> [!IMPORTANT]
> Ujistěte se, že používáte určitou databázovou kolaci UTF-8 (například `Latin1_General_100_CI_AS_SC_UTF8` ), protože řetězcové hodnoty v Cosmos DB analytických obchodech jsou kódované jako text v kódování UTF-8.
> Neshoda mezi kódováním textu v souboru a kolaci může způsobit neočekávané chyby převodu textu.
> Výchozí kolaci aktuální databáze můžete snadno změnit pomocí následujícího příkazu T-SQL: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

Připojovací řetězec má následující formát:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Název kontejneru Azure Cosmos DB je zadán bez uvozovek v `OPENROWSET` syntaxi. Pokud má název kontejneru nějaké speciální znaky (například pomlčku '-'), název by měl být zabalen do `[]` (hranaté závorky) v `OPENROWSET` syntaxi.

> [!NOTE]
> Fond SQL bez serveru nepodporuje dotazování Azure Cosmos DB transakčním úložištěm.

## <a name="sample-data-set"></a>Ukázková datová sada

Příklady v tomto článku jsou založené na datech z [Evropského centra pro prevenci a řízení nemocí (ECDC) COVID – 19 případů](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) a [COVID-19 Open Research DataSet (šňůra 19), Doi: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

Můžete si Zobrazit licenci a strukturu dat na těchto stránkách a stáhnout ukázková data pro sady dat [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) a [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) .

Pokud chcete postupovat podle tohoto článku předvádí, jak zadávat dotazy na data Cosmos DB s neserverovým fondem SQL, ujistěte se, že jste vytvořili tyto prostředky:
* Účet databáze Azure Cosmos DB s [povoleným odkazem synapse](../../cosmos-db/configure-synapse-link.md)
* Azure Cosmos DB databáze s názvem `covid`
* Byly načteny dva kontejnery Azure Cosmos DB s názvem `EcdcCases` a `Cord19` nad sadou vzorových datových sad.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Zkoumání Azure Cosmos DB dat pomocí automatického odvození schématu

Nejjednodušší způsob, jak prozkoumat data v Azure Cosmos DB, je využití možnosti automatického odvození schématu. Vyvoláním `WITH` klauzule z `OPENROWSET` příkazu můžete nastavit fond SQL bez serveru na automatické rozpoznávání (odvodit) schéma analytického úložiště kontejneru Azure Cosmos DB.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
V předchozím příkladu dáváme pokyn k tomu, aby se fond SQL bez serveru připojil k `covid` databázi v Azure Cosmos DB účet `MyCosmosDbAccount` ověřený pomocí Azure Cosmos DB Key (fiktivní v předchozím příkladu). Pak přistupujeme k `EcdcCases` analytickému úložišti kontejneru v `West US 2` oblasti. Vzhledem k tomu, že neexistuje žádná projekce specifických vlastností, `OPENROWSET` funkce vrátí všechny vlastnosti z Azure Cosmos DBch položek. 

V části předpokládá se, že položky v kontejneru Cosmos DB mají `date_rep` `cases` vlastnosti, a `geo_id` . výsledky tohoto dotazu jsou uvedeny v následující tabulce:

| date_rep | věcech | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

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

Azure Cosmos DB umožňuje znázornit složitější datové modely jejich sestavou jako vnořené objekty nebo pole. Funkce AutoSync pro synapse Link pro Azure Cosmos DB spravuje reprezentaci schématu v analytickém úložišti, která zahrnuje zpracování vnořených datových typů, což umožňuje získat bohatou dotazování z fondu SQL bez serveru.

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

Přečtěte si další informace o analýze [složitých datových typů v odkazech na synapse](../how-to-analyze-complex-schema.md) a [ve vnořených strukturách ve fondu SQL bez serveru](query-parquet-nested-types.md).

> [!IMPORTANT]
> Pokud se v textu zobrazí neočekávané znaky jako `MÃƒÂ©lade` místo, není `Mélade` kolace databáze nastavena na kolaci [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . 
> [Změňte kolaci databáze](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) na určitou kolaci UTF8 pomocí nějakého příkazu SQL `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` , jako je.

## <a name="flattening-nested-arrays"></a>Sloučení vnořených polí

Data Azure Cosmos DB můžou mít vnořená podpole, jako je pole autora ze sady dat [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) :

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

Je důležité nejdřív si uvědomit, že zatímco Azure Cosmos DB transakčního úložiště je Schema-nezávislá, je analytické úložiště schematized na optimalizaci pro výkon analytických dotazů. Díky schopnosti autosync v synapse Azure Cosmos DB propojení se v analytickém úložišti nástroje spravují reprezentace schématu, která zahrnuje zpracování vnořených datových typů. Vzhledem k tomu, že fond SQL bez serveru se dotazuje na analytické úložiště, je důležité pochopit, jak namapovat Azure Cosmos DB vstupní datové typy na datové typy SQL.

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

## <a name="full-fidelity-schema"></a>Schéma s úplnými kvalitami

Azure Cosmos DB plné schéma přesnosti zaznamenává obě hodnoty a jejich nejlepší typy shod pro každou vlastnost v kontejneru.
`OPENROWSET` funkce na kontejneru s schématem s plnou přesností poskytuje jak typ, tak skutečnou hodnotu v každé buňce. Řekněme, že následující dotaz čte položky z kontejneru se schématem s úplnými věrností:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
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

U každé hodnoty vidíte typ identifikovaný v Cosmos DB položka kontejneru. Většina hodnot `date_rep` vlastnosti obsahuje `date` hodnoty, ale některé z nich jsou nesprávně uloženy jako řetězce v Cosmos DB. Schéma s úplnou věrností vrátí správně zadané `date` hodnoty a nesprávně naformátované `string` hodnoty.
Počet případů je informace uložená jako `int32` hodnota, ale jedna hodnota je zadána jako desetinné číslo. Tato hodnota je `float64` typu. Pokud existují některé hodnoty, které překračují největší `int32` číslo, budou uloženy jako `int64` typ. Všechny `geo_id` hodnoty v tomto příkladu jsou uloženy jako `string` typy.

> [!IMPORTANT]
> `OPENROWSET` klauzule bez `WITH` klauzule zveřejňuje obě hodnoty s očekávanými typy a hodnotami s nesprávně zadanými typy. Tato funkce je navržena pro zkoumání dat, nikoli pro vytváření sestav. Neanalyzujte hodnoty JSON vrácené z této funkce pro vytváření sestav a použití explicitní [klauzule WITH](#querying-items-with-full-fidelity-schema) k vytvoření sestav.
> Měli byste vyčistit hodnoty, které mají nesprávné typy v kontejneru Azure Cosmos DB, aby bylo možné použít korect v plném analytickém úložišti s přesností. 

Pro dotazování Azure Cosmos DBch účtů typu rozhraní API služby Mongo DB můžete získat další informace o úplném vyjádření schématu přesnosti v analytickém úložišti a o rozšířených názvech vlastností, které se [tady](../../cosmos-db/analytical-store-introduction.md#analytical-schema)mají použít.

### <a name="querying-items-with-full-fidelity-schema"></a>Dotazování na položky pomocí schématu s plnou věrností

Při dotazování schématu na celé přesnosti musíte explicitně zadat typ SQL a očekávaný Cosmos DB typ vlastnosti v `WITH` klauzuli. Nepoužívejte `OPENROWSET` klauzuli bez `WITH` klauzule v sestavách, protože formát sady výsledků může být změněn ve verzi Preview na základě zpětné vazby.

V následujícím příkladu se předpokládá, že `string` je správný typ pro `geo_id` vlastnost a `int32` správný typ pro `cases` vlastnost:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

Hodnoty pro `geo_id` a `cases` , které mají jiné typy, budou vráceny jako `NULL` hodnoty. Tento dotaz bude odkazovat pouze na `cases` zadaný typ ve výrazu ( `cases.int32` ).

Pokud máte hodnoty s jinými typy ( `cases.int64` , `cases.float64` ), které nelze vyčistit v Cosmos DB kontejneru, bude nutné explicitně odkazovat v `WITH` klauzuli in a kombinovat výsledky. Následující dotaz agreguje a `int32` `int64` `float64` ukládá do `cases` sloupce:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

V tomto příkladu je počet případů uložen buď jako `int32` , `int64` nebo `float64` hodnoty a všechny hodnoty musí být extrahovány, aby bylo možné vypočítat počet případů na zemi. 

## <a name="known-issues"></a>Známé problémy

- Dotaz na možnosti, že fond SQL bez serveru poskytuje [Azure Cosmos DB úplných schémat přesnosti](#full-fidelity-schema) je dočasné chování, které se změní na základě zpětné vazby ve verzi Preview. Nespoléhá se na schéma, které `OPENROWSET` funkce bez `WITH` klauzule poskytuje během veřejné verze Preview, protože prostředí dotazů může být zarovnané podle dobře definovaného schématu na základě zpětné vazby od zákazníků. Pokud chcete poskytnout zpětnou vazbu, kontaktujte [produktový tým synapse Link](mailto:cosmosdbsynapselink@microsoft.com) .
- Fond SQL bez serveru nebude vracet chybu při kompilaci, pokud `OPENROSET` kolace sloupce nemá kódování UTF-8. Výchozí kolaci pro všechny `OPENROWSET` funkce běžící v aktuální databázi můžete snadno změnit pomocí následujícího příkazu T-SQL: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

Možné chyby a akce při řešení potíží jsou uvedené v následující tabulce:

| Chyba | Původní příčina |
| --- | --- |
| Chyby syntaxe:<br/> – Nesprávná syntaxe v blízkosti položky OpenRowset<br/> - `...` není rozpoznaná možnost HROMADNÉho poskytovatele OPENROWSET.<br/> – Nesprávná syntaxe poblíž textu `...` | Možné hlavní příčiny<br/> -Nepoužívá se jako první parametr ' CosmosDB ',<br/> -Použití řetězcového literálu místo identifikátoru ve třetím parametru<br/> -Nespecifikuje se třetí parametr (název kontejneru). |
| V připojovacím řetězci CosmosDB došlo k chybě. | – Účet, databáze, klíč není zadaný. <br/> -V připojovacím řetězci existuje možnost, která není rozpoznána.<br/> – Středník `;` je umístěný na konci připojovacího řetězce. |
| Překlad cesty CosmosDB se nezdařil s chybou, nesprávný název účtu nebo nesprávný název databáze. | Zadaný název účtu, název databáze nebo kontejner se nenašel nebo nebylo povolené analytické úložiště o zadané kolekci.|
| Překlad cesty CosmosDB se nezdařil s chybou "nesprávná tajná hodnota" nebo "tajný klíč má hodnotu null nebo je prázdný". | Klíč účtu není platný nebo chybí. |
| Sloupec `column name` typu není `type name` kompatibilní s externím datovým typem. `type name` | Zadaný typ sloupce `WITH` klauzule neodpovídá typu v kontejneru Cosmos DB. Zkuste změnit typ sloupce tak, jak je popsán v části [Azure Cosmos DB mapování typu SQL](#azure-cosmos-db-to-sql-type-mappings) nebo `VARCHAR` typ použití. |
| Sloupec obsahuje `NULL` hodnoty ve všech buňkách. | Pravděpodobnou příčinou je nesprávný název sloupce nebo výraz cesty v `WITH` klauzuli. Název sloupce (nebo výraz cesty za typem sloupce) klauzule v `WITH` klauzuli Cosmos DB Collection se musí shodovat s názvem některé vlastnosti. Porovnávání rozlišuje **velká a malá písmena**  (například `productCode` a `ProductCode` jsou různé vlastnosti). |

Návrhy a problémy můžete nahlásit na [stránce s názory na Azure synapse](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Použití Power BI a neserverového fondu SQL s propojením Azure synapse](../../cosmos-db/synapse-link-power-bi.md)
- [Postupy: vytváření a používání zobrazení ve fondu SQL bez serveru](create-use-views.md) 
- [Kurz týkající se vytváření zobrazení fondu SQL bez serveru přes Azure Cosmos DB a jejich propojení s Power BI modely prostřednictvím DirectQuery](./tutorial-data-analyst.md)
