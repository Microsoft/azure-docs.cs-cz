---
title: Připojení nebo odpojení datového disku k virtuálnímu počítači v Azure DevTest Labs
description: Zjistěte, jak připojit nebo odpojit datový disk k virtuálnímu počítači v Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8eebfbda421233bcec780d441a4020acce740618
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91328509"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Připojení nebo odpojení datového disku k virtuálnímu počítači v Azure DevTest Labs
[Azure Managed disks](../virtual-machines/managed-disks-overview.md) spravuje účty úložiště přidružené k datovým diskům virtuálních počítačů. Uživatel připojí k virtuálnímu počítači nový datový disk, určí typ a velikost potřebného disku a Azure automaticky vytvoří a spravuje disk. Datový disk se pak může z virtuálního počítače odpojit a buď znovu připojit ke stejnému virtuálnímu počítači, nebo připojit k jinému virtuálnímu počítači, který patří stejnému uživateli.

Tato funkce je užitečná pro správu úložiště nebo softwaru mimo každý jednotlivý virtuální počítač. Pokud už úložiště nebo software v datovém disku existuje, dá se snadno připojit, odpojit a znovu připojit k libovolnému virtuálnímu počítači vlastněné uživatelem, který vlastní tento datový disk.

## <a name="attach-a-data-disk"></a>Připojení datového disku
Než připojíte datový disk k virtuálnímu počítači, přečtěte si tyto tipy:

- Velikost virtuálního počítače určuje, kolik datových disků můžete připojit. Podrobnosti najdete v tématu [velikosti pro virtuální počítače](../virtual-machines/sizes.md).
- Datový disk můžete připojit jenom k virtuálnímu počítači, na kterém je spuštěný. Než se pokusíte připojit datový disk, ujistěte se, že je virtuální počítač spuštěný.

### <a name="attach-a-new-disk"></a>Připojit nový disk
Pomocí těchto kroků můžete vytvořit a připojit nový spravovaný datový disk k virtuálnímu počítači v Azure DevTest Labs.

1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
1. V seznamu cvičení vyberte požadované testovací prostředí. 
1. V seznamu **virtuálních počítačů** vyberte spuštěný virtuální počítač.
1. V nabídce na levé straně vyberte **disky**.
1. Kliknutím na **připojit nový** vytvořte nový datový disk a připojte ho k virtuálnímu počítači.

    ![Připojit nový datový disk k virtuálnímu počítači](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Dokončete podokno **připojit nový disk** zadáním názvu datového disku, typu a velikosti.

    ![Dokončete formulář připojit nový disk.](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Vyberte **OK**.

Po chvíli se nový datový disk vytvoří a připojí k virtuálnímu počítači a zobrazí se v seznamu **datových disků** pro tento virtuální počítač.

### <a name="attach-an-existing-disk"></a>Připojení stávajícího disku
Postupujte podle těchto kroků a znovu připojte existující datový disk k běžícímu virtuálnímu počítači. 

1. Vyberte spuštěný virtuální počítač, pro který chcete znovu připojit datový disk.
1. V nabídce na levé straně vyberte **disky**.
1. Vyberte **připojit existující** a k virtuálnímu počítači připojte datový disk, který je k dispozici.

    ![Snímek obrazovky s vybraným nastavením "disky" a vybraným "připojit existující".](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. V podokně **připojit existující disk** vyberte OK.

    ![Připojit existující datový disk k virtuálnímu počítači](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Po chvíli se datový disk připojí k virtuálnímu počítači a zobrazí se v seznamu **datových disků** pro daný virtuální počítač.

## <a name="detach-a-data-disk"></a>Odpojení datového disku
Když už nepotřebujete datový disk, který je připojený k virtuálnímu počítači, můžete ho snadno odpojit. Odpojením se disk z virtuálního počítače odebere, ale ponechá ho v úložišti pro pozdější použití.

Pokud chcete znovu použít stávající data na disku, můžete ho znovu připojit ke stejnému virtuálnímu počítači nebo jinému.

### <a name="detach-from-the-vms-management-pane"></a>Odpojení od podokna správy virtuálního počítače
1. V seznamu virtuálních počítačů vyberte virtuální počítač, který má připojený datový disk.
1. V nabídce na levé straně vyberte **disky**.
1. V seznamu **datových disků** vyberte datový disk, který chcete odpojit.

    ![Výběr datových disků pro virtuální počítač](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. V horní části podokna podrobností disku vyberte **Odpojit** .

    ![Snímek obrazovky zobrazuje podokno podrobností o disku se zvýrazněnou akcí odpojit.](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Vyberte **Ano** a potvrďte tak, že chcete odpojit datový disk.

Disk je odpojený a je k dispozici pro připojení k jinému virtuálnímu počítači. 
### <a name="detach-from-the-labs-main-pane"></a>Odpojení od hlavního podokna testovacího prostředí
1. V hlavním podokně testovacího prostředí vyberte **Moje datové disky**.
1. Klikněte pravým tlačítkem na datový disk, který chcete odpojit – nebo vyberte tři tečky (**...**) a zvolte **Odpojit**.

    ![Odpojení datového disku](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Výběrem **Ano** potvrďte, že ho chcete odpojit.

   > [!NOTE]
   > Pokud je datový disk už odpojený, můžete ho odebrat ze seznamu dostupných datových disků tak, že zvolíte **Odstranit**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Upgrade nespravovaného datového disku
Pokud máte existující virtuální počítač, který používá nespravované datové disky, můžete virtuální počítač snadno převést na používání spravovaných disků. Tento proces převede disk s operačním systémem i všechny připojené datové disky.

Pokud chcete upgradovat nespravovaný datový disk, postupujte podle kroků uvedených v tomto článku a [odpojte datový disk](#detach-a-data-disk) od nespravovaného virtuálního počítače. Pak znovu [Připojte disk](#attach-an-existing-disk) ke SPRAVOVANÉmu virtuálnímu počítači a automaticky Upgradujte datový disk z nespravovaného na spravovaný.

## <a name="next-steps"></a>Další kroky
Naučte se spravovat datové disky pro [vynucené virtuální počítače](devtest-lab-add-claimable-vm.md#unclaim-a-vm).
