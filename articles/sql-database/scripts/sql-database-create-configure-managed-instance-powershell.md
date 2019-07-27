---
title: Příklad PowerShellu – vytvoření spravované instance v Azure SQL Database | Microsoft Docs
description: Azure PowerShell ukázkový skript pro vytvoření spravované instance v Azure SQL Database
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
ms.openlocfilehash: 32b3c3b45c627d8dfdb42642228a7f9d9786111e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569972"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Použití PowerShellu k vytvoření spravované instance Azure SQL Database

Tento ukázkový skript PowerShellu vytvoří Azure SQL Database spravovanou instanci v vyhrazené podsíti v rámci nové virtuální sítě. Také nakonfiguruje směrovací tabulku a skupinu zabezpečení sítě pro virtuální síť. Po úspěšném spuštění skriptu je možné k spravované instanci přistupovat z virtuální sítě nebo z místního prostředí. Přečtěte si téma [Konfigurace virtuálního počítače Azure pro připojení k spravované instanci Azure SQL Database](../sql-database-managed-instance-configure-vm.md) a [Konfigurace připojení typu Point-to-site k Azure SQL Database spravované instanci z místního prostředí](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Omezení najdete v tématu [podporované oblasti](../sql-database-managed-instance-resource-limits.md#supported-regions) a [podporované typy](../sql-database-managed-instance-resource-limits.md#supported-subscription-types)předplatného.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

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
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Vytvoří spravovanou instanci Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
