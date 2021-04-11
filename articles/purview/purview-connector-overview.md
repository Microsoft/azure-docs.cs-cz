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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695766"
---
# <a name="supported-data-stores"></a>Podporované zdroje dat

Dosah podporuje následující úložiště dat. Klikněte na jednotlivá úložiště dat, abyste se seznámili s podporovanými funkcemi a odpovídajícími konfiguracemi v podrobnostech.

## <a name="purview-data-sources"></a>Zdroje dat dosah

|**Kategorie**|  **Úložiště dat**  |**Extrakce metadat**|**Úplná kontrola**|**Přírůstková kontrola**|**Vymezené prohledávání**|**Klasifikace**|**Lineage**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure SQL Database](register-scan-azure-sql-database.md)|Yes| Yes| No| Yes| Yes| Yes|
||[Azure SQL Database spravovaná instance](register-scan-azure-sql-database-managed-instance.md)|Yes| Yes| No| Yes| Yes| Yes|
||[Azure Synapse Analytics (dříve SQL DW)](register-scan-azure-synapse-analytics.md)|Yes| Yes| No| Yes| Yes| Yes|
|databáze|[Oracle DB](register-scan-oracle-source.md)|Yes| Yes| No| No| No| Yes|
||[SQL Server](register-scan-on-premises-sql-server.md)|Yes| Yes| No| Yes| Yes| Yes|
||[Teradata](register-scan-teradata-source.md)|Yes| Yes| No| No| No| Yes|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Yes| Yes| No| No| No| Yes|
|Služby a aplikace|[SAP ECC](register-scan-sapecc-source.md)|Yes| Yes| No| Yes| Yes| Yes|
||[SAP S4HANA](register-scan-saps4hana-source.md)|Yes| Yes| No| Yes| Yes| Ano|

## <a name="next-steps"></a>Další kroky

- [Registrace a kontrola zdroje Azure Blob Storage](register-scan-azure-blob-storage-source.md)