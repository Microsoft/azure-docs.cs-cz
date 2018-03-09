---
title: "Připojit virtuální sítě pomocí virtuální sítě partnerský vztah - rozhraní příkazového řádku Azure | Microsoft Docs"
description: "Zjistěte, jak připojit virtuální sítě pomocí virtuální sítě partnerský vztah."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: df56f2e3e13f80e7ce2c2b6c9cffeac3d03776e5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Připojit virtuální sítě pomocí virtuální sítě pomocí rozhraní příkazového řádku Azure vytvoření partnerského vztahu.

Virtuální sítě můžete připojit k sobě navzájem s partnerský vztah virtuální sítě. Jakmile se kterými mají partnerský virtuální sítě, prostředky v obě virtuální sítě jsou komunikovat s mezi sebou, se stejnou latenci a šířky pásma, jako kdyby byly prostředky ve stejné virtuální síti. Tento článek se týká vytvoření vztahu a partnerského vztahu dvě virtuální sítě. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte dvě virtuální sítě
> * Vytvoření partnerského vztahu mezi virtuálními sítěmi
> * Testování partnerského vztahu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Vytvoření virtuálních sítí

Před vytvořením virtuální sítě, budete muset vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky, které jsou vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork1* s předponou adresy *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Vytvořit virtuální síť s názvem *myVirtualNetwork2* s předponou adresy *10.1.0.0/16*. Předpona adresy se nepřekrývá s předponou adresy z *myVirtualNetwork1* virtuální sítě. Nejde partnerský uzel virtuálních sítí s překrývajícími se předpony adres.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Sdílené virtuální sítě

