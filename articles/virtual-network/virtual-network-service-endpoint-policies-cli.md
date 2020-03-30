---
title: Omezení exfiltrace dat na Azure Storage – azure cli
description: V tomto článku se dozvíte, jak omezit a omezit exfiltraci dat virtuální sítě na prostředky Azure Storage pomocí zásad koncového bodu služby virtuální sítě pomocí azure cli.
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
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: e01af052a936403162115965f2dc5b3ad46dd9cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271186"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Správa exfiltrace dat na účty Azure Storage pomocí zásad koncového bodu služby virtuální sítě pomocí azure cli

Zásady koncového bodu virtuální síťové služby umožňují použít řízení přístupu na účty Azure Storage z virtuální sítě přes koncové body služby. To to je klíč k zabezpečení úloh, správě účtů úložiště jsou povoleny a kde je povolena exfiltrace dat.
V tomto článku získáte informace o těchto tématech:

* Vytvořte virtuální síť a přidejte podsíť.
* Povolte koncový bod služby pro Azure Storage.
* Vytvořte dva účty Azure Storage a povolte přístup k síti z výše vytvořené podsítě.
* Vytvořte zásadu koncového bodu služby, která umožní přístup pouze k jednomu z účtů úložiště.
* Nasazení virtuálního počítače (VM) do podsítě.
* Potvrďte přístup k povolenému účtu úložiště z podsítě.
* Potvrďte, že přístup k nepovolenému účtu úložiště je odepřen z podsítě.

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
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Povolení koncového bodu služby 

V tomto příkladu je vytvořen koncový bod služby pro *Microsoft.Storage* pro podsíť *Private*: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
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

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Omezení přístupu k síti na účty Azure Storage

V této části jsou uvedeny kroky k omezení přístupu k síti pro účet Azure Storage z dané podsítě ve virtuální síti prostřednictvím koncového bodu služby.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvořte dva účty úložiště Azure s [vytvořením účtu úložiště az](/cli/azure/storage/account).

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

Po vytvoření účtů úložiště načtěte připojovací řetězec pro účty úložiště do proměnné s [účtem úložiště az show-connection-string](/cli/azure/storage/account). Připojovací řetězec se používá k vytvoření sdílené složky v pozdějším kroku.

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

<a name="account-key"></a>Zobrazení obsahu proměnné a poznamenejte si hodnotu **AccountKey** vrácenou ve výstupu, protože se používá v pozdějším kroku.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Vytvoření sdílené složky v účtu úložiště

Vytvořte sdílenou složku v účtu úložiště pomocí [vytvoření sdílené složky úložiště az](/cli/azure/storage/share). V pozdějším kroku je tato sdílená složka připojena k potvrzení přístupu k síti.

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

### <a name="deny-all-network-access-to-the-storage-account"></a>Odepřít veškerý síťový přístup k účtu úložiště

Účty úložiště ve výchozím nastavení přijímají síťová připojení z klientů v jakékoli síti. Chcete-li omezit přístup k vybraným sítím, změňte výchozí akci na *Příkaz Odepřít* s aktualizací účtu [úložiště az](/cli/azure/storage/account). Jakmile je přístup k síti zakázán, účet úložiště není přístupný ze žádné sítě.

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

### <a name="enable-network-access-from-virtual-network-subnet"></a>Povolení přístupu k síti z podsítě virtuální sítě

Povolit síťový přístup k účtu úložiště z *privátní* podsítě s [přidáním síťového pravidla účtu az.](/cli/azure/storage/account/network-rule)

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

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Použití zásad pro povolení přístupu k platnému účtu úložiště

Zásady Azure Service Endpoint jsou dostupné jenom pro Azure Storage. Takže budeme povoluje service endpoint pro *Microsoft.Storage* v této podsíti pro tento příklad nastavení.

Zásady koncového bodu služby se používají přes koncové body služby. Začneme vytvořením zásady koncového bodu služby. Poté vytvoříme definice zásad v rámci těchto zásad pro účty Azure Storage, které budou pro tuto podsíť uvedeny na seznamu povolených.

Vytvoření zásadkoncového bodu služby

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Uložte identifikátor URI prostředku pro povolený účet úložiště do proměnné. Před provedením níže uvedeného příkazu nahraďte * \<>id předplatného* skutečnou hodnotou ID předplatného.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Vytvoření & přidání definice zásad pro povolení výše uvedeného účtu úložiště Azure do zásad koncového bodu služby

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

A aktualizujte podsíť virtuální sítě tak, aby k ní přidružovala zásady koncového bodu služby vytvořené v předchozím kroku

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Ověření omezení přístupu k účtům Azure Storage

### <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

Chcete-li otestovat přístup k síti k účtu úložiště, nasaďte virtuální ho do podsítě.

Vytvořte virtuální virtuální ho v *soukromé* podsíti s [vytvořením virtuálního va](/cli/azure/vm). Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

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

### <a name="confirm-access-to-storage-account"></a>Ověření přístupu k účtu úložiště

SSH do virtuálního virtuálního m. *myVmPrivate.* Nahraďte * \<>publicIpAddress* veřejnou IP adresou vašeho virtuálního počítače *myVmPrivate.*

```bash 
ssh <publicIpAddress>
```

Vytvořte složku pro přípojný bod:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Připojte sdílenou složku Azure do adresáře, který jste vytvořili. Před provedením níže uvedeného příkazu nahraďte * \<>klíče účtu úložiště* hodnotou *AccountKey* z **$saConnectionString1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Zobrazí se `user@myVmPrivate:~$` výzva. Sdílená složka Azure byla úspěšně připojena k *parametru /mnt/MyAzureFileShare*.

### <a name="confirm-access-is-denied-to-storage-account"></a>Ověření odepření přístupu k účtu úložiště

Ze stejného virtuálního virtuálního měn *myVmPrivate*vytvořte adresář pro přípojný bod:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Pokus te se připojit sdílenou složku Azure z účtu úložiště *není povolenostorageacc* do adresáře, který jste vytvořili. Tento článek předpokládá, že jste nasadili nejnovější verzi Ubuntu. Pokud používáte starší verze Ubuntu, viz [Mount on Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) další pokyny o montáži sdílených složek. 

Před provedením níže uvedeného příkazu nahraďte * \<>klíče účtu úložiště* hodnotou *AccountKey* od **$saConnectionString2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Přístup je odepřen a `mount error(13): Permission denied` zobrazí se chyba, protože tento účet úložiště není v seznamu povolených zásad koncového bodu služby, které jsme použili v podsíti. 

Ukončete relaci SSH na virtuální m. *myVmPublic.*

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, použijte [az skupiny odstranit](/cli/azure) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste použili zásady koncového bodu služby přes koncový bod virtuální sítě Azure do azure storage. Vytvořili jste účty Azure Storage a omezený přístup k síti pouze na určité účty úložiště (a tím odepřeli ostatní) z podsítě virtuální sítě. Další informace o zásadách koncového bodu služby najdete v [tématu Přehled zásad koncových bodů služby](virtual-network-service-endpoint-policies-overview.md).
