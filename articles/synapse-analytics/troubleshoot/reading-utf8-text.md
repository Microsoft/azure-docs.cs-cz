---
title: 'Řešení potíží: čtení textu v kódování UTF-8 ze souborů CSV nebo PARQUET pomocí neserverového fondu SQL'
description: Čtení textu UTF-8 ze souborů CSV nebo PARQUET pomocí neserverového fondu SQL ve službě Azure synapse Analytics
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 12/03/2020
ms.openlocfilehash: 70ce3c82790db0296d5359b5db2e6a323306c309
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576413"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Řešení potíží s čtením textu ve formátu UTF-8 ze souborů CSV nebo Parquet pomocí neserverového fondu SQL ve službě Azure synapse Analytics

Tento článek popisuje postup řešení potíží pro čtení textu v kódování UTF-8 ze souborů CSV nebo Parquet s využitím SQL fondu bez serveru ve službě Azure synapse Analytics.

Když je text ve formátu UTF-8 načtený ze souboru CSV nebo PARQUET s použitím fondu SQL bez serveru, některé speciální znaky, jako je ü a ö, se nesprávně převedou, pokud dotaz vrací sloupce VARCHAR s nečitelnými kolami. Jedná se o známý problém v SQL Server a Azure SQL. Kolace bez UTF8 je výchozím nastavením v synapse SQL, takže budou mít vliv na dotazy zákazníků. Zákazníci, kteří používají standardní anglické znaky a některé podmnožiny rozšířených znaků latinky, nemusí chyby převodu všimnout. Nesprávný převod je podrobněji vysvětlen v tématu [vždy použití kolací UTF-8 pro čtení textu UTF-8 v neserverovém fondu SQL](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633) .

## <a name="workaround"></a>Alternativní řešení

Alternativním řešením tohoto problému je, aby při čtení textu UTF-8 ze souborů CSV nebo PARQUET vždy používala kolaci UTF-8.

- V mnoha případech stačí pro databázi (operace s metadaty) nastavit kolaci UTF8.

   ```sql
   alter database MyDB
         COLLATE Latin1_General_100_BIN2_UTF8;
   ```

- Můžete explicitně definovat kolaci ve sloupci VARCHAR v OPENROWSET nebo externí tabulce:

   ```sql
   select geo_id, cases = sum(cases)
   from openrowset(
           bulk 'latest/ecdc_cases.parquet', data_source = 'covid', format = 'parquet'
       ) with ( cases int,
                geo_id VARCHAR(6) COLLATE Latin1_General_100_BIN2_UTF8 ) as rows
   group by geo_id
   ```
 
- Pokud jste neurčili kolaci UTF8 u externích tabulek, které čtou data UTF8, je nutné znovu vytvořit ovlivněné externí tabulky a nastavit kolaci UTF8 pro sloupce VARCHAR (operace metadat).


## <a name="next-steps"></a>Další kroky

* [Dotazování souborů Parquet pomocí synapse SQL](../sql/query-parquet-files.md)
* [Dotazování na soubory CSV pomocí synapse SQL](../sql/query-single-csv-file.md)
* [CETAS s synapse SQL](../sql/develop-tables-cetas.md)
* [Rychlý Start: použití fondu SQL bez serveru](../quickstart-sql-on-demand.md)
