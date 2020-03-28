---
title: Příklad Prostředí PowerShell – vytvoření spravované instance v Azure SQL Database
description: Ukázkový skript Azure PowerShellu pro vytvoření spravované instance v Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 3e72a2f6754ad8e9c5bcfabe7eeee299468fa8f3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691638"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Vytvoření spravované instance Azure SQL Database pomocí PowerShellu

Tento příklad skriptu Prostředí PowerShell vytvoří spravovanou instanci Azure SQL Database ve vyhrazené podsíti v rámci nové virtuální sítě. Také konfiguruje směrovací tabulku a skupinu zabezpečení sítě pro virtuální síť. Po úspěšném spuštění skriptu lze ke spravované instanci přistupovat z virtuální sítě nebo z místního prostředí. Viz [Konfigurace virtuálního počítače Azure pro připojení ke spravované instanci Azure SQL Database A](../sql-database-managed-instance-configure-vm.md) konfigurace připojení z bodu na místo ke spravované [instanci Azure SQL Database z místního prostředí](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Omezení naleznete v tématu [podporované oblasti](../sql-database-managed-instance-resource-limits.md#supported-regions) a podporované [typy předplatného](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat prostředí PowerShell místně, tento kurz vyžaduje AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní spojené prostředky.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky.
| [Nová virtuální síť Az](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť |
| [Doplněk AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Přidá konfiguraci podsítě do virtuální sítě. |
| [Virtuální síť Get-Az](/powershell/module/az.network/Get-AzVirtualNetwork) | Získá virtuální síť ve skupině prostředků |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Nastaví stav cíle pro virtuální síť. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Získá podsíť ve virtuální síti |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Konfiguruje stav cíle pro konfiguraci podsítě ve virtuální síti. |
| [Nová-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Vytvoří směrovací tabulku. |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Získá tabulky tras. |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Nastaví stav cíle pro směrovací tabulku. |
| [Nová instance AzSql](/powershell/module/az.sql/New-AzSqlInstance) | Vytvoří spravovanou instanci Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
