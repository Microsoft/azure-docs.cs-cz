---
title: Ovládací prvek v modelu Classic Azure Virtual Network – Powershellu – směrování | Dokumentace Microsoftu
description: Zjistěte, jak řídit směrování do virtuální sítě s použitím prostředí PowerShell | Classic
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 930676a396ae316ec761ba5d03ad1a1d0fd7a425
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232562"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Řídit směrování a použití virtuálních zařízení (classic) pomocí Powershellu

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (Classic)](virtual-network-create-udr-classic-ps.md)
> * [Rozhraní příkazového řádku (Classic)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Než začnete pracovat s prostředky Azure, je potřeba si uvědomit, že Azure má v současné době dva modely nasazení: Azure Resource Manager a klasický. Před zahájením práce s jakýmikoli prostředky Azure se ujistěte, že rozumíte [modelům nasazení a příslušným nástrojům](../azure-resource-manager/resource-manager-deployment-model.md). Dokumentaci k různým nástrojům můžete zobrazit výběrem možnosti v horní části tohoto článku. Tento článek se týká modelu nasazení Classic.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Ukázky Azure Powershellu následující příkazy očekávat jednoduché prostředí už vytvořený podle výše uvedeného scénáře. Pokud chcete spustit příkazy, jak jsou zobrazeny v tomto dokumentu, vytvoření zobrazeného v prostředí [vytvoření virtuální sítě (classic) pomocí prostředí PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Vytvoření uživatelem definovaná TRASA pro front-endové podsítě
Vytvoření směrovací tabulky a trasy potřebné pro podsítě front end, který je založený na výše uvedeném scénáři, postupujte podle následujících kroků.

1. Spuštěním následujícího příkazu vytvořte tabulku směrování pro front-endové podsítě:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Spusťte následující příkaz k vytvoření trasy ve směrovací tabulce odesílat veškerý provoz směřující do back endové podsítě (192.168.2.0/24) k **FW1** virtuálního počítače (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Spusťte následující příkaz k přidružení směrovací tabulky s **front-endu** podsítě:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Vytvoření uživatelem definovaná TRASA pro podsíť back-end
Pokud chcete vytvořit směrovací tabulku a směrování pro podsíť back-endu na základě uvedeného scénáře potřeba, proveďte následující kroky:

1. Spuštěním následujícího příkazu vytvořte tabulku směrování pro podsíť back-end:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Spusťte následující příkaz k vytvoření trasy ve směrovací tabulce odesílat veškerý provoz směřující do front-endové podsítě (. 192.168.1.0/24) k **FW1** virtuálního počítače (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Spusťte následující příkaz k přidružení směrovací tabulky s **back-endu** podsítě:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Povolit předávání IP na virtuálním počítači FW1

Pokud chcete povolit předávání ve virtuálním počítači FW1 IP, proveďte následující kroky:

1. Spusťte následující příkaz a zkontrolujte stav předávání IP:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Spuštěním následujícího příkazu povolte pro předávání IP *FW1* virtuálního počítače:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
