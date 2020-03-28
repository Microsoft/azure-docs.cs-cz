---
title: Příklad auditování prostředí powershellu a rozšířené ochrany před hrozbami – Azure SQL Database
description: Ukázkový skript Azure PowerShellu pro konfiguraci auditování a rozšířené ochrany před hrozbami v azure sql databázi
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security
ms.devlang: PowerShell
ms.topic: sample
author: DavidTrigano
ms.author: datrigan
ms.reviewer: carlrab, vanto
ms.date: 08/05/2019
ms.openlocfilehash: f5e107058f983df98f7d14dbe1b41ce5a66f2535
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76719972"
---
# <a name="use-powershell-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>Konfigurace auditování databáze SQL a rozšířené ochrany před hrozbami pomocí prostředí PowerShell

Tento příklad skriptu prostředí PowerShell konfiguruje auditování databáze SQL a pokročilou ochranu před hrozbami.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat prostředí PowerShell místně, tento kurz vyžaduje AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.ps1?highlight=15-16 "Configure auditing and threat detection")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní spojené prostředky.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Nový-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří databázový server SQL, který je hostitelem jedné databáze nebo elastického fondu. |
| [Nová databáze AzSql](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří jednu databázi nebo elastický fond. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Vytvoří účet úložiště. |
| [Set-AzSqlAuditing](/powershell/module/az.sql/set-azsqldatabaseauditing) | Nastaví pro databázi zásady auditu. |
| [Set-AzSqlDatabaseThreatDetectionPolicy](/powershell/module/az.sql/set-azsqldatabasethreatdetectionpolicy) | Nastaví v databázi rozšířené zásady ochrany před hrozbami. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
