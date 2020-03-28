---
title: PowerShell example-monitor-scale-elastic-pool-Azure SQL Database
description: Ukázkový skript Azure PowerShellu pro monitorování a škálování elastického fondu v Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 6ab361b67741f2b96f593d04dafbabe355fc9121
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691620"
---
# <a name="use-powershell-to-monitor-and-scale-an-elastic-pool-in-azure-sql-database"></a>Použití PowerShellu k monitorování a škálování elastického fondu v Azure SQL Database

Tento ukázkový skript PowerShellu monitoruje metriky výkonu elastického fondu, škáluje ho na vyšší velikost výpočetních prostředků a vytvoří pravidlo upozornění na jednu z metrik výkonu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat prostředí PowerShell místně, tento kurz vyžaduje AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=17-18 "Monitor and scale a single SQL Database")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní spojené prostředky.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Nový-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří databázový server SQL, který je hostitelem jedné databáze nebo elastického fondu. |
| [Nový elastický fond AzSql](/powershell/module/az.sql/new-azsqlelasticpool) | Vytvoří elastický fond. |
| [Nová databáze AzSql](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří jednu databázi nebo databázi v elastickém fondu. |
| [Získat-AzMetric](/powershell/module/az.monitor/get-azmetric) | Zobrazí informace o využití velikosti databáze.|
| [Pravidlo přidání AzMetricAlert](/powershell/module/az.monitor/add-azmetricalertrule) | Přidá nebo aktualizuje pravidlo upozornění na základě metriky. |
| [Elastický fond Set-AzSql](/powershell/module/az.sql/set-azsqlelasticpool) | Aktualizuje vlastnosti elastického fondu. |
| [Pravidlo přidání AzMetricAlert](/powershell/module/az.monitor/add-azmetricalertrule) | Nastaví pravidlo upozornění pro budoucí automatické monitorování DTU. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
