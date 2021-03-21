---
title: Virtuální počítač s několika IP adresami pomocí Azure CLI
titlesuffix: Azure Virtual Network
description: Naučte se, jak přiřadit virtuálnímu počítači víc IP adres pomocí rozhraní příkazového řádku Azure (CLI).
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: allensu
ms.openlocfilehash: 0b5e8f28be9960a2bb4e1406a293ab5e03962681
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98680565"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Přiřazení více IP adres k virtuálním počítačům pomocí Azure CLI

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Tento článek vysvětluje, jak pomocí rozhraní příkazového řádku Azure vytvořit virtuální počítač (VM) pomocí modelu nasazení Azure Resource Manager. K prostředkům vytvořeným prostřednictvím modelu nasazení Classic nelze přiřadit více IP adres. Další informace o modelech nasazení Azure najdete v článku [Principy modelů nasazení](../azure-resource-manager/management/deployment-models.md) .

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Vytvoření virtuálního počítače s několika IP adresami

Následující postup vysvětluje, jak vytvořit ukázkový virtuální počítač s více IP adresami, jak je popsáno ve scénáři. Změňte hodnoty proměnných v "" a typy IP adres, jak je požadováno, pro vaši implementaci. 

1. Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) , pokud ho ještě nemáte nainstalovaný.
2. Vytvořte dvojici veřejného a privátního klíče SSH pro virtuální počítače se systémem Linux, a to provedením kroků v tématu [vytvoření dvojice veřejného a privátního klíče SSH pro virtuální počítače se systémem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Z příkazového prostředí se přihlaste pomocí příkazu `az login` a vyberte předplatné, které používáte.
4. Vytvořte virtuální počítač spuštěním skriptu, který následuje na počítači se systémem Linux nebo Mac. Skript vytvoří skupinu prostředků, jednu virtuální síť (VNet), jednu síťovou kartu se třemi konfiguracemi IP adres a virtuální počítač se dvěma síťovými rozhraními, které jsou k němu připojené. Síťové rozhraní, veřejná IP adresa, virtuální síť a prostředky virtuálních počítačů musí existovat ve stejném umístění a předplatném. I když prostředky nemusí všechny existovat ve stejné skupině prostředků, v následujících skriptech.

```azurecli
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP addresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location eastcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Kromě vytvoření virtuálního počítače s síťové karty se 3 konfiguracemi IP adres vytvoří skript:

- Ve výchozím nastavení se jedná o jeden spravovaný disk úrovně Premium, ale máte k dispozici další možnosti pro typ disku, který můžete vytvořit. Podrobnější informace najdete v článku [Vytvoření virtuálního počítače se systémem Linux pomocí Azure CLI](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .
- Virtuální síť s jednou podsítí a dvěma veřejnými IP adresami. Alternativně můžete použít *existující* prostředky virtuální sítě, podsítě, síťové karty nebo veřejné IP adresy. Pokud se chcete dozvědět, jak používat stávající síťové prostředky místo vytváření dalších prostředků, zadejte `az vm create -h` .

Veřejné IP adresy mají nominální poplatek. Pokud se chcete dozvědět víc o cenách IP adres, přečtěte si stránku [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses) . Počet veřejných IP adres, které se dají použít v předplatném, je omezený. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

Po vytvoření virtuálního počítače zadejte `az network nic show --name MyNic1 --resource-group myResourceGroup` příkaz pro zobrazení konfigurace síťové karty. Pokud `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` chcete zobrazit seznam konfigurací protokolu IP přidružených k síťovému rozhraní, zadejte.

Dokončete kroky pro operační systém v části [Přidání IP adres do operačního systému](#os-config) virtuálního počítače v tomto článku přidáním privátních IP adres do operačního systému virtuálního počítače.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Přidání IP adres k virtuálnímu počítači

K existujícímu síťovému rozhraní Azure můžete přidat další privátní a veřejné IP adresy, a to provedením následujících kroků. Příklady jsou založeny na [scénáři](#scenario) popsaném v tomto článku.

1. Otevřete příkazové prostředí a dokončete zbývající kroky v této části v rámci jedné relace. Pokud ještě nemáte nainstalované a nakonfigurované rozhraní příkazového řádku Azure CLI, proveďte kroky v článku [instalace Azure CLI](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) a přihlaste se k účtu Azure pomocí `az-login` příkazu.

2. V závislosti na vašich požadavcích proveďte kroky v jedné z následujících částí:

    **Přidat privátní IP adresu**
    
    Chcete-li přidat privátní IP adresu do síťového rozhraní, je nutné vytvořit konfiguraci protokolu IP pomocí příkazu, který následuje. Statická IP adresa musí být nepoužitou adresou pro podsíť.

    ```azurecli
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Pomocí jedinečných názvů konfigurací a privátních IP adres (pro konfigurace se statickými IP adresami) vytvořte tolik konfigurací, kolik jich vyžadujete.

    **Přidat veřejnou IP adresu**
    
    Veřejná IP adresa se přidá tím, že ji přidruží k nové konfiguraci protokolu IP nebo k existující konfiguraci protokolu IP. Proveďte kroky v jedné z částí, které následují, jak budete potřebovat.

    Veřejné IP adresy mají nominální poplatek. Pokud se chcete dozvědět víc o cenách IP adres, přečtěte si stránku [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses) . Počet veřejných IP adres, které se dají použít v předplatném, je omezený. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

    - **Přidružit prostředek k nové konfiguraci protokolu IP**
    
        Kdykoli přidáte veřejnou IP adresu v nové konfiguraci protokolu IP, musíte taky přidat privátní IP adresu, protože všechny konfigurace IP adres musí mít privátní IP adresu. Můžete buď přidat existující prostředek veřejné IP adresy, nebo vytvořit nový. Pokud chcete vytvořit nový, zadejte následující příkaz:
    
        ```azurecli
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Pokud chcete vytvořit novou konfiguraci protokolu IP se statickou privátní IP adresou a přiřazeným prostředkem veřejné IP adresy *myPublicIP3* , zadejte následující příkaz:

        ```azurecli
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Přidružte prostředek k existující konfiguraci protokolu IP** . Prostředek veřejné IP adresy se dá přidružit jenom ke konfiguraci protokolu IP, která ještě nemá přidruženou žádnou přidruženou adresu. Zadáním následujícího příkazu můžete určit, jestli má konfigurace protokolu IP přidruženou veřejnou IP adresu:

        ```azurecli
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Vrácený výstup:
    
        ```output
        Name        PublicIpAddressId
        
        ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3
        ```

        Vzhledem k tomu, že sloupec **PublicIpAddressId** pro *příkaz ipconfig-3* je ve výstupu prázdný, není k němu aktuálně PŘIDRUŽEN žádný prostředek veřejné IP adresy. Existující prostředek veřejné IP adresy můžete přidat do příkazu IpConfig-3 nebo můžete zadat následující příkaz a vytvořit ho:

        ```azurecli
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Zadejte následující příkaz pro přidružení prostředku veřejné IP adresy k existující konfiguraci protokolu IP s názvem *ipconfig-3*:
    
        ```azurecli
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Zadáním následujícího příkazu Zobrazte privátní IP adresy a ID prostředků veřejné IP adresy přiřazené k síťovému rozhraní:

    ```azurecli
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Vrácený výstup: <br>

    ```output
    Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
    
    ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
    IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
    IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    ```
    
4. Podle pokynů v části [Přidání IP adres do operačního systému](#os-config) virtuálního počítače v tomto článku přidejte privátní IP adresy, které jste přidali do síťového adaptéru, do operačního systému virtuálního počítače. Nepřidávat veřejné IP adresy do operačního systému.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]