Partnerských vztahů se vytvoří mezi ID virtuální sítě, proto musíte nejprve získat ID každé virtuální síť s [az sítě vnet show](/cli/azure/network/vnet#az_network_vnet_show) a uložte ID v proměnné.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Vytvoření partnerského vztahu z *myVirtualNetwork1* k *myVirtualNetwork2* s [vnet az sítě partnerský vztah vytvořit](/cli/azure/network/vnet/peering#az_network_vnet_peering_create). Pokud `--allow-vnet-access` není zadán parametr, se naváže partnerský vztah, ale žádná komunikace můžete procházet skrz ho.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

Ve výstupu vráceného po provedení předchozí příkaz, který uvidíte **peeringState** je *získaných*. Partnerského vztahu zůstane v aplikaci *získaných* stavu, dokud nevytvoříte partnerského vztahu z *myVirtualNetwork2* k *myVirtualNetwork1*. Vytvoření partnerského vztahu z *myVirtualNetwork2* k *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

Ve výstupu vráceného po provedení předchozí příkaz, který uvidíte **peeringState** je *připojeno*. Azure také změnit stav partnerského vztahu *myVirtualNetwork1 myVirtualNetwork2* partnerského vztahu k *připojeno*. Potvrďte, že stav partnerského vztahu *myVirtualNetwork1 myVirtualNetwork2* partnerský vztah změnit tak, aby *připojeno* s [az sítě vnet partnerského vztahu zobrazit](/cli/azure/network/vnet/peering#az_network_vnet_peering_show).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Prostředky v jedné virtuální sítě nemůže komunikovat s prostředky v jiné virtuální síti, dokud **peeringState** pro partnerské vztahy v obě virtuální sítě je *připojeno*. 

Partnerské vztahy jsou mezi dvěma virtuálními sítěmi, ale nejsou přechodné. Ano, například pokud jste chtěli také peer *myVirtualNetwork2* k *myVirtualNetwork3*, musíte vytvořit další vztahy mezi virtuálními sítěmi *myVirtualNetwork2* a *myVirtualNetwork3*. I když *myVirtualNetwork1* je peered s *myVirtualNetwork2*, prostředků v rámci *myVirtualNetwork1* může jenom přístup k prostředkům v  *myVirtualNetwork3* Pokud *myVirtualNetwork1* byl také peered s *myVirtualNetwork3*. 

Před partnerský vztah produkční virtuální sítě, je doporučeno, důkladně Seznamte se s [partnerského vztahu přehled](virtual-network-peering-overview.md), [Správa partnerský vztah](virtual-network-manage-peering.md), a [limity virtuální sítě ](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). I když tento článek ukazuje, partnerský vztah mezi dvěma virtuálními sítěmi ve stejném předplatném a umístění, můžete také partnerský uzel virtuálních sítí v [různých oblastech](#register) a [různých předplatných Azure](create-peering-different-subscriptions.md#cli). Můžete také vytvořit [hvězdicové sítě návrhů](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s partnerský vztah.

## <a name="test-peering"></a>Testování partnerského vztahu

Chcete-li otestovat síťové komunikace mezi virtuálními počítači v různých virtuálních sítích prostřednictvím partnerský vztah, nasazení virtuálního počítače pro každou podsíť a pak komunikaci mezi virtuálními počítači. 

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvoření virtuálního počítače s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVm1* v *myVirtualNetwork1* virtuální sítě. Pokud se klíče SSH již neexistují v umístění klíče výchozí, vytvoří příkaz je. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. `--no-wait` Možnost vytvoří virtuální počítač na pozadí, abyste mohli pokračovat k dalšímu kroku.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

Azure automaticky přiřadí 10.0.0.4 jako privátní IP adresu virtuálního počítače, protože se první dostupná IP adresa v 10.0.0.4 *Subnet1* z *myVirtualNetwork1*. 

Vytvoření virtuálního počítače v *myVirtualNetwork2* virtuální sítě.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

Virtuální počítač trvá několik minut pro vytvoření. Po vytvoření virtuálního počítače Azure CLI uvádí informace podobně jako v následujícím příkladu: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Ve výstupu v příkladu můžete vidět, že **privateIpAddress** je *10.1.0.4*. 10.1.0.4 Azure DHCP automaticky přiřazen k virtuálnímu počítači, protože je první dostupnou adresu v *Subnet1* z *myVirtualNetwork2*. Poznamenejte si **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu do pozdějšího kroku.

### <a name="test-virtual-machine-communication"></a>Testovací virtuální počítač komunikace

Použijte následující příkaz pro vytvoření relace SSH s *Můjvp2* virtuálního počítače. Nahraďte `<publicIpAddress>` s veřejnou IP adresu virtuálního počítače. V předchozím příkladu veřejná IP adresa je *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

Příkaz ping virtuálního počítače v *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Obdržíte čtyři odpovědi. Pokud jste příkaz ping podle názvu virtuálního počítače (*myVm1*), místo jeho IP adresu, ping nezdaří, protože *myVm1* název Neznámý hostitel. Rozlišení názvů výchozí Azure funguje mezi virtuálními počítači ve stejné virtuální síti, ale není mezi virtuálními počítači v různých virtuálních sítích. Překládat názvy virtuálních sítí, je nutné [nasazení serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) nebo použijte [privátní domén Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ukončení relace SSH k *Můjvp2* virtuálního počítače. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, použijte [odstranění skupiny az](/cli/azure/group#az_group_delete) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

**<a name="register"></a>Registrace pro partnerského vztahu preview globální virtuální sítě**

Vytváření partnerských vztahů virtuálních sítí ve stejné oblasti je všeobecně dostupné. Partnerský vztah virtuální sítě v různých oblastech je aktuálně ve verzi preview. V tématu [aktualizace virtuální sítě](https://azure.microsoft.com/updates/?product=virtual-network) pro dostupné oblasti. Rovnocenných počítačů virtuálních sítí v oblastech, nejprve je nutné zaregistrovat verzi Preview, pomocí následujících kroků (v rámci předplatného, které každý virtuální síť, kterou chcete peer se):

1. Registrovat verzi Preview zadáním následujících příkazů:

  ```azurecli-interactive
  az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
  az provider register --name Microsoft.Network
  ```

2. Potvrďte, že jste zaregistrováni ve verzi preview tak, že zadáte následující příkaz:

  ```azurecli-interactive
  az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
  ```

  Pokud se pokusíte rovnocenných počítačů v různých oblastech před virtuální sítě **RegistrationState** výstupu se zobrazí po zadání příkazu předchozí je **registrovaná** oba odběry partnerský vztah selže .

## <a name="next-steps"></a>Další postup

V tomto článku jste se naučili připojení dvě sítě pomocí virtuální sítě partnerský vztah. Můžete [svého počítače připojit k virtuální síti](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) prostřednictvím sítě VPN a komunikovat s prostředky ve virtuální síti, nebo peered virtuální sítě.

Nadále ukázky skriptu pro opakovaně použitelné skripty k dokončení mnoho úloh, které jsou popsané v článcích virtuální sítě.

> [!div class="nextstepaction"]
> [Ukázky skriptu virtuální sítě](../networking/cli-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
