---
title: Zálohování virtuálního počítače Azure z nastavení virtuálního počítače pomocí služby Azure Backup
description: Zjistěte, jak zálohovat virtuální počítač Azure pomocí služby Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 40557d4e71dfea5996396cde634f7a1c80913556
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430535"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Zálohování virtuálního počítače Azure z nastavení virtuálního počítače

Tento článek vysvětluje, jak zálohovat virtuální počítače Azure s [Azure Backup](backup-overview.md) služby. Můžete zálohovat virtuální počítače Azure pomocí několika metod:

- Jeden virtuální počítač Azure: Pokyny v tomto článku popisují, jak zálohovat virtuální počítač Azure přímo z nastavení virtuálního počítače.
- Více virtuálních počítačích Azure: Můžete nastavit trezor služby Recovery Services a konfiguraci zálohování několika virtuálních počítačů Azure. Postupujte podle pokynů v [v tomto článku](backup-azure-arm-vms-prepare.md) pro tento scénář.

 

## <a name="before-you-start"></a>Než začnete

1. [Přečtěte si](backup-architecture.md#how-does-azure-backup-work) fungování zálohování a [ověřte](backup-support-matrix.md#azure-vm-backup-support) požadavky na podporu. 
2. [Získejte přehled](backup-azure-vms-introduction.md) zálohování virtuálního počítače Azure.

### <a name="azure-vm-agent-installation"></a>Instalace agenta služby Azure VM

K zálohování virtuálních počítačů Azure, Azure Backup nainstaluje rozšíření virtuálního počítače agenta spuštěného na počítači. Pokud byl váš virtuální počítač vytvořen z image Azure marketplace, agent bude spuštěn. V některých případech, například pokud vytváříte vlastní virtuální počítač nebo migrací z místního počítače. můžete potřebovat ručně nainstalovat agenta. 

- Pokud potřebujete ručně nainstalovat agenta virtuálního počítače, postupujte podle pokynů pro [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) virtuálních počítačů. 
- Po instalaci agenta, když povolíte zálohování, Azure Backup nainstaluje rozšíření zálohování k agentovi. Aktualizace a opravy rozšíření bez zásahu uživatele.

## <a name="back-up-from-azure-vm-settings"></a>Zálohování z nastavení virtuálního počítače Azure


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na tlačítko **všechny služby** a do pole filtru zadejte **virtuálních počítačů**a potom klikněte na tlačítko **virtuálních počítačů**. 
3. Ze seznamu virtuálních počítačů vyberte virtuální počítač, které chcete zálohovat.
4. V nabídce virtuálního počítače klikněte na tlačítko **zálohování**. 
5. V **trezor služby Recovery Services**, postupujte takto:
  - Pokud už trezor máte, klikněte na tlačítko **vybrat existující**a vyberte trezor.
  - Pokud nemáte trezoru, klikněte na tlačítko **vytvořit nový**. Zadejte název pro trezor. Vytvoří se v stejném oblasti a skupině prostředků jako virtuální počítač. Tato nastavení nelze změnit, když povolíte zálohování přímo z nastavení virtuálního počítače.

  ![Průvodce povolením zálohování](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. V **výběr zásady zálohování**, postupujte takto:

  - Ponechte výchozí zásady. Zálohuje virtuální počítač jednou denně v době zadané a uchovává zálohy v trezoru po dobu 30 dnů.
  - Pokud nemáte, vyberte existující zásadu zálohování.
  - Vytvořit nové zásady a nastavení zásad.  

  ![Výběr zásady zálohování](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Klikněte na tlačítko **povolit zálohování**. To přiřadí zásady zálohování virtuálního počítače. 

    ![Tlačítko Povolit zálohování](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Můžete sledovat průběh konfigurace v oznámeních portálu.
9. Po dokončení úlohy v nabídce virtuálního počítače, klikněte na tlačítko **zálohování**. Na stránce se zobrazuje stav zálohování pro virtuální počítač, informace o body obnovení, spuštěné úlohy a výstrahy, které jsou vydány.

  ![Stav služby Backup](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Po povolení zálohování, [prvotní zálohování](#run-the-initial-backup) běží. Můžete okamžitě spustit prvotní zálohování, nebo počkejte, až se spustí podle plánu zálohování.
    - Až do dokončení prvotní zálohy **stav poslední zálohy** ukazovat **upozornění (nedokončené prvotní zálohování)**.
    - Pokud chcete zobrazit při příští plánované zálohování se spustí, klikněte na název zásady zálohování.
    
   

> [!NOTE]
> Azure Backup vytváří samostatné skupiny prostředků (než je skupina prostředků virtuálního počítače) pro uložení bodů obnovení, s formátem pojmenování **AzureBackupRG_geography_number** (Příklad: AzureBackupRG_northeurope_1). Tato skupina prostředků by neměly zamknout.



## <a name="run-a-backup-immediately"></a>Spustit zálohování ihned 

1. Chcete-li spustit zálohování ihned, v nabídce virtuálního počítače, klikněte na tlačítko **zálohování** > **zálohovat nyní**.

    ![Spustit zálohu](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. V **zálohovat nyní** pomocí ovládacího prvku kalendáře vyberte, až když bude uchovávat bod obnovení > a **OK**.
  
    ![Den uchovávání záloh](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Portálu oznámení vám umožňují vědět, že došlo ke spuštění úlohy zálohování. Pokud chcete monitorovat průběh zálohování, klikněte na tlačítko **zobrazit všechny úlohy**.




## <a name="back-up-from-the-recovery-services-vault"></a>Zálohování z trezoru služby Recovery Services

Postupujte podle pokynů v tomto článku a povolení zálohování pro virtuální počítače Azure s nastavením trezor služby Recovery Services zálohování Azure a povolení zálohování v trezoru.

## <a name="next-steps"></a>Další postup

- Pokud máte potíže s libovolný postup v tomto článku, obraťte se [Průvodce odstraňováním potíží](backup-azure-vms-troubleshoot.md).
- [Další informace o](backup-azure-manage-vms.md) správy zálohování.

