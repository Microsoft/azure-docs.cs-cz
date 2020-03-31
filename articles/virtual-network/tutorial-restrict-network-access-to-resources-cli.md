---
title: Omezení přístupu k síti k prostředkům PaaS – azure CLI
description: V tomto článku se dozvíte, jak omezit a omezit přístup k síti na prostředky Azure, jako je Azure Storage a Azure SQL Database, s koncovými body služby virtuální sítě pomocí azure cli.
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
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: f2dcc714bc9052dd51f114e24f0b9bd74b87480c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186409"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Omezení přístupu k síti k prostředkům PaaS pomocí koncových bodů služby virtuální sítě pomocí příkazového příkazového příkazu Azure

Koncové body služby pro virtuální síť umožňují omezení síťového přístupu k prostředkům některých služeb Azure na podsíť virtuální sítě. Můžete také odebrat internetový přístup k prostředkům. Koncové body služeb poskytují přímé připojení z vaší virtuální sítě k podporovaným službám Azure a umožňují pro přístup ke službám Azure použít privátní adresní prostor virtuální sítě. Provoz směřující do prostředků Azure prostřednictvím koncových bodů služby zůstává vždy v páteřní síti Microsoft Azure. V tomto článku získáte informace o těchto tématech:

* Vytvoření virtuální sítě s jednou podsítí
* Přidání podsítě a povolení koncového bodu služby
* Vytvoření prostředku Azure a povolení síťového přístupu k tomuto prostředku pouze z podsítě
* Nasazení virtuálního počítače do každé podsítě
* Ověření přístupu k prostředku z podsítě
* Ověření odepření přístupu k prostředku z podsítě a internetu

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Před vytvořením virtuální sítě je třeba vytvořit skupinu prostředků pro virtuální síť a všechny ostatní prostředky vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Vytvořte virtuální síť s jednou podsítí s [vytvořením virtuální sítě AZ](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Povolení koncového bodu služby 

Koncové body služby můžete povolit pouze pro služby, které podporují koncové body služby. Zobrazení služeb s povoleným koncovým bodem služby, které jsou dostupné v umístění Azure s [az síťovými službami pro koncový bod](/cli/azure/network/vnet). Následující příklad vrátí seznam služeb s povoleným koncovým bodem služby, které jsou k dispozici v oblasti *eastus.* Seznam vrácených služeb se časem zvýší, protože další služby Azure se stanou povoleným koncovým bodem služby.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Vytvořte další podsíť ve virtuální síti s [vytvořením podsítě sítě AZ](/cli/azure/network/vnet/subnet). V tomto příkladu je pro podsíť vytvořen koncový bod služby pro *Microsoft.Storage:* 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Omezení síťového přístupu pro podsíť

Vytvořte skupinu zabezpečení sítě pomocí vytvoření [sítě az nsg](/cli/azure/network/nsg). Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Přidružte skupinu zabezpečení sítě k *soukromé* podsíti k [aktualizaci podsítě sítě AZ](/cli/azure/network/vnet/subnet). Následující příklad přidruží skupinu zabezpečení sítě *myNsgPrivate* k *soukromé* podsíti:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Vytvořte pravidla zabezpečení pomocí [vytvoření pravidla az network nsg](/cli/azure/network/nsg/rule). Následující pravidlo umožňuje odchozí přístup k veřejným IP adresám přiřazeným ke službě Azure Storage: 

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

Každá skupina zabezpečení sítě obsahuje několik [výchozích pravidel zabezpečení](security-overview.md#default-security-rules). Pravidlo, které následuje, přepíše výchozí pravidlo zabezpečení, které umožňuje odchozí přístup ke všem veřejným IP adresám. Tato `destination-address-prefix "Internet"` možnost odepře odchozí přístup ke všem veřejným IP adresám. Předchozí pravidlo přepíše toto pravidlo, vzhledem k jeho vyšší prioritu, která umožňuje přístup k veřejné IP adresy Azure Storage.

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

Následující pravidlo umožňuje ssh přenosy příchozí do podsítě odkudkoli. Toto pravidlo přepíše výchozí pravidlo zabezpečení, které zakazuje veškerý příchozí provoz z internetu. SSH je povolena do podsítě tak, aby připojení lze testovat v pozdějším kroku.

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

Kroky potřebné k omezení síťového přístupu k prostředkům vytvořeným prostřednictvím služeb Azure povolených v koncových bodech se u jednotlivých služeb liší. Konkrétní kroky pro jednotlivé služby najdete v dokumentaci příslušné služby. Zbývající část tohoto článku obsahuje kroky k omezení přístupu k síti pro účet Azure Storage, jako příklad.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvořte účet úložiště Azure s [vytvořením účtu úložiště az](/cli/azure/storage/account). Nahraďte `<replace-with-your-unique-storage-account-name>` název, který je jedinečný ve všech umístěních Azure, mezi 3-24 znaků na délku, pomocí pouze čísla a malá písmena.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Po vytvoření účtu úložiště načtěte připojovací řetězec pro účet úložiště do proměnné s [účtem úložiště az show-connection-string](/cli/azure/storage/account). Připojovací řetězec se používá k vytvoření sdílené složky v pozdějším kroku.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Zobrazení obsahu proměnné a poznamenejte si hodnotu **AccountKey** vrácenou ve výstupu, protože se používá v pozdějším kroku.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Vytvoření sdílené složky v účtu úložiště

Vytvořte sdílenou složku v účtu úložiště pomocí [vytvoření sdílené složky úložiště az](/cli/azure/storage/share). V pozdějším kroku je tato sdílená složka připojena k potvrzení přístupu k síti.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Odepřít veškerý síťový přístup k účtu úložiště

Účty úložiště ve výchozím nastavení přijímají síťová připojení z klientů v jakékoli síti. Chcete-li omezit přístup k vybraným sítím, změňte výchozí akci na *Příkaz Odepřít* s aktualizací účtu [úložiště az](/cli/azure/storage/account). Jakmile je přístup k síti zakázán, účet úložiště není přístupný ze žádné sítě.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Povolení síťového přístupu z podsítě

Povolit síťový přístup k účtu úložiště z *privátní* podsítě s [přidáním síťového pravidla účtu az.](/cli/azure/storage/account/network-rule)

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

Vytvořte virtuální virtuální ho v *veřejné* podsíti s [vytvořením virtuálního va](/cli/azure/vm). Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního počítače azure cli zobrazí informace podobné v následujícím příkladu: 

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

Poznamenejte si **publicIpAddress** ve vráceném výstupu. Tato adresa se používá pro přístup k virtuálnímu virtuálnímu mněmu z internetu v pozdějším kroku.

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

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření, poznamenejte **si publicIpAddress** ve výstupu vrácena. Tato adresa se používá pro přístup k virtuálnímu virtuálnímu mněmu z internetu v pozdějším kroku.

## <a name="confirm-access-to-storage-account"></a>Ověření přístupu k účtu úložiště

SSH do virtuálního virtuálního m. *myVmPrivate.* Nahraďte * \<>publicIpAddress* veřejnou IP adresou vašeho virtuálního počítače *myVmPrivate.*

```bash 
ssh <publicIpAddress>
```

Vytvořte složku pro přípojný bod:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Připojte sdílenou složku Azure do adresáře, který jste vytvořili. Před spuštěním následujícího `<storage-account-name>` příkazu nahraďte názvem účtu a `<storage-account-key>` klíčem, který jste načetli v [tématu Vytvoření účtu úložiště](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Zobrazí se `user@myVmPrivate:~$` výzva. Sdílená složka Azure byla úspěšně připojena k *parametru /mnt/MyAzureFileShare*.

Zkontrolujte, že virtuální hotel nemá žádné odchozí připojení k jiným veřejným IP adresám:

```bash
ping bing.com -c 4
```

Neobdržíte žádné odpovědi, protože skupina zabezpečení sítě přidružená k podsíti *Private* nepovoluje odchozí přístup k jiným veřejným IP adresám, než jsou adresy přiřazené službě Azure Storage.

Ukončete relaci SSH na virtuální m *myVmPrivate.*

## <a name="confirm-access-is-denied-to-storage-account"></a>Ověření odepření přístupu k účtu úložiště

Pomocí následujícího příkazu vytvořte relaci SSH s virtuálním virtuálním mem *myVmPublic.* Nahraďte `<publicIpAddress>` veřejnou IP adresou vašeho virtuálního počítače *myVmPublic:* 

```bash 
ssh <publicIpAddress>
```

Vytvořte adresář pro přípojný bod:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Pokuste se připojit sdílenou složku Azure do adresáře, který jste vytvořili. Tento článek předpokládá, že jste nasadili nejnovější verzi Ubuntu. Pokud používáte starší verze Ubuntu, viz [Mount on Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) další pokyny o montáži sdílených složek. Před spuštěním následujícího `<storage-account-name>` příkazu nahraďte názvem účtu a `<storage-account-key>` klíčem, který jste načetli v [tématu Vytvoření účtu úložiště](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Přístup je odepřen a `mount error(13): Permission denied` zobrazí se chyba, protože virtuální virtuální byl virtuální *ms myVmPublic* nasazený v rámci *veřejné* podsítě. Podsíť *Public* nemá povolený koncový bod služby pro Azure Storage a účet úložiště umožňuje síťový přístup pouze z podsítě *Private*, a ne z podsítě *Public*.

Ukončete relaci SSH na virtuální m. *myVmPublic.*

Z počítače se pokuste zobrazit sdílené složky v účtu úložiště pomocí [seznamu sdílených položek úložiště az](/cli/azure/storage/share?view=azure-cli-latest). Nahraďte `<account-name>` a `<account-key>` s názvem účtu úložiště a klíč z vytvořit účet [úložiště](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Přístup je odepřen a obdržíte *Tento požadavek není oprávněn provést tuto chybu operace,* protože počítač není v *privátní* podsíti virtuální sítě *MyVirtualNetwork.*

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, použijte [az skupiny odstranit](/cli/azure) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste povolili koncový bod služby pro podsíť virtuální sítě. Dozvěděli jste se, že koncové body služeb je možné povolit pro prostředky nasazené pomocí několika služeb Azure. Vytvořili jste účet služby Azure Storage a omezili jste síťový přístup k účtu úložiště pouze na prostředky v rámci podsítě virtuální sítě. Další informace o koncových bodech služeb najdete v tématech [Přehled koncových bodů služeb](virtual-network-service-endpoints-overview.md) a [Správa podsítí](virtual-network-manage-subnet.md).

Pokud ve svém účtu máte více virtuálních sítí, možná budete chtít propojit dvě virtuální sítě, aby mezi sebou mohly komunikovat prostředky v obou virtuálních sítích. Informace najdete v tématu [Připojení virtuálních sítí](tutorial-connect-virtual-networks-cli.md).
