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
ms.openlocfilehash: 4aab699ff0141b648066fae0de445c4467be7509
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048397"
---
# <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

V zřízené výpočetní úrovni v rámci nákupního modelu založeném na vCore můžete vyměňovat stávající licence pro zvýhodněné sazby SQL Database pomocí [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Tato výhoda Azure vám umožní ušetřit až 30 procent na Azure SQL Database pomocí místních licencí SQL Server se Software Assurance. 

> [!NOTE]
> Změna na Zvýhodněné hybridní využití Azure nevyžaduje žádné výpadky.

![stanov](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Zvolit model licence

Pomocí Zvýhodněné hybridní využití Azure se můžete rozhodnout platit jenom za základní infrastrukturu Azure pomocí vaší stávající licence SQL Server pro samotný stroj SQL Database (základní výpočetní ceny), nebo můžete platit jak z základní infrastruktury, tak i SQL Server licence (ceny včetně licencí).

Licenční model můžete vybrat nebo změnit pomocí Azure Portal nebo pomocí některého z následujících rozhraní API:

- Chcete-li nastavit nebo aktualizovat typ licence pomocí prostředí PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Nastavení nebo aktualizace typu licence pomocí Azure CLI:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [AZ SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Chcete-li nastavit nebo aktualizovat typ licence pomocí REST API:

  - [Databáze – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Databáze – aktualizace](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Spravované instance – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Spravované instance – aktualizace](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="next-steps"></a>Další kroky

- Pokud chcete vybrat mezi možnostmi nasazení SQL Database, přečtěte si téma [Výběr správné možnosti nasazení v Azure SQL](sql-database-paas-vs-sql-server-iaas.md).
- Porovnání funkcí SQL Database najdete v tématu [Azure SQL Database funkce](sql-database-features.md).
