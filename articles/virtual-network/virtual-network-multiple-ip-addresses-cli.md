---
title: Virtuální počítač s více IP adres pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Zjistěte, jak přiřadit více IP adres k virtuálnímu počítači pomocí rozhraní příkazového řádku Azure (CLI).
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: jimdial
ms.openlocfilehash: a1e78f7fa892586385e1dbd186125630ee1fb307
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Přiřadit více IP adres virtuálních počítačů pomocí rozhraní příkazového řádku Azure

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Tento článek vysvětluje, jak vytvořit virtuální počítač (VM) pomocí modelu nasazení Azure Resource Manager pomocí rozhraní příkazového řádku Azure. Nelze přiřadit více IP adres k prostředkům, které jsou vytvořené pomocí modelu nasazení classic. Další informace o modelech nasazení Azure, najdete [pochopit modely nasazení](../resource-manager-deployment-model.md) článku.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Vytvoření virtuálního počítače s více IP adres

Kroky, které následují vysvětlují, jak vytvářet příklad virtuální počítač s více IP adres, jak je popsáno v tomto scénáři. Měnit hodnoty proměnných v "" a typy IP adresu, podle potřeby týkající se vaší implementace. 

1. Nainstalujte [Azure CLI 2.0](/cli/azure/install-az-cli2) Pokud ještě nemáte nainstalováno.
2. Vytvoření SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem pomocí kroků v [vytvoření SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Z příkazového prostředí, přihlášení pomocí příkazu `az login` a vyberte odběr, který používáte.
4. Vytvoření virtuálního počítače tak, že spustíte skript, který odpovídá na počítači se systémem Linux nebo Mac. Skript vytvoří skupinu prostředků, jednu virtuální síť (VNet), jeden síťový adaptér s tři konfigurace protokolu IP a virtuální počítač se dvěma síťovými adaptéry, které jsou k němu připojen. Síťový adaptér, veřejnou IP adresu, virtuální sítě a prostředky virtuálních počítačů musí existovat ve stejném umístění a předplatného. Když prostředky nebudete mít existovat ve stejné skupině prostředků, v následující skript dělají.

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP adresses unique to each Azure region. Download and view the file from
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

azure network nic ip-config create \
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
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes rticle. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location estcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the utput returned by entering the
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

Kromě vytvoření virtuálního počítače s síťový adaptér s 3: Konfigurace protokolu IP, vytvoří skript:

- Jeden premium spravovat disku ve výchozím nastavení, ale máte další možnosti pro typ disku, které lze vytvořit. Pro čtení [vytvoření virtuálního počítače s Linuxem pomocí Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) článku.
- Virtuální síť s jednou podsítí a dvě veřejné IP adresy. Alternativně můžete použít *existující* virtuální sítě, podsítě, síťový adaptér nebo adresu prostředky veřejné IP adresy. Chcete-li další informace o použití existující síťovým prostředkům, nikoli vytváření další zdroje informací, zadejte `az vm create -h`.

Veřejné IP adresy mají nominální poplatek. Další informace o cenách IP adresu, najdete [IP adresu ceny](https://azure.microsoft.com/pricing/details/ip-addresses) stránky. Maximální počet veřejné IP adresy, které lze použít v předplatném je. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-subscription-service-limits.md#networking-limits).

Po vytvoření virtuálního počítače, zadejte `az network nic show --name MyNic1 --resource-group myResourceGroup` příkazu zobrazte konfigurace síťových karet. Zadejte `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` zobrazíte seznam konfigurace protokolu IP, které jsou přidružené k síťovou kartu.

Přidání privátních IP adres do operačního systému virtuálního počítače pomocí kroků pro operační systém v [přidat IP adresy na operační systém virtuálního počítače](#os-config) tohoto článku.

## <a name="add"></a>Přidání IP adres pro virtuální počítač

Provedením následujících kroků můžete přidat další privátní a veřejné IP adresy pro existující rozhraní sítě Azure. Příklady stavějí [scénář](#Scenario) popsané v tomto článku.

1. Otevřete příkazové okno a dokončit zbývající kroky v této části v rámci jedné relace. Pokud ještě nemáte rozhraní příkazového řádku Azure, instalaci a konfiguraci, proveďte kroky v [instalace Azure CLI 2.0](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) článek a přihlášení k Azure váš účet, který `az-login` příkaz.

2. Proveďte kroky v jednom z těchto částí, podle potřeb:

    **Přidejte privátní IP adresy**
    
    Chcete-li přidat privátní IP adresy pro síťový adaptér, je nutné vytvořit konfiguraci IP adres pomocí příkazu, který následuje. Statická IP adresa musí být nepoužívané adresa podsítě.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Vytvořte tolik konfigurace, jak požadujete, pomocí konfigurace jedinečné názvy a privátní IP adresy (pro konfigurace s statické IP adresy).

    **Přidejte veřejnou IP adresu**
    
    Veřejná IP adresa se přidá tím, že přidružíte na novou konfiguraci protokolu IP nebo existující konfiguraci IP adres. Proveďte kroky v jednom z následujících, potřebujete.

    Veřejné IP adresy mají nominální poplatek. Další informace o cenách IP adresu, najdete [IP adresu ceny](https://azure.microsoft.com/pricing/details/ip-addresses) stránky. Maximální počet veřejné IP adresy, které lze použít v předplatném je. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-subscription-service-limits.md#networking-limits).

    - **Přidružte prostředek na novou konfiguraci protokolu IP**
    
        Vždy, když přidáte veřejnou IP adresu v novou konfiguraci protokolu IP, musíte taky přidat privátní IP adresy, protože všechny konfigurace protokolu IP, musí mít privátní IP adresy. Můžete přidat existující prostředek veřejné IP adresy, nebo vytvořte novou. Pokud chcete vytvořit nový, zadejte následující příkaz:
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Chcete-li vytvořit novou konfiguraci protokolu IP se statickou privátní IP adresou a přidruženého *myPublicIP3* veřejných IP adres prostředků, zadejte následující příkaz:

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Přidružte prostředek k existující konfigurace IP** prostředek veřejné IP adresy lze přidružit pouze pro konfiguraci IP adres, který ho přidružené neobsahuje. Můžete určit, zda má konfiguraci IP adres přidružené veřejnou IP adresu tak, že zadáte následující příkaz:

        ```bash
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Vrátí výstup:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Vzhledem k tomu **PublicIpAddressId** sloupec pro *IpConfig 3* je prázdné ve výstupu, žádný prostředek veřejné IP adresy je aktuálně k ní přidružena. Můžete přidat existující prostředek veřejné IP adresy na IpConfig 3, nebo zadejte následující příkaz k jeho vytvoření:

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Zadejte následující příkaz pro přidružení prostředek veřejné IP adresy ke stávající konfiguraci IP adresy s názvem *IPConfig 3*:
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Zobrazení privátních IP adres a prostředek veřejné IP adresy ID přiřazená na síťový adaptér tak, že zadáte následující příkaz:

    ```bash
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Vrátí výstup: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Přidat privátní IP adresy, které jste přidali do síťového adaptéru v operačním systému virtuálního počítače podle pokynů v [přidat IP adresy na operační systém virtuálního počítače](#os-config) tohoto článku. Nepřidávejte veřejné IP adresy v operačním systému.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
