---
title: Stažení virtuálního pevného disku windows z Azure
description: Stáhněte si virtuální pevný disk Windows pomocí portálu Azure.
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: e87f2436f69abed4c0d0ee415226f99b52f2e804
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085361"
---
# <a name="download-a-windows-vhd-from-azure"></a>Stažení virtuálního pevného disku windows z Azure

V tomto článku se dozvíte, jak stáhnout soubor virtuálního pevného disku (VHD) windows z Azure pomocí portálu Azure.

## <a name="optional-generalize-the-vm"></a>Volitelné: Generalizace virtuálního virtuálního mísy

Pokud chcete použít virtuální pevný disk jako [image](tutorial-custom-images.md) k vytvoření dalších virtuálních virtuálních měn, měli byste použít [program Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) k generalizaci operačního systému. 

Chcete-li použít virtuální pevný disk jako image k vytvoření dalších virtuálních virtuálních mích, zobecnit virtuální ho.

1. Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com/).
2. [Připojte se k virtuálnímu virtuálnímu virtuálnímu zařízení](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. Na virtuálním počítači otevřete okno příkazového řádku jako správce.
4. Změňte adresář na *%windir%\system32\sysprep* a spusťte soubor sysprep.exe.
5. V dialogovém okně Nástroj pro přípravu systému vyberte **Možnost zadat prostředí mimo systém (OOBE)** a ujistěte se, že je vybraná volba **Generalizovat.**
6. V části Možnosti vypnutí vyberte **Vypnout**a klepněte na tlačítko **OK**. 


## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Virtuální pevný disk nelze stáhnout z Azure, pokud je připojený ke spuštěnému virtuálnímu počítači. Chcete-li stáhnout virtuální pevný disk, musíte virtuální ho zastavit. 

1. V nabídce Hub na webu Azure Portal klikněte na **Virtuální počítače**.
1. Vyberte virtuální ho v seznamu.
1. V okně pro virtuální počítač klikněte na **Zastavit**.


## <a name="generate-download-url"></a>Generovat adresu URL pro stažení

Chcete-li stáhnout soubor VHD, je třeba vygenerovat adresu URL [sdíleného přístupového podpisu (SAS).](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Při generování adresy URL je adrese URL přiřazen čas vypršení platnosti.

1. Na stránce virtuálního počítače klikněte v levé nabídce na **Disky.**
1. Vyberte disk operačního systému pro virtuální počítače.
1. Na stránce disku vyberte export **disku** z levé nabídky.
1. Výchozí doba vypršení platnosti adresy URL je *3600* sekund. Zvyšte to na **36000** pro disky operačního systému Windows.
1. Klepněte na **tlačítko Generovat adresu URL**.

> [!NOTE]
> Doba vypršení platnosti je zvýšena z výchozího nastavení, aby byl dostatek času na stažení velkého souboru VHD pro operační systém Windows Server. V závislosti na připojení můžete očekávat, že stažení souboru VHD, který obsahuje operační systém Windows Server, bude trvat několik hodin. Pokud stahujete virtuální pevný disk pro datový disk, je výchozí čas dostatečný. 
> 
> 

## <a name="download-vhd"></a>Stažení VHD

1. Pod vygenerovanou adresou URL klikněte na Stáhnout soubor VHD.
1. Ke stažení může být nutné kliknout na **uložit** v prohlížeči. Výchozí název souboru VHD je *abcd*.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nahrát soubor VHD do Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Vytvořte spravované disky z nespravovaných disků v účtu úložiště](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Správa disků Azure pomocí PowerShellu](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

