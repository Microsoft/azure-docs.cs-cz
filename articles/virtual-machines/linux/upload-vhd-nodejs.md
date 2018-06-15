---
title: Nahrát vlastní image Linux s 1.0 rozhraní příkazového řádku Azure | Microsoft Docs
description: Vytvoření a nahrání virtuálního pevného disku (VHD) do Azure s vlastní image Linux pomocí modelu nasazení Resource Manager a Azure CLI 1.0.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou
ms.openlocfilehash: 6eb0cae2b70e0cbb9a4fb5fcab3a58d566d0f4d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915306"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image-by-using-the-azure-cli-10"></a>Nahrání a vytvoření virtuálního počítače s Linuxem z image vlastní disku pomocí Azure CLI 1.0
Tento článek ukazuje, jak nahrát virtuální pevný disk (VHD) do Azure pomocí modelu nasazení Resource Manager a vytvořit virtuální počítače s Linuxem z této vlastní image. Tato funkce umožňuje instalovat a konfigurovat Linux distro svých požadavků a použije tento virtuální pevný disk k rychlému vytvoření Azure virtuální počítače (VM).


## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure CLI 1.0](#quick-commands) – naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků


## <a name="quick-commands"></a>Rychlé příkazy
Pokud potřebujete rychle provedení úlohy, následující část podrobně popisuje základní příkazy k nahrání virtuálního počítače do Azure. Podrobnější informace a kontext pro každý krok naleznete zbývající části dokumentu, [od zde](#requirements).

Ujistěte se, že máte [Azure CLI 1.0](../../cli-install-nodejs.md) přihlášení a použití režimu Resource Manager:

```azurecli
azure config mode arm
```

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Názvy parametrů příklad zahrnuté `myResourceGroup`, `mystorageaccount`, a `myimages`.

Nejprve vytvořte skupinu prostředků. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v `WestUs` umístění:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

Vytvořte účet úložiště pro virtuální disky. Následující příklad vytvoří účet úložiště s názvem `mystorageaccount`:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Zobrazí seznam přístupové klíče pro účet úložiště. Poznamenejte si `key1`:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Vytvořte kontejner v rámci účtu úložiště pomocí klíče úložiště, který jste obdrželi. Následující příklad vytvoří kontejner s názvem `myimages` z úložiště klíčů hodnoty `key1`:

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Nakonec Nahrajte svůj disk VHD do kontejneru, kterou jste vytvořili. Zadejte místní cestu na vaše virtuální pevný disk v části `/path/to/disk/mydisk.vhd`:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Nyní můžete vytvořit virtuální počítač z nahraný virtuální disk [pomocí šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). Můžete také použít rozhraní příkazového řádku zadáním identifikátor URI na disk (`--image-urn`). Následující příklad vytvoří virtuální počítač s názvem `myVM` pomocí virtuálního disku předtím nahrála:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

Cílový účet úložiště musí být stejný jako kde jste nahráli virtuální disk. Budete taky muset zadat nebo odpověď výzvy pro všechny další parametry, vyžaduje `azure vm create` příkaz například virtuální sítě, veřejnou IP adresu, uživatelské jméno a klíče SSH. Další informace o [dostupné parametry příkazového řádku Resource Manager](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Požadavky
Chcete-li provést následující kroky, je třeba:

* **Operační systém Linux nainstalován v souboru VHD** -nainstalovat [distribuce schválené pro Azure Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (nebo v tématu [informace pro neschválené distribuce](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) na virtuální disk ve formátu virtuálního pevného disku . Existuje několik nástrojů k vytvoření virtuálního počítače a virtuálního pevného disku:
  * Instalace a konfigurace [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) nebo [KVM](http://www.linux-kvm.org/page/RunningKVM), aby byl používáte formát bitové kopie virtuálního pevného disku. V případě potřeby můžete [převést bitovou kopii](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) pomocí `qemu-img convert`.
  * Můžete také použít technologie Hyper-V [ve Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) nebo [v systému Windows Server 2012 nebo 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Novější formát VHDX není podporovaný v Azure. Když vytvoříte virtuální počítač, zadejte jako formát virtuálního pevného disku. V případě potřeby můžete převést disků VHDX virtuální pevný disk pomocí [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) nebo [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) rutiny prostředí PowerShell. Navíc Azure nepodporuje odesílání dynamické virtuální pevné disky, takže je nutné převést tyto disky na virtuální pevné disky statické před nahráním. Můžete použít nástroje, jako [nástroje Azure virtuálního pevného disku pro přejděte](https://github.com/Microsoft/azure-vhd-utils-for-go) převést dynamické disky během procesu odesílání do Azure.
> 
> 

* Virtuální počítače vytvořené pomocí vlastní image se musí nacházet ve stejném účtu úložiště jako samotný obraz
  * Vytvoření účtu úložiště a kontejner pro uložení vlastní image a vytvořené virtuální počítače
  * Po vytvoření všechny virtuální počítače, můžete bezpečně odstranit image

Ujistěte se, že máte [Azure CLI 1.0](../../cli-install-nodejs.md) přihlášení a použití režimu Resource Manager:

```azurecli
azure config mode arm
```

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Názvy parametrů příklad zahrnuté `myResourceGroup`, `mystorageaccount`, a `myimages`.

<a id="prepimage"> </a>

## <a name="prepare-the-image-to-be-uploaded"></a>Příprava bitové kopie k odeslání
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


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupiny prostředků logicky seskupit všechny prostředky Azure pro podporu virtuálních počítačů, jako je například virtuální sítě a úložiště. Další informace o [skupin prostředků Azure zde](../../azure-resource-manager/resource-group-overview.md). Před nahráním image vlastní disku a vytváření virtuálních počítačů, musíte nejprve vytvořit skupinu prostředků. 

Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v `WestUS` umístění:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
Virtuální počítače jsou uloženy jako objekty BLOB stránky v rámci účtu úložiště. Další informace o [zde úložiště objektů blob Azure](../../storage/common/storage-introduction.md#blob-storage). Můžete vytvořit účet úložiště pro image vlastní disku a virtuální počítače. Všechny virtuální počítače, které vytvoříte z bitové kopie vlastní disku musí být ve stejném účtu úložiště jako této bitové kopie.

Následující příklad vytvoří účet úložiště s názvem `mystorageaccount` ve skupině prostředků vytvořili:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Vypsat klíče účtu úložiště
Vygeneruje Azure dva 512bitové přístupové klíče pro každý účet úložiště. Tyto přístupové klíče se používají při ověřování k účtu úložiště, například k provedení operace zápisu. Další informace o [Správa přístupu k úložišti zde](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Můžete zobrazit přístupové klíče se `azure storage account keys list` příkaz.

Zobrazte přístupové klíče pro účet úložiště, který jste vytvořili:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
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
V stejným způsobem, který vytvoříte různé adresáře, které logicky uspořádat do místního systému souborů můžete vytvořit kontejnery v účtu úložiště k organizaci virtuální disky a bitové kopie. Účet úložiště může obsahovat libovolný počet kontejnerů. 

Následující příklad vytvoří kontejner s názvem `myimages`, zadání přístupového klíče získaných v předchozím kroku (`key1`):

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Nahrání virtuálního pevného disku
Nyní můžete ve skutečnosti nahrát vlastní disku image. S všechny virtuální disky, které jsou používány virtuálními počítači, můžete nahrát a uložení bitové kopie disku vlastní jako objekt blob stránky.

Zadejte přístupový klíč, kontejner, který jste vytvořili v předchozím kroku a pak cestu k bitové kopie vlastní disku v místním počítači:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Vytvoření virtuálního počítače z vlastní image
Když vytvoříte virtuální počítače z image vlastní disk, zadejte identifikátor URI pro bitové kopie disku. Zajistěte, aby na cílové odpovídá účet úložiště se uloží vlastní disku image. Můžete vytvořit pomocí rozhraní příkazového řádku Azure nebo Resource Manager JSON šablony virtuálního počítače.

### <a name="create-a-vm-using-the-azure-cli"></a>Vytvoření virtuálního počítače pomocí rozhraní příkazového řádku Azure
Zadáte `--image-urn` parametr s `azure vm create` příkaz tak, aby odkazovaly do bitové kopie disku vlastní. Ujistěte se, že `--storage-account-name` odpovídá účet úložiště, kde je uložena bitová kopie vaše vlastní disku. Není nutné používat stejný kontejner jako bitové kopie vlastní disku k uložení virtuálních počítačů. Ujistěte se, že jste před nahráním vaše vlastní disku Image vytvořit žádné další kontejnery stejným způsobem jako v předchozích krocích.

Následující příklad vytvoří virtuální počítač s názvem `myVM` z bitové kopie disku vlastní:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

Stále je třeba zadat nebo odpověď výzvy pro všechny další parametry, vyžaduje `azure vm create` příkaz například virtuální sítě, veřejnou IP adresu, uživatelské jméno a klíče SSH. Další informace o [dostupné parametry příkazového řádku Resource Manager](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Vytvoření virtuálního počítače pomocí šablony JSON
Šablony Azure Resource Manageru jsou soubory JavaScript Object Notation (JSON), které definují prostředí, ve kterém chcete vytvořit. Šablony jsou rozděleny různých prostředků zprostředkovatelé například výpočetní nebo sítě. Můžete použít existující šablony nebo napsat vlastní. Další informace o [pomocí Resource Manageru a šablony](../../azure-resource-manager/resource-group-overview.md).

V rámci `Microsoft.Compute/virtualMachines` poskytovatele šablony, máte `storageProfile` uzlu, který obsahuje podrobnosti o konfiguraci pro virtuální počítač. Jsou dvě hlavní parametry, které chcete upravit `image` a `vhd` identifikátory URI, které odkazují na vlastní disku image a virtuální disk nového virtuálního počítače. Na obrázku je příkladem JSON pro použití image vlastní disku:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Můžete použít [tento existující šablonu k vytvoření virtuálního počítače z vlastní image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) nebo přečtěte si o [vlastních šablon Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

Jakmile máte nakonfigurované šablony, vytvoříte virtuální počítače pomocí `azure group deployment create` příkaz. Zadejte identifikátor URI šablony JSON s `--template-uri` parametr:

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Pokud máte soubor JSON, který je uložený místně v počítači, můžete použít `--template-file` parametr místo:

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Další postup
Po připravené a nahrát vlastní virtuální disk, si můžete přečíst více o [pomocí Resource Manageru a šablony](../../azure-resource-manager/resource-group-overview.md). Můžete také chtít [přidat datový disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) na nové virtuální počítače. Pokud máte aplikace spuštěné na vaše virtuální počítače, které potřebujete získat přístup, je nutné [otevřete porty a koncové body](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

