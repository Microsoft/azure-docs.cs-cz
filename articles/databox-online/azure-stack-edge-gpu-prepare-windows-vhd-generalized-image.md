---
title: Vytvoření imagí virtuálních počítačů z generalizované image virtuálního pevného disku s Windows pro zařízení s grafickým procesorem Azure Stack Edge pro
description: Popisuje, jak se image virtuálních počítačů z generalizované image spouští z disku VHD nebo VHDX systému Windows. Pomocí této generalizované image můžete vytvářet image virtuálních počítačů pro použití s virtuálními počítači nasazenými na zařízení GPU Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: 978099accd57d15c750a27f77b2e220f569a2dd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962485"
---
# <a name="use-generalized-image-from-windows-vhd-to-create-a-vm-image-for-your-azure-stack-edge-pro-device"></a>Vytvoření image virtuálního počítače pro zařízení Azure Stack Edge pro pomocí generalizované image z Windows VHD

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Pokud chcete nasadit virtuální počítače na zařízení Azure Stack Edge pro, musíte mít možnost vytvářet vlastní image virtuálních počítačů, které můžete použít k vytvoření virtuálních počítačů. Tento článek popisuje kroky potřebné k přípravě souboru VHD nebo VHDX pro vytvoření generalizované image. Tato zobecněná bitová kopie se pak použije k vytvoření image virtuálního počítače pro zařízení Azure Stack Edge pro. 

## <a name="about-preparing-windows-vhd"></a>O přípravě virtuálního pevného disku s Windows

K vytvoření *generalizované* Image nebo *specializované* image se dá použít soubor VHD nebo VHDX Windows. Následující tabulka shrnuje hlavní rozdíly mezi *zobecněnými* a *specializovanými* bitovými kopiemi.


|Typ image  |Generalizovaná  |Specializovaná  |
|---------|---------|---------|
|Cíl     |Nasazeno v jakémkoli systému         | Cílem konkrétního systému        |
|Nastavit po spuštění     | Při prvním spuštění virtuálního počítače se vyžaduje Instalační program.          | Instalace není nutná. <br> Platforma zapne virtuální počítač.        |
|Konfigurace     |Vyžaduje se název hostitele, správce a další nastavení specifické pro virtuální počítač.         |Kompletně předem nakonfigurovaná.         |
|Používá se, když     |Vytváření několika nových virtuálních počítačů ze stejné image.         |Migrace určitého počítače nebo obnovení virtuálního počítače z předchozí zálohy.         |


Tento článek popisuje kroky potřebné k nasazení z generalizované image. Informace o nasazení z specializované image najdete v tématu [použití pevného disku s Windows VHD](azure-stack-edge-placeholder.md) pro vaše zařízení.

> [!IMPORTANT]
> Tento postup se nevztahuje na případy, kdy je zdrojový virtuální pevný disk nakonfigurovaný s vlastní konfigurací a nastavením. K generalizaci VHD obsahující vlastní pravidla brány firewall nebo nastavení proxy můžou být třeba další akce. Další informace o těchto dalších akcích najdete v tématu [Příprava virtuálního pevného disku s Windows pro nahrání do Azure – azure Virtual Machines](../virtual-machines/windows/prepare-for-upload-vhd-image.md).


## <a name="vm-image-workflow"></a>Pracovní postup image virtuálního počítače

Pracovní postup vysoké úrovně pro přípravu virtuálního pevného disku Windows pro použití jako zobecněné image má následující kroky:

1. Převeďte zdrojový VHD nebo VHDX na pevný disk s pevnou velikostí.
1. Vytvořte virtuální počítač v Hyper-V s použitím pevného virtuálního pevného disku.
1. Připojte se k virtuálnímu počítači Hyper-V.
1. Generalizujte VHD pomocí nástroje *Sysprep* . 
1. Zkopírujte zobecněnou image do úložiště objektů BLOB.
1. K nasazení virtuálních počítačů na zařízení použijte zobecněnou image. Další informace najdete v tématu [nasazení virtuálního počítače přes Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) nebo [nasazení virtuálního počítače přes PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).


## <a name="prerequisites"></a>Požadavky

Než připravíte virtuální pevný disk Windows pro použití jako zobecněnou image na Azure Stack Edge, ujistěte se, že:

- Máte disk VHD nebo VHDX obsahující podporovanou verzi Windows. Přečtěte si téma [podporované hostované operační systémy]() pro Azure Stack Edge pro. 
- Máte přístup k klientovi s Windows, který má nainstalovaného Správce technologie Hyper-V. 
- Máte přístup k účtu služby Azure Blob Storage, který po přípravě virtuálního pevného disku uloží váš VHD.

## <a name="prepare-a-generalized-windows-image-from-vhd"></a>Příprava generalizované image Windows z VHD

## <a name="convert-to-a-fixed-vhd"></a>Převod na pevný virtuální pevný disk 

Pro vaše zařízení budete potřebovat virtuální pevné disky pro vytváření imagí virtuálních počítačů. Musíte převést zdrojový virtuální pevný disk Windows nebo VHDX na pevný virtuální pevný disk. Postupujte takto:

