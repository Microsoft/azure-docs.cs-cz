---
title: 'PowerShell: vytvoření spravované instance'
titleSuffix: Azure SQL Managed Instance
description: Tento článek poskytuje ukázkový skript Azure PowerShell pro vytvoření spravované instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: fd091cedb67c07b7de2c7e8540e99c3e8daf7dcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323695"
---
# <a name="use-powershell-to-create-a-managed-instance"></a>Použití PowerShellu k vytvoření spravované instance

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Tento ukázkový skript PowerShellu vytvoří spravovanou instanci v vyhrazené podsíti v rámci nové virtuální sítě. Také nakonfiguruje směrovací tabulku a skupinu zabezpečení sítě pro virtuální síť. Po úspěšném spuštění skriptu je možné k spravované instanci přistupovat z virtuální sítě nebo z místního prostředí. Viz [Konfigurace virtuálního počítače Azure pro připojení k Azure SQL Database Managed instance](../connect-vm-instance-configure.md) a [Konfigurace připojení typu Point-to-site k spravované instanci Azure SQL z místního](../point-to-site-p2s-configure.md)prostředí.

> [!IMPORTANT]
> Omezení najdete v tématu [podporované oblasti](../resource-limits.md#supported-regions) a [podporované typy předplatného](../resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento kurz Azure PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

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
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Získá ve skupině prostředků virtuální síť. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Nastaví cílový stav pro virtuální síť. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Načte podsíť ve virtuální síti. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Nakonfiguruje cílový stav pro konfiguraci podsítě ve virtuální síti. |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Vytvoří směrovací tabulku. |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Získá směrovací tabulky. |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Nastaví cílový stav pro tabulku směrování. |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Vytvoří spravovanou instanci. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShell najdete v [dokumentaci k Azure PowerShell](/powershell/azure/).

Další ukázkové skripty PowerShellu pro spravovanou instanci Azure SQL najdete ve [skriptech PowerShellu spravovaných instancí Azure SQL](../../database/powershell-script-content-guide.md).
