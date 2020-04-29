---
title: Příklad PowerShellu – skupina převzetí služeb při selhání – Azure SQL Database spravovaná instance
description: Azure PowerShell ukázkový skript pro vytvoření Azure SQL Database spravované instance, přidejte ho do skupiny převzetí služeb při selhání a testovací převzetí služeb při selhání.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "73691762"
---
# <a name="use-powershell-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Přidání spravované instance Azure SQL Database do skupiny převzetí služeb při selhání pomocí PowerShellu 

Tento ukázkový skript PowerShellu vytvoří dvě spravované instance, přidá je do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání z primární spravované instance do sekundární spravované instance. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-scripts"></a>Ukázkové skripty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. Skupinu prostředků budete muset odebrat dvakrát. Když se skupina prostředků odebere poprvé, odebere se spravovaná instance a virtuální clustery, ale tato chybová zpráva `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`se pak nezdaří. Spusťte příkaz Remove-AzResourceGroup a podruhé odstraňte všechny zbývající prostředky i skupinu prostředků.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě do virtuální sítě. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Získá ve skupině prostředků virtuální síť. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Načte podsíť ve virtuální síti. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Vytvoří směrovací tabulku. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualizuje konfiguraci podsítě pro virtuální síť.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualizuje virtuální síť.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Načte skupinu zabezpečení sítě. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Přidá konfiguraci pravidla zabezpečení sítě do skupiny zabezpečení sítě. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualizuje skupinu zabezpečení sítě.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Přidá trasu do směrovací tabulky. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualizuje směrovací tabulku.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Vytvoří spravovanou instanci Azure SQL Database.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Vrátí informace o instanci spravované databáze SQL Azure. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Vytvoří konfiguraci protokolu IP pro bránu Virtual Network. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Vytvoří bránu Virtual Network. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Vytvoří připojení mezi dvěma branami virtuální sítě.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Vytvoří novou skupinu převzetí služeb při selhání spravované instance Azure SQL Database.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Získá nebo zobrazí seznam skupin převzetí služeb při selhání spravované instance.| 
| [Switch – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Provede převzetí služeb při selhání pro skupinu převzetí služeb při selhání spravované instance. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. | 

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
