---
title: Dotaz na soubory JSON pomocí SQL na vyžádání (náhled)
description: Tato část vysvětluje, jak číst soubory JSON pomocí SQL na vyžádání v Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1054328216d0517b3f450ba4fe08f3bef32d68f7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431719"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Dotaz na soubory JSON pomocí SQL na vyžádání (preview) v Azure Synapse Analytics

V tomto článku se dozvíte, jak napsat dotaz pomocí SQL na vyžádání (preview) v Azure Synapse Analytics. Cílem dotazu je číst soubory JSON.

## <a name="prerequisites"></a>Požadavky

Než si přečtete zbytek tohoto článku, přečtěte si následující články:

- [První nastavení](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>Ukázka souborů JSON

Níže uvedená část obsahuje ukázkové skripty pro čtení souborů JSON. Soubory jsou uloženy v kontejneru *json,* *knihy*složek a obsahují jednu položku knihy s následující strukturou:

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

## <a name="read-json-files"></a>Čtení souborů JSON

Chcete-li zpracovat soubory JSON pomocí JSON_VALUE a [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), je třeba číst soubor JSON z úložiště jako jeden sloupec. Následující skript přečte soubor *book1.json* jako jeden sloupec:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/book1.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r];
```

> [!NOTE]
> Čtete celý soubor JSON jako jeden řádek nebo sloupec. Takže FIELDTERMINATOR, FIELDQUOTE a ROWTERMINATOR jsou nastaveny na 0x0b.

## <a name="query-json-files-using-json_value"></a>Dotaz na soubory JSON pomocí JSON_VALUE

Níže uvedený dotaz ukazuje, jak používat [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) k načtení skalárních hodnot (název, vydavatel) z knihy s názvem *Pravděpodobnostní a statistické metody v kryptologii, Úvod vybranými články*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-json_query"></a>Dotaz na soubory JSON pomocí JSON_QUERY

Následující dotaz ukazuje, jak používat [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) k načtení objektů a polí (autorů) z knihy s názvem *Pravděpodobnostní a statistické metody v kryptologii, Úvod podle vybraných témat*:

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-openjson"></a>Dotaz na soubory JSON pomocí OPENJSON

Následující dotaz používá [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Načte objekty a vlastnosti v knize s názvem *Pravděpodobnostní a statistické metody v kryptologii, Úvod vybranými články*:

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(4000) --Note that you have to use NVARCHAR(4000) for OPENJSON to work.
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Další kroky

Další články v této sérii předvedou, jak:

- [Dotazování složek a více souborů](query-folders-multiple-csv-files.md)
- [Vytváření a používání zobrazení](create-use-views.md).
