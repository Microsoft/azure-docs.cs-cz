---
title: Povolit zálohování při vytváření virtuálního počítače Azure pomocí Azure Backup
description: Popisuje, jak povolit zálohování při vytváření virtuálního počítače Azure pomocí Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: dacurwin
ms.openlocfilehash: 90f69371457bbfe37789b12971343f738ff35e8e
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639714"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Povolení zálohování při vytvoření virtuálního počítače Azure

Použijte službu Azure Backup k zálohování virtuálních počítačů Azure (VM). Virtuální počítače se zálohují podle plánu zadaného v zásadách zálohování a body obnovení se vytvářejí ze záloh. Body obnovení jsou uloženy v úložištích Recovery Services.

Tento článek podrobně popisuje, jak povolit zálohování při vytváření virtuálního počítače v Azure Portal.  

## <a name="before-you-start"></a>Než začnete

- [Ověřte](backup-support-matrix-iaas.md#supported-backup-actions) , které operační systémy jsou podporované, pokud při vytváření virtuálního počítače povolíte zálohování.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud jste k vašemu účtu ještě přihlášeni, přihlaste se k [Azure Portal](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Vytvoření virtuálního počítače s nakonfigurovaným zálohováním

1. V Azure Portal klikněte na **vytvořit prostředek**.

2. V Azure Marketplace klikněte na **COMPUTE**a pak vyberte image virtuálního počítače.

3. Nastavte virtuální počítač v souladu s pokyny pro [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) .

4. Na kartě **Správa** v části **Povolit zálohování**klikněte **na zapnuto**.
5. Azure Backup zálohy do trezoru Recovery Services. Pokud nemáte existující trezor, klikněte na **vytvořit nový** .
6. Přijměte navrhovaný název trezoru nebo zadejte vlastní.
7. Zadejte nebo vytvořte skupinu prostředků, ve které bude uložený trezor. Úložiště skupin prostředků se může lišit od skupiny prostředků virtuálních počítačů.

    ![Povolení zálohování pro virtuální počítač](./media/backup-during-vm-creation/enable-backup.png)

8. Přijměte výchozí zásady zálohování nebo upravte nastavení.
    - Zásady zálohování určují, jak často se mají vytvářet záložní snímky virtuálního počítače, a jak dlouho se mají uchovávat tyto záložní kopie.
    - Výchozí zásada zálohuje virtuální počítač jednou denně.
    - Můžete přizpůsobit vlastní zásady zálohování pro virtuální počítač Azure a provádět zálohování každý den nebo každý týden.
    - [Přečtěte si další](backup-azure-vms-introduction.md#backup-and-restore-considerations) informace o požadavcích na zálohování virtuálních počítačů Azure.
    - [Přečtěte si další informace](backup-instant-restore-capability.md) o funkci okamžitého obnovení.

      ![Výchozí zásady zálohování](./media/backup-during-vm-creation/daily-policy.png)


> [!NOTE]
> Služba Azure Backup vytvoří samostatnou skupinu prostředků (jinou než skupinu prostředků virtuálního počítače) k uložení snímku s formátem názvů **AzureBackupRG_geography_number** (příklad: AzureBackupRG_northeurope_1). Data v této skupině prostředků se uchovávají po dobu ve dnech, jak je uvedeno v části *uchování snímku okamžitého obnovení* v zásadách zálohování virtuálních počítačů Azure.  Použití zámku u této skupiny prostředků může způsobit selhání zálohování.<br>
Tato skupina prostředků by se měla taky vyloučit z omezení podle názvů nebo značek, protože zásady omezení by zablokovaly vytváření kolekcí bodů prostředků v takovém případě, že způsobí selhání zálohování.


## <a name="start-a-backup-after-creating-the-vm"></a>Po vytvoření virtuálního počítače spustit zálohování

Zálohování virtuálního počítače se spustí v souladu se zásadami zálohování. Doporučujeme však spustit prvotní zálohování.

Po vytvoření virtuálního počítače postupujte takto:

1. Ve vlastnostech virtuálního počítače klikněte na **zálohovat**. Stav virtuálního počítače čeká na počáteční zálohování, dokud se nespustí počáteční záloha.
2. Pokud chcete spustit zálohování na vyžádání, klikněte na **Zálohovat nyní** .

    ![Spuštění zálohování na vyžádání](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Použití šablony Správce prostředků k nasazení chráněného virtuálního počítače

Předchozí kroky vysvětlují, jak pomocí Azure Portal vytvořit virtuální počítač a chránit ho v trezoru Recovery Services. Pokud chcete rychle nasadit jeden nebo více virtuálních počítačů a chránit je v úložišti Recovery Services, podívejte se do šablony [nasazení virtuálního počítače s Windows a povolení zálohování](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).



## <a name="next-steps"></a>Další kroky

Teď, když jste virtuální počítač chránili, zjistěte, jak ho spravovat a obnovovat.

- [Správa a monitorování virtuálních počítačů](backup-azure-manage-vms.md)
- [Obnovení virtuálního počítače](backup-azure-arm-restore-vms.md)

Pokud narazíte na nějaké problémy, [Přečtěte si](backup-azure-vms-troubleshoot.md) příručku k odstraňování potíží.
