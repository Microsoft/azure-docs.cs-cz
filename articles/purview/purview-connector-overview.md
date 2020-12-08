---
title: Přehled konektoru dosah
description: Tento článek popisuje různá úložiště dat a funkce podporované v dosah.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 26efa840bacd3ee542816cf861cf2ef586b0582f
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780208"
---
# <a name="supported-data-stores"></a>Podporované zdroje dat

Dosah podporuje následující úložiště dat. Klikněte na jednotlivá úložiště dat, abyste se seznámili s podporovanými funkcemi a odpovídajícími konfiguracemi v podrobnostech.

## <a name="purview-data-sources"></a>Zdroje dat dosah

|**Kategorie**|  **Úložiště dat**  |**Extrakce metadat**|**Úplná kontrola**|**Přírůstková kontrola**|**Vymezené prohledávání**|**Mazal**|**Lineage**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| Ano| Ano| Ano| Ano| Ano| Ano|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Ano| Ano| Ano| Ano| Ano| Ano|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|Ano| Ano| Ano| Ano| Ano| Ano|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|Ano| Ano| Ano| Ano| Ano| Ano|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|Ano| Ano| Ano| Ano| Ano| Ano|
||[Azure SQL Database](register-scan-azure-sql-database.md)|Ano| Ano| No| Ano| Ano| Ano|
||[Spravovaná instance Azure SQL Database](register-scan-azure-sql-database-managed-instance.md)|Ano| Ano| No| Ano| Ano| Ano|
||[Azure Synapse Analytics (dříve SQL DW)](register-scan-azure-synapse-analytics.md)|Ano| Ano| No| Ano| Ano| Ano|
|Databáze|[SQL Server](register-scan-on-premises-sql-server.md)|Ano| Ano| No| Ano| Ano| Ano|
||[Teradata (Preview)](register-scan-teradata-source.md)|Ano| Ano| No| No| No| Ano|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Ano| Ano| No| No| No| Ano|

## <a name="next-steps"></a>Další kroky

- [Registrace a kontrola zdroje Azure Blob Storage](register-scan-azure-blob-storage-source.md)