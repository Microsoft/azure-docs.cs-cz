---
title: Analýza schématu pomocí polí a vnořených struktur
description: Jak analyzovat pole a vnořené struktury pomocí Apache Spark a SQL
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: b02c3627cea5e441739c77d1882505c6b82489bc
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908085"
---
# <a name="analyze-complex-data-types-in-synapse"></a>Analýza složitých datových typů v synapse

Tento článek se týká souborů a kontejnerů Parquet v **odkazu na Azure synapse pro Azure Cosmos DB**. Vysvětluje, jak můžou uživatelé pomocí Sparku nebo SQL číst nebo transformovat data pomocí komplexního schématu, jako jsou pole nebo vnořené struktury. Následující příklad se provádí s jedním dokumentem, ale může snadno škálovat na miliardy dokumentů pomocí Sparku nebo SQL. Níže uvedený kód používá PySpark (Python).

## <a name="use-case"></a>Případ použití

S moderními datovými typy jsou komplexní datové typy často běžné pro zpracování a představují výzvu pro inženýry dat. Analýza vnořeného schématu a polí prezentuje problémy:
* Složité zápisy dotazů SQL
* Datový typ pro vnořené sloupce je obtížné přejmenovat/přetypovat.
* Dosažení potíží s výkonem u hluboce vnořených objektů

Technici dat musí pochopit, jak efektivně zpracovávat tyto datové typy a usnadnit přístup všem uživatelům.

V následujícím příkladu se synapse Spark použije ke čtení a transformaci objektů pomocí datových snímků do ploché struktury. Synapse SQL Server bez serveru se používá k dotazování na takové objekty přímo a vrátí tyto výsledky jako běžnou tabulku.

## <a name="what-are-arrays-and-nested-structures"></a>Co jsou pole a vnořené struktury?

Následující objekt pochází z přehledu aplikace. V tomto objektu jsou vnořené struktury, ale také pole obsahující vnořené struktury.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>Příklad schématu polí a vnořených struktur
Při tisku schématu datového rámce tohoto objektu (s názvem **DF**) pomocí příkazu **DF. printschema**se zobrazí následující reprezentace:

* žlutá barva představuje vnořenou strukturu
* zelená barva představuje pole se dvěma prvky.

[![Původ schématu](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

_rid, _ts a _etag byly přidány v systému, protože dokument byl zařazen do Azure Cosmos DB transakčního úložiště.

Výše uvedený datový rámec se počítá pro 5 sloupců a 1 řádek. Po transformaci bude mít datový rámec s obdobu 13 sloupců a 2 řádky ve formátu tabulky.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Sloučit vnořené struktury a rozbalit pole pomocí Apache Spark

Pomocí synapse Sparku transformují vnořené struktury na sloupce a prvky pole do více řádků, je snadné. Následující kroky lze použít pro všechny vlastní implementace.

[![Kroky pro transformace Sparku](./media/how-to-complex-schema/spark-transfo-steps.png)](./media/how-to-complex-schema/spark-transfo-steps.png#lightbox)

**Krok 1**: definujeme funkci pro sloučení vnořeného schématu. Tato funkce se dá použít bez změny. Vytvořte v Pyspark poznámkovém bloku buňku s touto funkcí:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

**Krok 2**: použití funkce pro sloučení vnořeného schématu datového rámce **DF** do nového datového rámce **df_flat**:

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

Funkce Display by měla vracet 10 sloupců a 1 řádek. Pole a jeho vnořené prvky jsou stále k dispozici.

**Krok 3**: transformaci pole **context_custom_dimensions** v datovém bloku jsme teď **df_flat** do nového **df_flat_explode**datového rámce. V níže uvedeném kódu definujeme, který sloupec vyberte:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

Funkce Display by měla vracet následující výsledek: 10 sloupců a 2 řádky. Dalším krokem je sloučení vnořených schémat pomocí funkce definované v kroku 1.

**Krok 4**: použití funkce pro sloučení vnořeného schématu datového rámce **df_flat_explode** do nového datového rámce **df_flat_explode_flat**:
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

Funkce Display by měla zobrazit 13 sloupců a 2 řádky:

Funkce printSchema datového rámce df_flat_explode_flat vrátí následující výsledek:

[![Schéma – konečné](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Čtení polí a vnořených struktur přímo bez SQL serveru

Dotazování, vytváření zobrazení a tabulek přes tyto objekty je možné bez SQL serveru.

První ze všech, podle toho, jak jsou data uložená, by uživatelé měli používat následující taxonomii. Všechna velká písmena jsou specifická pro váš případ použití:

| ROZMĚR              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = Account; Database = DatabaseName; Collection = CollectionName; region = REGIONTOQUERY, Secret = ' YOURSECRET ' |' CosmosDB ' (odkaz synapse)|



**SQL Server** bude podporovat propojenou službu pro Azure synapse Link pro Azure Cosmos DB a průchozí služby AAD. Tato funkce je v současné době v rámci ověřované verze Preview pro synapse odkaz.

Nahradit níže:
* ' Vaše hromadný výše ' připojovacím řetězcem zdroje dat, ke kterému se připojujete
* ' Váš typ výše ' podle formátu používaného pro připojení ke zdroji

```sql
select *
FROM
openrowset(
BULK 'YOUR BULK ABOVE',
            FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Existují dva různé typy operací:
* Následující řádek kódu bude definovat sloupec s názvem contextdataeventTime, který odkazuje na vnořený element: Context. data. čas události
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

Tento řádek definuje sloupec s názvem contextdataeventTime, který odkazuje na vnořený element: Context>data>čas události

* k vytvoření nových řádků pro každý prvek v poli a následným definováním každého vnořeného objektu, který je podobný prvnímu bodu odrážek, se použije **průřez** . 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Pokud má pole 5 prvků se 4 vnořenými strukturami, SQL Server bez serveru vrátí 5 řádků a 4 sloupce.

SQL bez serveru se může dotazovat na místě, mapovat pole ve dvou řádcích a zobrazit všechny vnořené struktury do sloupců.

## <a name="next-steps"></a>Další kroky

* [Naučte se dotazovat Azure synapse Link pro Azure Cosmos DB pomocí Sparku.](./synapse-link/how-to-query-analytical-store-spark.md)
* [Vnořené typy dotazů Parquet](./sql/query-parquet-nested-types.md) 