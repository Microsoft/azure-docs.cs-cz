---
title: Řídit směrování v virtuální síti příkazového - řádku - Azure Classic | Microsoft Docs
description: Zjistěte, jak řídit směrování v virtuální sítě pomocí rozhraní příkazového řádku Azure v modelu nasazení classic
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 0b6c8da03c4a67aadb38280ba958a9b0feb88d1f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793881"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Řídit směrování a použití virtuálních zařízení (klasické) pomocí rozhraní příkazového řádku Azure

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (Classic)](virtual-network-create-udr-classic-ps.md)
> * [Rozhraní příkazového řádku (Classic)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Můžete také [řídit směrování a použití virtuálních zařízení v modelu nasazení Resource Manager](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Níže uvedené příkazy rozhraní příkazového řádku Azure ukázka očekávat jednoduché prostředí již vytvořeny podle výše uvedené scénáře. Pokud chcete ke spuštění příkazů, jak jsou zobrazeny v tomto dokumentu, vytvoření prostředí ukazuje [vytvoření virtuální sítě (klasické) pomocí rozhraní příkazového řádku Azure](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Vytvoření UDR pro podsítě front end
Pokud chcete vytvořit směrovací tabulku a směrování, které jsou potřebné pro podsítě front end závislosti na scénáři výše, postupujte podle následujících kroků.

1. Spusťte následující příkaz, který přepnout do klasického režimu:

    ```azurecli
    azure config mode asm
    ```

    Výstup:

        info:    New mode is asm

2. Spusťte následující příkaz, který vytvořit směrovací tabulku front-end podsítě:

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Výstup:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Parametry:
   
   * **-l (nebo --location)**. Oblast Azure, kde bude vytvořena nová skupina NSG. Pro náš scénář *westus*.
   * **-n (nebo --name)**. Název nové skupiny NSG. Pro náš scénář *NSG front-endu*.
3. Spusťte následující příkaz k vytvoření trasy ve směrovací tabulce odeslat veškerý provoz, jehož k podsíti back-end (192.168.2.0/24) na **FW1** virtuálních počítačů (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Výstup:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parametry:
   
   * **-r (nebo--název směrovací tabulky)**. Název směrovací tabulka, kam bude přidána trasy. Pro náš scénář *UDR front-endu*.
   * **-a (nebo --address-prefixes)**. Předpona adresy podsítě, kde jsou pakety určené do. Pro náš scénář *192.168.2.0/24*.
   * **-t (nebo--další typ směrování)**. Typ objektu provozu se odešle do. Možné hodnoty jsou *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*, nebo *žádné*.
   * **-p (nebo--další směrování ip adresu**). IP adresa dalšího směrování. Pro náš scénář *192.168.0.4*.
4. Spusťte následující příkaz k přiřazení směrovací tabulka vytvořené pomocí **front-endu** podsítě:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Výstup:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    Parametry:
   
   * **-t (nebo--vnet-name)**. Název sítě VNet, kde je umístěný v podsíti. V našem scénáři je to *TestVNet*.
   * **-n (nebo--název podsítě**. Název podsítě, směrovací tabulka se zařadí do. V našem scénáři je to *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Vytvoření UDR pro podsíť back-end
Pokud chcete vytvořit směrovací tabulku a směrování, které jsou potřeba pro back-end podsíť závislosti na scénáři, proveďte následující kroky:

1. Spusťte následující příkaz a vytvořte tabulku směrování pro podsíť back-end:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Spusťte následující příkaz k vytvoření trasy ve směrovací tabulce odeslat veškerý provoz, jehož klientské podsíti (192.168.1.0/24) na **FW1** virtuálních počítačů (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Spusťte následující příkaz k přiřazení směrovací tabulka s **back-end** podsítě:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

