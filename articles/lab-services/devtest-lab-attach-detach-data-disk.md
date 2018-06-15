---
title: Připojit nebo odpojit datový disk k virtuálnímu počítači v Azure DevTest Labs | Microsoft Docs
description: Zjistěte, jak připojit nebo odpojit datový disk k virtuálnímu počítači v Azure DevTest Labs
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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 193b66cf8bdaaefed5f073bec3ecb9050d076f19
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787588"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Připojit nebo odpojit datový disk k virtuálnímu počítači v Azure DevTest Labs
[Disky systému Azure spravované](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) spravuje účty úložiště přidružené ke datových disků virtuálního počítače. Uživatel připojí, nová data na disku k virtuálnímu počítači, určuje typ a velikost disku, který je potřeba, a Azure vytváří a spravuje disku automaticky. Z virtuálního počítače lze poté odpojit datový disk a buď znovu připojit později na stejný virtuální počítač nebo připojené k jiné virtuální počítač, který patří do stejného uživatele.

Tato funkce je užitečný pro správu úložiště nebo softwaru mimo každý jednotlivý virtuální počítač. Pokud úložiště nebo softwaru již existuje v rámci datový disk, ho můžete být snadno připojit, odpojit a znovu připojit k žádné virtuální počítače, který je vlastněn uživatele, který vlastní tento datový disk.

## <a name="attach-a-data-disk"></a>Připojení datového disku
Než můžete připojit datový disk k virtuálnímu počítači, projděte si tyto tipy:

- Velikost virtuálního počítače určuje, kolik datových disků můžete připojit. Podrobnosti najdete v tématu [velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Je možné ještě připojit datový disk k virtuálnímu počítači se systémem. Zkontrolujte, zda že je virtuální počítač spuštěný, než se pokusíte připojit datový disk.

### <a name="attach-a-new-disk"></a>Připojit nový disk
Postupujte podle těchto kroků a vytvořte nový spravovaný datový disk k virtuálnímu počítači v Azure DevTest Labs.

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.
1. Ze seznamu labs vyberte požadované testovací prostředí. 
1. Ze seznamu **Můj virtuální počítače**, vyberte spuštění virtuálního počítače.
1. V nabídce na levé straně vyberte **disky**.

    ![Vyberte datové disky pro virtuální počítač](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png)
1. Zvolte **připojit nový** vytvořit nový datový disk a jeho připojení k virtuálnímu počítači.

    ![Připojit nový datový disk k virtuálnímu počítači](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Dokončení **připojit nový disk** podokně tak, že zadáte název datového disku, typ a velikost.

    ![Vyplňte formulář "připojit nový disk"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Vyberte **OK**.

Po chvíli se nový datový disk se vytvoří a připojené k virtuálnímu počítači a zobrazí se v seznamu **datových disků** tohoto virtuálního počítače.

### <a name="attach-an-existing-disk"></a>Připojení stávajícího disku
Postupujte podle těchto kroků znovu připojit existující disk data k dispozici pro spuštění virtuálního počítače. 

1. Vyberte spuštění virtuálního počítače, pro které chcete znovu připojit datový disk.
1. V nabídce na levé straně vyberte **disky**.
1. Vyberte **připojit existující** připojit k dostupné datový disk k virtuálnímu počítači.

    ![Připojit stávající datový disk k virtuálnímu počítači](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing2.png)

1. Z **připojit stávající disk** podokně, vyberte možnost OK.

    ![Připojit stávající datový disk k virtuálnímu počítači](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Po chvíli se datový disk je připojen k virtuálnímu počítači a zobrazí se v seznamu **datových disků** tohoto virtuálního počítače.

## <a name="detach-a-data-disk"></a>Odpojení datového disku
Pokud již nepotřebujete datový disk, který je připojen k virtuálnímu počítači, můžete ho snadno odpojit. Odpojování odebere disk z virtuálního počítače, ale zachová v úložišti pro pozdější použití.

Pokud chcete znovu použít stávající data na disku, můžete ho připojte stejný virtuální počítač nebo na jiný.

### <a name="detach-from-the-vms-management-pane"></a>Odpojení od podokně Správa Virtuálního počítače
1. Ze seznamu virtuálních počítačů vyberte virtuální počítač, který má datový disk připojit.
1. V nabídce na levé straně vyberte **disky**.

    ![Vyberte datové disky pro virtuální počítač](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png) 
1. Ze seznamu **datových disků**, vyberte datový disk, který chcete odpojit.
1. Vyberte **odpojení** z horní části podokna podrobností na disk.

    ![Odpojení datového disku](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Vyberte **Ano** potvrďte, že chcete odpojit datový disk.

Disk je odpojit a je k dispozici pro připojení k jiným virtuálním Počítačem. 
### <a name="detach-from-the-labs-main-pane"></a>Odpojení od hlavním podokně v prostředí
1. Na vašem testovacím prostředí hlavním podokně vyberte **Moje datových disků**.

    ![Přístup v prostředí datových disků](./media/devtest-lab-attach-detach-data-disk/devtest-lab-my-data-disks.png)
1. Klikněte pravým tlačítkem na datový disk, který chcete odpojit – nebo vyberte jeho třemi tečkami (...) – a zvolte **odpojení**.

    ![Odpojení datového disku](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Vyberte **Ano** potvrďte, že chcete jej odpojte.

   > [!NOTE]
   > Pokud datový disk je již odpojená, můžete odebrat ze seznamu dostupných datových disků výběrem **odstranit**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Upgrade nespravované datový disk
Pokud máte existující virtuální počítač, který používá nespravované datových disků, můžete snadno převést virtuální počítač použít spravované disky. Tento proces převede disk operačního systému a všechny připojené datových disků.

Pokud chcete upgradovat nespravované datový disk, postupujte podle kroků uvedených v tomto článku [odpojit datový disk](#detach-a-data-disk) z nespravovaných virtuálního počítače. Potom [připojte disk](#attach-an-existing-disk) na spravovaném virtuálním počítači automatický upgrade data disku z nespravované na spravované.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
Zjistěte, jak spravovat datové disky pro [vymahatelných virtuálních počítačů](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

