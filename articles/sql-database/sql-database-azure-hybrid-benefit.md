---
title: Zvýhodněné hybridní využití Azure
description: Použijte existující licence serveru SQL Server pro slevy databáze SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945624"
---
# <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

V zřízené výpočetní vrstvě nákupního modelu založeného na virtuálních jádrech můžete vyměnit stávající licence za zvýhodněné sazby v databázi SQL pomocí [hybridní výhody Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Tato výhoda Azure umožňuje ušetřit až 30 procent nebo dokonce vyšší v Azure SQL Database pomocí místních licencí SQL Serveru s Programem Software Assurance. Použijte kalkulačku hybridních výhod Azure pomocí odkazu uvedeného dříve pro správné hodnoty. 

> [!NOTE]
> Přechod na hybridní výhody Azure nevyžaduje žádné prostoje.

![Ceny](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Výběr licenčního modelu

S hybridní výhodou Azure můžete platit jenom za základní infrastrukturu Azure pomocí stávající licence SQL Serveru pro samotný databázový stroj SQL (základní výpočetní ceny) nebo můžete platit za základní infrastrukturu i SQL Server. licence (ceny zahrnuté v licenci).

Licenční model můžete zvolit nebo změnit pomocí portálu Azure nebo pomocí jednoho z následujících api:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nastavení nebo aktualizace typu licence pomocí Prostředí PowerShell:

- [Nová databáze AzSql](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabáze](/powershell/module/az.sql/set-azsqldatabase)
- [Nová instance AzSql](/powershell/module/az.sql/new-azsqlinstance)
- [Instance Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nastavení nebo aktualizace typu licence pomocí příkazového příkazového příkazu Azure:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi vytvořit](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi aktualizace](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[ROZHRANÍ API PRO ODPOČINEK](#tab/rest)

Nastavení nebo aktualizace typu licence pomocí rozhraní REST API:

- [Databáze – vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate)
- [Databáze - Aktualizace](/rest/api/sql/databases/update)
- [Spravované instance – vytvoření nebo aktualizace](/rest/api/sql/managedinstances/createorupdate)
- [Spravované instance – aktualizace](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Další kroky

- Výběr mezi možnostmi nasazení databáze SQL najdete [v tématu Výběr správné možnosti nasazení v Azure SQL](sql-database-paas-vs-sql-server-iaas.md).
- Porovnání funkcí databáze SQL najdete v tématu [Funkce databáze Azure SQL](sql-database-features.md).
