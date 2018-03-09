---
title: "Vytvoření virtuální sítě - rozhraní příkazového řádku Azure | Microsoft Docs"
description: "Rychle se Naučte se vytvořit virtuální síť pomocí rozhraní příkazového řádku Azure. Virtuální síť umožňuje mnoho typů prostředků Azure, aby soukromě vzájemně komunikovat."
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
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ff27d557f221be61a7384f6aaf6e57cef5cebb81
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Vytvoření virtuální sítě pomocí rozhraní příkazového řádku Azure

V tomto článku zjistěte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě, nasadíte dva virtuální počítače do virtuální sítě k otestování privátní síti komunikace mezi nimi.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, v tomto článku vyžaduje, že používáte Azure CLI verze verze 2.0.4 nebo novější. Chcete-li najít nainstalovanou verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*. Všechny prostředky Azure jsou vytvořeny v rámci Azure umístění (nebo oblasti).

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvoření virtuální sítě s [vytvoření sítě vnet az](/cli/azure/network/vnet#az_network_vnet_create) příkaz. Následující příklad vytvoří výchozí virtuální síť s názvem *myVirtualNetwork* s jednou podsítí s názvem *výchozí*. Protože umístění není zadána, Azure vytvoří virtuální sítě ve stejném umístění jako pro skupinu prostředků.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

Po vytvoření virtuální sítě je následující část vrácené informace.

```azurecli
"newVNet": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/16"
```

Všechny virtuální sítě mít jeden nebo více předpony adres, které jsou jim přiřazeny. Vzhledem k tomu, že předponu adresy nebyla zadána při vytváření virtuální sítě, Azure definované 10.0.0.0/16 adresního prostoru, ve výchozím nastavení. Adresní prostor je zadat v notaci CIDR. 10.0.0.0/16 prostoru adres zahrnuje 10.0.0.0-10.0.255.254.

Vrácená další část informací **addressPrefix** z *10.0.0.0/24* pro *výchozí* zadaná v příkazu podsíť. Virtuální síť obsahuje nula nebo více podsítí. Příkaz vytvořit jednu podsíť s názvem *výchozí*, ale nebyla určena žádná předpona adresy podsítě. Předponu adresy není zadán pro virtuální síť nebo podsíť, Azure definuje 10.0.0.0/24 jako předpona adresy pro první podsíť, ve výchozím nastavení. V důsledku toho podsíť zahrnuje 10.0.0.0-10.0.0.254, ale pouze 10.0.0.4-10.0.0.254 jsou k dispozici, protože první čtyři adresy (0-3) a poslední adresa v jednotlivých podsítích si vyhrazuje Azure.

## <a name="test-network-communication"></a>Test síťové komunikace

Virtuální síť umožňuje několik typů prostředků Azure, aby soukromě vzájemně komunikovat. Virtuální počítač je jeden typ prostředku, který můžete nasadit do virtuální sítě. Vytvořte dva virtuální počítače ve virtuální síti, abyste mohli ověřit privátní komunikaci mezi nimi později.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVm1*. Pokud se klíče SSH již neexistují v umístění klíče výchozí, vytvoří příkaz je. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. `--no-wait` Možnost vytvoří virtuální počítač na pozadí, abyste mohli pokračovat k dalšímu kroku.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

Azure automaticky vytvoří virtuální počítač *výchozí* podsíť *myVirtualNetwork* virtuální sítě, protože virtuální sítě existuje ve skupině prostředků a žádné virtuální sítě nebo podsíť je zadána v příkazu. Azure DHCP automaticky přiřadit 10.0.0.4 k virtuálnímu počítači během vytváření, protože se jedná o první dostupnou adresu v *výchozí* podsítě. Umístění, které virtuální počítač je vytvořen v musí být stejné umístění, které virtuální sítě v existuje. Virtuální počítač nemusí být ve stejné skupině prostředků jako virtuální síť, i když je v tomto článku.

Vytvořte druhý virtuální počítač. Ve výchozím nastavení, Azure vytvoří také tento virtuální počítač v *výchozí* podsítě.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Virtuální počítač trvá několik minut pro vytvoření. Po vytvoření virtuálního počítače Azure CLI vrátí výstup podobně jako v následujícím příkladu: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

V příkladu můžete vidět, že **privateIpAddress** je *10.0.0.5*. Azure DHCP automaticky přiřadit *10.0.0.5* k virtuálnímu počítači kvůli další dostupnou adresu v *výchozí* podsítě. Poznamenejte si **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu do pozdějšího kroku. Veřejná IP adresa není přiřazen z v rámci virtuální sítě nebo předpony adres podsítě. Veřejné IP adresy přiřazené z [fondu adres přiřazených každé oblasti Azure](https://www.microsoft.com/download/details.aspx?id=41653). Sice Azure nezná, která veřejná IP adresa je přiřazena k virtuálnímu počítači, ale operační systém spuštěný ve virtuálním počítači nemá žádné informace o tom všechny veřejné IP adresy přiřazené.

### <a name="connect-to-a-virtual-machine"></a>Připojit k virtuálnímu počítači

Použijte následující příkaz pro vytvoření relace SSH s *Můjvp2* virtuálního počítače. Nahraďte `<publicIpAddress>` s veřejnou IP adresu virtuálního počítače. V příkladu nahoře, IP adresa je *40.68.254.142*.

```bash 
ssh <publicIpAddress>
```

### <a name="validate-communication"></a>Ověření komunikace

Pomocí následujícího příkazu potvrďte komunikace s *myVm1* z *Můjvp2*:

```bash
ping myVm1 -c 4
```

Obdržíte čtyři odpovědi od *10.0.0.4*. Může komunikovat s *myVm1* z *Můjvp2*, protože oba virtuální počítače mají privátní IP adresy přiřazené z *výchozí* podsítě. Budete moci příkazem ping otestovat podle názvu hostitele, protože Azure automaticky poskytuje překlad názvů DNS pro všechny hostitele v rámci virtuální sítě.

Potvrďte odchozí komunikaci k Internetu, použijte následující příkaz:

```bash
ping bing.com -c 4
```

Obdržíte čtyři odpovědi od vyhledávače bing.com. Ve výchozím nastavení může komunikovat jakéhokoli virtuálního počítače ve virtuální síti odchozí k Internetu.

Ukončení relace SSH k virtuálnímu počítači.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, můžete použít [odstranění skupiny az](/cli/azure/group#az_group_delete) příkaz k odebrání skupiny prostředků a všechny prostředky, které obsahuje:

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto článku jste nasadili výchozí virtuální síť s jednou podsítí. Naučte se vytvářet vlastní virtuální síť s více podsítěmi, nadále kurz pro vytvoření vlastní virtuální sítě.

> [!div class="nextstepaction"]
> [Vytvoření vlastní virtuální sítě](virtual-networks-create-vnet-arm-cli.md)
