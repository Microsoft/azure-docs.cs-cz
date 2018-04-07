---
title: Nahrát vlastní disk Linux s 2.0 rozhraní příkazového řádku Azure | Microsoft Docs
description: Vytvoření a nahrání virtuálního pevného disku (VHD) do Azure pomocí modelu nasazení Resource Manager a Azure CLI 2.0
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
ms.openlocfilehash: 5c5c9894127386a05da9825435cfe5ca07b01542
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Nahrání a vytvoření virtuálního počítače s Linuxem z vlastní disk s 2.0 rozhraní příkazového řádku Azure
Tento článek ukazuje, jak nahrát virtuální pevný disk (VHD) pro účet úložiště Azure s Azure CLI 2.0 a vytvořit virtuální počítače s Linuxem z tento vlastní disk. K provedení těchto kroků můžete také využít [Azure CLI 1.0](upload-vhd-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tato funkce umožňuje instalovat a konfigurovat Linux distro svých požadavků a použije tento virtuální pevný disk k rychlému vytvoření Azure virtuální počítače (VM).

Toto téma používá účty úložiště pro poslední virtuální pevné disky, ale můžete také provést tyto kroky, pomocí [discích spravovaných](upload-vhd.md). 

## <a name="quick-commands"></a>Rychlé příkazy
Pokud potřebujete rychle provedení úlohy, následující část podrobně popisuje základní příkazy a nahrát VHD do Azure. Podrobnější informace a kontext pro každý krok naleznete zbývající části dokumentu, [od zde](#requirements).

Ujistěte se, že máte nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login).

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Názvy parametrů příklad zahrnuté `myResourceGroup`, `mystorageaccount`, a `mydisks`.

Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v `WestUs` umístění:

```azurecli
az group create --name myResourceGroup --location westus
```

Vytvořit účet úložiště pro virtuální disky s [vytvořit účet úložiště az](/cli/azure/storage/account#az_storage_account_create). Následující příklad vytvoří účet úložiště s názvem `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Seznam přístupové klíče pro účet úložiště s [seznam klíčů účtu úložiště az](/cli/azure/storage/account/keys#az_storage_account_keys_list). Poznamenejte si `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Vytvořit kontejner v rámci účtu úložiště pomocí klíče úložiště, můžete získat pomocí [vytvořit kontejner úložiště az](/cli/azure/storage/container#az_storage_container_create). Následující příklad vytvoří kontejner s názvem `mydisks` z úložiště klíčů hodnoty `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Nakonec nahrát svůj disk VHD do kontejneru, který jste vytvořili pomocí [az úložiště objektů blob nahrávání](/cli/azure/storage/blob#az_storage_blob_upload). Zadejte místní cestu na vaše virtuální pevný disk v části `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Zadejte identifikátor URI na disk (`--image`) s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem `myVM` pomocí virtuálního disku předtím nahrála:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Cílový účet úložiště musí být stejný jako kde jste nahráli virtuální disk. Budete taky muset zadat nebo odpověď výzvy pro všechny další parametry, vyžaduje **vytvořit virtuální počítač az** příkaz například virtuální sítě, veřejnou IP adresu, uživatelské jméno a klíče SSH. Další informace o [dostupné parametry příkazového řádku Resource Manager](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Požadavky
Chcete-li provést následující kroky, je třeba:

* **Operační systém Linux nainstalován v souboru VHD** -nainstalovat [distribuce schválené pro Azure Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (nebo v tématu [informace pro neschválené distribuce](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) na virtuální disk ve formátu virtuálního pevného disku . Existuje několik nástrojů k vytvoření virtuálního počítače a virtuálního pevného disku:
  * Instalace a konfigurace [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) nebo [KVM](http://www.linux-kvm.org/page/RunningKVM), aby byl používáte formát bitové kopie virtuálního pevného disku. V případě potřeby můžete [převést bitovou kopii](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) pomocí `qemu-img convert`.
  * Můžete také použít technologie Hyper-V [ve Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) nebo [v systému Windows Server 2012 nebo 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Novější formát VHDX není podporovaný v Azure. Když vytvoříte virtuální počítač, zadejte jako formát virtuálního pevného disku. V případě potřeby můžete převést disků VHDX virtuální pevný disk pomocí [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) nebo [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) rutiny prostředí PowerShell. Navíc Azure nepodporuje odesílání dynamické virtuální pevné disky, takže je nutné převést tyto disky na virtuální pevné disky statické před nahráním. Můžete použít nástroje, jako [nástroje Azure virtuálního pevného disku pro přejděte](https://github.com/Microsoft/azure-vhd-utils-for-go) převést dynamické disky během procesu odesílání do Azure.
> 
> 

* Virtuální počítače vytvořené z vlastní disk se musí nacházet ve stejném účtu úložiště jako samotném disku
  * Vytvoření účtu úložiště a kontejner pro uložení vlastní disku a vytvořené virtuální počítače
  * Po vytvoření všechny virtuální počítače, můžete bezpečně odstranit disk

Ujistěte se, že máte nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login).

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Názvy parametrů příklad zahrnuté `myResourceGroup`, `mystorageaccount`, a `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Příprava disku k odeslání
Azure podporuje různé Linuxových distribucích (viz [distribuce schválené](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Postup přípravy různé distribuce systému Linux, které jsou podporovány v Azure vás provede v následujících článcích:

* **[Na základě centOS distribuce](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Další - neschválené distribuce](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Viz také **[poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes)** Příprava bitové kopie systému Linux na Azure další Obecné tipy pro.

> [!NOTE]
> [Platformy Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se vztahují na virtuální počítače se systémem Linux jenom v případě, že jeden z potvrzená distribuce se používá s podrobností konfigurace uvedeného v části "podporované verze [Linux na Azure-Endorsed distribuce](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupiny prostředků logicky seskupit všechny prostředky Azure pro podporu virtuálních počítačů, jako je například virtuální sítě a úložiště. Další informace o skupin prostředků, najdete v části [skupiny zdrojů přehled](../../azure-resource-manager/resource-group-overview.md). Před nahráním váš vlastní disk a vytváření virtuálních počítačů, musíte nejprve vytvořit skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az_group_create).

Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v `westus` umístění:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvoření účtu úložiště vlastní disk a virtuální počítače s [vytvořit účet úložiště az](/cli/azure/storage/account#az_storage_account_create). Všechny virtuální počítače s nespravované disky, které vytvoříte z vaší vlastní disku musí být ve stejném účtu úložiště jako disk. 

Následující příklad vytvoří účet úložiště s názvem `mystorageaccount` ve skupině prostředků vytvořili:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Vypsat klíče účtu úložiště
Vygeneruje Azure dva 512bitové přístupové klíče pro každý účet úložiště. Tyto přístupové klíče se používají při ověřování k účtu úložiště, například k provedení operace zápisu. Další informace o [Správa přístupu k úložišti zde](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Zobrazit přístupové klíče s [seznam klíčů účtu úložiště az](/cli/azure/storage/account/keys#az_storage_account_keys_list).

Zobrazte přístupové klíče pro účet úložiště, který jste vytvořili:

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
Poznamenejte si `key1` jako použije k interakci se svým účtem úložiště v dalších krocích.

## <a name="create-a-storage-container"></a>Vytvoření kontejneru úložiště
V stejným způsobem, který vytvoříte různé adresáře, které logicky uspořádat do místního systému souborů můžete vytvořit kontejnery v účtu úložiště pro uspořádání vaše disky. Účet úložiště může obsahovat libovolný počet kontejnerů. Vytvořit kontejner s [vytvořit kontejner úložiště az](/cli/azure/storage/container#az_storage_container_create).

Následující příklad vytvoří kontejner s názvem `mydisks`:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Nahrání virtuálního pevného disku
Teď nahrát vlastní disk s [az úložiště objektů blob nahrávání](/cli/azure/storage/blob#az_storage_blob_upload). Nahrání a ukládat vlastní disk jako objekt blob stránky.

Zadejte přístupový klíč, kontejner, který jste vytvořili v předchozím kroku a pak cestu k vlastní disku v místním počítači:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Vytvořte virtuální počítač.
Pokud chcete vytvořit virtuální počítač s nespravované disky, zadejte identifikátor URI na disk (`--image`) s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem `myVM` pomocí virtuálního disku předtím nahrála:

Zadáte `--image` parametr s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) tak, aby odkazovalo na vlastní disk. Ujistěte se, že `--storage-account` odpovídá účet úložiště, kde je uložený váš vlastní disk. Nemusíte používat stejný kontejner jako vlastní disk k uložení virtuálních počítačů. Ujistěte se, že jste před nahráním váš vlastní disk vytvořit žádné další kontejnery stejným způsobem jako v předchozích krocích.

Následující příklad vytvoří virtuální počítač s názvem `myVM` z vlastní disku:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Stále je třeba zadat nebo odpověď výzvy pro všechny další parametry, vyžaduje **vytvořit virtuální počítač az** příkaz například uživatelské jméno a klíče SSH.


## <a name="resource-manager-template"></a>Šablona Resource Manageru
Šablony Azure Resource Manageru jsou soubory JavaScript Object Notation (JSON), které definují prostředí, ve kterém chcete vytvořit. Šablony jsou rozděleny různých prostředků zprostředkovatelé například výpočetní nebo sítě. Můžete použít existující šablony nebo napsat vlastní. Další informace o [pomocí Resource Manageru a šablony](../../azure-resource-manager/resource-group-overview.md).

V rámci `Microsoft.Compute/virtualMachines` poskytovatele šablony, máte `storageProfile` uzlu, který obsahuje podrobnosti o konfiguraci pro virtuální počítač. Jsou dvě hlavní parametry, které chcete upravit `image` a `vhd` identifikátory URI, které odkazují na vlastní disk a virtuální disk nového virtuálního počítače. Na obrázku je příkladem JSON pro použití vlastního disku:

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

Můžete použít [tento existující šablonu k vytvoření virtuálního počítače z vlastní image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) nebo přečtěte si o [vlastních šablon Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

Jakmile máte šablonu nakonfigurována, použijte [vytvořit nasazení skupiny az](/cli/azure/group/deployment#az_group_deployment_create) k vytvoření virtuálních počítačů. Zadejte identifikátor URI šablony JSON s `--template-uri` parametr:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Pokud máte soubor JSON, který je uložený místně v počítači, můžete použít `--template-file` parametr místo:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Další postup
Po připravené a nahrát vlastní virtuální disk, si můžete přečíst více o [pomocí Resource Manageru a šablony](../../azure-resource-manager/resource-group-overview.md). Můžete také chtít [přidat datový disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) na nové virtuální počítače. Pokud máte aplikace spuštěné na vaše virtuální počítače, které potřebujete získat přístup, je nutné [otevřete porty a koncové body](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

