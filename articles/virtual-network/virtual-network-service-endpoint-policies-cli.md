---
title: Omezení exfiltrace dat na Azure Storage – Azure CLI
description: V tomto článku se dozvíte, jak omezit a omezit exfiltrace dat virtuální sítě tak, aby Azure Storage prostředky pomocí zásad koncového bodu služby virtuální sítě pomocí rozhraní příkazového řádku Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 53f10996a7f15e32261f151600163f41df4e58ca
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91666768"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Správa exfiltrace dat pro Azure Storage účtů pomocí zásad koncového bodu služby virtuální sítě pomocí rozhraní příkazového řádku Azure

Zásady koncového bodu služby virtuální sítě umožňují použít řízení přístupu u účtů Azure Storage v rámci virtuální sítě prostřednictvím koncových bodů služby. Toto je klíč pro zabezpečení úloh, správu, které účty úložiště jsou povolené a kde je povolený exfiltrace dat.
V tomto článku získáte informace o těchto tématech:

* Vytvořte virtuální síť a přidejte podsíť.
* Povolit koncový bod služby pro Azure Storage.
* Vytvořte dva účty Azure Storage a povolte přístup k síti z podsítě vytvořené výše.
* Vytvořte zásadu koncového bodu služby, která povolí přístup jenom k jednomu z účtů úložiště.
* Nasaďte virtuální počítač (VM) do podsítě.
* Potvrďte přístup k povolenému účtu úložiště z podsítě.
* Potvrďte, že je přístup k nedovolenému účtu úložiště z podsítě odepřený.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

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
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Povolení koncového bodu služby 

V tomto příkladu se pro *privátní*podsíť vytvoří koncový bod služby pro *Microsoft. Storage* : 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
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

Každá skupina zabezpečení sítě obsahuje několik [výchozích pravidel zabezpečení](security-overview.md#default-security-rules). Následující pravidlo přepisuje výchozí pravidlo zabezpečení, které umožňuje odchozí přístup ke všem veřejným IP adresám. Tato `destination-address-prefix "Internet"` možnost zakazuje odchozí přístup ke všem veřejným IP adresám. Předchozí pravidlo přepisuje toto pravidlo z důvodu vyšší priority, což umožňuje přístup k veřejným IP adresám Azure Storage.

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

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Omezení síťového přístupu k účtům Azure Storage

V této části najdete seznam kroků pro omezení síťového přístupu pro účet Azure Storage z dané podsítě ve virtuální síti prostřednictvím koncového bodu služby.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvořte dva účty úložiště Azure pomocí [AZ Storage Account Create](/cli/azure/storage/account).

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Po vytvoření účtů úložiště načtěte připojovací řetězec pro účty úložiště do proměnné pomocí [AZ Storage Account show-Connection-String](/cli/azure/storage/account). Připojovací řetězec se používá k vytvoření sdílené složky v pozdějším kroku.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Zobrazte obsah proměnné a poznamenejte si hodnotu **AccountKey** vrácenou ve výstupu, protože se používá v pozdějším kroku.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Vytvoření sdílené složky v účtu úložiště

Vytvořte sdílenou složku v účtu úložiště pomocí [AZ Storage Share Create](/cli/azure/storage/share). V pozdějším kroku je tato sdílená složka připojená k ověření přístupu k síti.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Odepřít všem síťovým přístupům k účtu úložiště

Účty úložiště ve výchozím nastavení přijímají síťová připojení z klientů v jakékoli síti. Chcete-li omezit přístup k vybraným sítím, změňte výchozí akci na *Odepřít* pomocí [AZ Storage Account Update](/cli/azure/storage/account). Jakmile je přístup k síti zakázán, účet úložiště není přístupný ze žádné sítě.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Povolit přístup k síti z podsítě virtuální sítě

Povolte síťový přístup k účtu úložiště z *privátní* podsítě pomocí [AZ Storage Account Network-Rule Add](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Použijte zásady, aby se povolil přístup k platnému účtu úložiště.

Zásady koncového bodu služby Azure jsou dostupné jenom pro Azure Storage. Proto pro tento příklad nastavíme povolení koncového bodu služby *Microsoft. Storage* v této podsíti.

Zásady koncového bodu služby se používají prostřednictvím koncových bodů služby. Začneme vytvořením zásad koncového bodu služby. V rámci této zásady pak vytvoříme definice zásad pro účty Azure Storage, které se budou schvalovat pro tuto podsíť.

Vytvoření zásad koncového bodu služby

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Uložte identifikátor URI prostředku pro povolený účet úložiště v proměnné. Před spuštěním příkazu uvedeného níže nahraďte *\<your-subscription-id>* skutečnou hodnotou vašeho ID předplatného.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Vytvoření & Přidání definice zásady pro povolení výše uvedeného účtu Azure Storage k zásadě koncového bodu služby

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

A aktualizujte podsíť virtuální sítě tak, aby se k ní přidružil zásada koncového bodu služby vytvořená v předchozím kroku.

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Ověří omezení přístupu k účtům Azure Storage.

### <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

Pokud chcete otestovat síťový přístup k účtu úložiště, nasaďte do podsítě virtuální počítač.

Vytvořte virtuální počítač v *privátní* podsíti pomocí [AZ VM Create](/cli/azure/vm). Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

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

### <a name="confirm-access-to-storage-account"></a>Ověření přístupu k účtu úložiště

Připojte se přes SSH k virtuálnímu počítači *myVmPrivate* . Nahraďte *\<publicIpAddress>* veřejnou IP adresou vašeho virtuálního počítače s *myVmPrivate* .

```bash 
ssh <publicIpAddress>
```

Vytvořte složku pro přípojný bod:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Připojte sdílenou složku Azure k adresáři, který jste vytvořili. Před provedením následujícího příkazu nahraďte *\<storage-account-key>* hodnotu *AccountKey* z **$saConnectionString 1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Zobrazí se `user@myVmPrivate:~$` výzva. Sdílená složka Azure se úspěšně připojila k */mnt/MyAzureFileShare*.

### <a name="confirm-access-is-denied-to-storage-account"></a>Ověření odepření přístupu k účtu úložiště

Ze stejného *myVmPrivate*virtuálního počítače vytvořte adresář pro přípojný bod:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Pokuste se připojit sdílenou složku Azure z účtu úložiště *notallowedstorageacc* do adresáře, který jste vytvořili. V tomto článku se předpokládá, že jste nasadili nejnovější verzi Ubuntu. Pokud používáte starší verze Ubuntu, přečtěte si další informace o připojení sdílených složek v tématu [připojení na Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . 

Před spuštěním příkazu uvedeného níže nahraďte *\<storage-account-key>* hodnotou *AccountKey* z **$saConnectionString 2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Přístup je odepřený a zobrazí se `mount error(13): Permission denied` Chyba, protože tento účet úložiště není v seznamu povolených zásad koncového bodu služby, který jsme v podsíti použili. 

Ukončete relaci SSH na virtuálním počítači s *myVmPublic* .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků a všechny prostředky, které obsahuje, pomocí [AZ Group Delete](/cli/azure) .

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste u koncového bodu služby Azure Virtual Network použili zásadu koncového bodu služby na Azure Storage. Vytvořili jste účty Azure Storage a omezili jste přístup k síti pouze na určité účty úložiště (a tedy jiným uživatelům zamítli) z podsítě virtuální sítě. Další informace o zásadách koncového bodu služby najdete v tématu [Přehled zásad koncových bodů služby](virtual-network-service-endpoint-policies-overview.md).
