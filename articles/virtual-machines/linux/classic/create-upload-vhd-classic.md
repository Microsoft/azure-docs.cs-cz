---
title: Vytvoření a nahrání linuxového virtuálního pevného disku do Azure | Dokumentace Microsoftu
description: Vytvoření a nahrání Azure virtuálního pevného disku (VHD), který obsahuje operační systém Linux pomocí modelu nasazení Classic
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 8058ff98-db03-4309-9bf4-69842bd64dd4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: cynthn
ms.openlocfilehash: cdbe6aa5683ecf9d8bdaf6bbf9503ddc455f03ee
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928263"
---
# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Vytvoření a nahrání virtuálního pevného disku obsahujícího operační systém Linux
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Můžete také [nahrát vlastní imagí disku pomocí Azure Resource Manageru](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Tento článek ukazuje postup vytvoření a nahrání virtuálního pevného disku (VHD), takže se použije jako vlastní image k vytvoření virtuálních počítačů v Azure. Zjistěte, jak připravit operační systém, ve kterém můžete vytvořit několik virtuálních počítačů na základě této image. 


## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte následující položky:

* **Linux operační systém nainstalovaný na soubor VHD** – jste si nainstalovali [distribucí Linuxu schválených pro Azure](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (nebo se podívejte [informace pro neschválené distribuce](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) na virtuální disk v Formát virtuálního pevného disku. Existují více nástroje k vytvoření virtuálního počítače a virtuálního pevného disku:
  * Instalace a konfigurace [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) nebo [KVM](http://www.linux-kvm.org/page/RunningKVM), nezapomeňte použít virtuální pevný disk jako formát obrázku. V případě potřeby můžete [převedení obrázku](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) pomocí `qemu-img convert`.
  * Můžete také použít technologie Hyper-V [ve Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) nebo [v systému Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Novější formát VHDX nepodporuje v Azure. Při vytváření virtuálního počítače, zadejte jako formát virtuálního pevného disku. V případě potřeby můžete převést disky VHDX na virtuální pevný disk pomocí [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) nebo [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) rutiny Powershellu. Kromě toho Azure nepodporuje odesílání dynamických virtuálních pevných disků, takže budete muset převést těmito disky na statické virtuální pevné disky, před nahráním. Můžete použít nástroje jako [virtuálního pevného disku nástroje Azure pro GO](https://github.com/Microsoft/azure-vhd-utils-for-go) pro převod dynamických disků během procesu nahrávání do Azure.

* **Rozhraní příkazového řádku Azure** – nainstalujte si nejnovější [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) k nahrání virtuálního pevného disku.

<a id="prepimage"> </a>

## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Krok 1: Příprava k nahrání obrázku
Azure podporuje distribuce Linuxu (viz [distribuce schválené pro](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Následující články vás provedou postup přípravy různých Linuxových distribucí, které jsou podporovány v Azure. Po dokončení kroků v následujících příručkách, vraťte se sem až budete mít soubor virtuálního pevného disku, který je připravený k nahrání do Azure:

* **[Distribuce založené na centOS](../create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](../debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](../oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](../redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](../suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](../create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Jiné – neschválené distribuce](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

> [!NOTE]
> Platforma Azure SLA se vztahuje na virtuální počítače s operačním systémem Linux pouze v případě, že jednu z doporučených distribucích se používá s konfigurací podrobnosti uvedené v části "podporované verze v [Linux v distribucích Azure-Endorsed](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Všechny distribucí systému Linux v galerii imagí Azure jsou doporučených distribucích s požadovanou konfigurací.
> 
> 

Viz také **[poznámky k instalaci Linux](../create-upload-generic.md#general-linux-installation-notes)** pro další Obecné tipy pro Příprava imagí Linuxu na Azure.

<a id="connect"> </a>

## <a name="step-2-prepare-the-connection-to-azure"></a>Krok 2: Příprava připojení k Azure
Ujistěte se, že používáte rozhraní příkazového řádku Azure v modelu nasazení classic (`azure config mode asm`), pak se přihlaste ke svému účtu:

```azurecli
azure login
```


<a id="upload"> </a>

## <a name="step-3-upload-the-image-to-azure"></a>Krok 3: Odešlete image do Azure
Budete potřebovat k odeslání souboru VHD do účtu úložiště. Můžete buď vybrat existující účet úložiště nebo [vytvořte novou](../../../storage/common/storage-create-storage-account.md).

Pomocí Azure CLI pro nahrání image pomocí následujícího příkazu:

```azurecli
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

V předchozím příkladu:

* **BlobStorageURL** je adresa URL pro účet úložiště plánujete použít
* **YourImagesFolder** je kontejner v úložišti objektů blob, kam chcete uložit vaše Image
* **VHDName** je popisek, který se zobrazí na portálu pro identifikaci virtuálního pevného disku.
* **PathToVHDFile** je úplná cesta a název souboru VHD na svém počítači.

Následující příkaz zobrazí úplný příklad:

```azurecli
azure vm image create myImage `
    --blob-url https://mystorage.blob.core.windows.net/vhds/myimage.vhd `
    --os Linux /home/ahmet/myimage.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Krok 4: Vytvoření virtuálního počítače z image
Vytvoření virtuálního počítače pomocí `azure vm create` stejným způsobem jako regulární virtuálního počítače. Zadejte název, který jste přiřadili bitové kopie v předchozím kroku. V následujícím příkladu vytvoříme s využitím **myImage** název image, které jsou uvedeny v předchozím kroku:

```azurecli
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "myDeployedVM" myImage
```

Pokud chcete vytvořit vlastní virtuální počítače, zadejte vlastní uživatelské jméno a heslo, umístění, název DNS a název image.

## <a name="next-steps"></a>Další postup
Další informace najdete v tématu [referenční informace k Azure CLI pro model nasazení Azure classic](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

[Step 1: Prepare the image to be uploaded]:#prepimage
[Step 2: Prepare the connection to Azure]:#connect
[Step 3: Upload the image to Azure]:#upload
