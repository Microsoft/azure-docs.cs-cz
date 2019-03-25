---
title: Povolit zálohování virtuálního počítače Azure vytvořené pomocí služby Azure Backup
description: Popisuje, jak povolit zálohování virtuálního počítače Azure vytvořené pomocí služby Azure Backup.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: d96b898c8f72abd7e4eb3522ae046e9fc926f387
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403574"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Povolení zálohování při vytvoření virtuálního počítače Azure

Použití služby Azure Backup k zálohování virtuálních počítačů Azure (VM). Virtuální počítače se zálohují automaticky podle plánu, zadaný v zásadách zálohování a jsou vytvořeny body obnovení ze zálohy. Body obnovení jsou uloženy v trezorech služby Recovery Services.

Tento článek podrobně popisuje, jak povolit zálohování při vytváření virtuálního počítače (VM) na webu Azure Portal.  

## <a name="before-you-start"></a>Než začnete

- [Zkontrolujte](backup-support-matrix-iaas.md#supported-backup-actions) které operační systémy jsou podporovány, pokud povolíte zálohování při vytváření virtuálního počítače.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Pokud nejste ještě přihlášení ke svému účtu, přihlaste se k [webu Azure portal](https://portal.azure.com).
 
## <a name="create-a-vm-with-backup-configured"></a>Vytvoření Virtuálního počítače s zálohování je nakonfigurované

1. Na webu Azure portal, klikněte na tlačítko **vytvořit prostředek**.

2. Na webu Azure Marketplace, klikněte na tlačítko **Compute**a potom vyberte image virtuálního počítače.

3. Nastavení virtuálního počítače v souladu s maticí [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) pokyny.

4. Na **správu** kartě **povolit zálohování**, klikněte na tlačítko **na**.
5. Azure Backup zálohování do trezoru služby Recovery Services. Klikněte na tlačítko **vytvořit nový** Pokud nemáte existující trezor.
6. Přijmout název trezoru navrhované nebo zadat vlastní.
7. Určete nebo vytvořte skupinu prostředků, ve kterém budou umístěné v úložišti. Trezor skupiny prostředků se může lišit od skupina prostředků virtuálního počítače.

    ![Povolit zálohování virtuálního počítače](./media/backup-during-vm-creation/enable-backup.png) 

8. Přijměte výchozí zásady zálohování, nebo upravte nastavení.
    - Zásady zálohování Určuje, jak často se snímky se zálohami virtuálních počítačů a jak dlouho se mají uchovávat tyto záložní kopie. 
    - Výchozí zásady zálohuje virtuální počítač jednou denně.
    - Můžete přizpůsobit vlastní zásady zálohování pro virtuální počítač Azure zálohování denně nebo týdně.
    - [Další informace](backup-azure-vms-introduction.md#backup-and-restore-considerations) aspekty zálohování pro virtuální počítače Azure.
    - [Další informace](backup-instant-restore-capability.md) o rychlé obnovení.

      ![Výchozí zásady zálohování](./media/backup-during-vm-creation/daily-policy.png) 


## <a name="start-a-backup-after-creating-the-vm"></a>Spustit zálohování po vytvoření virtuálního počítače 

Zálohování virtuálního počítače se spustí v souladu s zásady zálohování. Doporučujeme však spustit prvotní zálohování. 

Po vytvoření virtuálního počítače, postupujte takto:

1. Ve vlastnostech virtuálního počítače klikněte na tlačítko **zálohování**. Stav virtuálního počítače je počáteční záložní čekající na vyřízení, dokud se nespustí prvotní zálohování
2. Klikněte na tlačítko **zálohovat nyní** spustit zálohu na vyžádání.

    ![Spustit zálohu na vyžádání](./media/backup-during-vm-creation/run-backup.png) 

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Nasazení chráněného virtuálního počítače pomocí šablony Resource Manageru

Předchozí kroky popisují, jak pomocí webu Azure portal k vytvoření virtuálního počítače a chránit v trezoru služby Recovery Services. Kvůli rychlému nasazení jednoho nebo více virtuálních počítačů a chránit je trezor služby Recovery Services, uvidí šablonu [nasazení virtuálního počítače s Windows a povolení zálohování](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).



## <a name="next-steps"></a>Další postup 

Teď, když máte chráněný virtuální počítač, zjistěte, jak spravovat a obnovovat je.

- [Správa a monitorování virtuálních počítačů](backup-azure-manage-vms.md) 
- [Obnovení virtuálního počítače](backup-azure-arm-restore-vms.md) 

Pokud budete mít nějaké problémy, [zkontrolujte](backup-azure-vms-troubleshoot.md) Průvodce odstraňováním potíží.
