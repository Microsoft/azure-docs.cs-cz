---
title: Připojení nebo odpojení datového disku k virtuálnímu počítači v azure devtest labs
description: Zjistěte, jak připojit nebo odpojit datový disk s virtuálním počítačem v Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2020
ms.author: spelluru
ms.openlocfilehash: 3f18425408e6526904db85eae1c3a4db41d11a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78198770"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Připojení nebo odpojení datového disku k virtuálnímu počítači v azure devtest labs
[Spravované disky Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) spravují účty úložiště přidružené k datovým diskům virtuálních počítačů. Uživatel připojí nový datový disk k virtuálnímu počítači, určí typ a velikost disku, který je potřeba, a Azure vytvoří a spravuje disk automaticky. Datový disk pak můžete odpojit od virtuálního počítače a buď znovu připojit později ke stejnému virtuálnímu počítače nebo připojené k jinému virtuálnímu počítače, který patří stejnému uživateli.

Tato funkce je užitečná pro správu úložiště nebo softwaru mimo každý jednotlivý virtuální počítač. Pokud úložiště nebo software již existuje uvnitř datového disku, lze jej snadno připojit, odpojit a znovu připojit k libovolnému virtuálnímu virtuálnímu počítače, který je vlastněn uživatelem, který tento datový disk vlastní.

## <a name="attach-a-data-disk"></a>Připojení datového disku
Než připojíte datový disk k virtuálnímu počítače, přečtěte si tyto tipy:

- Velikost virtuálního počítače určuje, kolik datových disků můžete připojit. Podrobnosti najdete [v tématu Velikosti pro virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- K spuštěnému virtuálnímu počítače můžete připojit jenom datový disk. Ujistěte se, že virtuální počítače běží před pokusem o připojení datového disku.

### <a name="attach-a-new-disk"></a>Připojení nového disku
Podle těchto kroků vytvořte a připojte nový spravovaný datový disk k virtuálnímu počítači v Azure DevTest Labs.

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
1. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí. 
1. Ze seznamu **Moje virtuální počítače**vyberte spuštěný virtuální počítač.
1. V nabídce vlevo vyberte **Disky**.
1. Zvolte **Připojit nový,** chcete-li vytvořit nový datový disk a připojit jej k virtuálnímu počítače.

    ![Připojení nového datového disku k virtuálnímu počítači](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Dokončete podokno **Připojit nový disk** zadáním názvu datového disku, typu a velikosti.

    ![Vyplňte formulář "připojit nový disk"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Vyberte **OK**.

Po několika okamžicích se vytvoří nový datový disk a připojí se k virtuálnímu počítače a zobrazí se v seznamu **datových disků** pro daný virtuální modul.

### <a name="attach-an-existing-disk"></a>Připojení stávajícího disku
Podle těchto kroků znovu připojte existující dostupný datový disk ke spuštěnému virtuálnímu počítače. 

1. Vyberte spuštěný virtuální počítače, pro který chcete znovu připojit datový disk.
1. V nabídce vlevo vyberte **Disky**.
1. Vyberte **Připojit existující,** chcete-li k virtuálnímu počítače připojit dostupný datový disk.

    ![Připojení existujícího datového disku k virtuálnímu počítači](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. V podokně **Připojit existující disk** vyberte OK.

    ![Připojení existujícího datového disku k virtuálnímu počítači](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Po několika okamžicích je datový disk připojen k virtuálnímu počítače a zobrazí se v seznamu **datových disků** pro daný virtuální počítače.

## <a name="detach-a-data-disk"></a>Odpojení datového disku
Když už nepotřebujete datový disk, který je připojený k virtuálnímu počítače, můžete ho snadno odpojit. Odpojení odebere disk z virtuálního počítače, ale uchovává jej v úložišti pro pozdější použití.

Pokud chcete znovu použít existující data na disku, můžete je znovu připojit ke stejnému virtuálnímu počítači nebo k jinému.

### <a name="detach-from-the-vms-management-pane"></a>Odpojení od podokna správy virtuálního zařízení
1. Ze seznamu virtuálních počítačů vyberte virtuální počítač, který má připojený datový disk.
1. V nabídce vlevo vyberte **Disky**.
1. Ze seznamu **datových disků**vyberte datový disk, který chcete odpojit.

    ![Výběr datových disků pro virtuální počítač](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. V horní části podokna podrobností disku vyberte **Odpojit.**

    ![Odpojení datového disku](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Výběrem **možnosti Ano** potvrďte, že chcete datový disk odpojit.

Disk je odpojený a je k dispozici pro připojení k jinému virtuálnímu počítače. 
### <a name="detach-from-the-labs-main-pane"></a>Odpojení od hlavního podokna laboratoře
1. V hlavním podokně testovacího prostředí vyberte **možnost Moje datové disky**.
1. Klikněte pravým tlačítkem myši na datový disk, který chcete odpojit – nebo vyberte jeho tři tečky (**...**) – a zvolte **Odpojit**.

    ![Odpojení datového disku](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Výběrem **možnosti Ano** potvrďte, že ji chcete odpojit.

   > [!NOTE]
   > Pokud je datový disk již odpojen, můžete jej odebrat ze seznamu dostupných datových disků výběrem **možnosti Odstranit**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Upgrade nespravovaného datového disku
Pokud máte existující virtuální počítače, který používá nespravované datové disky, můžete ho snadno převést na spravované disky. Tento proces převádí disk operačního systému i všechny připojené datové disky.

Chcete-li upgradovat nespravovaný datový disk, postupujte podle kroků popsaných v tomto článku [a odpojte datový disk](#detach-a-data-disk) od nespravovaného virtuálního počítače. Potom [znovu připojte disk](#attach-an-existing-disk) ke spravovanému virtuálnímu počítače a automaticky upgradujte datový disk z nespravovaného na spravovaný.

## <a name="next-steps"></a>Další kroky
Zjistěte, jak spravovat datové disky pro [nárokovatelné virtuální počítače](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

