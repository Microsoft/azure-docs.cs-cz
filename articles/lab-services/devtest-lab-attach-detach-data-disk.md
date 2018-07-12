---
title: Připojit nebo odpojit datový disk k virtuálnímu počítači v Azure DevTest Labs | Dokumentace Microsoftu
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299070"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Připojit nebo odpojit datový disk k virtuálnímu počítači v Azure DevTest Labs
[Služba Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) spravuje účty úložiště přidružené k datových disků virtuálního počítače. Uživatel připojí nový datový disk k virtuálnímu počítači, určuje typ a velikost disku, který je nezbytný, a Azure disk vytvoří a postará automaticky. Pak lze odpojit datový disk z virtuálního počítače a buď znovu připojit později na jednom virtuálním počítači, nebo připojené k jinému virtuálnímu počítači, který patří do stejného uživatele.

Tato funkce je užitečná pro správu úložiště nebo software mimo každý virtuální počítač. Pokud úložiště nebo softwaru již existuje v rámci datového disku, může být snadno připojit, odpojit a znovu připojit jakýkoli virtuální počítač, který je vlastněn uživatele, který vlastní tento datový disk.

## <a name="attach-a-data-disk"></a>Připojení datového disku
Před připojení datového disku k virtuálnímu počítači, přečtěte si tyto typy:

- Velikost virtuálního počítače určuje, kolik datových disků můžete připojit. Podrobnosti najdete v tématu [velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Můžete pouze připojení datového disku k virtuálnímu počítači, na kterém běží. Ujistěte se, že virtuální počítač je spuštěný před pokusem o připojení datového disku.

### <a name="attach-a-new-disk"></a>Připojit nový disk
Postupujte podle těchto kroků k vytvoření a připojení nového spravovaného datového disku k virtuálnímu počítači v Azure DevTest Labs.

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
1. V seznamu testovacích prostředí vyberte požadované prostředí. 
1. Ze seznamu **Moje virtual machines**, vyberte spuštěného virtuálního počítače.
1. V nabídce na levé straně vyberte **disky**.

    ![Vyberte datové disky pro virtuální počítač](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png)
1. Zvolte **připojit nový** chcete vytvořit nový datový disk a připojit k virtuálnímu počítači.

    ![Připojit nový datový disk k virtuálnímu počítači](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Dokončení **připojit nový disk** podokně tak, že zadáte název datového disku, typ a velikost.

    ![Vyplňte formulář "připojit nový disk"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Vyberte **OK**.

Po chvíli se nový datový disk vytvoří a připojí k virtuálnímu počítači a zobrazí se v seznamu **datové disky** pro tento virtuální počítač.

### <a name="attach-an-existing-disk"></a>Připojení stávajícího disku
Postupujte podle těchto kroků se znovu připojit stávající dostupné datový disk do spuštěného virtuálního počítače. 

1. Vyberte spuštěný virtuální počítač, pro kterou chcete znovu připojit datový disk.
1. V nabídce na levé straně vyberte **disky**.
1. Vyberte **připojit stávající** připojit k dispozici datový disk k virtuálnímu počítači.

    ![Připojit stávající datový disk k virtuálnímu počítači](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing2.png)

1. Z **připojit stávající disk** podokně, vyberte OK.

    ![Připojit stávající datový disk k virtuálnímu počítači](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Po chvíli se datový disk je připojený k virtuálnímu počítači a zobrazí se v seznamu **datové disky** pro tento virtuální počítač.

## <a name="detach-a-data-disk"></a>Odpojení datového disku
Pokud už nepotřebujete datový disk, který je připojen k virtuálnímu počítači, můžete ho jednoduše odpojit. Odpojuje se odebrání disku z virtuálního počítače, ale zůstane v úložišti pro pozdější použití.

Pokud chcete znovu použít stávající data na disku, můžete znovu připojit ho k stejnému virtuálnímu počítači nebo na jiný.

### <a name="detach-from-the-vms-management-pane"></a>Odpojit z podokna Správa Virtuálního počítače
1. V seznamu virtuálních počítačů vyberte virtuální počítač, který má datový disk připojený.
1. V nabídce na levé straně vyberte **disky**.

    ![Vyberte datové disky pro virtuální počítač](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png) 
1. Ze seznamu **datové disky**, vyberte datový disk, který se má odpojit.
1. Vyberte **odpojit** z horní části podokna podrobností na disku.

    ![Odpojení datového disku](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Vyberte **Ano** potvrďte, že chcete odpojit datový disk.

Disk je odpojená a je možné připojit k jinému virtuálnímu počítači. 
### <a name="detach-from-the-labs-main-pane"></a>Odpojení od hlavní podokno testovacího prostředí
1. V hlavním podokně testovacího prostředí, vyberte **Moje datové disky**.

    ![Přístup k diskům data testovacího prostředí](./media/devtest-lab-attach-detach-data-disk/devtest-lab-my-data-disks.png)
1. Klikněte pravým tlačítkem na datový disk, který chcete odpojit – nebo vyberte jeho ikonu tří teček (...) – a zvolte **odpojit**.

    ![Odpojení datového disku](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Vyberte **Ano** potvrďte, že chcete odpojit.

   > [!NOTE]
   > Pokud datový disk je již odpojená, můžete ho odebrat ze seznamu dostupných datových disků tak, že vyberete **odstranit**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Upgrade nespravovaného datového disku
Pokud máte existující virtuální počítač, který používá nespravovaných datových disků, můžete snadno převést virtuální počítač použít spravované disky. Tento proces převede disk s operačním systémem i všechny připojené datové disky.

Pokud chcete upgradovat nespravovaného datového disku, postupujte podle kroků uvedených v tomto článku [odpojit datový disk](#detach-a-data-disk) z nespravovaného virtuálního počítače. Potom [znovu připojit disk](#attach-an-existing-disk) na spravovaném virtuálním počítači automatický upgrade dat disků z nespravovaných do spravovaných.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
Zjistěte, jak spravovat datové disky pro [nárokovatelné virtuální počítače](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

