---
title: Stáhněte si Windows virtuálního pevného disku z Azure | Microsoft Docs
description: Stáhněte si Windows virtuální pevný disk pomocí portálu Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: f62c1b815180e39468a39b8bc2a220a6bfb9ea5a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726291"
---
# <a name="download-a-windows-vhd-from-azure"></a>Stáhněte si Windows virtuálního pevného disku z Azure

V tomto článku se dozvíte, jak stáhnout [Windows virtuální pevný disk (VHD)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) soubor z Azure pomocí portálu Azure. 

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Virtuální pevný disk nelze stáhnout ze služby Azure, pokud je připojen k spuštění virtuálního počítače. Budete muset zastavit virtuální počítač ke stažení virtuální pevný disk. Pokud chcete použít jako virtuální pevný disk [image](tutorial-custom-images.md) k vytvoření dalších virtuálních počítačů s nové disky, použijete [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) zobecní operační systém obsažený v souboru a potom zastavte virtuální počítač. Pokud chcete použít virtuální pevný disk jako disk pro novou instanci třídy na existující virtuální počítač nebo datový disk, stačí k zastavení a zrušit přidělení virtuálního počítače.

Chcete-li použít virtuální pevný disk jako bitovou kopii k vytvoření dalších virtuálních počítačů, proveďte tyto kroky:

1.  Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com/).
2.  [Připojte se k Virtuálnímu](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Ve virtuálním počítači otevřete okno příkazového řádku jako správce.
4.  Změňte adresář na *%windir%\system32\sysprep* a spusťte sysprep.exe.
5.  V dialogovém okně Nástroj pro přípravu systému vyberte **prostředí Out-of-Box zadejte systému (při prvním zapnutí)** a ujistěte se, že **generalizace** je vybrána.
6.  V možnosti vypnutí, vyberte **vypnutí**a potom klikněte na **OK**. 

Pokud chcete použít virtuální pevný disk jako disk pro novou instanci třídy na existující virtuální počítač nebo datový disk, proveďte tyto kroky:

1.  V nabídce centra v portálu Azure, klikněte na tlačítko **virtuální počítače**.
2.  Vyberte virtuální počítač ze seznamu.
3.  V okně pro virtuální počítač, klikněte na **Zastavit**.

    ![Zastavit virtuální počítač](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generování adresy URL SAS

Ke stažení souboru virtuálního pevného disku, je nutné generovat [sdílený přístupový podpis (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) adresy URL. Generování adresy URL čas vypršení platnosti je přiřazena k adrese URL.

1.  V nabídce v okně pro virtuální počítač, klikněte na tlačítko **disky**.
2.  Vyberte disk operačního systému pro virtuální počítač a pak klikněte na tlačítko **exportovat**.
3.  Nastavte hodnotu doby vypršení platnosti adresy URL do *36000*.
4.  Klikněte na tlačítko **generování adresy URL**.

    ![Generování adresy URL](./media/download-vhd/export-generate.png)

> [!NOTE]
> Čas vypršení platnosti je vyšší než výchozí zajistit dostatek času na stažení velkého souboru virtuálního pevného disku pro operační systém Windows Server. Můžete očekávat, že soubor virtuálního pevného disku, který obsahuje operační systém Windows Server do trvat několik hodin v závislosti na připojení stáhnout. Pokud stahujete virtuální pevný disk pro datový disk, je výchozí doba dostatečná. 
> 
> 

## <a name="download-vhd"></a>Stáhnout virtuálního pevného disku

1.  V části Adresa URL, která byla vygenerována klikněte na tlačítko Stáhnout soubor VHD.

    ![Stáhnout virtuálního pevného disku](./media/download-vhd/export-download.png)

2.  Je třeba kliknout na **Uložit** v prohlížeči zahájíte stahování. Výchozí název souboru virtuálního pevného disku je *abcd*.

    ![Kliknutím na Uložit v prohlížeči](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [nahrát soubor virtuálního pevného disku do Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Vytvoření spravované disky z nespravovaných disků v účtu úložiště](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Správa Azure disky pomocí prostředí PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

