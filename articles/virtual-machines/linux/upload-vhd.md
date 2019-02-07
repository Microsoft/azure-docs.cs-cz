---
title: Odeslání nebo zkopírovat vlastní virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Uložit nebo zkopírovat vlastní virtuální počítač s použitím modelu nasazení Resource Manager a Azure CLI
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
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: edebd2efda7d8217c9b554f2d1f54135203502cb
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821554"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Vytvoření virtuálního počítače s Linuxem z vlastního disku pomocí rozhraní příkazového řádku Azure

<!-- rename to create-vm-specialized -->

V tomto článku se dozvíte, jak nahrát vlastní virtuální pevný disk (VHD) a jak kopírovat existujícího virtuálního pevného disku v Azure. Nově vytvořený virtuální pevný disk se pak použije k vytvoření nové virtuální počítače Linux (VM). Můžete nainstalovat a nakonfigurovat distribuce Linuxu svých požadavků a pak tento virtuální pevný disk můžete vytvořit nový virtuální počítač Azure.

K vytvoření několika virtuálních počítačů z vlastní disku, nejprve vytvořte image z virtuálního počítače nebo virtuální pevný disk. Další informace najdete v tématu [vytvořit vlastní image virtuálního počítače Azure pomocí rozhraní příkazového řádku](tutorial-custom-images.md).

Máte dvě možnosti, jak vytvořit vlastní disk:
* Nahrání virtuálního pevného disku
* Zkopírovat existující virtuální počítač Azure

## <a name="quick-commands"></a>Rychlé příkazy

