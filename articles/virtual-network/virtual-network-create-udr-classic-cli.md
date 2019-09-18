---
title: Řízení směrování v Azure Virtual Network-CLI – Classic | Microsoft Docs
description: Naučte se řídit směrování v virtuální sítě pomocí rozhraní příkazového řádku Azure v modelu nasazení Classic.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 1193145b315175e6394db4caf93ab2e76a942ed9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058794"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Řízení směrování a používání virtuálních zařízení (Classic) pomocí rozhraní příkazového řádku Azure

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (Classic)](virtual-network-create-udr-classic-ps.md)
> * [Rozhraní příkazového řádku (Classic)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Můžete také [řídit směrování a používat virtuální zařízení v modelu nasazení Správce prostředků](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Ukázkové příkazy Azure CLI níže očekávají jednoduché prostředí, které už je vytvořené na základě výše uvedeného scénáře. Pokud chcete spustit příkazy, které se zobrazují v tomto dokumentu, vytvořte prostředí zobrazené v [části vytvoření virtuální sítě (Classic) pomocí Azure CLI](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Vytvoření UDR pro podsíť front-endu
Pokud chcete vytvořit směrovací tabulku a trasu potřebnou pro podsíť front-endu na základě výše uvedeného scénáře, postupujte podle následujících kroků.

1. Spusťte následující příkaz, který přepne do klasického režimu:

    ```azurecli
    azure config mode asm
    ```

    Výstup:

        info:    New mode is asm

2. Spusťte následující příkaz, který vytvoří směrovací tabulku pro front-end podsíť:

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
   
   * **-l (nebo --location)** . Oblast Azure, ve které se vytvoří nový NSG. Pro náš scénář *westus*.
   * **-n (nebo --name)** . Název nové NSG. Pro náš scénář se *NSG-front-endu*.
3. Spuštěním následujícího příkazu vytvořte trasu v tabulce směrování pro odeslání veškerého provozu určeného do back-endové podsítě (192.168.2.0/24) do virtuálního počítače **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Výstup:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parametry:
   
   * **-r (nebo--Route-Table-Name)** . Název směrovací tabulky, do které bude trasa přidána. Pro náš scénář se *udr-front-endu*.
   * **-a (nebo --address-prefixes)** . Předpona adresy pro podsíť, na kterou mají být pakety určeny Pro náš scénář *192.168.2.0/24*.
   * **-t (nebo--Next-Hop-Type)** . Typ provozu objektu bude odeslán do. Možné hodnoty jsou *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*nebo *none*.
   * **-p (nebo--Next-Hop-IP-Address**). IP adresa pro další segment směrování. Pro náš scénář *192.168.0.4*.
4. Spuštěním následujícího příkazu přidružte směrovací tabulku vytvořenou s podsítí front- **Endu** :

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
   
   * **-t (nebo--VNet-Name)** . Název virtuální sítě, ve které se podsíť nachází. V našem scénáři je to *TestVNet*.
   * **-n (nebo--Subnet-Name**. Název podsítě, do které bude tabulka směrování přidána. V našem scénáři je to *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Vytvoření UDR pro back-endové podsítě
Chcete-li vytvořit směrovací tabulku a trasu potřebnou pro back-endové podsíť na základě scénáře, proveďte následující kroky:

1. Spusťte následující příkaz, který vytvoří směrovací tabulku pro back-endové podsítě:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Spuštěním následujícího příkazu vytvořte trasu v tabulce směrování pro odeslání veškerého provozu určeného do front-endové podsítě (192.168.1.0/24) do virtuálního počítače **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Spusťte následující příkaz k přidružení směrovací tabulky k podsíti **back-endu** :

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

