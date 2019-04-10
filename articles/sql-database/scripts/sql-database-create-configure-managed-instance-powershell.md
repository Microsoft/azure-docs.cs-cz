---
title: Příklad Powershellu – vytvoření ve službě Azure SQL Database managed instance | Dokumentace Microsoftu
description: Ukázkový skript Azure Powershellu pro vytvoření spravované instance Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: c85b967615e866635cb4dd93be5ddeb78a8c7129
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357009"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Použití Powershellu k vytvoření služby Azure SQL Database managed instance

Tento ukázkový skript Powershellu vytvoří spravovanou instanci Azure SQL Database ve vyhrazené podsíti v rámci nové virtuální sítě. Nakonfiguruje taky směrovací tabulku a skupinu zabezpečení sítě pro virtuální síť. Po úspěšném spuštění skriptu bude spravovaná instance je přístupný z v rámci virtuální sítě nebo z místního prostředí. Zobrazit [konfigurace virtuálního počítače Azure pro připojení k Azure SQL Database Managed Instance](../sql-database-managed-instance-configure-vm.md) a [konfigurace připojení typu point-to-site k Azure SQL Database Managed Instance z místní](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Omezení, najdete v části [podporované oblasti](../sql-database-managed-instance-resource-limits.md#supported-regions) a [podporované typy předplatného](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, v tomto kurzu vyžaduje AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Použijte následující příkaz k odebrání skupiny prostředků a všechny prostředky, které s ním spojená.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Přidá konfiguraci podsítě do virtuální sítě |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Získá virtuální sítě ve skupině prostředků |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Nastaví cílový stav pro virtuální síť |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Získá podsíť ve virtuální síti |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Nastaví cílový stav pro konfiguraci podsítě ve virtuální síti |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Vytvoří směrovací tabulku |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Získá směrovací tabulky |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Nastaví cílový stav pro směrovací tabulky |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Vytvoří spravovanou instanci Azure SQL Database |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
