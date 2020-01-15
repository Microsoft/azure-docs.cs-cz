---
title: Stažení virtuálního pevného disku s Windows z Azure
description: Stažení virtuálního pevného disku s Windows pomocí Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: d1c98fa4f3572c40279978d787b1719746478a06
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940452"
---
# <a name="download-a-windows-vhd-from-azure"></a>Stažení virtuálního pevného disku s Windows z Azure

V tomto článku se dozvíte, jak stáhnout soubor virtuálního pevného disku (VHD) Windows z Azure pomocí Azure Portal.

## <a name="optional-generalize-the-vm"></a>Volitelné: generalizace virtuálního počítače

Pokud chcete virtuální pevný disk použít jako [Image](tutorial-custom-images.md) k vytvoření dalších virtuálních počítačů, měli byste k generalizaci operačního systému použít [Nástroj Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) . 

Pokud chcete virtuální pevný disk použít jako image k vytvoření dalších virtuálních počítačů, proveďte generalizaci virtuálního počítače.

1. Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com/).
2. [Připojte se k virtuálnímu počítači](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. Na virtuálním počítači otevřete okno příkazového řádku jako správce.
4. Změňte adresář na *%WINDIR%\system32\sysprep* a spusťte nástroj Sysprep. exe.
5. V dialogovém okně Nástroj pro přípravu systému vyberte možnost **Zadejte systém při spuštění prostředí (OOBE)** a ujistěte se, že je vybraná možnost **generalizace** .
6. V možnosti vypnutí vyberte **vypnout**a pak klikněte na **OK**. 


## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Virtuální pevný disk se nedá stáhnout z Azure, pokud je připojený ke spuštěnému virtuálnímu počítači. Pro stažení virtuálního pevného disku je nutné zastavit virtuální počítač. 

1. V nabídce centra v Azure Portal klikněte na **Virtual Machines**.
1. V seznamu vyberte virtuální počítač.
1. V okně pro virtuální počítač klikněte na **zastavit**.


## <a name="generate-download-url"></a>Generovat adresu URL pro stažení

Pokud chcete stáhnout soubor VHD, musíte vygenerovat adresu URL [sdíleného přístupového podpisu (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Po vygenerování adresy URL se adresa URL přiřadí čas vypršení platnosti.

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

- Přečtěte si, jak [nahrát soubor VHD do Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Vytvořte spravované disky z nespravovaných disků v účtu úložiště](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Spravujte disky Azure pomocí PowerShellu](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

