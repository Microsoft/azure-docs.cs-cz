---
title: Instalace sady Visual Studio 2019
description: Nainstalovat Visual Studio a vývojové nástroje SQL Server (SSDT) pro synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f83ae9c8290a52381c8087b46da959d4723d7f4e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745163"
---
# <a name="getting-started-with-visual-studio-2019"></a>Začínáme se sadou Visual Studio 2019

Sady Visual Studio **2019** SQL Server Data Tools (SSDT) je jeden nástroj, který vám umožní provádět tyto akce:

- Připojení, dotazování a vývoj aplikací
- Pomocí Průzkumníka objektů můžete vizuálně prozkoumat všechny objekty v datovém modelu, včetně tabulek, zobrazení, uložených procedur a atd.
- Generování skriptů DDL (Data Definition Language) jazyka T-SQL pro vaše objekty
- Vývoj datového skladu pomocí přístupu na základě stavu s projekty SSDT Database
- Integrujte svůj projekt databáze se systémy správy zdrojového kódu, jako je git, pomocí Azure Repos
- Nastavení kanálů průběžné integrace a nasazování pomocí automatizačních serverů, jako je Azure DevOps

## <a name="install-visual-studio-2019"></a>Instalace sady Visual Studio 2019

Viz [Stažení sady Visual studio 2019](https://visualstudio.microsoft.com/downloads/) ke stažení a instalaci sady visual Studio **16,3 a novější**. Během instalace vyberte úlohu ukládání a zpracování dat. Samostatná instalace SSDT už není v aplikaci Visual Studio 2019 nutná.

## <a name="unsupported-features-in-ssdt"></a>Nepodporované funkce v SSDT

V některých případech se může stát, že vydání funkcí pro synapse SQL nezahrnují podporu pro SSDT. Následující funkce jsou momentálně nepodporované:

- [Materializovaná zobrazení](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Seřazené clusterované indexy columnstore](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#examples--and-)
- [Příkaz COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Správa úloh](sql-data-warehouse-workload-management.md) – skupiny úloh a třídění
- [Zabezpečení na úrovni řádku](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - Pro získání podporované funkce odešlete [lístek podpory nebo hlasování](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) .
- [Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
   - Pro získání podporované funkce odešlete [lístek podpory nebo hlasování](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) .
- [Tabulky s omezeními](sql-data-warehouse-table-constraints.md#table-constraints) nejsou podporovány. Pro tyto objekty tabulky nastavte akci sestavení na None.

## <a name="next-steps"></a>Další kroky

Teď, když máte nejnovější verzi SSDT, jste připraveni se [připojit](sql-data-warehouse-query-visual-studio.md) ke svému fondu SQL.
