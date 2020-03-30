---
title: Virtuální počítač s více IP adresami pomocí azure cli
titlesuffix: Azure Virtual Network
description: Zjistěte, jak přiřadit více IP adres virtuálnímu počítači pomocí rozhraní příkazového řádku Azure (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: kumud
ms.openlocfilehash: 144f30463adb3dfbce1717e06548baccc8286f8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240226"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Přiřazení více IP adres virtuálním počítačům pomocí azure cli

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Tento článek vysvětluje, jak vytvořit virtuální počítač (VM) prostřednictvím modelu nasazení Azure Resource Manager pomocí Azure CLI. Více ADRES IP nelze přiřadit k prostředkům vytvořeným prostřednictvím klasického modelu nasazení. Další informace o modelech nasazení Azure najdete v článku [Principy modelů nasazení.](../resource-manager-deployment-model.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Vytvoření virtuálního počítače s několika IP adresami

Následující kroky vysvětlují, jak vytvořit ukázkový virtuální počítač s více ADRESAMI IP, jak je popsáno ve scénáři. Změňte hodnoty proměnných v typech "" a IP adres podle potřeby pro implementaci. 

1. Pokud ho ještě nemáte nainstalované, nainstalujte [příkazový příkaz k příkazu Konazure.](/cli/azure/install-azure-cli)
2. Vytvořte dvojici veřejných a soukromých klíčů SSH pro virtuální počítače s Linuxem dokončením kroků v [tématu Vytvoření páru veřejných a soukromých klíčů SSH pro virtuální počítače s Linuxem](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Z příkazového prostředí se `az login` přihlaste pomocí příkazu a vyberte předplatné, které používáte.
4. Vytvořte virtuální počítač spuštěním skriptu, který následuje na počítači s Linuxem nebo Mac. Skript vytvoří skupinu prostředků, jednu virtuální síť (VNet), jednu síťovou síť se třemi konfiguracemi IP a virtuální počítač se dvěma síťovými kartami, které jsou k němu připojeny. Síťové karty, veřejné IP adresy, virtuální sítě a prostředků virtuálních počítačů musí existovat ve stejném umístění a předplatném. Ačkoli všechny prostředky nemusí existovat ve stejné skupině prostředků, v následujícím skriptu.

```bash
    
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
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. The script fails if the VM size
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

Kromě vytvoření virtuálního počítače s pomocí nic s 3 konfiguracemi IP, skript vytvoří:

- Ve výchozím nastavení je to jeden prémiový spravovaný disk, ale máte další možnosti pro typ disku, který můžete vytvořit. Přečtěte si [vytvoření virtuálního počítače s Linuxem pomocí](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) článku Azure CLI pro podrobnosti.
- Virtuální síť s jednou podsítí a dvěma veřejnými IP adresami. Případně můžete použít *existující* prostředky virtuální sítě, podsítě, síťové karty nebo veřejné IP adresy. Chcete-li se dozvědět, jak používat stávající `az vm create -h`síťové prostředky, nikoli vytvářet další prostředky, zadejte .

Veřejné IP adresy mají nominální poplatek. Další informace o cenách IP adres najdete na stránce [s cenami ip adres.](https://azure.microsoft.com/pricing/details/ip-addresses) Počet veřejných IP adres, které lze použít v předplatném, je omezen. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

Po vytvoření virtuálního počítače `az network nic show --name MyNic1 --resource-group myResourceGroup` zadejte příkaz pro zobrazení konfigurace nic. Zadejte `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` zobrazení seznamu konfigurací IP adres přidružených k nic nic.

Přidejte privátní IP adresy do operačního systému virtuálního počítače dokončením kroků pro váš operační systém v části [Přidat IP adresy do operačního systému virtuálního počítače](#os-config) v tomto článku.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Přidání IP adres do virtuálního počítačů

Další privátní a veřejné IP adresy můžete přidat do existujícího síťového rozhraní Azure provedením následujících kroků. Příklady vycházet ze [scénáře](#scenario) popsaného v tomto článku.

1. Otevřete příkazové prostředí a dokončete zbývající kroky v této části v rámci jedné relace. Pokud ještě nemáte nainstalované a nakonfigurované azure cli, proveďte kroky v článku `az-login` instalace Azure [CLI](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) a přihlaste se ke svému účtu Azure pomocí příkazu.

2. Podle vašich požadavků proveďte kroky v jedné z následujících částí:

    **Přidání privátní IP adresy**
    
    Chcete-li přidat privátní adresu IP do nic, musíte vytvořit konfiguraci IP pomocí následujícího příkazu. Statická adresa IP musí být nepoužívaná adresa podsítě.

    ```azurecli
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Vytvořte tolik konfigurací, kolik potřebujete, pomocí jedinečných názvů konfigurací a privátních IP adres (pro konfigurace se statickými ADRESAMI IP).

    **Přidání veřejné IP adresy**
    
    Veřejná IP adresa je přidána tak, že se přidruží k nové konfiguraci IP nebo k existující konfiguraci IP. Podle potřeby proveďte kroky v jedné z následujících částí.

    Veřejné IP adresy mají nominální poplatek. Další informace o cenách IP adres najdete na stránce [s cenami ip adres.](https://azure.microsoft.com/pricing/details/ip-addresses) Počet veřejných IP adres, které lze použít v předplatném, je omezen. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

    - **Přidružení prostředku k nové konfiguraci protokolu IP**
    
        Kdykoli přidáte veřejnou IP adresu do nové konfigurace IP, musíte také přidat privátní IP adresu, protože všechny konfigurace IP musí mít privátní IP adresu. Můžete buď přidat existující prostředek veřejné IP adresy, nebo vytvořit nový. Chcete-li vytvořit nový, zadejte následující příkaz:
    
        ```azurecli
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Chcete-li vytvořit novou konfiguraci IP se statickou privátní IP adresou a přidruženým zdrojem veřejné IP adresy *myPublicIP3,* zadejte následující příkaz:

        ```azurecli
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Přidružení prostředku k existující konfiguraci protokolu IP** Prostředek veřejné IP adresy lze přidružit pouze ke konfiguraci IP adresy, která ještě nemá jeden přidružený prostředek. Zadáním následujícího příkazu můžete určit, zda má konfigurace protokolu IP přidruženou veřejnou adresu IP:

        ```azurecli
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Vrácený výstup:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Vzhledem k tomu, že sloupec **PublicIpAddressId** pro *Protokol IpConfig-3* je ve výstupu prázdný, není k němu aktuálně přidružen žádný prostředek veřejné IP adresy. Do protokolu IpConfig-3 můžete přidat existující prostředek veřejné IP adresy nebo zadat následující příkaz pro jeho vytvoření:

        ```azurecli
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Zadáním následujícího příkazu přidružte prostředek veřejné IP adresy k existující konfiguraci IP s názvem *IPConfig-3*:
    
        ```azurecli
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Zobrazte privátní IP adresy a ID prostředků veřejné IP adresy přiřazené k řenici zadáním následujícího příkazu:

    ```azurecli
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Vrácený výstup: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Přidejte privátní IP adresy, které jste přidali do nic do operačního systému virtuálního počítačů podle pokynů v [části Přidat IP adresy do operačního systému virtuálního počítačů](#os-config) v tomto článku. Nepřidávejte veřejné IP adresy do operačního systému.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
