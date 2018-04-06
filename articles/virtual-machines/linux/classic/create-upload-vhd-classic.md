---
title: Vytvoření a nahrání virtuálního pevného disku Linux do Azure | Microsoft Docs
description: Vytvoření a odeslání Azure virtuálního pevného disku (VHD) obsahující operační systém Linux pomocí modelu nasazení Classic
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 1ba568eeaf3bbc3d786cc48e54404aa65a00fecc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Vytvoření a nahrání virtuálního pevného disku obsahujícího operační systém Linux
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Můžete také [nahrajte image vlastní disku pomocí Azure Resource Manager](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Tento článek ukazuje, jak vytvořit a odeslat virtuální pevný disk (VHD), můžete ho použít jako vlastní image pro vytváření virtuálních počítačů v Azure. Zjistěte, jak připravit operační systém, ve kterém můžete vytvořit více virtuálních počítačů na základě této bitové kopie. 


## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte následující položky:

* **Operační systém Linux nainstalován v souboru VHD** -instalaci [distribuce schválené pro Azure Linux](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (nebo v tématu [informace pro neschválené distribuce](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) na virtuální disk v Formát virtuálního pevného disku. Existuje několik nástrojů k vytvoření virtuálního počítače a virtuálního pevného disku:
  * Instalace a konfigurace [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) nebo [KVM](http://www.linux-kvm.org/page/RunningKVM), aby byl používáte formát bitové kopie virtuálního pevného disku. V případě potřeby můžete [převést bitovou kopii](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) pomocí `qemu-img convert`.
  * Můžete také použít technologie Hyper-V [ve Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) nebo [v systému Windows Server 2012 nebo 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Novější formát VHDX není podporovaný v Azure. Když vytvoříte virtuální počítač, zadejte jako formát virtuálního pevného disku. V případě potřeby můžete převést disků VHDX virtuální pevný disk pomocí [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) nebo [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) rutiny prostředí PowerShell. Navíc Azure nepodporuje odesílání dynamické virtuální pevné disky, takže je nutné převést tyto disky na virtuální pevné disky statické před nahráním. Můžete použít nástroje, jako [nástroje Azure virtuálního pevného disku pro přejděte](https://github.com/Microsoft/azure-vhd-utils-for-go) převést dynamické disky během procesu odesílání do Azure.

* **Rozhraní příkazového řádku Azure** – nainstalujte si nejnovější verzi [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) odeslání disku VHD.

<a id="prepimage"> </a>

## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Krok 1: Příprava bitové kopie k odeslání
Azure podporuje různé Linuxových distribucích (viz [distribuce schválené](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). V následujících článcích vás provede postup přípravy různé distribuce systému Linux, které jsou podporovány v Azure. Po dokončení kroků v následujících pokynech se vraťte se sem, až budete mít soubor virtuálního pevného disku, který je připraven k nahrání do Azure:

* **[Na základě centOS distribuce](../create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](../debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](../oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](../redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](../suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](../create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Další - neschválené distribuce](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

> [!NOTE]
> Platformy Azure SLA se vztahuje na virtuální počítače běžící operační systém Linux jenom v případě, že jeden z potvrzená distribuce se používá s konfigurací podrobnosti uvedeného v části "podporované verze v [Linux na Azure-Endorsed distribuce](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Všechny Linuxových distribucích v galerii Azure bitové kopie jsou potvrzená distribuce s požadovanou konfigurací.
> 
> 

Viz také **[poznámky k instalaci Linux](../create-upload-generic.md#general-linux-installation-notes)** Příprava bitové kopie systému Linux na Azure další Obecné tipy pro.

<a id="connect"> </a>

## <a name="step-2-prepare-the-connection-to-azure"></a>Krok 2: Příprava připojení k Azure
Zajistěte, aby se pomocí rozhraní příkazového řádku Azure v modelu nasazení classic (`azure config mode asm`), pak se přihlaste k účtu:

```azurecli
azure login
```


<a id="upload"> </a>

## <a name="step-3-upload-the-image-to-azure"></a>Krok 3: Nahrát na server bitovou kopii do Azure
Musíte nahrát váš soubor virtuálního pevného disku do účtu úložiště. Můžete vybrat buď existující účet úložiště nebo [vytvořte novou](../../../storage/common/storage-create-storage-account.md).

Pomocí rozhraní příkazového řádku Azure CLI pro nahrání bitovou kopii pomocí následujícího příkazu:

```azurecli
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

V předchozím příkladu:

* **BlobStorageURL** je adresa URL pro účet úložiště, kterou plánujete použít
* **YourImagesFolder** je kontejneru v úložiště objektů blob, kam chcete uložit obrázků
* **VHDName** je štítek, který se zobrazí na portálu pro identifikaci virtuální pevný disk.
* **PathToVHDFile** je úplná cesta a název souboru VHD na váš počítač.

Následující příkaz zobrazí úplný příklad:

```azurecli
azure vm image create myImage `
    --blob-url https://mystorage.blob.core.windows.net/vhds/myimage.vhd `
    --os Linux /home/ahmet/myimage.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Krok 4: Vytvořte virtuální počítač z bitové kopie
Vytvoříte virtuální počítač pomocí `azure vm create` stejným způsobem jako regulární virtuální počítač. Zadejte název bitové kopie jste zadali v předchozím kroku. V následujícím příkladu použijeme **myImage** název bitové kopie v předchozím kroku:

```azurecli
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "myDeployedVM" myImage
```

Pokud chcete vytvořit vlastní virtuální počítače, zadejte vlastní uživatelské jméno + heslo, umístění, název DNS a název bitové kopie.

## <a name="next-steps"></a>Další postup
Další informace najdete v tématu [referenční dokumentace rozhraní příkazového řádku Azure pro model nasazení Azure classic](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

[Step 1: Prepare the image to be uploaded]:#prepimage
[Step 2: Prepare the connection to Azure]:#connect
[Step 3: Upload the image to Azure]:#upload
