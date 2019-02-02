---
title: Nahrání vlastního disku Linux pomocí příkazového řádku Azure | Dokumentace Microsoftu
description: Vytvoření a nahrání virtuálního pevného disku (VHD) do Azure s využitím modelu nasazení Resource Manager a Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: a04c4d41d9682389347009446c590fc4e27400b1
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55659537"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Nahrání a vytvoření virtuálního počítače s Linuxem z vlastního disku pomocí rozhraní příkazového řádku Azure

Tento článek popisuje, jak vytvořit virtuální počítače s Linuxem z vlastního disku a nahrání virtuálního pevného disku (VHD) do účtu služby Azure storage pomocí Azure CLI. Tato funkce umožňuje instalovat a konfigurovat distribuce Linuxu svých požadavků a pak použít tento virtuální pevný disk můžete rychle vytvořit virtuální počítače Azure (VM).

Toto téma používá účty úložiště pro poslední virtuální pevné disky, ale můžete také provést tento postup pomocí [spravované disky](upload-vhd.md). 

## <a name="quick-commands"></a>Rychlé příkazy
Pokud je potřeba rychle provést úlohu, následující část podrobně popisuje základní příkazy pro nahrání virtuálního pevného disku do Azure. Podrobnější informace a kontext pro každý krok najdete zbývající části dokumentu, [od tady](#requirements).

Ujistěte se, že máte nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index#az_login).

V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Ukázkové názvy parametrů zahrnutých `myResourceGroup`, `mystorageaccount`, a `mydisks`.

Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků `myResourceGroup` v umístění `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Vytvořte účet úložiště pro uložení virtuálních discích s [vytvořit účet úložiště az](/cli/azure/storage/account). Následující příklad vytvoří účet úložiště s názvem `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Seznam přístupových klíčů pro účet úložiště s [seznamu klíčů účtu úložiště az](/cli/azure/storage/account/keys#az_storage_account_keys_list). Poznamenejte si `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Vytvoření kontejneru v účtu úložiště pomocí klíče úložiště, můžete získat pomocí [vytvořit kontejner úložiště az](/cli/azure/storage/container). Následující příklad vytvoří kontejner s názvem `mydisks` hodnotu klíče úložiště z `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Nakonec odešlete vašeho virtuálního pevného disku do kontejneru, který jste vytvořili s [az storage blob nahrávání](/cli/azure/storage/blob#az_storage_blob_upload). Zadejte místní cestu na virtuální pevný disk v části `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Zadejte identifikátor URI na disk (`--image`) s [az vm vytvořit](/cli/azure/vm). Následující příklad vytvoří virtuální počítač s názvem `myVM` pomocí virtuální disk byl dříve odeslán:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Cílový účet úložiště musí být stejná jako ve kterém jste nahráli virtuálního disku. Budete taky muset zadat nebo odpověď vyzve k zadání dalších parametrů vyžadovaných **az vm vytvořit** příkazu, jako je například virtuální síť, veřejnou IP adresu, uživatelského jména a klíče SSH. Další informace o [dostupné parametry příkazového řádku Resource Manageru](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Požadavky
K dokončení následujících kroků, potřebujete:

* **Linux operační systém nainstalovaný na soubor VHD** – instalace [distribucí Linuxu schválených pro Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (nebo se podívejte [informace pro neschválené distribuce](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) na virtuální disk ve formátu virtuálního pevného disku . Existují více nástroje k vytvoření virtuálního počítače a virtuálního pevného disku:
  * Instalace a konfigurace [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) nebo [KVM](http://www.linux-kvm.org/page/RunningKVM), nezapomeňte použít virtuální pevný disk jako formát obrázku. V případě potřeby můžete [převedení obrázku](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) pomocí `qemu-img convert`.
  * Můžete také použít technologie Hyper-V [ve Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) nebo [v systému Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Novější formát VHDX nepodporuje v Azure. Při vytváření virtuálního počítače, zadejte jako formát virtuálního pevného disku. V případě potřeby můžete převést disky VHDX na virtuální pevný disk pomocí [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) nebo [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) rutiny Powershellu. Kromě toho Azure nepodporuje odesílání dynamických virtuálních pevných disků, takže budete muset převést těmito disky na statické virtuální pevné disky, před nahráním. Můžete použít nástroje jako [virtuálního pevného disku nástroje Azure pro GO](https://github.com/Microsoft/azure-vhd-utils-for-go) pro převod dynamických disků během procesu nahrávání do Azure.
> 
> 

* Virtuální počítače vytvořené z vlastního disku se musí nacházet ve stejném účtu úložiště jako samotném disku
  * Vytvoření účtu úložiště a kontejner pro uložení vlastního disku a vytvořené virtuální počítače
  * Po vytvoření všech virtuálních počítačů můžete bezpečně odstranit disk

Ujistěte se, že máte nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index#az_login).

V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Ukázkové názvy parametrů zahrnutých `myResourceGroup`, `mystorageaccount`, a `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Příprava k odeslání disku
Azure podporuje distribuce Linuxu (viz [distribuce schválené pro](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Následující články vás provedou postup přípravy různých Linuxových distribucí, které jsou podporovány v Azure:

* **[Distribuce založené na centOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Jiné – neschválené distribuce](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Viz také **[poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes)** pro další Obecné tipy pro Příprava imagí Linuxu na Azure.

> [!NOTE]
> [Platformy Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) platí pro virtuální počítače s Linuxem, pouze v případě, že jednu z doporučených distribucích se používá s podrobnosti o konfiguraci, jak je uvedeno v části "podporované verze [Linux na schválené pro Azure Distribuce](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupiny prostředků logicky pohromadě všechny prostředky Azure pro podporu vašich virtuálních počítačích, jako jsou virtuální sítě a úložiště. Další informace o skupin prostředků, najdete v části [přehled skupin prostředků](../../azure-resource-manager/resource-group-overview.md). Před nahráním vlastního disku a vytvoření virtuálních počítačů, musíte nejprve vytvořit skupinu prostředků pomocí [vytvořit skupiny az](/cli/azure/group#az_group_create).

Následující příklad vytvoří skupinu prostředků `myResourceGroup` v umístění `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvoření účtu úložiště pro váš vlastní disk a virtuální počítače s [vytvořit účet úložiště az](/cli/azure/storage/account). Všechny virtuální počítače s nespravovanými disky, které jste vytvořili z vašeho vlastního disku musí být ve stejném účtu úložiště jako disk. 

Následující příklad vytvoří účet úložiště s názvem `mystorageaccount` ve skupině prostředků vytvořili dříve:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Vypsat klíče účtu úložiště
Vygeneruje Azure dva 512bitové přístupové klíče pro každý účet úložiště. Tyto přístupové klíče se používají při ověřování k účtu úložiště, například k provádění operací zápisu. Další informace o [Správa přístupu k úložišti zde](../../storage/common/storage-account-manage.md#access-keys). Zobrazení přístupových klíčů pomocí [seznamu klíčů účtu úložiště az](/cli/azure/storage/account/keys#az_storage_account_keys_list).

Zobrazení přístupových klíčů pro účet úložiště, který jste vytvořili:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Výstup je podobný tomuto:

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
Poznamenejte si `key1` protože budete ho používat k interakci se svým účtem úložiště v dalších krocích.

## <a name="create-a-storage-container"></a>Vytvoření kontejneru úložiště
Stejným způsobem, který vytvoříte různým adresářům logicky tak uspořádat vašeho místního systému souborů můžete vytvořit kontejnery v rámci účtu úložiště pro uspořádání disků. Účet úložiště může obsahovat libovolný počet kontejnerů. Vytvořte kontejner s [vytvořit kontejner úložiště az](/cli/azure/storage/container).

Následující příklad vytvoří kontejner s názvem `mydisks`:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Nahrání virtuálního pevného disku
Teď nahrajte vlastní disku s [az storage blob nahrávání](/cli/azure/storage/blob#az_storage_blob_upload). Nahrávat a ukládat vlastní disk jako objekt blob stránky.

Zadejte přístupový klíč, kontejner, který jste vytvořili v předchozím kroku a cesta k disku vlastní v místním počítači:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Vytvořte virtuální počítač.
Vytvoření virtuálního počítače s nespravovanými disky, zadejte identifikátor URI na disk (`--image`) s [az vm vytvořit](/cli/azure/vm). Následující příklad vytvoří virtuální počítač s názvem `myVM` pomocí virtuální disk byl dříve odeslán:

Můžete zadat `--image` parametr s [az vm vytvořit](/cli/azure/vm) tak, aby odkazoval na vlastní disk. Ujistěte se, že `--storage-account` odpovídá ukládat vlastní disk účtu úložiště. Není nutné použít stejný kontejner jako vlastní disku pro ukládání virtuálních počítačů. Ujistěte se, že před nahráním vlastního disku vytvořit žádné další kontejnery stejným způsobem jako v předchozích krocích.

Následující příklad vytvoří virtuální počítač s názvem `myVM` z vlastního disku:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Stále je třeba zadat nebo odpověď vyzve k zadání dalších parametrů vyžadovaných **az vm vytvořit** příkaz například uživatelského jména a klíče SSH.


## <a name="resource-manager-template"></a>Šablona Resource Manageru
Šablony Azure Resource Manageru jsou soubory JavaScript Object Notation (JSON), které definují prostředí, ve kterém chcete vytvořit. Šablony jsou rozdělené do jiného prostředku zprostředkovatelů například výpočetní nebo síťový. Můžete použít existující šablony nebo napište vlastní. Další informace o [pomocí Resource Manageru a šablony](../../azure-resource-manager/resource-group-overview.md).

V rámci `Microsoft.Compute/virtualMachines` poskytovatele šablony, budete mít `storageProfile` uzel, který obsahuje podrobnosti o konfiguraci pro virtuální počítač. Jsou dvě hlavní parametry upravit `image` a `vhd` identifikátory URI, které odkazují na vlastní disk a virtuální disk nového virtuálního počítače. Následuje příklad JSON pro použití vlastního disku:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Můžete použít [tento existující šablonu k vytvoření virtuálního počítače z vlastní image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) nebo si přečtěte [vytváření šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md). 

Jakmile budete mít nakonfigurované šablony, použijte [vytvořit nasazení skupiny pro az](/cli/azure/group/deployment#az_group_deployment_create) k vytvoření virtuálních počítačů. Zadejte identifikátor URI šablony JSON s `--template-uri` parametr:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Pokud máte soubor JSON, které se ukládají místně v počítači, můžete použít `--template-file` parametr místo:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Další postup
Jakmile připraven a nahrát vlastní virtuální disk, si můžete přečíst více o [pomocí Resource Manageru a šablony](../../azure-resource-manager/resource-group-overview.md). Můžete také chtít [přidat datový disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) na nové virtuální počítače. Pokud máte aplikace běžící na virtuálních počítačích, které potřebujete získat přístup, je potřeba [otevření portů a koncových bodů](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