1. V klientském systému otevřete Správce technologie Hyper-V. Přejít na **Upravit disk**.

    ![Otevřít Správce technologie Hyper-V](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. Na stránce **než začnete** vyberte **Další>**.

1. Na stránce **najít virtuální pevný disk** vyhledejte umístění zdrojového virtuálního pevného disku systému Windows nebo VHDX, které chcete převést. Vyberte **další>**.

    ![Stránka najít virtuální pevný disk](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. Na stránce **Zvolte akci** vyberte **převést** a vyberte **Další>**.

    ![Stránka pro výběr akce](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. Na stránce **Výběr formátu disku** vyberte formát **VHD** a pak vyberte **Další>**.

   ![Stránka pro výběr formátu disku](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)


1. Na stránce **zvolit typ disku** zvolte možnost **Pevná velikost** a vyberte **Další>**.

   ![Výběr stránky typ disku](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)


1. Na stránce **Konfigurovat disk** vyhledejte umístění a zadejte název disku VHD s pevnou velikostí. Vyberte **další>**.

   ![Stránka konfigurace disku](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)


1. Zkontrolujte souhrn a vyberte **Dokončit**. Převod VHD nebo VHDX trvá několik minut. Čas převodu závisí na velikosti zdrojového disku. 

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
Tento pevný virtuální pevný disk použijete pro všechny následující kroky v tomto článku.


## <a name="create-a-hyper-v-vm-from-fixed-vhd"></a>Vytvoření virtuálního počítače s technologií Hyper-V z pevného disku VHD

1. Ve **Správci technologie Hyper-v** v podokně obor klikněte pravým tlačítkem myši na uzel systém a otevřete místní nabídku a vyberte možnost **Nový**  >  **virtuální počítač**.

    ![Výběr nového virtuálního počítače v podokně oboru](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. Na stránce **než začnete** v Průvodci novým virtuálním počítačem vyberte **Další**.

1. Na stránce **zadat název a umístění** zadejte **název** a **umístění** virtuálního počítače. Vyberte **Další**.

    ![Zadejte název a umístění virtuálního počítače.](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. Na stránce **zadat generaci** zvolte pro typ image zařízení. VHD možnost **generace 1** a pak vyberte **Další**.    

    ![Zadat generaci](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. Přiřaďte požadované konfigurace paměti a sítě.

1. Na stránce **připojit virtuální pevný disk** zvolte **použít existující virtuální pevný disk**, zadejte umístění pevného disku pevného disku s Windows, který jsme vytvořili dříve, a pak vyberte **Další**.

    ![Stránka připojit virtuální pevný disk](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. Zkontrolujte **Souhrn** a pak kliknutím na **Dokončit** vytvořte virtuální počítač.

Vytvoření virtuálního počítače trvá několik minut.
     

## <a name="connect-to-the-hyper-v-vm"></a>Připojení k virtuálnímu počítači Hyper-V

Virtuální počítač se zobrazí v seznamu virtuálních počítačů v klientském systému. 


1. Vyberte virtuální počítač, klikněte na něj pravým tlačítkem a vyberte **Spustit**. 

    ![Vyberte virtuální počítač a spusťte ho.](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-2.png)

2. Na virtuálním počítači se zobrazuje **spuštěno**. Vyberte virtuální počítač, klikněte na něj pravým tlačítkem a vyberte **připojit**.

    ![Připojení k virtuálnímu počítači](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-4.png)

Až budete připojeni k virtuálnímu počítači, dokončete Průvodce instalací počítače a přihlaste se k VIRTUÁLNÍmu počítači.


## <a name="generalize-the-vhd"></a>Generalizace virtuálního pevného disku  

K generalizaci VHD použijte nástroj *Sysprep* . 

1. Uvnitř virtuálního počítače otevřete příkazový řádek.
1. Spusťte následující příkaz pro generalizaci VHD. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    Podrobnosti najdete v tématu  [Přehled nástroje Sysprep (Příprava systému)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).
1.  Po dokončení příkazu se virtuální počítač vypne. **Virtuální počítač nerestartujte**.

## <a name="upload-the-vhd-to-azure-blob-storage"></a>Nahrání virtuálního pevného disku do Azure Blob Storage

Virtuální pevný disk se teď dá použít k vytvoření generalizované image na Azure Stack Edge. 

1. Nahrajte virtuální pevný disk do úložiště objektů BLOB v Azure. Podrobné pokyny najdete v tématu [nahrání VHD pomocí Průzkumník služby Azure Storage](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).
1. Po dokončení nahrávání můžete k vytvoření imagí virtuálních počítačů a virtuálních počítačů použít nahranou bitovou kopii. 

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->



## <a name="next-steps"></a>Další kroky

V závislosti na povaze nasazení můžete zvolit jeden z následujících postupů.

- [Nasazení virtuálního počítače z generalizované Image pomocí Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Nasazení virtuálního počítače z generalizované Image pomocí Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
