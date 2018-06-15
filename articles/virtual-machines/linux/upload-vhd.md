---
title: Nahrát, nebo můžete zkopírovat vlastní virtuální počítač s Linuxem pomocí Azure CLI 2.0 | Microsoft Docs
description: Nahrát, nebo můžete zkopírovat vlastní virtuální počítač pomocí modelu nasazení Resource Manager a Azure CLI 2.0
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
ms.date: 07/06/2017
ms.author: cynthn
ms.openlocfilehash: fc3d72ace6398b69a5efa5543c590bba166baaf0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918570"
---
# <a name="create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Vytvoření virtuálního počítače s Linuxem z vlastní disk s 2.0 rozhraní příkazového řádku Azure

<!-- rename to create-vm-specialized -->

Tento článek ukazuje, jak nahrát vlastní virtuální pevný disk (VHD) nebo kopírování existující virtuální pevný disk v Azure a vytvoření nových Linux virtuálních počítačů (VM) z vlastní disku. Můžete nainstalovat a nakonfigurovat Linux distro svých požadavků a použije tento virtuální pevný disk rychle vytvořit nový virtuální počítač Azure.

Pokud chcete vytvořit víc virtuálních počítačů z přizpůsobené disku, měli byste vytvořit bitovou kopii z virtuálního počítače nebo virtuální pevný disk. Další informace najdete v tématu [vytvořit vlastní image virtuálního počítače Azure pomocí rozhraní příkazového řádku](tutorial-custom-images.md).

