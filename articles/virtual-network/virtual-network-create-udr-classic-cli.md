---
title: Řídit směrování v modelu Classic Azure Virtual Network - CLI - | Dokumentace Microsoftu
description: Zjistěte, jak řídit směrování ve virtuálních sítích v modelu nasazení classic pomocí rozhraní příkazového řádku Azure
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38678574"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Řídit směrování a použití virtuálních zařízení (classic) pomocí rozhraní příkazového řádku Azure

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (Classic)](virtual-network-create-udr-classic-ps.md)
> * [Rozhraní příkazového řádku (Classic)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Můžete také [řídit směrování a použití virtuálních zařízení v modelu nasazení Resource Manager](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Ukázkové příkazy rozhraní příkazového řádku Azure níže očekávat jednoduché prostředí už vytvořeny podle výše uvedeného scénáře. Pokud chcete spustit příkazy, jak jsou zobrazeny v tomto dokumentu, vytvoření zobrazeného v prostředí [vytvoření virtuální sítě (classic) pomocí Azure CLI](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Vytvoření uživatelem definovaná TRASA pro front-endové podsítě
Vytvoření směrovací tabulky a trasy potřebné pro podsítě front end, který je založený na výše uvedeném scénáři, postupujte podle následujících kroků.

1. Spusťte následující příkaz pro přepnutí do klasického režimu:

    ```azurecli
    azure config mode asm
    ```

    Výstup:

        info:    New mode is asm

2. Spuštěním následujícího příkazu vytvořte tabulku směrování pro front-endové podsítě:

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
   
   * **-l (nebo --location)**. Oblasti Azure, ve kterém se vytvoří nová skupina NSG. Pro náš scénář *westus*.
   * **-n (nebo --name)**. Název nové skupiny NSG. Pro náš scénář *NSG front-endu*.
3. Spusťte následující příkaz k vytvoření trasy ve směrovací tabulce odesílat veškerý provoz směřující do back endové podsítě (192.168.2.0/24) k **FW1** virtuálního počítače (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Výstup:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parametry:
   
   * **-r (nebo--název směrovací tabulky)**. Název směrovací tabulka, ve kterém se trasa přidá. Pro náš scénář *uživatelem definovaná TRASA front-endu*.
   * **-a (nebo --address-prefixes)**. Předpona pro podsíť, ve kterém jsou pakety směřující na adresy. Pro náš scénář *192.168.2.0/24*.
   * **-t (nebo--další typ segmentu směrování)**. Typ objektu provozu se odešlou do. Možné hodnoty jsou *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*, nebo *žádný*.
   * **-p (nebo--další segment směrování ip adresy**). IP adresa dalšího segmentu směrování. Pro náš scénář *192.168.0.4*.
4. Přidružení směrovací tabulky vytvořené pomocí následujícího příkazu spusťte **front-endu** podsítě:

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
   
   * **-t (nebo--vnet-name)**. Název sítě VNet, ve kterém se podsíť nachází. V našem scénáři je to *TestVNet*.
   * **-n (nebo--subnet-name**. Název směrovací tabulky podsítě se přidají do. V našem scénáři je to *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Vytvoření uživatelem definovaná TRASA pro podsíť back-end
Vytvoření směrovací tabulky a trasy potřebné pro back endové podsítě, v závislosti na scénáři, proveďte následující kroky:

1. Spuštěním následujícího příkazu vytvořte tabulku směrování pro podsíť back-end:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Spusťte následující příkaz k vytvoření trasy ve směrovací tabulce odesílat veškerý provoz směřující do front-endové podsítě (. 192.168.1.0/24) k **FW1** virtuálního počítače (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Spusťte následující příkaz k přidružení směrovací tabulky s **back-endu** podsítě:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

