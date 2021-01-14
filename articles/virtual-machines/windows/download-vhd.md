---
title: Stažení virtuálního pevného disku s Windows z Azure
description: Stažení virtuálního pevného disku s Windows pomocí Azure Portal.
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 5e6f7b461d205478d6a3fe3eecbbb933d0533ec9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200511"
---
# <a name="download-a-windows-vhd-from-azure"></a>Stažení virtuálního pevného disku s Windows z Azure

V tomto článku se dozvíte, jak stáhnout soubor virtuálního pevného disku (VHD) Windows z Azure pomocí Azure Portal.

## <a name="optional-generalize-the-vm"></a>Volitelné: generalizace virtuálního počítače

Pokud chcete virtuální pevný disk použít jako [Image](tutorial-custom-images.md) k vytvoření dalších virtuálních počítačů, měli byste k generalizaci operačního systému použít [Nástroj Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) . 

Pokud chcete virtuální pevný disk použít jako image k vytvoření dalších virtuálních počítačů, proveďte generalizaci virtuálního počítače.

1. Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com/).
2. [Připojte se k virtuálnímu počítači](connect-logon.md). 
3. Na virtuálním počítači otevřete okno příkazového řádku jako správce.
4. Změňte adresář na *%WINDIR%\system32\sysprep* a spusťte sysprep.exe.
5. V dialogovém okně Nástroj pro přípravu systému vyberte možnost **Zadejte systém při spuštění prostředí (OOBE)** a ujistěte se, že je vybraná možnost **generalizace** .
6. V možnosti vypnutí vyberte **vypnout** a pak klikněte na **OK**. 


## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Virtuální pevný disk se nedá stáhnout z Azure, pokud je připojený ke spuštěnému virtuálnímu počítači. Pro stažení virtuálního pevného disku je nutné zastavit virtuální počítač. 

1. V nabídce centra v Azure Portal klikněte na **Virtual Machines**.
1. V seznamu vyberte virtuální počítač.
1. V okně pro virtuální počítač klikněte na **zastavit**.


## <a name="generate-download-url"></a>Generovat adresu URL pro stažení

Pokud chcete stáhnout soubor VHD, musíte vygenerovat adresu URL [sdíleného přístupového podpisu (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) . Po vygenerování adresy URL se adresa URL přiřadí čas vypršení platnosti.

1. Na stránce pro virtuální počítač klikněte v levé nabídce na **disky** .
1. Vyberte disk operačního systému pro virtuální počítač.
1. Na stránce pro disk vyberte z nabídky vlevo možnost **Export disku** .
1. Výchozí doba vypršení platnosti adresy URL je *3600* sekund. Zvyšte tuto **36000** na disky s operačním systémem Windows.
1. Klikněte na **vygenerovat adresu URL**.

> [!NOTE]
> Čas vypršení platnosti se zvýší z výchozí hodnoty a poskytne dostatek času ke stažení velkého souboru VHD pro operační systém Windows Server. Můžete očekávat, že soubor VHD, který obsahuje operační systém Windows Server, bude trvat několik hodin, než se stáhne v závislosti na vašem připojení. Pokud stahujete VHD pro datový disk, je výchozí čas dostačující. 
> 
> 

## <a name="download-vhd"></a>Stáhnout VHD

1. Pod vygenerovanou adresou URL klikněte na Stáhnout soubor VHD.
1. Možná budete muset kliknutím na **Uložit** v prohlížeči spustit stahování. Výchozí název souboru VHD je *abcd*.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nahrát soubor VHD do Azure](upload-generalized-managed.md). 
- [Vytvořte spravované disky z nespravovaných disků v účtu úložiště](attach-disk-ps.md).
- [Spravujte disky Azure pomocí PowerShellu](tutorial-manage-data-disk.md).
