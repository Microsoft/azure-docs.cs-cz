---
title: Povolit zálohování virtuálních počítačů Azure při vytváření
description: Postup povolení zálohování virtuálních počítačů Azure během procesu vytváření.
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: 518d171c96b9c4f9bf3e195a7130f4c022b7ad07
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879872"
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Povolit zálohování během vytváření virtuálních počítačů Azure 

Služba Azure Backup poskytuje rozhraní pro vytvoření a konfigurace zálohování do cloudu. Ochrana dat pomocí zálohy, označení body obnovení v pravidelných intervalech. Azure Backup vytváří body obnovení, které je možné uchovávat v geograficky redundantních trezorech obnovení. Tento článek podrobně popisuje, jak povolit zálohování při vytváření virtuálního počítače (VM) na webu Azure Portal.  

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Pokud si nejste ještě v přihlášení ke svému účtu, přihlaste se k [webu Azure portal](http://portal.azure.com).
 
## <a name="create-virtual-machine-with-backup-configured"></a>Vytvoření virtuálního počítače pomocí zálohování je nakonfigurované 

1. V levém horním rohu webu Azure portal klikněte na tlačítko **nový**. 

2. Vyberte **Compute**a potom vyberte image virtuálního počítače.   

3. Zadejte informace o virtuálním počítači. Uživatelské jméno a heslo se používá k přihlášení k virtuálnímu počítači. Jakmile budete hotovi, klikněte na **OK**. 

4. Vyberte velikost virtuálního počítače.  

5. V části **Nastavení > Zálohování**, klikněte na tlačítko **povoleno** a zobrazte si nastavení konfigurace zálohování. Můžete přijmout výchozí hodnoty a klikněte na tlačítko **OK** na stránce nastavení přejděte na stránce Souhrn vytvoření virtuálního počítače. Pokud chcete změnit hodnoty, postupujte podle dalších kroků.  

6. Vytvořte nebo vyberte trezor služby Recovery Services, který uchovává zálohy virtuálního počítače. Při vytváření trezoru služby recovery services, můžete skupina prostředků trezoru.  

    ![Konfigurace zálohování na virtuálním počítači vytvořit stránku](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Skupiny prostředků pro trezor služby Recovery Services může být jiné než skupiny prostředků pro virtuální počítač.  
    > 
    > 

7. Ve výchozím nastavení zásady zálohování je vybraná k rychlému zajištění ochrany virtuálního počítače. Zásady zálohování Určuje, jak často se snímky se zálohami a jak dlouho chcete uchovat tyto záložní kopie. Můžete přijmout výchozí zásady, nebo můžete vytvořit nebo vybrat jiné zásady zálohování. Chcete-li upravit zásady zálohování, vyberte **zásady zálohování** a změňte hodnoty zásad.  

8. Až budete spokojení s hodnotami konfiguraci zálohování, na stránce nastavení, klikněte na tlačítko **OK**.  

9. Na stránce Souhrn po uplynutí ověřování, klikněte na tlačítko **vytvořit** k vytvoření virtuálního počítače, který používá nastavení nakonfigurované zálohování. 

## <a name="initiate-a-backup-after-creating-the-vm"></a>Spustit zálohování po vytvoření virtuálního počítače 

I když vytvoření zásady zálohování, je vhodné vytvořit prvotní zálohování. Chcete-li zobrazit podrobnosti zálohování pro virtuální počítač po dokončení šablonu vytvoření virtuálního počítače, ze **operace** nastavení v nabídce vlevo klikněte na tlačítko **zálohování**. Můžete spustit zálohu na vyžádání, obnovení úplné virtuální počítač nebo všechny disky, obnovení souborů ze záloh virtuálních počítačů nebo změnit zásady zálohování přidružené k virtuálnímu počítači.  

## <a name="using-a-resource-manager-template-to-deploy-a-protected-vm"></a>Nasazení chráněného virtuálního počítače pomocí šablony Resource Manageru

Předchozí kroky popisují, jak pomocí webu Azure portal k vytvoření virtuálního počítače a chránit je trezor služby Recovery Services. Pokud chcete rychle nasadit jeden nebo více virtuálních počítačů a chránit je trezor služby Recovery Services, uvidí šablonu, [nasazení virtuálního počítače s Windows a povolení zálohování](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>Které imagí virtuálních počítačů povolit zálohování v době vytvoření virtuálního počítače? 

Pro povolení zálohování během vytváření virtuálních počítačů jsou podporovány následující seznam základní Image publikované společností Microsoft. Pro jiné virtuální počítače můžete povolit zálohování po vytvoření virtuálního počítače. Další informace [povolit zálohování po vytvoření virtuálního počítače](quick-backup-vm-portal.md) 

- **Windows** – Windows Server 2016 datového centra, jádra Windows serveru 2016 datového centra, Windows Server 2012 DataCenter, Windows Server 2012 R2 DataCenter, Windows Server 2008 R2 SP1 
- **Ubuntu** – Ubuntu Server 1710, Ubuntu Server 1704, UUbuntu Server 1604(LTS) 1404(LTS) Ubuntu Server 
- **Red Hat** -RHEL 6.7, 6.8 6.9, 7.2, 7.3, 7.4 
- **SUSE** -SP4 operačním systémem SUSE Linux Enterprise Server 11, 12 SP2, 12 SP3 
- **Debian** – Debian 8, Debian 9 
- **CentOS** -CentOS 6.9, CentOS 7.3 
- **Oracle Linux** – Oracle Linux 6.7, 6.8 6.9, 7.2, 7.3 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>Je záložní cena součástí cena za virtuální počítače? 

Ne, náklady na zálohování jsou samostatné a odlišné od nákladů na virtuální počítače. Další informace o cenách zálohování, najdete v článku [ceny za službu Backup site](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Jaká oprávnění jsou vyžadována k povolení zálohování na virtuálním počítači? 

Pokud jste Přispěvatel virtuálních počítačů, můžete povolit zálohování na virtuálním počítači. Pokud používáte vlastní roli, potřebujete následující oprávnění, aby úspěšně povolit zálohování na virtuálním počítači. 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Pokud váš trezor služby recovery services a virtuální počítače různých skupin prostředků, ujistěte se, že máte oprávnění k zápisu ve skupině prostředků trezoru služeb zotavení.  

## <a name="next-steps"></a>Další postup 

Teď, když jste nastavili ochranu virtuálního počítače, naleznete v následujících článcích se dozvíte o úlohy správy virtuálních počítačů a jak obnovit virtuální počítače. 

- [Správa a monitorování virtuálních počítačů](backup-azure-manage-vms.md) 
- [Obnovení virtuálních počítačů](backup-azure-arm-restore-vms.md) 
