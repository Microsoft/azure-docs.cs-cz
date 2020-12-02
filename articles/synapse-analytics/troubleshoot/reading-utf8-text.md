---
title: 'Řešení potíží: čtení textu v kódování UTF-8 ze souborů CSV nebo PARQUET pomocí neserverového fondu SQL'
description: Čtení textu UTF-8 ze souborů CSV nebo PARQUET pomocí neserverového fondu SQL ve službě Azure synapse Analytics
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 11/24/2020
ms.openlocfilehash: 238880cb3f3628df7591e8d08e3057ebfd885900
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466168"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Řešení potíží s čtením textu ve formátu UTF-8 ze souborů CSV nebo Parquet pomocí neserverového fondu SQL ve službě Azure synapse Analytics

Tento článek popisuje postup řešení potíží pro čtení textu v kódování UTF-8 ze souborů CSV nebo Parquet s využitím SQL fondu bez serveru ve službě Azure synapse Analytics.

Když je text ve formátu UTF-8 načtený ze souboru CSV nebo PARQUET s použitím fondu SQL bez serveru, některé speciální znaky, jako je ü a ö, se nesprávně převedou, pokud dotaz vrací sloupce VARCHAR s nečitelnými kolami. Jedná se o známý problém v SQL Server a Azure SQL. Kolace bez UTF8 je výchozím nastavením v synapse SQL, takže budou mít vliv na dotazy zákazníků. Zákazníci, kteří používají standardní anglické znaky a některé podmnožiny rozšířených znaků latinky, nemusí chyby převodu všimnout. Nesprávný převod je podrobněji vysvětlen v tématu [vždy použití kolací UTF-8 pro čtení textu UTF-8 v neserverovém fondu SQL](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633) .

## <a name="workaround"></a>Alternativní řešení

Alternativním řešením tohoto problému je, aby při čtení textu UTF-8 ze souborů CSV nebo PARQUET vždy používala kolaci UTF-8.

-   V mnoha případech stačí pro databázi (operace s metadaty) nastavit kolaci UTF8.
-   Pokud jste neurčili kolaci UTF8 u externích tabulek, které čtou data UTF8, je nutné znovu vytvořit ovlivněné externí tabulky a nastavit kolaci UTF8 pro sloupce VARCHAR (operace metadat).


## <a name="next-steps"></a>Další kroky

* [CETAS s synapse SQL](../sql/develop-tables-cetas.md)
* [Rychlý Start: použití fondu SQL bez serveru](../quickstart-sql-on-demand.md)
