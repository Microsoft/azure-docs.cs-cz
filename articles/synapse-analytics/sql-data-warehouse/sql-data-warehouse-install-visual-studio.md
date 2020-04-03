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
ms.openlocfilehash: aa2f903611f52f8f7a8ede0040b592a7dddd0e89
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584465"
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

- [Zhmotněné pohledy](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (probíhá)
- [Objednané clusterované indexy columnstore](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (probíhá)
- [PŘÍKAZ COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (probíhá)
- [Správa pracovního vytížení](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) – skupiny pracovních vytížení a klasifikátory (probíhá)
- [Zabezpečení na úrovni řádku](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Odešlete lístek podpory nebo hlaste [zde,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) abyste tuto funkci podpořili.
- [Dynamické maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Odešlete lístek podpory nebo hlaste [zde,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) abyste tuto funkci podpořili. 
- [PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) 
- [Tabulky s omezeními nejsou podporovány.](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) Pro tyto objekty tabulky nastavte akci sestavení na "Žádné".

## <a name="next-steps"></a>Další kroky

Teď, když máte nejnovější verzi SSDT, jste připraveni se [připojit](sql-data-warehouse-query-visual-studio.md) k fondu SQL.




<!--Other-->

[Stáhněte si Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
