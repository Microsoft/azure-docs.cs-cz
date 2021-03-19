---
title: sys.external_job_streams (Transact-SQL) – Azure SQL Edge
description: Další informace o použití sys.external_job_streams ve službě Azure SQL Edge
keywords: sys.external_job_streams, Edge SQL
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 35010d3aba7f6d5ee3185291c917ff7726ba8bd7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90900349"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys.external_job_streams (Transact-SQL)

Vrátí řádek pro vstupní nebo výstupní externí objekt datového proudu namapovaný na externí úlohu streamování.

|Název sloupce|Datový typ|Popis|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| Identifikační číslo objektu pro objekt úlohy streamování. Tento sloupec se mapuje na sloupec object_id sys.external_streaming_jobs.|
|**stream_id**|**int**| Identifikační číslo objektu pro objekt datového proudu. Tento sloupec se mapuje na sloupec object_id sys.external_streams. |
|**is_input**|**bit**| 1, pokud je objekt datového proudu použit jako vstup pro úlohu streamování, jinak 0.|
|**is_output**|**bit**| 1, pokud je objekt datového proudu použit jako výstup pro úlohu streamování, jinak 0.|

## <a name="example"></a>Příklad

Toto zobrazení katalogu se používá spolu se `sys.external_streams` `sys.external_streaming_jobs` zobrazeními katalogu a. Níže je uvedený ukázkový dotaz.

```sql
Select
    sj.Name as Job_Name,
    sj.Create_date as Job_Create_date,
    sj.modify_date as Job_Modify_date,
    sj.statement as Stream_Job_Query,
    Input_Stream_Name =
        Case js.is_input
            when 1 then s.Name
            else null
        END,
    output_Stream_Name =
        case js.is_output
            when 1 then s.Name
            else null
        END,
    s.location as Stream_Location
from sys.external_job_streams js
inner join sys.external_streams s on s.object_id = js.stream_id
inner join sys.external_streaming_jobs sj on sj.object_id = js.job_id
```

## <a name="permissions"></a>Oprávnění

Viditelnost metadat v zobrazeních katalogu je omezená na to, že uživatel buď vlastní, nebo ke kterému má uživatel udělené oprávnění. Další informace najdete v tématu [Konfigurace viditelnosti metadat](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Viz také

- [Zobrazení katalogu (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Systémová zobrazení (Transact-SQL)](/sql/t-sql/language-reference/)
