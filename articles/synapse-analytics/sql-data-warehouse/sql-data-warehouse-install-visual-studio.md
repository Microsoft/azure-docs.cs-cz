---
title: Instalace Visual Studia 2019
description: Instalace visual studio a sql server vývojové nástroje (SSDT) pro Synapse SQL
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
ms.openlocfilehash: b8e37a15f473e5777fe5412b785a0916bd78f095
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655916"
---
# <a name="getting-started-with-visual-studio-2019"></a>Začínáme s Visual Studio 2019

Visual Studio **2019** SQL Server Data Tools (SSDT) je jediný nástroj, který vám umožní provést následující:

- Připojení, dotazování a vývoj aplikací
- Využijte průzkumníka objektů k vizuálnímu prozkoumání všech objektů v datovém modelu, včetně tabulek, zobrazení, uložených procedur atd.
- Generování skriptů jazyka DDL (T-SQL data definition language) pro vaše objekty
- Vývoj datového skladu pomocí přístupu založeného na stavu pomocí databázových projektů SSDT
- Integrace databázového projektu se systémy správy zdrojového kódu, jako je Git, s Azure Repos
- Nastavení kanálů průběžné integrace a nasazení s automatizačními servery, jako je Azure DevOps

## <a name="install-visual-studio-2019"></a>Instalace Visual Studia 2019

Viz [Stažení Visual Studia 2019][] ke stažení a instalaci Visual Studia **16.3 a vyšší**. Během instalace vyberte úlohu ukládání a zpracování dat. V sadě Visual Studio 2019 už není nutná samostatná instalace SSDT.

## <a name="unsupported-features-in-ssdt"></a>Nepodporované funkce v SSDT

Jsou chvíle, kdy verze funkcí pro Synapse SQL nemusí zahrnovat podporu pro SSDT. Následující funkce nejsou v současné době podporovány:

- [Zhmotněné pohledy](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Objednané clusterované indexy columnstore](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#examples--and-)
- [Příkaz COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Správa pracovního vytížení](sql-data-warehouse-workload-management.md) – skupiny úloh a klasifikátory
- [Zabezpečení na úrovni řádku](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - Odešlete lístek podpory nebo hlaste [zde,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) abyste tuto funkci podpořili.
- [Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
  - Odešlete lístek podpory nebo hlaste [zde,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) abyste tuto funkci podpořili.
- [Tabulky s omezeními nejsou podporovány.](sql-data-warehouse-table-constraints.md#table-constraints) Pro tyto objekty tabulky nastavte akci sestavení na "Žádné".

## <a name="next-steps"></a>Další kroky

Teď, když máte nejnovější verzi SSDT, jste připraveni se [připojit](sql-data-warehouse-query-visual-studio.md) k fondu SQL.

<!--Other-->

[Stáhněte si Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
