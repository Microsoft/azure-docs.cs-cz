---
title: Přehled konektoru dosah
description: Tento článek popisuje různá úložiště dat a funkce podporované v dosah.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 08b22af8743082bab1d547205e51917cb9d92a11
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695766"
---
# <a name="supported-data-stores"></a>Podporované zdroje dat

Dosah podporuje následující úložiště dat. Klikněte na jednotlivá úložiště dat, abyste se seznámili s podporovanými funkcemi a odpovídajícími konfiguracemi v podrobnostech.

## <a name="purview-data-sources"></a>Zdroje dat dosah

|**Kategorie**|  **Úložiště dat**  |**Extrakce metadat**|**Úplná kontrola**|**Přírůstková kontrola**|**Vymezené prohledávání**|**Klasifikace**|**Lineage**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| Ano| Ano| Ano| Ano| Ano| Ano|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Ano| Ano| Ano| Ano| Ano| Ano|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|Ano| Ano| Ano| Ano| Ano| Ano|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|Ano| Ano| Ano| Ano| Ano| Ano|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|Ano| Ano| Ano| Ano| Ano| Ano|
||[Azure SQL Database](register-scan-azure-sql-database.md)|Ano| Ano| Ne| Ano| Ano| Ano|
||[Azure SQL Database spravovaná instance](register-scan-azure-sql-database-managed-instance.md)|Ano| Ano| Ne| Ano| Ano| Ano|
||[Azure Synapse Analytics (dříve SQL DW)](register-scan-azure-synapse-analytics.md)|Ano| Ano| Ne| Ano| Ano| Ano|
|databáze|[Oracle DB](register-scan-oracle-source.md)|Ano| Ano| Ne| Ne| Ne| Ano|
||[SQL Server](register-scan-on-premises-sql-server.md)|Ano| Ano| Ne| Ano| Ano| Ano|
||[Teradata](register-scan-teradata-source.md)|Ano| Ano| Ne| Ne| Ne| Ano|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Ano| Ano| Ne| Ne| Ne| Ano|
|Služby a aplikace|[SAP ECC](register-scan-sapecc-source.md)|Ano| Ano| Ne| Ano| Ano| Ano|
||[SAP S4HANA](register-scan-saps4hana-source.md)|Ano| Ano| Ne| Ano| Ano| Ano|

## <a name="next-steps"></a>Další kroky

- [Registrace a kontrola zdroje Azure Blob Storage](register-scan-azure-blob-storage-source.md)