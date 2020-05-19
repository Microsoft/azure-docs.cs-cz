---
title: ALTER EXTERNAL STREAM (Transact-SQL) – Azure SQL Edge (Preview)
description: Přečtěte si o příkazu ALTER EXTERNAL STREAM ve službě Azure SQL Edge (Preview).
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 0183972b5eb92d3f081b857940609bffc183b331
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597937"
---
# <a name="alter-external-stream-transact-sql"></a>ALTER EXTERNAL STREAM (Transact-SQL)

Upraví definici externího datového proudu. Úprava externího datového proudu, který je používán úlohou streamování ve *spuštěném* stavu, není povolená. 



## <a name="syntax"></a>Syntaxe

```sql
  ALTER EXTERNAL STREAM external_stream_name 
  SET 
    [DATA_SOURCE] = <data_source_name> 
    , LOCATION = <location_name> 
    , EXTERNAL_FILE_FORMAT = <external_file_format_name> 
    , INPUT_OPTIONS = <input_options> 
    , OUTPUT_OPTIONS = <output_options> 
```

## <a name="arguments"></a>Arguments

Podrobnosti o argumentech příkazu ALTER External Stream naleznete v tématu [Create External Stream (Transact-SQL)](create-external-stream-transact-sql.md).

## <a name="return-code-values"></a>Hodnoty návratového kódu

Příkaz ALTER EXTERNAL STREAM vrátí hodnotu 0, pokud bylo úspěšné. Nenulová návratová hodnota označuje selhání.


## <a name="see-also"></a>Viz také

- [VYTVOŘIT externí datový proud (Transact-SQL)](create-external-stream-transact-sql.md) 
- [Vynechat externí datový proud (Transact-SQL)](drop-external-stream-transact-sql.md) 
