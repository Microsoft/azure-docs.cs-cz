---
title: Omezit síťový přístup k prostředkům PaaS - rozhraní příkazového řádku Azure | Microsoft Docs
description: V tomto článku zjistěte, jak chcete omezit a omezit přístup k síti na prostředky Azure, například Azure Storage a Azure SQL Database, s koncovými body služby virtuální sítě pomocí rozhraní příkazového řádku Azure.
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
ms.openlocfilehash: f357861a7a44b249e06f091a8693b7f2d8dd5178
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Omezit přístup k síti na PaaS prostředky s koncovými body služby virtuální sítě pomocí rozhraní příkazového řádku Azure

Koncové body služby virtuální sítě umožňují omezit přístup k síti k některým prostředkům služby Azure do podsítě virtuální sítě. Můžete také odebrat přístup k Internetu k prostředkům. Koncové body služby poskytují přímé připojení z virtuální sítě k podporované služby Azure, budete moci použít privátní adresní prostor vaší virtuální sítě pro přístup ke službám Azure. Přenosy určené prostředky Azure prostřednictvím koncových bodů služby vždy zůstává na páteřní síti Microsoft Azure. V tomto článku se dozvíte, jak:

* Vytvořit virtuální síť s jednou podsítí
* Přidat podsíť a povolení koncového bodu služby
* Vytvořit prostředek služby Azure a povolit přístup k síti z jenom podsítě
* Nasazení virtuálního počítače (VM) pro každou podsíť
* Potvrďte přístup k prostředku z podsítě
* Potvrďte, že byl odepřen přístup k prostředku z podsítě a z Internetu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, vyžaduje tento rychlý start, že používáte Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Před vytvořením virtuální sítě, budete muset vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky, které jsou vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Vytvořit virtuální síť s jednou podsítí s [vytvoření sítě vnet az](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Povolení koncového bodu služby 

Můžete povolit koncové body služby pouze pro služby, které podporují koncové body služby. Zobrazit služby koncový bod s podporou služeb dostupných v Azure umístění s [az sítě vnet seznamu-koncový bod services](/cli/azure/network/vnet#az_network_vnet_list_endpoint_services). Následující příklad vrátí seznam hodnot povolena služba-koncový bod služby k dispozici v *eastus* oblast. Seznam služeb, vrátí se zvýší v čase, více Azure services jsou povolené koncový bod služby.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Vytvořte další podsítě ve virtuální síti s [az sítě vnet podsíť vytváření](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). V tomto příkladu koncový bod služby pro *Microsoft.Storage* pro podsíť se vytvoří: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Omezit přístup k síti pro podsíť

Vytvořit skupinu zabezpečení sítě s [vytvořit az sítě nsg](/cli/azure/network/nsg#az_network_nsg_create). Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Přidružení skupiny zabezpečení sítě k *privátní* podsíť s [aktualizace az sítě vnet podsíť](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). V následujícím příkladu *myNsgPrivate* skupinu zabezpečení sítě *privátní* podsítě:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Vytvoření pravidla zabezpečení s [vytvořit pravidla nsg sítě az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Pravidla, která následuje umožňuje odchozí přístup na veřejné IP adresy přiřazené ke službě Azure Storage: 

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

## <a name="restrict-network-access-to-a-resource"></a>Omezit přístup k síti na prostředek

Kroky potřebné k omezit přístup k síti na prostředky, které jsou vytvořené pomocí služby Azure, které jsou povolené pro koncové body služby se liší v rámci služby. Najdete v dokumentaci pro jednotlivé služby pro konkrétní kroky pro každou službu. Zbývající část tohoto článku obsahuje kroky k omezit přístup k síti pro účet úložiště Azure jako příklad.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvoření účtu úložiště Azure s [vytvořit účet úložiště az](/cli/azure/storage/account#az_storage_account_create). Nahraďte `<replace-with-your-unique-storage-account-name>` s názvem, který je jedinečné ve všech umístěních Azure, 3 až 24 znaků, pomocí jenom čísla a malá písmena.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Po vytvoření účtu úložiště načíst připojovací řetězec pro účet úložiště do proměnné s [az úložiště účet zobrazit. připojovací řetězec](/cli/azure/storage/account#az_storage_account_show_connection_string). Připojovací řetězec se používá k vytvoření sdílené složky v pozdější fázi.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Zobrazit obsah proměnné a poznamenejte si hodnotu pro **AccountKey** vrátila ve výstupu, protože je používán v pozdější fázi.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Vytvoření sdílené složky v účtu úložiště

Vytvoření sdílené složky v účtu úložiště s [vytvořit sdílenou složku úložiště az](/cli/azure/storage/share#az_storage_share_create). V pozdější fázi tuto sdílenou složku připojené k potvrzení, přístup k síti na ni.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Odepřít všechny přístup k síti na účet úložiště

Ve výchozím nastavení účty úložiště přijmout síťová připojení z klientů ve žádné síti. Omezit přístup k vybrané sítě, změňte výchozí akci na *Odepřít* s [aktualizace účtu úložiště az](/cli/azure/storage/account#az_storage_account_update). Jakmile je odepřen přístup k síti, účet úložiště není přístupné z jakékoli sítě.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Povolit přístup k síti z podsítě

Povolit přístup k síti na účet úložiště z *privátní* podsíť s [přidat účet úložiště az sítě pravidlo](/cli/azure/storage/account/network-rule#az_storage_account_network_rule_add).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Otestovat přístup k síti na účet úložiště, nasaďte virtuální počítač do každé podsítě.

### <a name="create-the-first-virtual-machine"></a>Vytvořte první virtuální počítač

Vytvoření virtuálního počítače v *veřejné* podsíť s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). Pokud se klíče SSH již neexistují v umístění klíče výchozí, vytvoří příkaz je. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

Virtuální počítač trvá několik minut pro vytvoření. Po vytvoření virtuálního počítače Azure CLI uvádí informace podobně jako v následujícím příkladu: 

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

Poznamenejte si **publicIpAddress** ve vrácené výstupu. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu do pozdějšího kroku.

### <a name="create-the-second-virtual-machine"></a>Vytvořit druhý virtuální počítač

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

Virtuální počítač trvá několik minut pro vytvoření. Po vytvoření, poznamenejte si **publicIpAddress** v výstup. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu do pozdějšího kroku.

## <a name="confirm-access-to-storage-account"></a>Potvrďte volbu přístup k účtu úložiště

SSH do *myVmPrivate* virtuálních počítačů. Nahraďte *<publicIpAddress>* s veřejnou IP adresu vašeho *myVmPrivate* virtuálních počítačů.

```bash 
ssh <publicIpAddress>
```

Vytvořte složku pro bod připojení:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Připojení Azure sdílené složky do adresáře, kterou jste vytvořili. Před spuštěním následujícího příkazu, nahraďte `<storage-account-name>` s názvem účtu a `<storage-account-key>` s klíčem, který jste získali v [vytvořit účet úložiště](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Zobrazí `user@myVmPrivate:~$` řádku. Sdílenou složkou Azure bylo úspěšně připojeno k */mnt/MyAzureFileShare*.

Potvrďte, že virtuální počítač bez odchozí připojení k jakékoli jiné veřejné IP adresy:

```bash
ping bing.com -c 4
```

Obdržíte bez odpovědi, protože skupina zabezpečení sítě přidružené k *privátní* podsíť neumožňuje odchozí přístup k veřejné IP adresy než adresy přiřazené ke službě Azure Storage.

Ukončení relace SSH do *myVmPrivate* virtuálních počítačů.

## <a name="confirm-access-is-denied-to-storage-account"></a>Potvrďte, že byl odepřen přístup k účtu úložiště

Použijte následující příkaz pro vytvoření relace SSH s *myVmPublic* virtuálních počítačů. Nahraďte `<publicIpAddress>` s veřejnou IP adresu vašeho *myVmPublic* virtuálních počítačů: 

```bash 
ssh <publicIpAddress>
```

Vytvořte adresář pro bod připojení:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Pokus o připojení Azure sdílené složky do adresáře, kterou jste vytvořili. Tento článek předpokládá, že jste nasadili nejnovější verze Ubuntu. Pokud používáte starší verze Ubuntu, přečtěte si téma [připojení v systému Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) další pokyny o připojení sdílené složky. Před spuštěním následujícího příkazu, nahraďte `<storage-account-name>` s názvem účtu a `<storage-account-key>` s klíčem, který jste získali v [vytvořit účet úložiště](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Byl odepřen přístup, a zobrazí se `mount error(13): Permission denied` chyby, protože *myVmPublic* virtuální počítač nasazen v rámci *veřejné* podsítě. *Veřejné* podsíť nemá povoleno pro Azure Storage koncového bodu služby a účet úložiště pouze umožňuje přístup k síti z *privátní* podsíť, není *veřejné*podsítě.

Ukončení relace SSH do *myVmPublic* virtuálních počítačů.

Z počítače, se pokusí o zobrazení sdílené složky ve vašem účtu úložiště s [seznam sdílenou složku úložiště az](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Nahraďte `<account-name>` a `<account-key>` s názvem účtu úložiště a klíč z [vytvořit účet úložiště](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Byl odepřen přístup a zobrazí se *tento požadavek není autorizovaný k provedení této operace* chyby, protože počítač není v *privátní* podsíť *MyVirtualNetwork* virtuální sítě.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, použijte [odstranění skupiny az](/cli/azure#az_group_delete) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto článku jste povolili koncového bodu služby pro podsíť virtuální sítě. Jste zjistili, že koncových bodů služby se dá nastavit pro prostředky nasazené s více službami Azure. Vytvořili jste účet úložiště Azure a omezený přístup k síti k účtu úložiště na pouze prostředky v podsíti virtuální sítě. Další informace o koncových bodů služby najdete v tématu [přehled koncové body služby](virtual-network-service-endpoints-overview.md) a [spravovat podsítě](virtual-network-manage-subnet.md).

Pokud máte více virtuálních sítí ve vašem účtu, můžete k propojení dvou virtuálních sítí tak prostředky v rámci jedné virtuální sítě může komunikovat navzájem. Další informace, jak zjistit, [připojení virtuální sítě](tutorial-connect-virtual-networks-cli.md).