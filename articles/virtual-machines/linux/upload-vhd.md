---
title: Nahrání nebo zkopírování vlastního virtuálního počítače s Linuxem pomocí azure CLI
description: Nahrání nebo zkopírování přizpůsobeného virtuálního počítače pomocí modelu nasazení Správce prostředků a nastavení příkazového příkazu Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 3306647078c46a7c66b3d7b257b213c7a48e690d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460422"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Vytvoření virtuálního počítače s Linuxem z vlastního disku pomocí azure cli

<!-- rename to create-vm-specialized -->

Tento článek ukazuje, jak nahrát vlastní virtuální pevný disk (VHD) a jak zkopírovat existující virtuální pevný disk v Azure. Nově vytvořený virtuální pevný disk se pak používá k vytvoření nových virtuálních počítačů linuxu. Můžete nainstalovat a nakonfigurovat distro Linuxu podle vašich požadavků a pak použít tento virtuální pevný disk k vytvoření nového virtuálního počítače Azure.

Pokud chcete vytvořit více virtuálních počítačů z přizpůsobeného disku, nejdřív vytvořte image z virtuálního počítače nebo virtuálního počítače. Další informace najdete [v tématu vytvoření vlastní image virtuálního počítače Azure pomocí příkazového příkazového příkazu](tutorial-custom-images.md).

Máte dvě možnosti vytvoření vlastního disku:
* Nahrání virtuálního pevného disku
* Kopírování existujícího virtuálního počítače Azure


## <a name="requirements"></a>Požadavky
Chcete-li provést následující kroky, budete potřebovat:

- Virtuální počítač s Linuxem, který byl připravený pro použití v Azure. [Připravit virtuální počítač](#prepare-the-vm) části tohoto článku popisuje, jak najít informace specifické pro distro na instalaci Agent Azure Linux (waagent), který je potřeba pro připojení k virtuálnímu počítači s SSH.
- Soubor VHD z existující [distribuce Linuxu schválenou Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (nebo viz [informace pro neschválené distribuce)](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)na virtuální disk ve formátu Virtuálního pevného disku. Existuje více nástrojů pro vytvoření virtuálního a virtuálního pevného disku:
  - Nainstalujte a nakonfigurujte [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) nebo [KVM](https://www.linux-kvm.org/page/RunningKVM)a dávejte pozor na použití virtuálního pevného disku jako obrazového formátu. V případě potřeby můžete [převést obrázek](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) pomocí aplikace `qemu-img convert`.
  - Hyper-V můžete taky používat [ve Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) nebo [windows server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Novější formát VHDX není v Azure podporován. Při vytváření virtuálního virtuálního montu zadejte jako formát virtuální pevný disk. V případě potřeby můžete převést disky VHDX na vhd pomocí [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) nebo rutiny [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell. Azure nepodporuje nahrávání dynamických virtuálních pevných disků, takže budete muset tyto disky před nahráním převést na statické virtuální disky. Pomocí nástrojů, jako jsou [nástroje Virtuálního pevného disku Azure pro GO,](https://github.com/Microsoft/azure-vhd-utils-for-go) můžete použít k převodu dynamických disků během procesu jejich nahrávání do Azure.
> 
> 


- Ujistěte se, že máte nainstalovaný nejnovější [Azure CLI](/cli/azure/install-az-cli2) a jste přihlášeni k účtu Azure s [az přihlášení](/cli/azure/reference-index#az-login).

V následujících příkladech nahraďte názvy příkladů `myResourceGroup`vlastními hodnotami, například , `mystorageaccount`a `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Příprava virtuálního počítače

Azure podporuje různé distribuce Linuxu (viz [Schválené distribuce).](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Následující články popisují, jak připravit různé distribuce Linuxu, které jsou podporované v Azure:

* [Distribuce založené na CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES a openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ostatní: Neschválené distribuce](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Další obecné tipy na přípravu ibiz linuxových iobrazů pro Azure najdete také v [poznámkách](create-upload-generic.md#general-linux-installation-notes) k instalaci Linuxu.

> [!NOTE]
> Smlouva [SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se vztahuje na virtuální počítače se systémem Linux pouze v případě, že se jedna z schválených distribucí používá s podrobnostmi o konfiguraci, jak je uvedeno v části "Podporované verze" v Linuxu v [distribucích s podporou Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Možnost 1: Nahrání virtuálního pevného disku

Nyní můžete nahrát virtuální pevný disk přímo na spravovaný disk. Pokyny najdete [v tématu Upload a VHD to Azure using Azure CLI](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>Možnost 2: Kopírování existujícího virtuálního virtuálního montova.

Můžete také vytvořit vlastní virtuální počítač v Azure a pak zkopírovat disk operačního systému a připojit jej k novému virtuálnímu počítači k vytvoření další kopie. To je v pořádku pro testování, ale pokud chcete použít existující virtuální počítač Azure jako model pro více nových virtuálních počítačů, místo toho *vytvořte image.* Další informace o vytvoření image z existujícího virtuálního počítače Azure najdete [v tématu vytvoření vlastní image virtuálního počítače Azure pomocí příkazového příkazu k příkazu .](tutorial-custom-images.md)

Pokud chcete zkopírovat existující virtuální hod do jiné oblasti, můžete použít azkopii k [vytvoření kopie disku v jiné oblasti](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk). 

V opačném případě byste měli pořídit snímek virtuálního počítače a potom vytvořit nový virtuální pevný disk operačního systému ze snímku.

### <a name="create-a-snapshot"></a>Vytvoření snímku

Tento příklad vytvoří snímek virtuálního počítače s názvem *myVM* ve skupině prostředků *myResourceGroup* a vytvoří snímek s názvem *osDiskSnapshot*.

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Vytvoření spravovaného disku

Vytvořte nový spravovaný disk ze snímku.

Získat ID snímku. V tomto příkladu je snímek pojmenován *osDiskSnapshot* a je ve skupině prostředků *myResourceGroup.*

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Vytvořte spravovaný disk. V tomto příkladu vytvoříme spravovaný disk s názvem *myManagedDisk* z našeho snímku, kde je disk ve standardním úložišti a má velikost 128 GB.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvořte virtuální počítač pomocí [az vm vytvořit](/cli/azure/vm#az-vm-create) a připojit (--attach-os-disk) spravovaný disk jako disk operačního systému. Následující příklad vytvoří virtuální počítač s názvem *myNewVM* pomocí spravovaného disku, který jste vytvořili z nahraného virtuálního pevného disku:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Měli byste být schopni SSH do virtuálního virtuálního mísy s pověřeními ze zdrojového virtuálního virtuálního mísy. 

## <a name="next-steps"></a>Další kroky
Po přípravě a nahrání vlastního virtuálního disku si můžete přečíst další informace o [použití Správce prostředků a šablon](../../azure-resource-manager/management/overview.md). Můžete také přidat [datový disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) do nových virtuálních počítačů. Pokud máte aplikace spuštěné na virtuálních počítačích, ke kterým potřebujete přístup, nezapomeňte [otevřít porty a koncové body](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
