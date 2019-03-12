---
title: Povolit zálohování virtuálních počítačů Azure při vytváření
description: Jak povolit zálohování virtuálních počítačů Azure během procesu vytváření.
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: fd2beaa39f03d4f2342c94bf1cd8b8aea7440e62
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780439"
---
# <a name="enable-backup-when-you-create-an-azure-virtual-machine"></a>Povolit zálohování při vytváření virtuálního počítače Azure

Použití služby Azure Backup k zálohování virtuálních počítačů Azure (VM). Virtuální počítače se zálohují automaticky podle plánu, zadaný v zásadách zálohování a jsou vytvořeny body obnovení ze zálohy. Body obnovení jsou uloženy v trezorech služby Recovery Services.

Tento článek podrobně popisuje, jak povolit zálohování při vytváření virtuálního počítače (VM) na webu Azure Portal.  

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Pokud nejste ještě přihlášení ke svému účtu, přihlaste se k [webu Azure portal](https://portal.azure.com).
 
## <a name="create-a-vm-with-backup-configured"></a>Vytvoření Virtuálního počítače s zálohování je nakonfigurované 

1. V levém horním rohu webu Azure portal, vyberte **nový**.

1. Vyberte **Compute**a potom vyberte image virtuálního počítače.

1. Zadejte informace o virtuálním počítači. Uživatelské jméno a heslo, které zadáte, se použije k přihlášení k virtuálnímu počítači. Jakmile budete hotovi, vyberte **OK**. 

1. Vyberte velikost virtuálního počítače.  

1. V části **nastavení** > **zálohování**vyberte **povoleno** otevřete nastavení konfigurace zálohování.

   - Pokud chcete přijmout výchozí hodnoty, vyberte **OK** na **nastavení** stránky. Pak přejdete **Souhrn** stránky vytvořte virtuální počítač. Přeskočit kroky 6 až 8.
   - Chcete-li změnit hodnoty konfigurace zálohování, postupujte podle dalších kroků.  

1. Vytvořte nebo vyberte trezor služby Recovery Services pro uložení zálohy virtuálního počítače. Pokud vytváříte trezor služby Recovery Services, můžete skupina prostředků trezoru.  

    ![Nastavení konfigurace zálohování na stránce vytváření virtuálních počítačů](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Skupiny prostředků pro trezor služby Recovery Services může být jiné než skupiny prostředků pro virtuální počítač.  

1. Ve výchozím nastavení zásady zálohování je vybraná pro ochranu virtuálního počítače. Zásady zálohování Určuje, jak často chcete pořizovat snímky zálohování a jak dlouho se mají uchovávat tyto záložní kopie. 

   - Můžete přijmout výchozí zásady, nebo můžete vytvořit nebo vybrat jiné zásady zálohování. 
   - Chcete-li upravit zásady zálohování, vyberte **zásady zálohování** a změňte hodnoty.  

1. Jakmile budete hotovi, nastavování hodnot konfigurace zálohování, vyberte **OK** na **nastavení** stránky.  

1. Na **Souhrn** stránce po uplynutí ověřování, vyberte **vytvořit** k vytvoření virtuálního počítače, který používá nastavení nakonfigurované zálohování. 

## <a name="start-a-backup-after-creating-the-vm"></a>Spustit zálohování po vytvoření virtuálního počítače 

I když jste nakonfigurovali zásady zálohování pro virtuální počítač, je vhodné vytvořit prvotní zálohování. 

Po dokončení vytvoření šablony virtuálního počítače, přejděte na **operace** v levé nabídce a vyberte **zálohování** zobrazíte podrobnosti zálohování pro virtuální počítač. Můžete použít tuto stránku:

- Spustit zálohu na vyžádání.
- Obnovení úplné virtuálního počítače nebo jeho disky.
- Obnovení souborů ze záloh virtuálních počítačů.
- Změňte zásady zálohování přidružené k virtuálnímu počítači.  

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Nasazení chráněného virtuálního počítače pomocí šablony Resource Manageru

Předchozí kroky popisují, jak pomocí webu Azure portal k vytvoření virtuálního počítače a chránit v trezoru služby Recovery Services. Kvůli rychlému nasazení jednoho nebo více virtuálních počítačů a chránit je trezor služby Recovery Services, uvidí šablonu [nasazení virtuálního počítače s Windows a povolení zálohování](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy 

### <a name="which-vm-images-support-backup-configuration-during-vm-creation"></a>Které imagí virtuálních počítačů podporují konfiguraci zálohování během vytváření virtuálních počítačů?

Pro povolení zálohování během vytváření virtuálních počítačů jsou podporovány následující základní Image publikované společností Microsoft. Pro jiné virtuální počítače můžete povolit zálohování po vytvoření virtuálního počítače. Další informace najdete v tématu [povolit zálohování po vytvoření virtuálního počítače](quick-backup-vm-portal.md).

- **Windows** – Windows Server 2016 Datacenter, jádro systému Windows Server 2016 Datacenter, Windows Server 2012 Datacenter, Windows Server 2012 R2 Datacenter, Windows Server 2008 R2 SP1 
- **Ubuntu** – Ubuntu Server 17.10, Ubuntu Server č. 17.04, Ubuntu Server 16.04 (LTS), Ubuntu Server 14.04 (LTS) 
- **Red Hat** -RHEL 6.7, 6.8 6.9, 7.2, 7.3, 7.4 
- **SUSE** -SP4 operačním systémem SUSE Linux Enterprise Server 11, 12 SP2, 12 SP3 
- **Debian** – Debian 8, Debian 9 
- **CentOS** - CentOS 6.9, CentOS 7.3 
- **Oracle Linux** - Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3 
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Stojí zálohování součástí cena za virtuální počítače? 

Ne. Náklady na zálohování jsou oddělené od nákladů virtuální počítač. Další informace o cenách zálohování najdete v tématu [ceny za službu Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Jaká oprávnění jsou vyžadována k povolení zálohování na virtuálním počítači? 

Pokud jste Přispěvatel virtuálních počítačů, můžete povolit zálohování na virtuálním počítači. Pokud používáte vlastní roli, potřebujete následující oprávnění k povolení zálohování na virtuálním počítači: 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Pokud váš trezor služby Recovery Services a virtuální počítač různých skupin prostředků, ujistěte se, že máte oprávnění k zápisu ve skupině prostředků pro trezor služby Recovery Services.  

## <a name="next-steps"></a>Další postup 

Teď, když máte chráněný virtuální počítač, naleznete v následujících článcích se naučíte spravovat a obnovovat virtuální počítače:

- [Správa a monitorování virtuálních počítačů](backup-azure-manage-vms.md) 
- [Obnovení virtuálních počítačů](backup-azure-arm-restore-vms.md) 