Máte dvě možnosti:
* [Nahrání virtuálního pevného disku](#option-1-upload-a-specialized-vhd)
* [Zkopírujte existující virtuální počítač Azure](#option-2-copy-an-existing-azure-vm)

## <a name="quick-commands"></a>Rychlé příkazy

Při vytváření nového virtuálního počítače pomocí [az virtuální počítač vytvořit](/cli/azure/vm#az_vm_create) z přizpůsobené nebo specializované disku můžete **připojit** disku (– připojit disk operačního systému) místo zadávání vlastního nebo marketplace image (--bitové kopie). Následující příklad vytvoří virtuální počítač s názvem *Můjvp* pomocí spravovaného disku s názvem *myManagedDisk* vytvořené z vaší vlastní virtuální pevný disk:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Požadavky
Chcete-li provést následující kroky, je třeba:

* Virtuální počítač Linux, který je připravena pro použití v Azure. [Příprava virtuálního počítače](#prepare-the-vm) tohoto článku popisuje jak najít distro konkrétní informace o instalaci Azure Linux Agent (příkaz waagent), který je nutný pro virtuální počítač fungovat správně v Azure a abyste mohli připojit se pomocí SSH.
* Soubor virtuálního pevného disku z existující [distribuce schválené pro Azure Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (nebo v tématu [informace pro neschválené distribuce](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) na virtuální disk ve formátu virtuálního pevného disku. Existuje několik nástrojů k vytvoření virtuálního počítače a virtuálního pevného disku:
  * Instalace a konfigurace [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) nebo [KVM](http://www.linux-kvm.org/page/RunningKVM), aby byl používáte formát bitové kopie virtuálního pevného disku. V případě potřeby můžete [převést bitovou kopii](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) pomocí **qemu img převést**.
  * Můžete také použít technologie Hyper-V [ve Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) nebo [v systému Windows Server 2012 nebo 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Novější formát VHDX není podporovaný v Azure. Když vytvoříte virtuální počítač, zadejte jako formát virtuálního pevného disku. V případě potřeby můžete převést disků VHDX virtuální pevný disk pomocí [qemu img převést](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) nebo [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) rutiny prostředí PowerShell. Navíc Azure nepodporuje odesílání dynamické virtuální pevné disky, takže je nutné převést tyto disky na virtuální pevné disky statické před nahráním. Můžete použít nástroje, jako [nástroje Azure virtuálního pevného disku pro přejděte](https://github.com/Microsoft/azure-vhd-utils-for-go) převést dynamické disky během procesu odesílání do Azure.
> 
> 


* Ujistěte se, že máte nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login).

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Názvy parametrů příklad zahrnuté *myResourceGroup*, *můj_účet_úložiště*, a *mydisks*.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Příprava virtuálního počítače

Azure podporuje různé Linuxových distribucích (viz [distribuce schválené](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Postup přípravy různé distribuce systému Linux, které jsou podporovány v Azure vás provede v následujících článcích:

* [Na základě centOS distribuce](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Další - neschválené distribuce](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Viz také [poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Příprava bitové kopie systému Linux na Azure další Obecné tipy pro.

> [!NOTE]
> [Platformy Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se vztahují na virtuální počítače se systémem Linux jenom v případě, že jeden z potvrzená distribuce se používá s podrobností konfigurace uvedeného v části "podporované verze [Linux na Azure-Endorsed distribuce](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Možnost 1: Nahrání virtuálního pevného disku

Můžete nahrát vlastní virtuální pevný disk, ke které máte spuštěné v místním počítači nebo který jste exportovali z jiného cloudu. Chcete-li použít virtuální pevný disk k vytvoření nového virtuálního počítače Azure, potřebujete nahrání virtuálního pevného disku do účtu úložiště a vytvoření spravovaného disku z virtuálního pevného disku. 

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Před nahráním váš vlastní disk a vytváření virtuálních počítačů, musíte nejprve vytvořit skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az_group_create).

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *eastus* umístění: [disky spravované Azure – přehled](../windows/managed-disks-overview.md)
```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvoření účtu úložiště vlastní disk a virtuální počítače s [vytvořit účet úložiště az](/cli/azure/storage/account#az_storage_account_create). 

Následující příklad vytvoří účet úložiště s názvem *můj_účet_úložiště* ve skupině prostředků vytvořili:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Vypsat klíče účtu úložiště
Vygeneruje Azure dva 512bitové přístupové klíče pro každý účet úložiště. Tyto přístupové klíče se používají při ověřování k účtu úložiště, jako je provádění operace zápisu. Další informace o [Správa přístupu k úložišti zde](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Zobrazit přístupové klíče s [seznam klíčů účtu úložiště az](/cli/azure/storage/account/keys#az_storage_account_keys_list).

Zobrazte přístupové klíče pro účet úložiště, který jste vytvořili:

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
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
Poznamenejte si **key1** jako použije k interakci se svým účtem úložiště v dalších krocích.

### <a name="create-a-storage-container"></a>Vytvoření kontejneru úložiště
V stejným způsobem, který vytvoříte různé adresáře, které logicky uspořádat do místního systému souborů můžete vytvořit kontejnery v účtu úložiště pro uspořádání vaše disky. Účet úložiště může obsahovat libovolný počet kontejnerů. Vytvořit kontejner s [vytvořit kontejner úložiště az](/cli/azure/storage/container#az_storage_container_create).

Následující příklad vytvoří kontejner s názvem *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Nahrání virtuálního pevného disku
Teď nahrát vlastní disk s [az úložiště objektů blob nahrávání](/cli/azure/storage/blob#az_storage_blob_upload). Nahrání a ukládat vlastní disk jako objekt blob stránky.

Zadejte přístupový klíč, kontejner, který jste vytvořili v předchozím kroku a pak cestu k vlastní disku v místním počítači:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
Nahrání virtuálního pevného disku, může chvíli trvat.

### <a name="create-a-managed-disk"></a>Vytvoření spravovaného disku


Vytvoření spravovaného disku z virtuálního pevného disku pomocí [vytvoření disku az](/cli/azure/disk#az_disk_create). Následující příklad vytvoří spravované disk s názvem *myManagedDisk* z virtuálního pevného disku, který jste nahráli k účtu s názvem úložiště a kontejneru:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Možnost 2: Zkopírujte existující virtuální počítač

Můžete také vytvořit vlastní virtuální počítač v Azure a pak zkopírujte disk operačního systému a připojte ji k vytvoření nového virtuálního počítače vytvořit další kopii. To je v pořádku pro testování, ale pokud chcete použít existující virtuální počítač Azure jako model pro více nové virtuální počítače, ve skutečnosti měli byste vytvořit **image** místo. Další informace o vytvoření bitové kopie ze stávajícího virtuálního počítače Azure najdete v tématu [vytvořit vlastní image virtuálního počítače Azure pomocí rozhraní příkazového řádku](tutorial-custom-images.md)

### <a name="create-a-snapshot"></a>Vytvoření snímku

Tento příklad vytvoří snímek virtuálního počítače s názvem *Můjvp* ve skupině prostředků *myResourceGroup* a vytvoří snímek s názvem *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Vytvoření spravovaného disku

Vytvoření nového spravovaného disku ze snímku.

Získáte Identifikátor snímku. V tomto příkladu je název snímku *osDiskSnapshot* a je v *myResourceGroup* skupinu prostředků.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Vytvoření spravovaného disku. V tomto příkladu vytvoříme spravované disk s názvem *myManagedDisk* ze naše snímek, který je 128 GB velikost ve standardní úložiště.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Teď vytvořte virtuální počítač s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) a připojte (--připojit disk operačního systému) spravovaných disk jako disk operačního systému. Následující příklad vytvoří virtuální počítač s názvem *myNewVM* pomocí spravovaného disku vytvořené z vaší nahraný virtuální pevný disk:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Nyní byste měli mít k SSH do virtuálního počítače pomocí přihlašovacích údajů, ze zdrojového virtuálního počítače. 

## <a name="next-steps"></a>Další postup
Po připravené a nahrát vlastní virtuální disk, si můžete přečíst více o [pomocí Resource Manageru a šablony](../../azure-resource-manager/resource-group-overview.md). Můžete také chtít [přidat datový disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) na nové virtuální počítače. Pokud máte aplikace spuštěné na vaše virtuální počítače, které potřebujete získat přístup, je nutné [otevřete porty a koncové body](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

