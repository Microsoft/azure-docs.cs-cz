---
title: Nahrání nebo zkopírování vlastního virtuálního počítače se systémem Linux pomocí Azure CLI
description: Nahrání nebo zkopírování vlastního virtuálního počítače pomocí modelu nasazení Správce prostředků a Azure CLI
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 9d549d77b4a60f7543f69a9fd89e8b538c95d010
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564607"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Vytvoření virtuálního počítače se systémem Linux z vlastního disku pomocí Azure CLI

<!-- rename to create-vm-specialized -->

V tomto článku se dozvíte, jak nahrát přizpůsobený virtuální pevný disk (VHD) a jak zkopírovat existující VHD do Azure. Nově vytvořený virtuální pevný disk se pak použije k vytvoření nových virtuálních počítačů (VM) pro Linux. Distribuce pro Linux můžete nainstalovat a nakonfigurovat podle svých požadavků a pak pomocí tohoto virtuálního pevného disku vytvořit nový virtuální počítač Azure.

Pokud chcete vytvořit víc virtuálních počítačů z přizpůsobeného disku, nejdřív vytvořte image z virtuálního počítače nebo virtuálního pevného disku. Další informace najdete v tématu [Vytvoření vlastní image virtuálního počítače Azure pomocí rozhraní](tutorial-custom-images.md)příkazového řádku (CLI).

Máte dvě možnosti, jak vytvořit vlastní disk:
* Nahrání virtuálního pevného disku
* Kopírovat existující virtuální počítač Azure


## <a name="requirements"></a>Požadavky
K provedení následujících kroků budete potřebovat:

- Virtuální počítač se systémem Linux připravený pro použití v Azure. Část [Příprava virtuálního počítače](#prepare-the-vm) v tomto článku popisuje, jak najít informace specifické pro distribuce k instalaci agenta Azure Linux (waagent), který je potřeba pro připojení k virtuálnímu počítači pomocí SSH.
- Soubor VHD z existující distribuce systému [Linux schváleného službou Azure](endorsed-distros.md) (nebo zobrazit [informace o neschválených distribucích](create-upload-generic.md)) na virtuální disk ve formátu VHD. Existuje několik nástrojů, které slouží k vytvoření virtuálního počítače a virtuálního pevného disku:
  - Nainstalujte a nakonfigurujte [qemu](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) nebo [KVM](https://www.linux-kvm.org/page/RunningKVM), přičemž se ujistěte, že jako formát obrázku použijete VHD. V případě potřeby můžete [obrázek převést](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) pomocí `qemu-img convert` .
  - Můžete také použít Hyper-V [ve Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) nebo [Windows Serveru 2012/2012 R2](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

> [!NOTE]
> Novější formát VHDX se v Azure nepodporuje. Když vytváříte virtuální počítač, zadejte jako formát VHD. V případě potřeby můžete disky VHDX převést na VHD pomocí rutiny Convert- [img qemu-img](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) nebo rutiny [Convert-VHD](/powershell/module/hyper-v/convert-vhd) prostředí PowerShell. Azure nepodporuje nahrávání dynamických virtuálních pevných disků, takže před odesláním budete muset tyto disky převést na statické virtuální pevné disky. Pomocí nástrojů, jako jsou například [nástroje Azure VHD](https://github.com/Microsoft/azure-vhd-utils-for-go) Tools, můžete na cestách převést dynamické disky během procesu jejich nahrávání do Azure.
> 
> 


- Ujistěte se, že máte nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a jste přihlášení k účtu Azure pomocí [AZ Login](/cli/azure/reference-index#az-login).

V následujících příkladech nahraďte příklady názvů parametrů vlastními hodnotami, například, `myResourceGroup` `mystorageaccount` a `mydisks` .

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Příprava virtuálního počítače

Azure podporuje různé distribuce systému Linux (viz [schválené distribuce](endorsed-distros.md)). Následující články popisují, jak připravit různé distribuce systému Linux podporované v Azure:

* [Distribuce založené na CentOS](create-upload-centos.md)
* [Debian Linux](debian-create-upload-vhd.md)
* [Oracle Linux](oracle-create-upload-vhd.md)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
* [SLES a openSUSE](suse-create-upload-vhd.md)
* [Ubuntu](create-upload-ubuntu.md)
* [Ostatní: neschválené distribuce](create-upload-generic.md)

Další obecné tipy k přípravě imagí pro Linux pro Azure najdete také v [poznámkách k instalaci systému Linux](create-upload-generic.md#general-linux-installation-notes) .

> [!NOTE]
> [Smlouva SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se vztahuje na virtuální počítače se systémem Linux jenom v případě, že se jedno z označených distribucí používá s podrobnostmi konfigurace, jak je uvedeno v části podporované verze v [linuxech v Azure-Endorsedch distribucích](endorsed-distros.md).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Možnost 1: nahrání VHD

Virtuální pevný disk teď můžete nahrát přímo do spravovaného disku. Pokyny najdete v tématu [nahrání virtuálního pevného disku do Azure pomocí rozhraní příkazového řádku Azure CLI](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>Možnost 2: Zkopírování existujícího virtuálního počítače

Můžete také vytvořit přizpůsobený virtuální počítač v Azure a potom zkopírovat disk s operačním systémem a připojit ho k novému virtuálnímu počítači a vytvořit další kopii. To je pro testování jemné, ale pokud chcete použít existující virtuální počítač Azure jako model pro více nových virtuálních počítačů, vytvořte místo toho *Obrázek* . Další informace o vytvoření image z existujícího virtuálního počítače Azure najdete v tématu [Vytvoření vlastní image virtuálního počítače Azure pomocí rozhraní](tutorial-custom-images.md)příkazového řádku (CLI).

Pokud chcete zkopírovat existující virtuální počítač do jiné oblasti, můžete použít AzCopy k [Vytvoření kopie disku v jiné oblasti](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk). 

V opačném případě byste měli pořídit snímek virtuálního počítače a pak ze snímku vytvořit nový virtuální pevný disk s operačním systémem.

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

Z snímku vytvořte nový spravovaný disk.

Získá ID snímku. V tomto příkladu má snímek název *osDiskSnapshot* a je ve skupině prostředků *myResourceGroup* .

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Vytvořte spravovaný disk. V tomto příkladu vytvoříme z našeho snímku spravovaný disk s názvem *myManagedDisk* , kde se disk nachází ve standardním úložišti a má velikost na 128 GB.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí [AZ VM Create](/cli/azure/vm#az-vm-create) a Attach (--Attach-OS-disk) spravovaného disku jako disku s operačním systémem. Následující příklad vytvoří virtuální počítač s názvem *myNewVM* pomocí spravovaného disku, který jste vytvořili z nahraného VHD:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Měli byste být schopni se k virtuálnímu počítači přihlásit přes SSH pomocí přihlašovacích údajů ze zdrojového virtuálního počítače. 

## <a name="next-steps"></a>Další kroky
Po přípravě a nahrání vlastního virtuálního disku si můžete přečíst další informace o [používání Správce prostředků a šablon](../../azure-resource-manager/management/overview.md). Na nové virtuální počítače můžete také [přidat datový disk](add-disk.md) . Pokud máte aplikace spuštěné na virtuálních počítačích, ke kterým potřebujete přístup, nezapomeňte [otevřít porty a koncové body](nsg-quickstart.md).
