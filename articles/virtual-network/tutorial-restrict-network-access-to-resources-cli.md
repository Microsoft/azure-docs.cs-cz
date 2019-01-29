---
title: Omezení síťového přístupu k prostředkům PaaS – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak k omezení a omezení síťového přístupu k prostředkům Azure, jako jsou služby Azure Storage a Azure SQL Database s koncovými body služby virtuální sítě pomocí Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 80ca9df064239e9c7beb9d45acfabe963c532e4a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150544"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Omezení síťového přístupu k prostředkům PaaS s koncovými body služby virtuální sítě pomocí Azure CLI

Koncové body služby virtuální sítě umožňují omezení síťového přístupu k prostředkům některých služeb Azure na podsíť virtuální sítě. Můžete také odebrat internetový přístup k prostředkům. Koncové body služeb poskytují přímé připojení z vaší virtuální sítě k podporovaným službám Azure a umožňují pro přístup ke službám Azure použít privátní adresní prostor virtuální sítě. Provoz směřující do prostředků Azure prostřednictvím koncových bodů služby zůstává vždy v páteřní síti Microsoft Azure. V tomto článku získáte informace o těchto tématech:

* Vytvoření virtuální sítě s jednou podsítí
* Přidání podsítě a povolení koncového bodu služby
* Vytvoření prostředku Azure a povolení síťového přístupu k tomuto prostředku pouze z podsítě
* Nasazení virtuálního počítače do každé podsítě
* Ověření přístupu k prostředku z podsítě
* Ověření odepření přístupu k prostředku z podsítě a internetu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Před vytvořením virtuální sítě, je nutné vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Vytvoření virtuální sítě s jednou podsítí s [az network vnet vytvořit](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Povolení koncového bodu služby 

Můžete povolit koncové body služby pouze pro služby, které podporují koncových bodů služby. Zobrazit povolený koncový bod služby k dispozici v umístění Azure s [az síťové připojení typu vnet seznam endpoint služby](/cli/azure/network/vnet#az_network_vnet_list_endpoint_services). Následující příklad vrátí seznam hodnot povolený koncový bod služby služeb dostupných v *eastus* oblasti. Seznam služeb, vrátí se zvětší v průběhu času dalších služeb Azure jsou povolený koncový bod služby.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Vytvoření další podsítě ve virtuální síti s [az podsíti virtuální sítě vytvořit](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). V tomto příkladu koncový bod služby pro *Microsoft.Storage* pro podsíť se vytvoří: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Omezení síťového přístupu pro podsíť

Vytvořte skupinu zabezpečení sítě pomocí [az network nsg vytvořit](/cli/azure/network/nsg#az_network_nsg_create). Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Přidružení skupiny zabezpečení sítě k *privátní* podsíť s [az network vnet podsíť aktualizace](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). V následujícím příkladu *myNsgPrivate* skupiny zabezpečení sítě *privátní* podsítě:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Vytvoření pravidla zabezpečení se [az network nsg pravidlo vytvořte](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Pravidlo, které následuje umožňuje odchozí přístup k veřejné IP adresy přiřazené příslušné službě Azure Storage: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"

Each network security group contains several [default security rules](security-overview.md#default-security-rules). The rule that follows overrides a default security rule that allows outbound access to all public IP addresses. The `destination-address-prefix "Internet"` option denies outbound access to all public IP addresses. The previous rule overrides this rule, due to its higher priority, which allows access to the public IP addresses of Azure Storage.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"

The following rule allows SSH traffic inbound to the subnet from anywhere. The rule overrides a default security rule that denies all inbound traffic from the internet. SSH is allowed to the subnet so that connectivity can be tested in a later step.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>Omezení síťového přístupu k prostředku

Kroky potřebné k omezení síťového přístupu k prostředkům vytvořeným prostřednictvím služeb Azure povolených v koncových bodech se u jednotlivých služeb liší. Konkrétní kroky pro jednotlivé služby najdete v dokumentaci příslušné služby. Zbývající část tohoto článku obsahuje kroky k omezení síťového přístupu k účtu služby Azure Storage, jako příklad.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvoření účtu služby Azure storage s [vytvořit účet úložiště az](/cli/azure/storage/account#az_storage_account_create). Nahraďte `<replace-with-your-unique-storage-account-name>` s názvem, který je jedinečný ve všech umístěních Azure, mezi 3 až 24 znaků a používat pouze číslice a malá písmena.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Po vytvoření účtu úložiště načíst připojovací řetězec pro účet úložiště do proměnné s [az storage account show-connection-string](/cli/azure/storage/account#az_storage_account_show_connection_string). Připojovací řetězec se používá k vytvoření sdílené složky v pozdějším kroku.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Zobrazit obsah proměnné a poznamenejte si hodnotu pro **AccountKey** vrací výstup, protože se používá v pozdější fázi.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Vytvoření sdílené složky v účtu úložiště

Vytvoření sdílené složky v účtu úložiště pomocí [vytvořit sdílenou složku úložiště az](/cli/azure/storage/share). V pozdějším kroku je potvrďte síťový přístup k ní připojit tuto sdílenou složku souborového.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Zakázat všechny přístup k účtu úložiště

Účty úložiště ve výchozím nastavení přijímají síťová připojení z klientů v jakékoli síti. Omezení přístupu k vybrané sítě, změňte výchozí akci na *Odepřít* s [aktualizace účtu úložiště az](/cli/azure/storage/account#az_storage_account_update). Jakmile byl odepřen přístup k síti, účet úložiště není přístupná z libovolné sítě.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Povolení síťového přístupu z podsítě

Povolení síťového přístupu k účtu úložiště u *privátní* podsíť s [přidat účet úložiště az network-rule](/cli/azure/storage/account/network-rule#az_storage_account_network_rule_add).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Pokud chcete otestovat síťový přístup k účtu úložiště, nasaďte do každé podsítě virtuální počítač.

### <a name="create-the-first-virtual-machine"></a>Vytvoření prvního virtuálního počítače

Vytvářet virtuální počítače *veřejné* podsíť s [az vm vytvořit](/cli/azure/vm#az_vm_create). Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního počítače Azure CLI zobrazí podobné informace jako v následujícím příkladu: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Poznamenejte si **publicIpAddress** ve vrácené výstupu. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu v pozdějším kroku.

### <a name="create-the-second-virtual-machine"></a>Vytvoření druhého virtuálního počítače

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření, poznamenejte si **publicIpAddress** ve výstupu vráceného. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu v pozdějším kroku.

## <a name="confirm-access-to-storage-account"></a>Ověření přístupu k účtu úložiště

Připojte se přes SSH *myVmPrivate* virtuálního počítače. Nahraďte *<publicIpAddress>* s veřejnou IP adresu vašeho *myVmPrivate* virtuálního počítače.

```bash 
ssh <publicIpAddress>
```

Vytvořte složku pro bod připojení:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Připojení sdílené složky Azure do adresáře, který jste vytvořili. Před spuštěním následujícího příkazu, nahraďte `<storage-account-name>` pomocí názvu účtu a `<storage-account-key>` s klíčem, který jste získali v [vytvořit účet úložiště](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Zobrazí `user@myVmPrivate:~$` řádku. Sdílená složka Azure se úspěšně připojil k */mnt/MyAzureFileShare*.

Potvrďte, že virtuální počítač nemá možnost odchozího připojení do všech ostatních veřejných IP adres:

```bash
ping bing.com -c 4
```

Neobdržíte žádné odpovědi, protože skupina zabezpečení sítě přidružená k podsíti *Private* nepovoluje odchozí přístup k jiným veřejným IP adresám, než jsou adresy přiřazené službě Azure Storage.

Ukončete relaci SSH k *myVmPrivate* virtuálního počítače.

## <a name="confirm-access-is-denied-to-storage-account"></a>Ověření odepření přístupu k účtu úložiště

Pomocí následujícího příkazu vytvořte relaci SSH s *myVmPublic* virtuálního počítače. Nahraďte `<publicIpAddress>` s veřejnou IP adresu vašeho *myVmPublic* virtuálního počítače: 

```bash 
ssh <publicIpAddress>
```

Vytvořte adresář pro bod připojení:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Pokus o připojení sdílené složky Azure do adresáře, který jste vytvořili. Tento článek předpokládá, že jste nasadili nejnovější verze Ubuntu. Pokud používáte starší verze Ubuntu, přečtěte si téma [připojení v systému Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro další informace o připojení sdílené složky. Před spuštěním následujícího příkazu, nahraďte `<storage-account-name>` pomocí názvu účtu a `<storage-account-key>` s klíčem, který jste získali v [vytvořit účet úložiště](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Přístup byl odepřen, a zobrazí se `mount error(13): Permission denied` chybu, protože *myVmPublic* virtuální počítač je nasazený v rámci *veřejné* podsítě. Podsíť *Public* nemá povolený koncový bod služby pro Azure Storage a účet úložiště umožňuje síťový přístup pouze z podsítě *Private*, a ne z podsítě *Public*.

Ukončete relaci SSH k *myVmPublic* virtuálního počítače.

Z počítače, se pokusí o zobrazení sdílené složky v účtu úložiště s [seznamu sdílenou složku úložiště az](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Nahraďte `<account-name>` a `<account-key>` s názvem účtu úložiště a klíč z [vytvořit účet úložiště](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Přístup se odepře a zobrazí *tento požadavek není autorizovaný k provedení této operace* chybu, protože počítač není v *privátní* podsíť *MyVirtualNetwork* virtuální sítě.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, použijte [odstranění skupiny az](/cli/azure#az_group_delete) k odebrání skupiny prostředků a všech prostředků, které obsahuje.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto článku jste povolili koncový bod služby pro podsíť virtuální sítě. Dozvěděli jste se, že koncové body služeb je možné povolit pro prostředky nasazené pomocí několika služeb Azure. Vytvořili jste účet služby Azure Storage a omezili jste síťový přístup k účtu úložiště pouze na prostředky v rámci podsítě virtuální sítě. Další informace o koncových bodech služeb najdete v tématech [Přehled koncových bodů služeb](virtual-network-service-endpoints-overview.md) a [Správa podsítí](virtual-network-manage-subnet.md).

Pokud ve svém účtu máte více virtuálních sítí, možná budete chtít propojit dvě virtuální sítě, aby mezi sebou mohly komunikovat prostředky v obou virtuálních sítích. Další informace o postupu [propojení virtuálních sítí](tutorial-connect-virtual-networks-cli.md).
