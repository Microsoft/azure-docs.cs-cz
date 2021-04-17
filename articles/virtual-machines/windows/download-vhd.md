---
title: Stažení virtuálního pevného disku s Windows z Azure
description: Stažení virtuálního pevného disku s Windows pomocí Azure Portal.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 32b9753b79273ce747d00cba077dd8a5ee6d724d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565283"
---
# <a name="download-a-windows-vhd-from-azure"></a>Stažení virtuálního pevného disku s Windows z Azure

V tomto článku se dozvíte, jak stáhnout soubor virtuálního pevného disku (VHD) Windows z Azure pomocí Azure Portal.

## <a name="optional-generalize-the-vm"></a>Volitelné: generalizace virtuálního počítače

Pokud chcete virtuální pevný disk použít jako [Image](tutorial-custom-images.md) k vytvoření dalších virtuálních počítačů, měli byste k generalizaci operačního systému použít [Nástroj Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) . Jinak budete muset vytvořit kopii disku pro každý virtuální počítač, který chcete vytvořit.

Pokud chcete virtuální pevný disk použít jako image k vytvoření dalších virtuálních počítačů, proveďte generalizaci virtuálního počítače.

1. Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com/).
2. [Připojte se k virtuálnímu počítači](connect-logon.md). 
3. Na virtuálním počítači otevřete okno příkazového řádku jako správce.
4. Změňte adresář na *%WINDIR%\system32\sysprep* a spusťte sysprep.exe.
5. V dialogovém okně Nástroj pro přípravu systému vyberte možnost **Zadejte systém při spuštění prostředí (OOBE)** a ujistěte se, že je vybraná možnost **generalizace** .
6. V možnosti vypnutí vyberte **vypnout** a pak klikněte na **OK**. 

Pokud nechcete zobecnit aktuální virtuální počítač, můžete přesto vytvořit zobecněnou bitovou kopii tím, že nejprve vytvoříte [snímek disku s operačním systémem](#alternative-snapshot-the-vm-disk), vytvoříte nový virtuální počítač ze snímku a pak provedete generalizaci kopie.

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Virtuální pevný disk se nedá stáhnout z Azure, pokud je připojený ke spuštěnému virtuálnímu počítači. Pokud chcete, aby byl virtuální počítač spuštěný, můžete [vytvořit snímek a potom stáhnout snímek](#alternative-snapshot-the-vm-disk).

1. V nabídce centra v Azure Portal klikněte na **Virtual Machines**.
1. V seznamu vyberte virtuální počítač.
1. V okně pro virtuální počítač klikněte na **zastavit**.

### <a name="alternative-snapshot-the-vm-disk"></a>Alternativa: vytvoření snímku disku virtuálního počítače

Pořídit snímek disku, který se má stáhnout.

1. Vyberte virtuální počítač na [portálu](https://portal.azure.com).
2. V nabídce vlevo vyberte **disky** a pak vyberte disk, který chcete snímek. Zobrazí se podrobnosti o disku.  
3. V nabídce v horní části stránky vyberte **vytvořit snímek** . Otevře se stránka **vytvořit snímek** .
4. Do pole **název** zadejte název snímku. 
5. Jako **typ snímku** vyberte **úplné** nebo **přírůstkové**.
6. Až budete hotovi, vyberte **zkontrolovat + vytvořit**.

Vaše snímky se vytvoří krátce a pak se můžou použít ke stažení nebo vytvoření jiného virtuálního počítače z.

> [!NOTE]
> Pokud virtuální počítač nezastavíte jako první, snímek se nevyčistí. Snímek bude ve stejném stavu, jako kdyby byl virtuální počítač v době, kdy byl snímek vytvořen, v okamžiku, kdy došlo k jeho vypnutí, nebo došlo k chybě.  Obvykle to může způsobit problémy, pokud spuštěné aplikace spuštěné v daném čase nebyly odolné proti chybám.
>  
> Tato metoda se doporučuje jenom pro virtuální počítače s jedním diskem s operačním systémem. Před stažením nebo před vytvořením snímku pro disk s operačním systémem a každý datový disk je třeba zastavit virtuální počítače s jedním nebo více datovými disky.

## <a name="generate-download-url"></a>Generovat adresu URL pro stažení

Pokud chcete stáhnout soubor VHD, musíte vygenerovat adresu URL [sdíleného přístupového podpisu (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) . Po vygenerování adresy URL se adresa URL přiřadí čas vypršení platnosti.

1. Na stránce pro virtuální počítač klikněte v levé nabídce na **disky** .
1. Vyberte disk operačního systému pro virtuální počítač.
1. Na stránce pro disk vyberte z nabídky vlevo možnost **Export disku** .
1. Výchozí čas vypršení platnosti adresy URL je *3600* sekund (jedna hodina). Možná budete muset tento postup zvýšit pro disky s operačním systémem Windows nebo velké datové disky. **36000** sekund (10 hodin) je obvykle dostačující.
1. Klikněte na **vygenerovat adresu URL**.

> [!NOTE]
> Čas vypršení platnosti se zvýší z výchozí hodnoty a poskytne dostatek času ke stažení velkého souboru VHD pro operační systém Windows Server. Stažení velkých virtuálních pevných disků může trvat až několik hodin v závislosti na připojení a velikosti virtuálního počítače. 
> 
> 

## <a name="download-vhd"></a>Stáhnout VHD

1. Pod vygenerovanou adresou URL klikněte na Stáhnout soubor VHD.
1. Možná budete muset kliknutím na **Uložit** v prohlížeči spustit stahování. Výchozí název souboru VHD je *abcd*.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nahrát soubor VHD do Azure](upload-generalized-managed.md). 
- [Vytvořte spravované disky z nespravovaných disků v účtu úložiště](attach-disk-ps.md).
- [Spravujte disky Azure pomocí PowerShellu](tutorial-manage-data-disk.md).
