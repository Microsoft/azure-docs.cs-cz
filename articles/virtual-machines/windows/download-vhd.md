---
title: Stažení virtuálního pevného disku Windows v Azure | Dokumentace Microsoftu
description: Stáhněte si Windows virtuálního pevného disku pomocí webu Azure portal.
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
ms.openlocfilehash: 3d44a4a723c39bf9780475a2ac3088da94285f6e
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329366"
---
# <a name="download-a-windows-vhd-from-azure"></a>Stáhněte si Windows virtuálního pevného disku z Azure

V tomto článku se dozvíte, jak stáhnout soubor virtuálního pevného disku (VHD) pro Windows z Azure pomocí webu Azure portal.

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Virtuální pevný disk nejde stáhnout z Azure, pokud je připojen k spuštěného virtuálního počítače. Budete muset zastavit virtuální počítač ke stažení virtuálního pevného disku. Pokud chcete použít jako virtuální pevný disk [image](tutorial-custom-images.md) ostatním virtuálním počítačům s nové disky, které vytvoříte pomocí [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) zobecní operační systém obsažený v souboru a poté zastavte virtuální počítač. Použití virtuálního pevného disku jako disku pro nové instance existujícího virtuálního počítače nebo datový disk, potřebujete jenom zastavit a zrušit přidělení virtuálního počítače.

Chcete-li použít virtuální pevný disk jako image k vytvoření dalších virtuálních počítačů, postupujte takto:

1.  Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com/).
2.  [Připojte se k virtuálnímu počítači](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Na virtuálním počítači otevřete okno příkazového řádku jako správce.
4.  Změňte adresář na *%windir%\system32\sysprep* a spustit sysprep.exe.
5.  V dialogovém okně nástroje pro přípravu systému vyberte **zadejte systému Out-of-Box zapnutí**a ujistěte se, že **generalizace** zaškrtnuto.
6.  V možnosti vypnutí vyberte **vypnutí**a potom klikněte na tlačítko **OK**. 

Použití virtuálního pevného disku jako disku pro nové instance existujícího virtuálního počítače nebo datový disk, proveďte tyto kroky:

1.  V nabídce centra na webu Azure Portal, klikněte na tlačítko **virtuálních počítačů**.
2.  Vyberte virtuální počítač ze seznamu.
3.  V okně pro virtuální počítač, klikněte na **Zastavit**.

    ![Zastavení virtuálního počítače](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Vygenerovat adresu SAS URL

Stáhněte soubor virtuálního pevného disku, budete muset vygenerovat [sdílený přístupový podpis (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) adresy URL. Při generování adresy URL, čas vypršení platnosti je přiřazena k adrese URL.

1.  V nabídce v okně pro virtuální počítač, klikněte na tlačítko **disky**.
2.  Vyberte disk s operačním systémem virtuálního počítače a potom klikněte na tlačítko **exportovat**.
3.  Nastavit čas vypršení platnosti adresy URL, aby *36000*.
4.  Klikněte na tlačítko **generování adresy URL**.

    ![Vygenerovat URL](./media/download-vhd/export-generate.png)

> [!NOTE]
> Čas vypršení platnosti se zvýšil z výchozí poskytnout dostatek času na stažení velkých souborů virtuálního pevného disku pro operační systém Windows Server. Můžete očekávat virtuálního pevného disku obsahující operační systém Windows Server do trvat několik hodin, stáhněte si v závislosti na připojení. Pokud stahujete virtuální pevný disk pro datový disk, výchozí doba je dostačující. 
> 
> 

## <a name="download-vhd"></a>Stažení virtuálního pevného disku

1.  V části Adresa URL, která byla vygenerována klikněte na stáhnout soubor VHD.

    ![Stažení virtuálního pevného disku](./media/download-vhd/export-download.png)

2.  Budete muset kliknout na **Uložit** v prohlížeči a spusťte soubor ke stažení. Výchozí název pro soubor virtuálního pevného disku je *abcd*.

    ![Klikněte na Uložit v prohlížeči](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [nahrání souboru VHD do Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Vytvoření spravovaných disků z nespravovaných disků v účtu úložiště](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Správa disků v Azure pomocí Powershellu](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

