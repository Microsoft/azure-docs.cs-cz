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
ms.openlocfilehash: 3ab0188fa44eeb09e3981b8e1809832948963d47
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511637"
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
  - Pro získání podporované funkce odešlete [lístek podpory nebo hlasování](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) .
- Tabulky se [sloupcem identity](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property?view=sql-server-ver15)
- Některé funkce T-SQL, například:
   - V klauzuli *Group* ve funkci [STRING_AGGového](/sql/t-sql/functions/string-agg-transact-sql?view=sqlallproducts-allversions) řetězce.

## <a name="next-steps"></a>Další kroky

Teď, když máte nejnovější verzi SSDT, jste připraveni se [připojit](sql-data-warehouse-query-visual-studio.md) ke svému fondu SQL.
