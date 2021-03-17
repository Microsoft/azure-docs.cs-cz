---
title: Vytvoření imagí virtuálních počítačů pro zařízení s grafickým procesorem Azure Stack Edge pro
description: Popisuje, jak vytvořit image virtuálních počítačů se systémem Linux nebo Windows pro použití se zařízením s grafickým procesorem Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: ee0587063c0fac67068869c58471ada58354fab7
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437668"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-device"></a>Vytváření vlastních imagí virtuálních počítačů pro zařízení Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Pokud chcete nasadit virtuální počítače na zařízení Azure Stack Edge pro, musíte mít možnost vytvářet vlastní image virtuálních počítačů, které můžete použít k vytvoření virtuálních počítačů. Tento článek popisuje kroky, které jsou potřeba k vytvoření vlastních imagí pro Linux nebo Windows VM, které můžete použít k nasazení virtuálních počítačů na zařízení Azure Stack Edge pro.

## <a name="vm-image-workflow"></a>Pracovní postup image virtuálního počítače

Pracovní postup vyžaduje, abyste v Azure vytvořili virtuální počítač, přizpůsobili virtuální počítač, generalizaci a stažení virtuálního pevného disku odpovídajícího tomuto VIRTUÁLNÍmu počítači. Tento zobecněný virtuální pevný disk se nahraje do Azure Stack Edge pro. Z tohoto virtuálního pevného disku se vytvoří spravovaný disk. Z spravovaného disku se vytvoří obrázek. A nakonec se z této image vytvoří virtuální počítače.

Další informace najdete [v poznámkách k nasazení virtuálního počítače na zařízení Azure Stack Edge pro pomocí Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Vytvoření vlastní image virtuálního počítače s Windows

K vytvoření image virtuálního počítače s Windows proveďte následující kroky.

1. Vytvořte virtuální počítač s Windows. Další informace najdete v kurzu postup [: vytváření a správa virtuálních počítačů s Windows pomocí Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Stáhněte si existující disk s operačním systémem.

    - Postupujte podle kroků v části [stažení virtuálního pevného disku](../virtual-machines/windows/download-vhd.md).

    - Použijte následující `sysprep` příkaz místo toho, co je popsáno v předchozím postupu.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       Můžete se také podívat na [Přehled nástroje Sysprep (Příprava systému)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Pomocí tohoto virtuálního pevného disku teď vytvoříte a nasadíte virtuální počítač na zařízení Azure Stack Edge pro.

## <a name="create-a-linux-custom-vm-image"></a>Vytvoření vlastní image virtuálního počítače se systémem Linux

Chcete-li vytvořit bitovou kopii virtuálního počítače se systémem Linux, proveďte následující kroky.

1. Vytvoření virtuálního počítače s Linuxem Další informace najdete v kurzu postup [: vytváření a správa virtuálních počítačů se systémem Linux pomocí Azure CLI](../virtual-machines/linux/tutorial-manage-vm.md).

1. Zrušte zřízení virtuálního počítače. Použijte agenta virtuálního počítače Azure k odstranění souborů a dat specifických pro konkrétní počítač. Použijte `waagent` příkaz s `-deprovision+user` parametrem na ZDROJovém virtuálním počítači Linux. Další informace najdete v tématu [Principy a použití agenta Azure Linux](../virtual-machines/extensions/agent-linux.md).

    1. Připojte se k VIRTUÁLNÍmu počítači se systémem Linux pomocí klienta SSH.
    2. V okně SSH zadejte následující příkaz:
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > Spusťte tento příkaz jenom na virtuálním počítači, který budete zachytit jako image. Tento příkaz nezaručuje, že image je smazána u všech citlivých informací, nebo je vhodná pro redistribuci. `+user`Parametr také odebere naposledy zřízený uživatelský účet. Pokud chcete zachovat přihlašovací údaje uživatelského účtu ve virtuálním počítači, používejte jenom `-deprovision` .
     
    3. Pokračujte zadáním **y** . `-force`Chcete-li se tomuto kroku potvrzení vyhnout, můžete přidat parametr.
    4. Po dokončení příkazu zadejte **Exit** a zavřete tak klienta ssh.  Virtuální počítač bude v tuto chvíli i nadále spuštěn.


1. [Stáhněte si existující disk s operačním systémem](../virtual-machines/linux/download-vhd.md).

Pomocí tohoto virtuálního pevného disku teď vytvoříte a nasadíte virtuální počítač na zařízení Azure Stack Edge pro. Vlastní image pro Linux můžete vytvořit pomocí následujících dvou Azure Marketplace imagí:

|Název položky  |Popis  |Publisher  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Ubuntu Server je nejoblíbenější Linux pro cloudová prostředí na světě.|Canonical|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux je jednou z nejoblíbenějších distribucí systému Linux.     |credativ|

Úplný seznam Azure Marketplacech imagí, které by mohly fungovat (dosud netestované), najdete v [Azure Marketplace položkách, které jsou k dispozici pro Azure Stack hub](/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910&preserve-view=true).


## <a name="next-steps"></a>Další kroky

[Nasaďte virtuální počítače na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).