Při vytváření nového virtuálního počítače s [vytvořit az vm](/cli/azure/vm#az-vm-create) z přizpůsobené nebo specializovaného disku, můžete **připojit** disku (– připojit disk operačního systému) místo zadávání vlastního prostředku nebo marketplace image (--bitové kopie). Následující příklad vytvoří virtuální počítač s názvem *myVM* použití spravovaného disku s názvem *myManagedDisk* vytvořen z vlastní virtuální pevné disky:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Požadavky
Chcete-li provést následující kroky, budete potřebovat:

* Virtuální počítač s Linuxem, která byla připravena pro použití v Azure. [Počítač připravit tak](#prepare-the-vm) části tohoto článku popisuje, jak najít distribuce konkrétní informace o instalaci agenta Azure Linux (waagent), která je potřebná pro připojení k virtuálnímu počítači pomocí protokolu SSH.
* Soubor virtuálního pevného disku z existující [distribucí Linuxu schválených pro Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (nebo se podívejte [informace pro neschválené distribuce](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) na virtuální disk ve formátu virtuálního pevného disku. Existují více nástroje k vytvoření virtuálního počítače a virtuálního pevného disku:
  * Instalace a konfigurace [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) nebo [KVM](http://www.linux-kvm.org/page/RunningKVM), nezapomeňte použít virtuální pevný disk jako formát obrázku. V případě potřeby můžete [převedení obrázku](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) s `qemu-img convert`.
  * Můžete také použít technologie Hyper-V [ve Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) nebo [v systému Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Novější formát VHDX nepodporuje v Azure. Při vytváření virtuálního počítače, zadejte jako formát virtuálního pevného disku. V případě potřeby můžete převést disky VHDX na VHD s [převést qemu img](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) nebo [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) rutiny Powershellu. Azure nepodporuje odesílání dynamických virtuálních pevných disků, takže budete muset převést těmito disky na statické virtuální pevné disky, před nahráním. Můžete použít nástroje jako [virtuálního pevného disku nástroje Azure pro GO](https://github.com/Microsoft/azure-vhd-utils-for-go) pro převod dynamických disků během procesu nahrávání do Azure.
> 
> 


* Ujistěte se, že máte nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a jste přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index#az-login).

V následujících příkladech, příklad parametr názvy nahraďte vlastními hodnotami, jako *myResourceGroup*, *mystorageaccount*, a *mydisks*.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Příprava virtuálního počítače

Azure podporuje distribuce Linuxu (viz [distribuce schválené pro](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Následující články popisují, jak připravit různých Linuxových distribucí, které jsou podporovány v Azure:

* [Distribuce založené na centOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ostatní: Neschválené distribuce](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Viz také [poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) pro další Obecné tipy pro Příprava imagí Linuxu na Azure.

> [!NOTE]
> [Platformy Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) platí pro virtuální počítače s Linuxem, pouze v případě, že jednu z doporučených distribucích se používá s podrobnosti o konfiguraci, jak je uvedeno v části "Podporované verze" [Linux na schválené pro Azure Distribuce](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Option 1: Nahrání virtuálního pevného disku

Můžete nahrát vlastní virtuální pevný disk, které máte spuštěné na místním počítači nebo které jste vyexportovali z jiného cloudu. Pokud chcete použít virtuální pevný disk k vytvoření nového virtuálního počítače Azure, budete potřebovat k nahrání virtuálního pevného disku do účtu úložiště a vytvoření spravovaného disku z virtuálního pevného disku. Další informace najdete v tématu [Přehled služby Azure Managed Disks](../windows/managed-disks-overview.md).

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Před nahráním vlastního disku a vytvoření virtuálních počítačů, je potřeba vytvořit skupinu prostředků pomocí [vytvořit skupiny az](/cli/azure/group#az-group-create).

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvoření účtu úložiště pro váš vlastní disk a virtuální počítače s [vytvořit účet úložiště az](/cli/azure/storage/account). Následující příklad vytvoří účet úložiště s názvem *mystorageaccount* ve skupině prostředků vytvořili dříve:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Vypsat klíče účtu úložiště
Vygeneruje Azure dva 512bitové přístupové klíče pro každý účet úložiště. Tyto přístupové klíče se používají při ověřování k účtu úložiště, jako je například při provádění operace zápisu. Další informace najdete v tématu [Správa přístupu k úložišti](../../storage/common/storage-account-manage.md#access-keys). 

Zobrazení přístupových klíčů pomocí [seznamu klíčů účtu úložiště az](/cli/azure/storage/account/keys#az-storage-account-keys-list). Například k zobrazení přístupových klíčů pro ukládání účtu, který jste vytvořili:

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
Poznamenejte si **key1** protože budete ho používat k interakci se svým účtem úložiště v dalších krocích.

### <a name="create-a-storage-container"></a>Vytvoření kontejneru úložiště
Stejným způsobem, který vytvoříte různým adresářům logicky tak uspořádat vašeho místního systému souborů vytvoříte kontejnery v rámci účtu úložiště pro uspořádání disků. Účet úložiště může obsahovat mnoho kontejnerů. Vytvořte kontejner s [vytvořit kontejner úložiště az](/cli/azure/storage/container#az-storage-container-create).

Následující příklad vytvoří kontejner s názvem *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Nahrání virtuálního pevného disku
Nahrání vlastního disku pomocí [az storage blob nahrávání](/cli/azure/storage/blob#az-storage-blob-upload). Budete nahrávat a ukládat vlastní disk jako objekt blob stránky.

Zadejte přístupový klíč, kontejner, který jste vytvořili v předchozím kroku a cesta k disku vlastní v místním počítači:

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


Vytvoření spravovaného disku z virtuálního pevného disku s [az disk vytvořit](/cli/azure/disk#az-disk-create). Následující příklad vytvoří spravovaný disk s názvem *myManagedDisk* z virtuálního pevného disku jste nahráli do účtu s názvem úložiště a kontejneru:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Option 2: Zkopírovat existující virtuální počítač

Můžete také vytvořit vlastní virtuální počítač v Azure a potom zkopírujte disk s operačním systémem a připojí nový virtuální počítač vytvořit další kopii. To je v pořádku pro testování, ale pokud chcete používat existujícího virtuálního počítače Azure jako model pro více nových virtuálních počítačů, vytvořte *image* místo. Další informace o vytváření image z existujícího virtuálního počítače Azure najdete v tématu [vytvořit vlastní image virtuálního počítače Azure pomocí rozhraní příkazového řádku](tutorial-custom-images.md).

### <a name="create-a-snapshot"></a>Vytvoření snímku

Tento příklad vytvoří snímek virtuálního počítače s názvem *myVM* ve skupině prostředků *myResourceGroup* a vytvoří snímek s názvem *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Vytvoření spravovaného disku

Vytvoření nového spravovaného disku ze snímku.

Získejte ID snímku. V tomto příkladu je název snímku *osDiskSnapshot* a má *myResourceGroup* skupinu prostředků.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Vytvoření spravovaného disku. V tomto příkladu vytvoříme spravovaný disk s názvem *myManagedDisk* z našich snímku, ve kterém disk je ve standardním úložišti a velikost na 128 GB.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvoření virtuálního počítače s [az vm vytvořit](/cli/azure/vm#az-vm-create) a připojení (– připojení disku operačního systému) spravovaného disku jako disku s operačním systémem. Následující příklad vytvoří virtuální počítač s názvem *myNewVM* použití spravovaného disku, který jste vytvořili z nahraný virtuální pevný disk:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Byste měli k SSH k virtuálnímu počítači pomocí přihlašovacích údajů ze zdrojového virtuálního počítače. 

## <a name="next-steps"></a>Další postup
Jakmile připraven a nahrát vlastní virtuální disk, si můžete přečíst více o [pomocí Resource Manageru a šablony](../../azure-resource-manager/resource-group-overview.md). Můžete také chtít [přidat datový disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) na nové virtuální počítače. Pokud máte aplikace běžící na virtuálních počítačích, které potřebujete získat přístup, je potřeba [otevření portů a koncových bodů](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
