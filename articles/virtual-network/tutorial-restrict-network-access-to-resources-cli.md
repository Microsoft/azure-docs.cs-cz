---
title: Omezení síťového přístupu k prostředkům PaaS – Azure CLI
description: V tomto článku se dozvíte, jak omezit a omezit síťový přístup k prostředkům Azure, jako jsou Azure Storage a Azure SQL Database, pomocí koncových bodů služby virtuální sítě pomocí rozhraní příkazového řádku Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: b4f9dcf6fd43749cdf71098b3cd0da8a42020f6d
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221761"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Omezení síťového přístupu k prostředkům PaaS pomocí koncových bodů služby virtuální sítě pomocí Azure CLI

Koncové body služby pro virtuální síť umožňují omezení síťového přístupu k prostředkům některých služeb Azure na podsíť virtuální sítě. Můžete také odebrat internetový přístup k prostředkům. Koncové body služeb poskytují přímé připojení z vaší virtuální sítě k podporovaným službám Azure a umožňují pro přístup ke službám Azure použít privátní adresní prostor virtuální sítě. Provoz směřující do prostředků Azure prostřednictvím koncových bodů služby zůstává vždy v páteřní síti Microsoft Azure. V tomto článku získáte informace o těchto tématech:

* Vytvoření virtuální sítě s jednou podsítí
* Přidání podsítě a povolení koncového bodu služby
* Vytvoření prostředku Azure a povolení síťového přístupu k tomuto prostředku pouze z podsítě
* Nasazení virtuálního počítače do každé podsítě
* Ověření přístupu k prostředku z podsítě
* Ověření odepření přístupu k prostředku z podsítě a internetu

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Před vytvořením virtuální sítě je nutné vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Vytvořte virtuální síť s jednou podsítí pomocí [AZ Network VNet Create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Povolení koncového bodu služby 

Koncové body služby můžete povolit pouze pro služby, které podporují koncové body služby. Zobrazení služeb s povoleným koncovým bodem služby dostupných v umístění Azure pomocí [AZ Network VNet list-Endpoint-Services](/cli/azure/network/vnet). Následující příklad vrátí seznam služeb s povoleným koncovým bodem služby dostupných v oblasti *eastus* . Seznam vrácených služeb bude v průběhu času růst, protože se další služby Azure stávají koncovým bodem služby.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Vytvořte další podsíť ve virtuální síti pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet). V tomto příkladu se pro podsíť vytvoří koncový bod služby pro *Microsoft. Storage* : 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Omezení síťového přístupu pro podsíť

Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](/cli/azure/network/nsg). Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Přidružte skupinu zabezpečení sítě k *privátní* podsíti pomocí [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet). Následující příklad přidruží skupinu zabezpečení sítě *myNsgPrivate* k *privátní* podsíti:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Vytvořte pravidla zabezpečení pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule). Následující pravidlo umožňuje odchozí přístup k veřejným IP adresám přiřazeným službě Azure Storage: 

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
```

Každá skupina zabezpečení sítě obsahuje několik [výchozích pravidel zabezpečení](./network-security-groups-overview.md#default-security-rules). Následující pravidlo přepisuje výchozí pravidlo zabezpečení, které umožňuje odchozí přístup ke všem veřejným IP adresám. Tato `destination-address-prefix "Internet"` možnost zakazuje odchozí přístup ke všem veřejným IP adresám. Předchozí pravidlo přepisuje toto pravidlo z důvodu vyšší priority, což umožňuje přístup k veřejným IP adresám Azure Storage.

```azurecli-interactive
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
```

Následující pravidlo umožňuje příchozí komunikaci protokolu SSH do podsítě odkudkoli. Toto pravidlo přepíše výchozí pravidlo zabezpečení, které zakazuje veškerý příchozí provoz z internetu. K podsíti je povolený SSH, aby bylo možné otestovat připojení v pozdějším kroku.

```azurecli-interactive
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

Kroky potřebné k omezení síťového přístupu k prostředkům vytvořeným prostřednictvím služeb Azure povolených v koncových bodech se u jednotlivých služeb liší. Konkrétní kroky pro jednotlivé služby najdete v dokumentaci příslušné služby. Zbývající část tohoto článku obsahuje kroky pro omezení síťového přístupu pro účet Azure Storage jako příklad.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvořte účet úložiště Azure pomocí [AZ Storage Account Create](/cli/azure/storage/account). Nahraďte `<replace-with-your-unique-storage-account-name>` názvem, který je jedinečný v rámci všech umístění Azure, mezi 3-24 znaků a číslicí a malými písmeny.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Po vytvoření účtu úložiště načtěte připojovací řetězec pro účet úložiště do proměnné pomocí [AZ Storage Account show-Connection-String](/cli/azure/storage/account). Připojovací řetězec se používá k vytvoření sdílené složky v pozdějším kroku.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Zobrazte obsah proměnné a poznamenejte si hodnotu **AccountKey** vrácenou ve výstupu, protože se používá v pozdějším kroku.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Vytvoření sdílené složky v účtu úložiště

Vytvořte sdílenou složku v účtu úložiště pomocí [AZ Storage Share Create](/cli/azure/storage/share). V pozdějším kroku je tato sdílená složka připojená k ověření přístupu k síti.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Odepřít všem síťovým přístupům účet úložiště

