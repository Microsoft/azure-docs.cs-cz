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
ms.openlocfilehash: ad6761466cc958235557609e929e641a0311ee43
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999161"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Analýza složitých datových typů ve službě Azure synapse Analytics

Tento článek se týká souborů Parquet a kontejnerů v [odkazu na synapse pro Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). Vysvětluje, jak můžou uživatelé pomocí Sparku nebo SQL číst nebo transformovat data pomocí složitých schémat, jako jsou pole nebo vnořené struktury. Následující příklad je dokončen s jedním dokumentem, ale lze jej snadno škálovat na miliardy dokumentů pomocí Sparku nebo SQL. Kód obsažený v tomto článku používá PySpark (Python).

## <a name="use-case"></a>Případ použití

Komplexní datové typy jsou stále běžné a představují výzvu pro inženýry dat při analýze vnořeného schématu a polí, které mají za úkol zahrnovat časově náročné a složité dotazy SQL. Kromě toho může být obtížné přejmenovat nebo přetypovat datový typ vnořené sloupce. Problémy s výkonem také vznikají při práci s hluboce vnořenými objekty.

Technici dat potřebují porozumět tomu, jak efektivně zpracovávat komplexní datové typy a snadno dostupné všem.

V následujícím příkladu se synapse Spark používá ke čtení a transformaci objektů do ploché struktury prostřednictvím datových rámců. Synapse SQL Server bez serveru se používá k dotazování takových objektů přímo a vrácení těchto výsledků jako běžné tabulky.

## <a name="what-are-arrays-and-nested-structures"></a>Co jsou pole a vnořené struktury?

Následující objekt pochází z [přehledu aplikace](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). V tomto objektu jsou vnořené struktury a pole obsahující vnořené struktury.

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
Při tisku schématu datového rámce objektu (s názvem **DF**) pomocí příkazu se `df.printschema` zobrazí následující reprezentace:

* Žlutá barva představuje vnořenou strukturu
* Zelená barva představuje pole se dvěma elementy.

[![Původ schématu](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

**_rid**, **_ts**a **_etag** byly přidány do systému, protože dokument byl zařazen do Azure Cosmos DB transakčního úložiště.

Výše uvedený datový rámec se počítá pro 5 sloupců a 1 řádek. Po transformaci bude mít datový rámec s obdobu 13 sloupců a 2 řádky ve formátu tabulky.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Sloučit vnořené struktury a rozbalit pole pomocí Apache Spark

Pomocí synapse Sparku je snadné transformovat vnořené struktury na sloupce a prvky pole na více řádků. K implementaci lze použít následující kroky.

[![Kroky pro transformace Sparku](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

**Krok 1**: definujeme funkci pro sloučení vnořeného schématu. Tato funkce se dá použít bez změny. Vytvořte v [PySpark poznámkovém bloku](quickstart-apache-spark-notebook.md) buňku s následující funkcí:

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

**Krok 2**: použití funkce pro sloučení vnořeného schématu datového rámce (**DF**) do nového datového rámce `df_flat` :

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

Funkce Display by měla vracet 10 sloupců a 1 řádek. Pole a jeho vnořené prvky jsou stále k dispozici.

**Krok 3**: transformace pole `context_custom_dimensions` v datovém rámečku `df_flat` do nového datového rámce `df_flat_explode` . V následujícím kódu definujeme, který sloupec vybrat:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

Funkce Display by měla vracet 10 sloupců a 2 řádky. Dalším krokem je sloučení vnořených schémat pomocí funkce definované v kroku 1.

**Krok 4**: použití funkce pro sloučení vnořeného schématu datového rámce `df_flat_explode` do nového datového rámce `df_flat_explode_flat` :
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

Funkce Display by měla zobrazit 13 sloupců a 2 řádky.

Funkce `printSchema` datového rámce `df_flat_explode_flat` vrátí následující výsledek:

[![Schéma – konečné](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Čtení polí a vnořených struktur přímo bez SQL serveru

Dotazování a vytváření zobrazení a tabulek přes tyto objekty je možné bez SQL serveru.

V závislosti na tom, jak byla data uložena, by uživatelé měli použít následující taxonomii. Všechno zobrazené v HORNÍm případě je specifické pro váš případ použití:

| ROZMĚR              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = Account; Database = DatabaseName; Collection = CollectionName; region = REGIONTOQUERY, Secret = ' YOURSECRET ' |' CosmosDB ' (odkaz synapse)|


> [!NOTE]
> SQL Server bude podporovat propojenou službu pro synapse propojení Azure Cosmos a AAD PassThrough. Tato funkce je v současné době v rámci ověřované verze Preview pro synapse odkaz.

Jednotlivá pole nahraďte následujícím způsobem:
* ' Vaše HROMADná výše ' = připojovací řetězec zdroje dat, ke kterému se připojujete
* ' Váš typ výše ' = formát, který používáte pro připojení ke zdroji

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

První typ operace je uveden v následujícím řádku kódu, který definuje sloupec `contextdataeventTime` s názvem, který odkazuje na vnořený element: Context. data. čas události 
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

Tento řádek definuje sloupec s názvem contextdataeventTime, který odkazuje na vnořený element: Context>data>čas události

Druhý typ operace používá `cross apply` k vytvoření nových řádků pro každý prvek v poli a poté s definuje každý vnořený objekt podobný prvnímu bodu odrážky: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Pokud má pole 5 prvků se 4 vnořenými strukturami, SQL Server bez serveru vrátí 5 řádků a 4 sloupce. SQL bez serveru se může dotazovat na místě, mapovat pole ve dvou řádcích a zobrazit všechny vnořené struktury do sloupců.

## <a name="next-steps"></a>Další kroky

* [Naučte se dotazovat synapse odkaz na Azure Cosmos DB pomocí Sparku.](./synapse-link/how-to-query-analytical-store-spark.md)
* [Vnořené typy dotazů Parquet](./sql/query-parquet-nested-types.md) 