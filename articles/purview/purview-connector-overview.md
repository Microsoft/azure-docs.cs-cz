---
title: Přehled konektoru dosah
description: Tento článek popisuje různá úložiště dat a funkce podporované v dosah.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 88fb9c823df6ae5df345911ccce1c579009fba02
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "96780208"
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
||[Azure SQL Database](register-scan-azure-sql-database.md)|Ano| Ano| No| Ano| Ano| Ano|
||[Spravovaná instance Azure SQL Database](register-scan-azure-sql-database-managed-instance.md)|Ano| Ano| No| Ano| Ano| Ano|
||[Azure Synapse Analytics (dříve SQL DW)](register-scan-azure-synapse-analytics.md)|Ano| Ano| No| Ano| Ano| Ano|
|databáze|[SQL Server](register-scan-on-premises-sql-server.md)|Ano| Ano| No| Ano| Ano| Ano|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Ano| Ano| No| No| No| Ano|

## <a name="next-steps"></a>Další kroky

- [Registrace a kontrola zdroje Azure Blob Storage](register-scan-azure-blob-storage-source.md)