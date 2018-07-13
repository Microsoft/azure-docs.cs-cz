---
title: Virtuální počítač s několika IP adresy, které používají rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak přiřadit několik IP adres k virtuálnímu počítači pomocí rozhraní příkazového řádku Azure (CLI).
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
ms.openlocfilehash: c11883156f53ab53ebe6f84d66232f81f8cf31ff
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697360"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Přiřadit několik IP adres pro virtuální počítače pomocí Azure CLI

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Tento článek vysvětluje, jak vytvořit virtuální počítač (VM) prostřednictvím modelu nasazení Azure Resource Manageru pomocí rozhraní příkazového řádku Azure. Několik IP adres nelze přiřadit k prostředkům vytvořeným prostřednictvím modelu nasazení classic. Další informace o modelech nasazení Azure najdete v článku [vysvětlení modelů nasazení](../resource-manager-deployment-model.md) článku.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Vytvoření virtuálního počítače s několika IP adresami

Následující kroky popisují, jak vytvořit virtuální počítač příklad s několika IP adresami, jak je popsáno ve scénáři. Změna hodnot proměnných v "" a typy IP adres, jak je vyžadováno pro implementaci. 

1. Nainstalujte [příkazového řádku Azure CLI 2.0](/cli/azure/install-az-cli2) Pokud ještě nemáte nainstalovaný.
2. Vytvoření veřejného a privátního pár klíčů SSH pro virtuální počítače s Linuxem podle postupu uvedeného v [vytvoření veřejného a privátního pár klíčů SSH pro virtuální počítače s Linuxem](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Z příkazového prostředí, přihlaste se pomocí příkazu `az login` a vyberte předplatné, které používáte.
4. Vytvoření virtuálního počítače tak, že spustíte skript, který odpovídá na počítači se systémem Linux nebo Mac. Skript vytvoří skupinu prostředků, jednou virtuální sítí (VNet), jednu síťovou kartu pomocí tří konfigurací protokolu IP a virtuální počítač se dvěma síťovými kartami k němu připojená. Síťové rozhraní, veřejné IP adresy, virtuální síť a prostředky virtuálního počítače musí existovat ve stejném umístění a předplatném. I když prostředky všechny nemusí existovat ve stejné skupině prostředků, v následujícím skriptu dělají.

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

Kromě vytvoření virtuálního počítače se síťovým rozhraním s 3: Konfigurace protokolu IP, skript vytvoří:

- Na úrovni premium jeden spravovaný disk ve výchozím nastavení, ale máte další možnosti pro typ disku, které lze vytvořit. Přečtěte si [vytvořit virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , kde najdete podrobnosti.
- Virtuální síť s jednou podsítí a dvě veřejné IP adresy. Alternativně můžete použít *existující* virtuální sítě, podsítě, síťová karta nebo prostředky veřejné IP adresy. Chcete-li další informace o použití stávajících síťových prostředků, namísto vytváření dalších prostředků, zadejte `az vm create -h`.

Veřejné IP adresy mají nominální poplatek. Další informace o cenách IP adres, [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses) stránky. Platí omezení na počet veřejné IP adresy, které je možné v rámci předplatného. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-subscription-service-limits.md#networking-limits).

Po vytvoření virtuálního počítače zadejte `az network nic show --name MyNic1 --resource-group myResourceGroup` příkazu zobrazíte konfigurací síťové karty. Zadejte `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` zobrazíte seznam konfigurací protokolu IP, které jsou přidružené k síťové kartě

Přidat privátních IP adres do operačního systému virtuálního počítače pomocí kroků pro váš operační systém v [přidat IP adresy na operační systém virtuálního počítače](#os-config) části tohoto článku.

## <a name="add"></a>Přidejte IP adresy virtuálního počítače

Provedením následujících kroků můžete přidat další privátní a veřejné IP adresy pro existující rozhraní sítě Azure. V příkladech stavět [scénář](#Scenario) popsaných v tomto článku.

1. Otevřete příkazové okno a proveďte zbývající kroky v této části v rámci jedné relace. Pokud ještě nemáte rozhraní příkazového řádku Azure, instalaci a konfiguraci, proveďte kroky v [instalace Azure CLI 2.0](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) článku a přihlaste se k Azure, účet se `az-login` příkazu.

2. Proveďte kroky v jednom z následujících částí, na základě vašich požadavků:

    **Přidejte privátní IP adresy**
    
    Chcete-li přidat privátní IP adresu k síťové KARTĚ, musíte vytvořit konfiguraci IP pomocí následujícího příkazu. Nepoužívaná adresa podsítě musí být statickou IP adresu.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Vytvořte tolik konfigurace podle potřeby, pomocí jedinečnou konfiguraci názvy a privátní IP adresy (pro konfigurace se statickými IP adresami).

    **Přidání veřejné IP adresy**
    
    Veřejná IP adresa se přidá jeho přidružením k novou konfiguraci protokolu IP nebo existující konfigurace protokolu IP. Proveďte kroky v jednom z částí, které následují, podle potřeby.

    Veřejné IP adresy mají nominální poplatek. Další informace o cenách IP adres, [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses) stránky. Platí omezení na počet veřejné IP adresy, které je možné v rámci předplatného. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-subscription-service-limits.md#networking-limits).

    - **Přidružte prostředek, který se nová konfigurace IP**
    
        Pokaždé, když přidáte novou konfiguraci IP veřejnou IP adresu, musíte také přidat privátní IP adresu, protože všechny konfigurace protokolu IP, musí mít privátní IP adresu. Můžete přidat existující prostředek veřejné IP adresy, nebo vytvořte novou. Chcete-li vytvořit nový, zadejte následující příkaz:
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Chcete-li vytvořit novou konfiguraci IP adresy se statickou privátní IP adresou a přidružené *myPublicIP3* veřejné IP adresy prostředků, zadejte následující příkaz:

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Přidružit tento prostředek do existující konfigurace IP** prostředek veřejné IP adresy lze přidružit pouze ke konfiguraci IP, která ještě neexistuje spojené. Můžete určit, zda má konfiguraci IP přidružených veřejnou IP adresu tak, že zadáte následující příkaz:

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

        Protože **PublicIpAddressId** sloupec pro *IpConfig 3* je prázdné ve výstupu, žádný prostředek veřejné IP adresy je aktuálně k ní přidružena. Můžete přidat existující prostředek veřejné IP adresy na IpConfig 3, nebo zadejte následující příkaz k jejímu vytvoření:

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Zadejte následující příkaz, který přidružte prostředek veřejné IP adresy ke stávající konfiguraci IP adresy s názvem *IPConfig 3*:
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Zobrazte soukromé IP adresy a prostředek veřejné IP adresy ID přiřazené k síťovému rozhraní tak, že zadáte následující příkaz:

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
    

4. Přidejte privátní IP adresy, které jste přidali do síťového adaptéru v operačním systému virtuálního počítače podle pokynů v [přidat IP adresy na operační systém virtuálního počítače](#os-config) části tohoto článku. Nepřidávejte veřejných IP adres do operačního systému.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
