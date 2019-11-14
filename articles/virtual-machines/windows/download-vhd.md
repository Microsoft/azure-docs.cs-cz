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
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: c5891d7ea2b53ab3524cfff267e71b4f05779cfc
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033597"
---
# <a name="download-a-windows-vhd-from-azure"></a>Stažení virtuálního pevného disku s Windows z Azure

V tomto článku se dozvíte, jak stáhnout soubor virtuálního pevného disku (VHD) Windows z Azure pomocí Azure Portal.

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Virtuální pevný disk se nedá stáhnout z Azure, pokud je připojený ke spuštěnému virtuálnímu počítači. Pro stažení virtuálního pevného disku je nutné zastavit virtuální počítač. Pokud chcete virtuální pevný disk použít jako [Image](tutorial-custom-images.md) k vytvoření dalších virtuálních počítačů s novými disky, použijte [Nástroj Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) k generalizaci operačního systému obsaženého v souboru a potom virtuální počítač zastavte. Pokud chcete virtuální pevný disk použít jako disk pro novou instanci existujícího virtuálního počítače nebo datového disku, stačí zastavit a zrušit přidělení virtuálního počítače.

Pokud chcete virtuální pevný disk použít jako image k vytvoření dalších virtuálních počítačů, proveďte tyto kroky:

1.  Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com/).
2.  [Připojte se k virtuálnímu počítači](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Na virtuálním počítači otevřete okno příkazového řádku jako správce.
4.  Změňte adresář na *%WINDIR%\system32\sysprep* a spusťte nástroj Sysprep. exe.
5.  V dialogovém okně Nástroj pro přípravu systému vyberte možnost **Zadejte systém při spuštění prostředí (OOBE)** a ujistěte se, že je vybraná možnost **generalizace** .
6.  V možnosti vypnutí vyberte **vypnout**a pak klikněte na **OK**. 

Pokud chcete virtuální pevný disk použít jako disk pro novou instanci existujícího virtuálního počítače nebo datového disku, proveďte tyto kroky:

1.  V nabídce centra v Azure Portal klikněte na **Virtual Machines**.
2.  V seznamu vyberte virtuální počítač.
3.  V okně pro virtuální počítač klikněte na **zastavit**.

    ![Zastavit virtuální počítač](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Vygenerovat adresu URL SAS

Pokud chcete stáhnout soubor VHD, musíte vygenerovat adresu URL [sdíleného přístupového podpisu (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Po vygenerování adresy URL se adresa URL přiřadí čas vypršení platnosti.

1.  V nabídce okna pro virtuální počítač klikněte na **disky**.
2.  Vyberte disk s operačním systémem pro virtuální počítač a klikněte na **exportovat disk**.
3.  Nastavte čas vypršení platnosti adresy URL na *36000*.
4.  Klikněte na **vygenerovat adresu URL**.

    ![Vygenerovat adresu URL](./media/download-vhd/export-generate-new.png)

> [!NOTE]
> Čas vypršení platnosti se zvýší z výchozí hodnoty a poskytne dostatek času ke stažení velkého souboru VHD pro operační systém Windows Server. Můžete očekávat, že soubor VHD, který obsahuje operační systém Windows Server, bude trvat několik hodin, než se stáhne v závislosti na vašem připojení. Pokud stahujete VHD pro datový disk, je výchozí čas dostačující. 
> 
> 

## <a name="download-vhd"></a>Stáhnout VHD

1.  Pod vygenerovanou adresou URL klikněte na Stáhnout soubor VHD.

    ![Stáhnout VHD](./media/download-vhd/export-download.png)

2.  Možná budete muset kliknutím na **Uložit** v prohlížeči spustit stahování. Výchozí název souboru VHD je *abcd*.

    ![V prohlížeči klikněte na Uložit.](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nahrát soubor VHD do Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Vytvořte spravované disky z nespravovaných disků v účtu úložiště](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Spravujte disky Azure pomocí PowerShellu](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

