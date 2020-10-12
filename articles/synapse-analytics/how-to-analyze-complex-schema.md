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
ms.openlocfilehash: 51422bd47b5bd2d7d5103c154e90eaa910396024
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89661021"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Analýza složitých datových typů ve službě Azure synapse Analytics

Tento článek se týká souborů a kontejnerů Parquet v [odkazu na Azure synapse pro Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). Spark nebo SQL můžete použít ke čtení nebo transformaci dat pomocí složitých schémat, jako jsou pole nebo vnořené struktury. Následující příklad je dokončen s jedním dokumentem, ale může snadno škálovat na miliardy dokumentů pomocí Sparku nebo SQL. Kód obsažený v tomto článku používá PySpark (Python).

## <a name="use-case"></a>Případ použití

Komplexní datové typy jsou stále běžné a představují výzvu pro inženýry dat. Analýza vnořeného schématu a polí může zahrnovat časově náročné a složité dotazy SQL. Kromě toho může být obtížné přejmenovat nebo přetypovat datový typ vnořené sloupce. Při práci s hluboce vnořenými objekty můžete také narazit na problémy s výkonem.

Technici dat potřebují porozumět tomu, jak efektivně zpracovávat komplexní datové typy a snadno dostupné všem. V následujícím příkladu použijete Spark ve službě Azure synapse Analytics ke čtení a transformaci objektů do ploché struktury prostřednictvím datových rámců. Model SQL bez serveru v Azure synapse Analytics použijete k dotazování těchto objektů přímo a vrátíte tyto výsledky jako běžnou tabulku.

## <a name="what-are-arrays-and-nested-structures"></a>Co jsou pole a vnořené struktury?

Následující objekt pochází z [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). V tomto objektu jsou vnořené struktury a pole obsahující vnořené struktury.

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

* Žlutá reprezentuje vnořené struktury.
* Zelená představuje pole se dvěma prvky.

[![Kód se žlutým a zeleným zvýrazněním, který ukazuje původ schématu](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

`_rid`, `_ts` a byly `_etag` přidány do systému, protože dokument byl přijat do transakčního úložiště Azure Cosmos DB.

Předchozí datový rámec se počítá jenom z 5 sloupců a 1 řádek. Po transformaci bude mít datový rámec s obdobu 13 sloupců a 2 řádky ve formátu tabulky.

## <a name="flatten-nested-structures-and-explode-arrays"></a>Shrnout vnořené struktury a rozbalit pole

Pomocí Sparku ve službě Azure synapse Analytics je možné snadno transformovat vnořené struktury na sloupce a prvky pole na více řádků. K implementaci použijte následující postup.

[![Vývojový diagram znázorňující kroky pro transformace Spark](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>Definovat funkci pro sloučení vnořeného schématu

Tuto funkci můžete použít beze změny. Vytvořte v [PySpark poznámkovém bloku](quickstart-apache-spark-notebook.md) buňku s následující funkcí:

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

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Použití funkce pro sloučení vnořeného schématu

V tomto kroku sloučíte vnořené schéma datového rámce (**DF**) do nového datového rámce ( `df_flat` ):

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

Funkce Display by měla vracet 10 sloupců a 1 řádek. Pole a jeho vnořené prvky jsou stále k dispozici.

### <a name="transform-the-array"></a>Transformovat pole

Zde můžete transformovat pole, `context_custom_dimensions` v datovém rámečku na `df_flat` nový datový rámec `df_flat_explode` . V následujícím kódu můžete také definovat, který sloupec vybrat:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

Funkce Display by měla vracet 10 sloupců a 2 řádky. Dalším krokem je sloučení vnořených schémat pomocí funkce definované v kroku 1.

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Použití funkce pro sloučení vnořeného schématu

Nakonec použijete funkci pro sloučení vnořeného schématu datového rámce `df_flat_explode` do nového datového rámce `df_flat_explode_flat` :
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

Funkce Display by měla zobrazit 13 sloupců a 2 řádky.

Funkce `printSchema` datového rámce `df_flat_explode_flat` vrátí následující výsledek:

[![Kód zobrazující konečné schéma](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>Číst pole a vnořené struktury přímo

S modelem SQL bez serveru můžete zadávat dotazy a vytvářet zobrazení a tabulky nad těmito objekty.

V závislosti na tom, jak byla data uložena, by uživatelé měli použít následující taxonomii. Vše zobrazené velkými písmeny je specifické pro váš případ použití:

| Hromadné ingestování | Formát |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = Account; Database = DatabaseName; Collection = CollectionName; region = REGIONTOQUERY ', Secret = ' YOURSECRET ' |' CosmosDB ' (odkaz Azure synapse)|


Jednotlivá pole nahraďte následujícím způsobem:
* ' Vaše HROMADně nahoře je připojovací řetězec zdroje dat, ke kterému se připojujete.
* ' Váš typ výše ' je formát, který používáte pro připojení ke zdroji.

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

- První typ operace je uveden v následujícím řádku kódu, který definuje sloupec `contextdataeventTime` s názvem, který odkazuje na vnořený element, `Context.Data.eventTime` . 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  Tento řádek definuje sloupec s názvem `contextdataeventTime` , který odkazuje na vnořený element `Context>Data>eventTime` .

- Druhý typ operace používá `cross apply` k vytvoření nových řádků pro každý prvek v poli. Pak definuje každý vnořený objekt. 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  Pokud má pole 5 prvků se 4 vnořenými strukturami, model SQL bez serveru vrátí 5 řádků a 4 sloupce. Model SQL bez serveru se může dotazovat na místě, namapovat pole ve dvou řádcích a zobrazit všechny vnořené struktury na sloupce.

## <a name="next-steps"></a>Další kroky

* [Naučte se dotazovat synapse odkaz na Azure Cosmos DB pomocí Sparku.](./synapse-link/how-to-query-analytical-store-spark.md)
* [Dotazování vnořených typů Parquet](./sql/query-parquet-nested-types.md) 
