---
title: Příklad PowerShellu – Monitorování a škálování izolované databáze SQL Azure | Microsoft Docs
description: Ukázkový skript Azure PowerShellu pro monitorování a škálování izolované databáze SQL Azure
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: ''
ms.service: sql-database
ms.custom: monitor & tune, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/01/2018
ms.author: janeng
ms.openlocfilehash: adb2da80ecf0b0c8feb7ebd242b1e98a871aef77
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="use-powershell-to-monitor-and-scale-a-single-sql-database"></a>Monitorování a škálování izolované databáze SQL pomocí PowerShellu

Tento ukázkový skript PowerShellu monitoruje metriky výkonu databáze, škáluje ji na vyšší úroveň výkonu a vytvoří pravidlo upozornění na jednu z metrik výkonu. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1?highlight=13-14 "Monitor and scale single SQL Database")]

> [!TIP]
> Pomocí rutiny [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) zobrazíte stav operací databáze a pomocí rutiny [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity) můžete zrušit operaci aktualizace databáze.

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Vytvoří logický server, který je hostitelem databáze nebo elastického fondu. |
| [Get-AzureRmMetric](/powershell/module/azurerm.insights/get-azurermmetric) | Zobrazí informace o využití velikosti databáze.|
| [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) | Aktualizuje vlastnosti databáze nebo přesune databázi do nebo z elastického fondu nebo mezi elastickými fondy. |
| [Add-AzureRMMetricAlertRule](/powershell/module/azurerm.insights/add-azurermmetricalertrule) | Nastaví pravidlo upozornění pro budoucí automatické monitorování DTU. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
