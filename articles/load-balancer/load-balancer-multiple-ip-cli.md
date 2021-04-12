---
title: Vyrovnávání zatížení u více konfigurací IP pomocí Azure CLI
titleSuffix: Azure Load Balancer
description: Přečtěte si, jak přiřadit virtuálnímu počítači víc IP adres pomocí Azure CLI.
services: virtual-network
documentationcenter: na
author: asudbring
ms.custom: seodec18, devx-track-azurecli
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 67f3ffa15b46eec9f352e08b0fe7c8b1bb15ac7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103417786"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-azure-cli"></a>Vyrovnávání zatížení u více konfigurací IP pomocí Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](load-balancer-multiple-ip.md)
> * [Rozhraní příkazového řádku](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

Tento článek popisuje, jak použít Azure Load Balancer s více IP adresami na sekundárním síťovém rozhraní (NIC). V tomto scénáři máme dva virtuální počítače s Windows, každý s primárním a sekundárním síťovým ADAPTÉRem. Každá ze sekundárních síťových adaptérů má dvě konfigurace protokolu IP. Každý virtuální počítač je hostitelem webů contoso.com a fabrikam.com. Každý web je vázán na jednu z konfigurací protokolu IP v sekundárním síťovém adaptéru. K vystavení přenosu dat do příslušné konfigurace protokolu IP pro web používáme Azure Load Balancer k vystavení dvou IP adres front-endu, jeden pro každý web. V tomto scénáři se používá stejné číslo portu v obou front-endu i v obou IP adresách back-end fondu.

![Obrázek scénáře vyrovnávání zatížení](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Postup vyrovnávání zatížení u více konfigurací IP adres

Chcete-li dosáhnout scénáře popsaného v tomto článku, proveďte následující kroky:

1. [Nainstalujte a nakonfigurujte rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli) podle kroků v propojeném článku a přihlaste se k účtu Azure.
2. [Vytvořte skupinu prostředků s](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) názvem *contosofabrikam* následujícím způsobem:

    ```azurecli
    az group create contosofabrikam westcentralus
    ```

3. Vytvořte pro tyto dva virtuální počítače [skupinu dostupnosti](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) . V tomto scénáři použijte následující příkaz:

    ```azurecli
    az vm availability-set create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Vytvořte virtuální síť s](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) názvem *myVNet* a podsíť s názvem *mySubnet*:

    ```azurecli
    az network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus --subnet-name MySubnet --subnet-prefix 10.0.0.0/24

    ```

5. [Vytvořte Nástroj pro vyrovnávání zatížení s](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) názvem *mylb*:

    ```azurecli
    az network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Vytvořte dvě dynamické veřejné IP adresy pro konfigurace IP adresy front-endu vašeho nástroje pro vyrovnávání zatížení:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Vytvořte dvě konfigurace IP adresy front-endu, *contosofe* a *fabrikamfe* :

    ```azurecli
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Vytvořte své fondy back-end adres – *contosopool* a *fabrikampool*, [test](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  -  *http* a vaše pravidla vyrovnávání zatížení – *HTTPc* a *HTTPf*:

    ```azurecli
    az network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    az network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Zkontrolujte výstup, abyste [ověřili, že se Váš nástroj pro vyrovnávání zatížení](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) správně vytvořil, spuštěním následujícího příkazu:

    ```azurecli
    az network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Vytvořte veřejnou IP adresu](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), *myPublicIp* a [účet úložiště](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) *mystorageaccont1* pro první virtuální počítač VM1 následujícím způsobem:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Vytvořte síťová rozhraní](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) pro VM1 a přidejte druhou konfiguraci protokolu IP, *VM1-ipconfig2* a [vytvořte virtuální počítač](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm) následujícím způsobem:

    ```azurecli
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. Opakujte kroky 10-11 pro druhý virtuální počítač:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Nakonec je třeba nakonfigurovat záznamy prostředků DNS tak, aby odkazovaly na příslušné IP adresy front-endu Load Balancer. Domény můžete hostovat v Azure DNS. Další informace o použití Azure DNS s Load Balancer najdete v tématu [použití Azure DNS s dalšími službami Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o tom, jak kombinovat služby Vyrovnávání zatížení v Azure [pomocí služeb vyrovnávání zatížení v Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Přečtěte si, jak můžete pomocí různých typů protokolů v Azure spravovat a řešit potíže s vyrovnáváním zatížení v [Log Analytics pro Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).