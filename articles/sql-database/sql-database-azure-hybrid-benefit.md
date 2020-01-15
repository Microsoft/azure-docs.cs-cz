---
title: Zvýhodněné hybridní využití Azure
description: Pro SQL Database slevy použijte existující licence SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945624"
---
# <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

V zřízené výpočetní úrovni v rámci nákupního modelu založeném na vCore můžete vyměňovat stávající licence pro zvýhodněné sazby SQL Database pomocí [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Tato výhoda Azure vám umožňuje ušetřit až 30 procent nebo dokonce vyšší na Azure SQL Database pomocí místních licencí SQL Server se Software Assurance. Použijte prosím Zvýhodněné hybridní využití Azure kalkulačku pomocí odkazu zmíněného níže pro správné hodnoty. 

> [!NOTE]
> Změna na Zvýhodněné hybridní využití Azure nevyžaduje žádné výpadky.

![ceny](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Zvolit model licence

Pomocí Zvýhodněné hybridní využití Azure se můžete rozhodnout platit jenom za základní infrastrukturu Azure pomocí vaší stávající licence SQL Server pro samotný stroj SQL Database (základní výpočetní ceny), nebo můžete platit jak z základní infrastruktury, tak i SQL Server licence (ceny včetně licencí).

Licenční model můžete vybrat nebo změnit pomocí Azure Portal nebo pomocí některého z následujících rozhraní API:

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li nastavit nebo aktualizovat typ licence pomocí prostředí PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Nastavení nebo aktualizace typu licence pomocí Azure CLI:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [AZ SQL mi Create](/cli/azure/sql/mi#az-sql-mi-create)
- [AZ SQL mi Update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-apitabrest"></a>[REST API](#tab/rest)

Chcete-li nastavit nebo aktualizovat typ licence pomocí REST API:

- [Databáze – vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate)
- [Databáze – aktualizace](/rest/api/sql/databases/update)
- [Spravované instance – vytvořit nebo aktualizovat](/rest/api/sql/managedinstances/createorupdate)
- [Spravované instance – aktualizace](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Další kroky

- Pokud chcete vybrat mezi možnostmi nasazení SQL Database, přečtěte si téma [Výběr správné možnosti nasazení v Azure SQL](sql-database-paas-vs-sql-server-iaas.md).
- Porovnání funkcí SQL Database najdete v tématu [Azure SQL Database funkce](sql-database-features.md).
