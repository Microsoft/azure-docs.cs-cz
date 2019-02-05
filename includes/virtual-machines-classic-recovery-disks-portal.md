---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 5490bdd3934b438a683ce4271fbec20b3d13735d
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735505"
---
Pokud na vašem virtuálním počítači v Azure dochází k chybám spouštění nebo disku, možná bude nutné provést postup při řešení potíží na samotném virtuálním pevném disku. Běžným příkladem je aktualizace aplikace, která selhala a brání úspěšnému spuštění virtuálního počítače. Tento článek popisuje, jak pomocí webu Azure Portal připojit virtuální pevný disk k jinému virtuálnímu počítači, opravit všechny chyby a potom znovu vytvořit původní virtuální počítač.


## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Proces řešení potíží je následující:

1. Odstranění virtuálního počítače, na kterém dochází k chybám, při zachování virtuálních pevných disků.
2. Připojení virtuálního pevného disku k jinému virtuálnímu počítači pro účely řešení potíží.
3. Připojení k virtuálnímu počítači pro řešení potíží. Opravení chyb na původním virtuálním pevném disku pomocí upravení souborů nebo spuštění nástrojů.
4. Odpojení virtuálního pevného disku od virtuálního počítače pro řešení potíží.
5. Vytvoření virtuálního počítače s použitím původního virtuálního pevného disku.

## <a name="delete-the-original-vm"></a>Odstranění původního virtuálního počítače
Virtuální pevné disky a virtuální počítače jsou v Azure dva různé prostředky. Virtuální pevný disk je úložištěm operačního systému, aplikací a konfigurací. Virtuální počítač tvoří jenom metadata, která definují velikost nebo umístění a odkazují na prostředky, jako jsou virtuální pevný disk nebo virtuální síťová karta. Každému virtuálnímu pevnému disku se při připojení k virtuálnímu počítači přiřadí zapůjčení. Přestože datové disky je možné připojovat a odpojovat dokonce i za běhu virtuálního počítače, disk s operačním systémem není možné odpojit, dokud se neodstraní prostředek virtuálního počítače. Zapůjčení nadále přidružuje disk s operačním systémem k virtuálnímu počítači dokonce i když je virtuální počítač v zastaveném a uvolněném stavu.

Prvním krokem k obnovení virtuálního počítače je odstranění samotného prostředku virtuálního počítače. Když odstraníte virtuální počítač, virtuální pevné disky zůstanou ve vašem účtu úložiště. Po odstranění virtuálního počítače můžete připojit virtuální pevný disk k jinému virtuálnímu počítači za účelem řešení a odstranění problémů. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. V nabídce na levé straně klikněte na **Virtuální počítače (Classic)**.
3. Vyberte virtuální počítač, u kterého dochází k problému, klikněte na **Disky** a potom najděte název virtuálního pevného disku. 
4. Vyberte virtuální pevný disk s operačním systémem a zkontrolujte **Umístění**, abyste identifikovali účet úložiště, který tento pevný disk obsahuje. V následujícím příkladu je název účtu úložiště řetězec bezprostředně před „.blob.core.windows.net“.

    ```
    https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd
    ```

    ![Obrázek s umístěním virtuálního počítače](./media/virtual-machines-classic-recovery-disks-portal/vm-location.png)

5. Klikněte pravým tlačítkem na virtuální počítač a vyberte **Odstranit**. Při odstraňování virtuálního počítače se ujistěte, že nejsou vybrané disky.
6. Vytvořte nový virtuální počítač pro obnovení. Tento virtuální počítač musí být ve stejné oblasti a skupině prostředků (cloudová služba) jako problémový virtuální počítač.
7. Vyberte virtuální počítač pro obnovení a potom vyberte **Disky** > **Připojit stávající**.
8. Klikněte na **Soubor VHD** a vyberte váš existující virtuální pevný disk:

    ![Vyhledání existujícího VHD procházením](./media/virtual-machines-classic-recovery-disks-portal/select-vhd-location.png)

9. Vyberte účet úložiště > kontejner VHD > virtuální pevný disk a kliknutím na **Vybrat** potvrďte výběr.

    ![Výběr existujícího VHD](./media/virtual-machines-classic-recovery-disks-portal/select-vhd.png)

10. Když je teď vybrán váš VHD, kliknutím na **OK** připojte existující virtuální pevný disk.
11. Po několika sekundách se v podokně **Disky** pro váš virtuální počítač zobrazí existující virtuální pevný disk připojený jako datový disk:

    ![Existující virtuální pevný disk připojený jako datový disk](./media/virtual-machines-classic-recovery-disks-portal/attached-disk.png)

## <a name="fix-issues-on-the-original-virtual-hard-disk"></a>Oprava problémů na původním virtuálním pevném disku
Když je existující virtuální pevný disk připojený, můžete podle potřeby provádět libovolné kroky údržby a řešení potíží. Jakmile vyřešíte problémy, pokračujte následujícími kroky.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Odpojení původního virtuálního pevného disku
Po vyřešení všech chyb odpojte existující virtuální pevný disk od virtuálního počítače pro řešení potíží. Virtuální pevný disk nebude možné použít s žádným jiným virtuálním počítačem, dokud se neuvolní zapůjčení, které virtuální pevný disk připojuje k virtuálnímu počítači pro řešení potíží.  

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. V nabídce na levé straně vyberte **Virtuální počítače (Classic)**.
3. Vyhledejte virtuální počítač pro obnovení. Vyberte Disky, klikněte pravým tlačítkem na disk a potom vyberte **Odpojit**.

## <a name="create-a-vm-from-the-original-hard-disk"></a>Vytvoření virtuálního počítače z původního pevného disku

Vytvoření virtuálního počítače z původního virtuálního pevného disku, použijte [webu Azure portal](https://portal.azure.com).

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V levém horním rohu portálu, vyberte **vytvořit prostředek** > **Compute** > **virtuálního počítače** > **z Galerie**.
3. V části **Zvolit image** vyberte **Moje disky** a potom vyberte původní virtuální pevný disk. Zkontrolujte informace o umístění. To je oblast, ve které musí být virtuální počítač nasazen. Klikněte na tlačítko Další.
4. V části **Konfigurace virtuálního počítače** zadejte název virtuálního počítače a vyberte jeho velikost.
