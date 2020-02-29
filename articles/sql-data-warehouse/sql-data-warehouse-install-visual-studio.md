---
title: Instalace sady Visual Studio 2019
description: Nainstalovat Visual Studio a vývojové nástroje pro SQL Server (SSDT) pro SQL Analytics
services: sql-data-warehouse
ms.custom: azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b9ad0f4aedc5a095bfa70dec1b67e52cd4d152e0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195335"
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
Viz [Stáhnout Visual Studio 2019][] ke stažení a instalaci sady visual Studio **16,3 a novější**. Během instalace vyberte úlohu ukládání a zpracování dat. Samostatná instalace SSDT už není v aplikaci Visual Studio 2019 nutná.

## <a name="unsupported-features-in-ssdt"></a>Nepodporované funkce v SSDT

K dispozici jsou situace, kdy vydání funkcí pro SQL Analytics nemusí zahrnovat podporu pro SSDT. Následující funkce jsou momentálně nepodporované:

- [Materializovaná zobrazení](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (probíhá)
- [Seřazené clusterované indexy columnstore](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (probíhá)
- [Copy – příkaz](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (probíhá)
- [Správa úloh](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) – skupiny úloh a třídění (probíhající)
- [Zabezpečení na úrovni řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Pokud [chcete funkci](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) získat, pošlete lístek podpory nebo hlasování.
- [Dynamické maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Pokud [chcete funkci](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) získat, pošlete lístek podpory nebo hlasování. 
- [Předpověď](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) – funkce 
- [Tabulky s omezeními](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) nejsou podporovány. Pro tyto objekty tabulky nastavte akci sestavení na None.

## <a name="next-steps"></a>Další kroky

Teď, když máte nejnovější verzi SSDT, jste připraveni se [připojit][connect] ke svému fondu SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->

[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->

[Stáhnout Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
