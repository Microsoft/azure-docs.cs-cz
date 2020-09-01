---
title: Vytvoření imagí virtuálních počítačů pro zařízení s grafickým procesorem Azure Stack Edge
description: Popisuje, jak vytvořit image virtuálních počítačů se systémem Linux nebo Windows pro použití se zařízením s grafickým procesorem Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 83332c3bfa0b2b99d7333fa679fb8d398aecf8bd
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268906"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-device"></a>Vytváření vlastních imagí virtuálních počítačů pro zařízení Azure Stack Edge

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Pokud chcete nasadit virtuální počítače na Azure Stack hraničním zařízení, musíte mít možnost vytvářet vlastní image virtuálních počítačů, které můžete použít k vytvoření virtuálních počítačů. Tento článek popisuje kroky potřebné k vytvoření vlastních imagí pro Linux nebo Windows VM, které můžete použít k nasazení virtuálních počítačů na zařízení Azure Stack Edge.

## <a name="vm-image-workflow"></a>Pracovní postup image virtuálního počítače

Pracovní postup vyžaduje, abyste v Azure vytvořili virtuální počítač, přizpůsobili virtuální počítač, generalizaci a stažení virtuálního pevného disku odpovídajícího tomuto VIRTUÁLNÍmu počítači. Tento zobecněný virtuální pevný disk se nahraje do Azure Stack Edge, z tohoto virtuálního pevného disku se vytvoří spravovaný disk, image se vytvoří ze spravovaného disku a z této image se vytvoří virtuální počítače.   

Další informace najdete [v poznámkách k nasazení virtuálního počítače na zařízení Azure Stack Edge pomocí Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Vytvoření vlastní image virtuálního počítače s Windows

K vytvoření image virtuálního počítače s Windows proveďte následující kroky.

1. Vytvořte virtuální počítač s Windows. Další informace najdete v kurzu postup [: vytváření a správa virtuálních počítačů s Windows pomocí Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Stáhněte si existující disk s operačním systémem.

    - Postupujte podle kroků v části [stažení virtuálního pevného disku](../virtual-machines/windows/download-vhd.md).

    - Použijte následující `sysprep` příkaz místo toho, co je popsáno v předchozím postupu.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       Můžete se také podívat na [Přehled nástroje Sysprep (Příprava systému)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Pomocí tohoto virtuálního pevného disku teď vytvoříte a nasadíte virtuální počítač na zařízení Azure Stack Edge.

## <a name="create-a-linux-custom-vm-image"></a>Vytvoření vlastní image virtuálního počítače se systémem Linux

Chcete-li vytvořit bitovou kopii virtuálního počítače se systémem Linux, proveďte následující kroky.

1. Vytvoření virtuálního počítače s Linuxem Další informace najdete v kurzu postup [: vytváření a správa virtuálních počítačů se systémem Linux pomocí Azure CLI](../virtual-machines/linux/tutorial-manage-vm.md).

2. [Stáhněte si existující disk s operačním systémem](../virtual-machines/linux/download-vhd.md).

Pomocí tohoto virtuálního pevného disku teď vytvoříte a nasadíte virtuální počítač na zařízení Azure Stack Edge. Vlastní image pro Linux můžete vytvořit pomocí následujících dvou Azure Marketplace imagí:

|Název položky  |Popis  |Publisher  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Ubuntu Server je nejoblíbenější Linux pro cloudová prostředí na světě.|Canonical|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux je jednou z nejoblíbenějších distribucí systému Linux.     |credativ|

Úplný seznam imagí Azure Marketplace, které by mohly fungovat (dosud netestované), najdete v [Azure Marketplace položkách dostupných pro centrum Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910).


## <a name="next-steps"></a>Další kroky

[Nasaďte virtuální počítače na Azure Stack hraniční zařízení](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).
