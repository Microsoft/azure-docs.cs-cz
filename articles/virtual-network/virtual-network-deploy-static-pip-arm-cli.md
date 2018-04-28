---
title: Vytvoření virtuálního počítače se statickou veřejnou IP adresu - rozhraní příkazového řádku Azure | Microsoft Docs
description: Naučte se vytvořit virtuální počítač se statickou veřejnou IP adresu pomocí rozhraní příkazového řádku Azure (CLI).
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd44971162a79e53b731c5c89316f14e8bb0a1a6
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresu pomocí rozhraní příkazového řádku Azure

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (Classic)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tento článek se zabývá pomocí modelu nasazení Resource Manager, které společnost Microsoft doporučuje pro většinu nových nasazení místo modelu nasazení classic.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>Vytvoření virtuálního počítače

Hodnoty v "" pro proměnné v krocích, které následují vytvořit prostředky s nastaveními z scénáře. Změňte hodnoty, jako je vhodné pro vaše prostředí.

1. Nainstalujte [Azure CLI 2.0](/cli/azure/install-az-cli2) Pokud ještě nemáte nainstalováno.
2. Vytvoření SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem pomocí kroků v [vytvoření SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Z příkazového prostředí, přihlášení pomocí příkazu `az login`.
4. Vytvoření virtuálního počítače tak, že spustíte skript, který odpovídá na počítači se systémem Linux nebo Mac. Azure veřejnou IP adresu, virtuální sítě, síťové a prostředky virtuálních počítačů musí všechny existovat ve stejném umístění. Když prostředky nebudete mít existovat ve stejné skupině prostředků, v následující skript dělají.

```bash
RgName="IaaSStory"
Location="westus"

# Create a resource group.

az group create \
--name $RgName \
--location $Location

# Create a public IP address resource with a static IP address using the --allocation-method Static option.
# If you do not specify this option, the address is allocated dynamically. The address is assigned to the
# resource from a pool of IP adresses unique to each Azure region. The DnsName must be unique within the
# Azure location it's created in. Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653#
# that lists the ranges for each region.

PipName="PIPWEB1"
DnsName="iaasstoryws1"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static \
--dns-name $DnsName

# Create a virtual network with one subnet

VnetName="TestVNet"
VnetPrefix="192.168.0.0/16"
SubnetName="FrontEnd"
SubnetPrefix="192.168.1.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $SubnetName \
--subnet-prefix $SubnetPrefix

# Create a network interface connected to the VNet with a static private IP address and associate the public IP address
# resource to the NIC.

NicName="NICWEB1"
PrivateIpAddress="192.168.1.101"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $SubnetName \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress \
--public-ip-address $PipName

# Create a new VM with the NIC

VmName="WEB1"

# Replace the value for the VmSize variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article.
VmSize="Standard_DS1"

# Replace the value for the OsImage variable with a value for *urn* from the output returned by entering
# the `az vm image list` command. 

OsImage="credativ:Debian:8:latest"
Username='adminuser'

# Replace the following value with the path to your public key file.
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
# If creating a Windows VM, remove the previous line and you'll be prompted for the password you want to configure for the VM.
```

Kromě vytvoření virtuálního počítače, vytvoří skript:
- Jeden premium spravovat disku ve výchozím nastavení, ale máte další možnosti pro typ disku, které lze vytvořit. Pro čtení [vytvoření virtuálního počítače s Linuxem pomocí Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) článku.
- Virtuální sítě, podsítě, seskupování a veřejnou IP adresu prostředky. Alternativně můžete použít *existující* virtuální sítě, podsítě, síťový adaptér nebo adresu prostředky veřejné IP adresy. Chcete-li další informace o použití existující síťovým prostředkům, nikoli vytváření další zdroje informací, zadejte `az vm create -h`.

## <a name = "validate"></a>Ověření vytvoření virtuálních počítačů a veřejnou IP adresu

1. Zadejte příkaz `az resource list --resouce-group IaaSStory --output table` zobrazíte seznam prostředky vytvořené skriptem. Měla by existovat pět prostředky ve vrácené výstupu: Síťová rozhraní, disk, veřejnou IP adresu, virtuální sítě a virtuální počítač.
2. Zadejte příkaz `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`. Ve vrácené výstupu si poznamenejte hodnotu **IpAddress** a že hodnota **PublicIpAllocationMethod** je *statické*.
3. Před spuštěním následujícího příkazu, odeberte <>, nahraďte *uživatelské jméno* s názvem, který jste použili pro **uživatelské jméno** proměnné ve skriptu a nahraďte *ipAddress*s **ipAddress** z předchozího kroku. Spusťte následující příkaz pro připojení k virtuálnímu počítači: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 

## <a name= "clean-up"></a>Odebrat virtuální počítač a přidružených zdrojů.

Doporučujeme vám odstranit prostředky vytvořené v tomto cvičení, pokud nebude je používat v produkčním prostředí. Virtuální počítač, veřejnou IP adresu a diskové prostředky platit poplatky, tak dlouho, dokud se jejich zřízení. Odebrat prostředky vytvořené během tohoto cvičení, proveďte následující kroky:

1. Chcete-li zobrazit prostředky ve skupině prostředků, spusťte `az resource list --resource-group IaaSStory` příkaz.
2. Potvrďte, že neexistují žádné prostředky ve skupině prostředků, než prostředky vytvořené pomocí skriptu, který v tomto článku. 
3. Chcete-li odstranit všechny prostředky, které jsou vytvořené v tomto cvičení, spusťte `az group delete -n IaaSStory` příkaz. Příkaz odstraní skupinu prostředků a všechny prostředky, které obsahuje.
 
## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adresy v rámci operačního systému

Byste měli přiřadit nikdy ručně veřejnou IP adresu přiřazené pro virtuální počítač Azure v rámci operačního systému virtuálního počítače. Doporučuje se, že nepřiřadíte staticky privátní IP přiřazené k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače, pokud nezbytné, jako např. kdy [přiřazení více IP adres pro virtuální počítač s Windows](virtual-network-multiple-ip-addresses-cli.md). Pokud ručně nastavit privátní IP adresu v operačním systému, zajistěte, aby byl stejnou adresu jako přiřazené Azure privátní IP adresy [síťové rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings), nebo můžete ztratit připojení k virtuálnímu počítači. Další informace o [privátní IP adresu](virtual-network-network-interface-addresses.md#private) nastavení.

## <a name="next-steps"></a>Další postup

Síťové přenosy můžete procházet do a z virtuálního počítače vytvořit v tomto článku. Můžete definovat pravidla pro příchozí a odchozí zabezpečení v rámci skupiny zabezpečení sítě, které omezit přenos, který může obtékat do a z rozhraní sítě, podsítě nebo obojí. Další informace o skupinách zabezpečení sítě najdete v tématu [přehled skupiny zabezpečení sítě](security-overview.md).