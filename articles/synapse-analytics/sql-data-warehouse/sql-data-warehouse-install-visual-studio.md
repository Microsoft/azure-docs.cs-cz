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
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 073efd41c754b0d28e0bdab712f692f100d3316e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289510"
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


- [Správa úloh](sql-data-warehouse-workload-management.md) – skupiny úloh a třídění
- [Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (včetně funkcí s hodnotou tabulky)
  - Pro získání podporované funkce odešlete [lístek podpory nebo hlasování](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) .
- [Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
   - Pro získání podporované funkce odešlete [lístek podpory nebo hlasování](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) .
- Tabulky se [sloupcem identity](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property?view=sql-server-ver15)

## <a name="next-steps"></a>Další kroky

Teď, když máte nejnovější verzi SSDT, jste připraveni se [připojit](sql-data-warehouse-query-visual-studio.md) ke svému fondu SQL.
