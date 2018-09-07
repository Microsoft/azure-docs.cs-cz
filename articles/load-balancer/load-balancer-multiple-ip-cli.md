---
title: Pro více konfigurací IP pomocí rozhraní příkazového řádku Azure pro vyrovnávání zatížení | Dokumentace Microsoftu
description: Zjistěte, jak přiřadit několik IP adres k virtuálnímu počítači pomocí rozhraní příkazového řádku Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: kumud
ms.openlocfilehash: 4080e4e3e274d64046f46d24b34959542e0ad304
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44049588"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-azure-cli"></a>Vyrovnávání zatížení na více konfigurací protokolu IP pomocí Azure CLI

Tento článek popisuje, jak používat Azure Load Balancer s několika IP adresami na sekundární síťové rozhraní (NIC). V tomto scénáři máme dva virtuální počítače se systémem Windows, každý s primární a sekundární síťové rozhraní Každá sekundární síťová rozhraní obsahuje dvěma konfiguracemi IP. Každý virtuální počítač hostuje websites contoso.com a fabrikam.com. Každý z těchto webů je vázán na jednu z konfigurace protokolu IP na sekundární síťové rozhraní Nástroj pro vyrovnávání zatížení Azure používáme k vystavení dva front-endové IP adresy, jeden pro každý web, za účelem distribuce provozu do příslušné konfigurace protokolu IP pro web. Tento scénář používá stejné číslo portu mezi jak front-endů, tak i back-endový fond IP adres.

![Obrázek scénář LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Postup pro více konfigurací IP Vyrovnávání zatížení

K dosažení scénář popsaný v tomto článku, proveďte následující kroky:

1. [Instalace a konfigurace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pomocí následujících kroků v odkazovaném článku a protokolu ke svému účtu Azure.
2. [Vytvořte skupinu prostředků](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) volá *contosofabrikam* následujícím způsobem:

    ```azurecli
    az group create contosofabrikam westcentralus
    ```

3. [Vytvoření skupiny dostupnosti](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) k pro dva virtuální počítače. V tomto scénáři použijte následující příkaz:

    ```azurecli
    az vm availability-set create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Vytvoření virtuální sítě](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) volá *myVNet* podsíť s názvem *mySubnet*:

    ```azurecli
    az network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus --subnet-name MySubnet --subnet-prefix 10.0.0.0/24

    ```

5. [Vytvoření load balanceru úrovně](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) volá *mylb*:

    ```azurecli
    az network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Vytvořte dvě dynamické veřejné IP adresy pro front-endová konfigurace protokolu IP nástroje pro vyrovnávání zatížení:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Vytvořte dvě konfigurace protokolu IP front-endu *contosofe* a *fabrikamfe* v uvedeném pořadí:

    ```azurecli
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Vytvoření back-end fondu adres – *contosopool* a *fabrikampool*, [sondy](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - *HTTP*a zatížení pravidla - vyrovnávání *HTTPc* a *HTTPf*:

    ```azurecli
    az network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    az network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Zkontrolujte výstup [ověřit nástroj pro vyrovnávání zatížení](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) správně vytvořila spuštěním následujícího příkazu:

    ```azurecli
    az network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Vytvoření veřejné IP adresy](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), *myPublicIp*, a [účtu úložiště](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json), *mystorageaccont1* pro svůj první virtuální počítač VM1 následujícím způsobem:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Vytvoření síťových rozhraní](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) pro VM1 a druhá konfigurace IP adresy, přidejte *VM1 ipconfig2*, a [vytvořit virtuální počítač](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm) následujícím způsobem:

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

13. Nakonec musíte nakonfigurovat záznamy prostředků DNS tak, aby odkazoval na IP adresu odpovídající front-endu nástroje pro vyrovnávání zatížení. Může hostovat domény v Azure DNS. Další informace o použití Azure DNS s nástrojem pro vyrovnávání zatížení najdete v tématu [pomocí Azure DNS s ostatními službami Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Další postup
- Další informace o tom, jak kombinací služeb Vyrovnávání zatížení v Azure v [pomocí služby Vyrovnávání zatížení v Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Zjistěte, jak pomocí různých typů protokolů v Azure pro správu a řešení potíží s nástroj pro vyrovnávání zatížení v [Log analytics pro Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
