---
title: Dotazování souborů JSON pomocí SQL na vyžádání (Preview)
description: V této části se dozvíte, jak číst soubory JSON pomocí SQL na vyžádání v Azure synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4c1fe9ac5d3b2470fb70231a83e57f3e08d0dfb1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197581"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Dotazování souborů JSON pomocí SQL na vyžádání (Preview) ve službě Azure synapse Analytics

V tomto článku se dozvíte, jak napsat dotaz pomocí SQL na vyžádání (ve verzi Preview) ve službě Azure synapse Analytics. Cílem dotazu je přečíst soubory JSON. Podporované formáty jsou uvedeny ve funkci [OpenRowset](develop-openrowset.md).

## <a name="prerequisites"></a>Požadavky

Než si přečtete zbytek tohoto článku, přečtěte si následující články:

- [Nastavení při prvním spuštění](query-data-storage.md#first-time-setup)
- [Požadavky](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>Ukázkové soubory JSON

Následující část obsahuje ukázkové skripty pro čtení souborů JSON. Soubory jsou uloženy v kontejneru *JSON* , v *knihách*složek a obsahují jednu položku knihy s následující strukturou:

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

Chcete-li zpracovat soubory JSON pomocí JSON_VALUE a [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), je nutné načíst soubor JSON z úložiště jako jeden sloupec. Následující skript přečte soubor *Book1. JSON* jako jeden sloupec:

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
> Načítáte celý soubor JSON jako jeden řádek nebo sloupec. FIELDTERMINATOR, FIELDQUOTE a ROWTERMINATOR jsou tedy nastavené na 0x0B.

## <a name="query-json-files-using-json_value"></a>Dotazování souborů JSON pomocí JSON_VALUE

Následující dotaz ukazuje, jak použít [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) k načtení skalárních hodnot (title, Publisher) z knihy s názvem *pravděpodobnostní a statistických metod v Cryptology, Úvod do vybraných článků*:

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

## <a name="query-json-files-using-json_query"></a>Dotazování souborů JSON pomocí JSON_QUERY

Následující dotaz ukazuje, jak pomocí [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) načíst objekty a pole (autoři) z knihy s názvem *pravděpodobnostní a statistické metody v Cryptology, Úvod do vybraných témat*:

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

## <a name="query-json-files-using-openjson"></a>Dotazování souborů JSON pomocí OPENJSON

Následující dotaz používá [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Načte objekty a vlastnosti v rámci knihy s názvem *pravděpodobnostní a statistické metody v Cryptology, Úvod do vybraných článků*:

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
        jsonContent NVARCHAR(max) -- Use appropriate length. Make sure JSON fits. 
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Další kroky

Další články v této sérii vám poukazují, jak:

- [Dotazování na složky a více souborů](query-folders-multiple-csv-files.md)
- [Vytváření a používání zobrazení](create-use-views.md)
