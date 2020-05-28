---
title: 'PowerShell: vytvoření instance'
titleSuffix: Azure SQL Managed Instance
description: Azure PowerShell ukázkový skript pro vytvoření spravované instance Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 9dfe6177ed46f133772a46930d1e56c6007b33f3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053973"
---
# <a name="use-powershell-to-create-an-azure-sql-managed-instance"></a>Použití PowerShellu k vytvoření spravované instance Azure SQL
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Tento ukázkový skript PowerShellu vytvoří spravovanou instanci Azure SQL ve vyhrazené podsíti v rámci nové virtuální sítě. Také nakonfiguruje směrovací tabulku a skupinu zabezpečení sítě pro virtuální síť. Po úspěšném spuštění skriptu bude mít ke spravované instanci SQL pøístup z virtuální sítě nebo z místního prostředí. Viz [Konfigurace virtuálního počítače Azure pro připojení k Azure SQL Database spravované instanci](../connect-vm-instance-configure.md) a [Konfigurace připojení typu Point-to-site k spravované instanci Azure SQL z místního prostředí](../point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Omezení najdete v tématu [podporované oblasti](../resource-limits.md#supported-regions) a [podporované typy předplatného](../resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá některé z následujících příkazů. Další informace o použitých a dalších příkazech v následující tabulce získáte kliknutím na odkazy na konkrétní příkaz v dokumentaci.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Přidá konfiguraci podsítě do virtuální sítě. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Načte virtuální síť ve skupině prostředků. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Nastaví cílový stav pro virtuální síť. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Načte podsíť ve virtuální síti. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Nakonfiguruje cílový stav pro konfiguraci podsítě ve virtuální síti. |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Vytvoří směrovací tabulku. |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Načte směrovací tabulky. |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Nastaví cílový stav pro tabulku směrování. |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Vytvoří spravovanou instanci Azure SQL. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu spravované instance SQL najdete v [skriptech PowerShell spravované instance Azure SQL](../../database/powershell-script-content-guide.md).
