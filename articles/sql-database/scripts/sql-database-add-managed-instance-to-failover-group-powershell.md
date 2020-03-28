---
title: Příklad prostředí PowerShell – skupina převzetí služeb při selhání – spravovaná instance Azure SQL Database
description: Ukázkový skript Azure PowerShellu k vytvoření instance spravované azure SQL database, jeho přidání do skupiny převzetí služeb při selhání a testování převzetí služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: e50877f6f3194885b139683fe865144384716b48
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691762"
---
# <a name="use-powershell-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Přidání instance spravované azure SQL database do skupiny převzetí služeb při selhání pomocí Prostředí PowerShell 

Tento příklad skriptu prostředí PowerShell vytvoří dvě spravované instance, přidá je do skupiny převzetí služeb při selhání a pak testuje převzetí služeb při selhání z primární spravované instance na sekundární spravovanou instanci. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat prostředí PowerShell místně, tento kurz vyžaduje AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-scripts"></a>Ukázkové skripty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní spojené prostředky. Skupinu prostředků budete muset odebrat dvakrát. První odebrání skupiny prostředků odebere spravované instance a virtuální clustery, `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`ale poté se chybová zpráva nezdaří . Spusťte příkaz Remove-AzResourceGroup podruhé, abyste odebrali všechny zbývající prostředky i skupinu prostředků.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků Azure.  |
| [Nová virtuální síť Az](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť.  |
| [Doplněk AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Virtuální síť Get-Az](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Získá podsíť ve virtuální síti. | 
| [Nová skupina zabezpečení sítě AzNetwork](/powershell/module/az.network/new-aznetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě. | 
| [Nová-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Vytvoří směrovací tabulku. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfiguraci podsítě pro virtuální síť.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Získá skupinu zabezpečení sítě. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Přidá konfiguraci pravidla zabezpečení sítě do skupiny zabezpečení sítě. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje skupinu zabezpečení sítě.  | 
| [Doplněk AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Přidá trasu do směrovací tabulky. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje směrovací tabulku.  |
| [Nová instance AzSql](/powershell/module/az.sql/new-azsqlinstance) | Vytvoří spravovanou instanci Azure SQL Database.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Vrátí informace o instanci spravované databáze Azure SQL. |
| [Nová adresa AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu.  | 
| [Nový-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Vytvoří konfiguraci IP adresy pro bránu virtuální sítě. |
| [Nová-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Vytvoří bránu virtuální sítě. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Vytvoří připojení mezi dvěma branami virtuální sítě.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Vytvoří novou skupinu převzetí služeb při selhání azure SQL Database spravované instance.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Získá nebo zobrazí seznam skupin služeb převzetí služeb při selhání spravované instance.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Provede převzetí služeb při selhání skupiny převzetí služeb při selhání spravované instance. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. | 

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
