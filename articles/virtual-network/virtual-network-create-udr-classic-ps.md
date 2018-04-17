---
title: Řídit směrování v Azure Virtual Network - PowerShell – Classic | Microsoft Docs
description: Zjistěte, jak řídit směrování v virtuální sítě pomocí prostředí PowerShell | Classic
services: virtual-network
documentationcenter: na
author: genli
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
ms.openlocfilehash: 870beabc3f238764e362dc99565f350a2329f4ec
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Řídit směrování a použití virtuálních zařízení (klasické) pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [Šablona](virtual-network-create-udr-arm-template.md)
> * [PowerShell (Classic)](virtual-network-create-udr-classic-ps.md)
> * [Rozhraní příkazového řádku (Classic)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Než začnete pracovat s prostředky Azure, je potřeba si uvědomit, že Azure má v současné době dva modely nasazení: Azure Resource Manager a klasický. Před zahájením práce s jakýmikoli prostředky Azure se ujistěte, že rozumíte [modelům nasazení a příslušným nástrojům](../azure-resource-manager/resource-manager-deployment-model.md). Dokumentaci k různým nástrojům můžete zobrazit výběrem možnosti v horní části tohoto článku. Tento článek se týká modelu nasazení Classic.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Ukázka prostředí Azure PowerShell níže uvedené příkazy očekávat jednoduché prostředí už vytvořený založené na výše uvedené scénáře. Pokud chcete ke spuštění příkazů, jak jsou zobrazeny v tomto dokumentu, vytvoření prostředí ukazuje [vytvoření virtuální sítě (klasické) pomocí prostředí PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Vytvoření UDR pro podsítě front end
Pokud chcete vytvořit směrovací tabulku a směrování, které jsou potřebné pro podsítě front end závislosti na scénáři výše, postupujte podle následujících kroků.

1. Spusťte následující příkaz, který vytvořit směrovací tabulku front-end podsítě:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Spusťte následující příkaz k vytvoření trasy ve směrovací tabulce odeslat veškerý provoz, jehož k podsíti back-end (192.168.2.0/24) na **FW1** virtuálních počítačů (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Spusťte následující příkaz k přiřazení směrovací tabulka s **front-endu** podsítě:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Vytvoření UDR pro podsíť back-end
Pokud chcete vytvořit směrovací tabulku a směrování, které jsou potřeba pro back-end podsíť závislosti na scénáři, proveďte následující kroky:

1. Spusťte následující příkaz a vytvořte tabulku směrování pro podsíť back-end:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Spusťte následující příkaz k vytvoření trasy ve směrovací tabulce odeslat veškerý provoz, jehož klientské podsíti (192.168.1.0/24) na **FW1** virtuálních počítačů (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Spusťte následující příkaz k přiřazení směrovací tabulka s **back-end** podsítě:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Povolení předávání IP ve virtuálním počítači FW1

Pokud chcete povolit předávání ve virtuálním počítači FW1 protokolu IP, proveďte následující kroky:

1. Spusťte následující příkaz a zkontrolujte stav předávání IP:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Spusťte následující příkaz k povolení předávání protokolu IP pro *FW1* virtuálních počítačů:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