Účty úložiště ve výchozím nastavení přijímají síťová připojení z klientů v jakékoli síti. Chcete-li omezit přístup k vybraným sítím, změňte výchozí akci na *Odepřít* pomocí [AZ Storage Account Update](/cli/azure/storage/account). Jakmile je přístup k síti zakázán, účet úložiště není přístupný ze žádné sítě.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Povolení síťového přístupu z podsítě

Povolte síťový přístup k účtu úložiště z *privátní* podsítě pomocí [AZ Storage Account Network-Rule Add](/cli/azure/storage/account/network-rule).

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

Vytvořte virtuální počítač ve *veřejné* podsíti pomocí [AZ VM Create](/cli/azure/vm). Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního počítače se v Azure CLI zobrazí podobné informace jako v následujícím příkladu: 

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

Poznamenejte si **publicIpAddress** ve vráceném výstupu. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu v pozdějším kroku.

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

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření si poznamenejte **publicIpAddress** ve vráceném výstupu. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu v pozdějším kroku.

## <a name="confirm-access-to-storage-account"></a>Ověření přístupu k účtu úložiště

Připojte se přes SSH k virtuálnímu počítači *myVmPrivate* . Nahraďte *\<publicIpAddress>* veřejnou IP adresou vašeho virtuálního počítače s *myVmPrivate* .

```bash 
ssh <publicIpAddress>
```

Vytvořte složku pro přípojný bod:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Připojte sdílenou složku Azure k adresáři, který jste vytvořili. Před spuštěním následujícího příkazu nahraďte `<storage-account-name>` názvem účtu a `<storage-account-key>` klíčem, který jste získali v části [Vytvoření účtu úložiště](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Zobrazí se `user@myVmPrivate:~$` výzva. Sdílená složka Azure se úspěšně připojila k */mnt/MyAzureFileShare*.

Potvrďte, že virtuální počítač nemá žádné odchozí připojení k žádným jiným veřejným IP adresám:

```bash
ping bing.com -c 4
```

Neobdržíte žádné odpovědi, protože skupina zabezpečení sítě přidružená k podsíti *Private* nepovoluje odchozí přístup k jiným veřejným IP adresám, než jsou adresy přiřazené službě Azure Storage.

Ukončete relaci SSH na virtuálním počítači s *myVmPrivate* .

## <a name="confirm-access-is-denied-to-storage-account"></a>Ověření odepření přístupu k účtu úložiště

Pomocí následujícího příkazu vytvořte relaci SSH s virtuálním počítačem s *myVmPublic* . Nahraďte `<publicIpAddress>` veřejnou IP adresou vašeho virtuálního počítače s *myVmPublic* : 

```bash 
ssh <publicIpAddress>
```

Vytvořte adresář pro přípojný bod:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Pokuste se připojit sdílenou složku Azure k adresáři, který jste vytvořili. V tomto článku se předpokládá, že jste nasadili nejnovější verzi Ubuntu. Pokud používáte starší verze Ubuntu, přečtěte si další informace o připojení sdílených složek v tématu [připojení na Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Před spuštěním následujícího příkazu nahraďte `<storage-account-name>` názvem účtu a `<storage-account-key>` klíčem, který jste získali v části [Vytvoření účtu úložiště](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Přístup je odepřený a zobrazí se `mount error(13): Permission denied` Chyba, protože virtuální počítač *myVmPublic* je nasazený ve *veřejné* podsíti. Podsíť *Public* nemá povolený koncový bod služby pro Azure Storage a účet úložiště umožňuje síťový přístup pouze z podsítě *Private*, a ne z podsítě *Public*.

Ukončete relaci SSH na virtuálním počítači s *myVmPublic* .

Z počítače se pokuste zobrazit sdílené složky ve vašem účtu úložiště pomocí [AZ Storage Share list](/cli/azure/storage/share?view=azure-cli-latest). Nahraďte `<account-name>` a `<account-key>` názvem účtu úložiště a klíčem z části [Vytvoření účtu úložiště](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Přístup byl odepřen a obdržíte-li *tuto žádost nemáte autorizaci k provedení této operace* , protože počítač není v *privátní* podsíti virtuální sítě *MyVirtualNetwork* .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků a všechny prostředky, které obsahuje, pomocí [AZ Group Delete](/cli/azure) .

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste povolili koncový bod služby pro podsíť virtuální sítě. Dozvěděli jste se, že koncové body služeb je možné povolit pro prostředky nasazené pomocí několika služeb Azure. Vytvořili jste účet služby Azure Storage a omezili jste síťový přístup k účtu úložiště pouze na prostředky v rámci podsítě virtuální sítě. Další informace o koncových bodech služeb najdete v tématech [Přehled koncových bodů služeb](virtual-network-service-endpoints-overview.md) a [Správa podsítí](virtual-network-manage-subnet.md).

Pokud ve svém účtu máte více virtuálních sítí, možná budete chtít propojit dvě virtuální sítě, aby mezi sebou mohly komunikovat prostředky v obou virtuálních sítích. Informace o postupu najdete v tématu [připojení virtuálních sítí](tutorial-connect-virtual-networks-cli.md).