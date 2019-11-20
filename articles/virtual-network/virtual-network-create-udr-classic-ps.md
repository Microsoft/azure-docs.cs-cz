---
title: Řízení směrování ve virtuální síti Azure – PowerShell – Classic
description: Naučte se řídit směrování v virtuální sítě pomocí PowerShellu | Standardním
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 4170418b4f53277a8f4306fe2835286c8ac99dee
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186344"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Řízení směrování a používání virtuálních zařízení (Classic) pomocí prostředí PowerShell

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

Následující ukázkové Azure PowerShell příkazy očekávají jednoduché prostředí již vytvořené v závislosti na výše uvedeném scénáři. Pokud chcete spustit příkazy, které se zobrazují v tomto dokumentu, vytvořte prostředí zobrazené v části [vytvoření virtuální sítě (Classic) pomocí PowerShellu](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Vytvoření UDR pro podsíť front-endu
Pokud chcete vytvořit směrovací tabulku a trasu potřebnou pro podsíť front-endu na základě výše uvedeného scénáře, postupujte podle následujících kroků.

1. Spusťte následující příkaz, který vytvoří směrovací tabulku pro front-end podsíť:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Spuštěním následujícího příkazu vytvořte trasu v tabulce směrování pro odeslání veškerého provozu určeného do back-endové podsítě (192.168.2.0/24) do virtuálního počítače **FW1** (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Spusťte následující příkaz k přidružení směrovací tabulky k podsíti front- **Endu** :

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Vytvoření UDR pro back-endové podsítě
Pokud chcete vytvořit směrovací tabulku a trasu potřebnou pro podsíť back-end založenou na tomto scénáři, proveďte následující kroky:

1. Spusťte následující příkaz, který vytvoří směrovací tabulku pro back-endové podsítě:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Spuštěním následujícího příkazu vytvořte trasu v tabulce směrování pro odeslání veškerého provozu určeného do front-endové podsítě (192.168.1.0/24) do virtuálního počítače **FW1** (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Spusťte následující příkaz k přidružení směrovací tabulky k podsíti **back-endu** :

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Povolení předávání IP na virtuálním počítači s FW1

Pokud chcete povolit předávání IP adres na virtuálním počítači s FW1, proveďte následující kroky:

1. Spuštěním následujícího příkazu zkontrolujete stav předávání IP:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Spusťte následující příkaz, který povolí předávání IP pro virtuální počítač *FW1* :

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
