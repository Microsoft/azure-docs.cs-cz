---
title: Povolit zálohování virtuálních počítačů Azure při vytváření | Microsoft Docs
description: Prohlédněte si postup povolení zálohování virtuálního počítače Azure během procesu vytváření.
services: backup, virtual-machines
documentationcenter: ''
author: markgalioto
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: e16714eee697d9dc8102b5f29d9639d650517562
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Povolit zálohování při vytvoření virtuálního počítače Azure 

Služba Azure Backup poskytuje rozhraní pro vytvoření a konfigurace zálohování do cloudu. Ochrana dat pomocí záloh, body obnovení, volá se v pravidelných intervalech. Azure Backup vytváří body obnovení, které je možné uchovávat v geograficky redundantních trezorech obnovení. Tento článek podrobně popisují k povolení zálohování při vytváření virtuálního počítače (VM) na portálu Azure.  

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Pokud jste ještě nejsou v přihlášení ke svému účtu, přihlaste se k [portál Azure](http://portal.azure.com).
 
## <a name="create-virtual-machine-with-backup-configured"></a>Vytvoření virtuálního počítače pomocí zálohování je nakonfigurované 

1. V levém horním rohu portálu Azure klikněte na **nový**. 

2. Vyberte **výpočetní**a pak vyberte bitovou kopii virtuálního počítače.   

3. Zadejte informace o virtuálním počítači. Uživatelské jméno a heslo zadané se používá k přihlášení k virtuálnímu počítači. Jakmile budete hotovi, klikněte na **OK**. 

4. Vyberte velikost virtuálního počítače.  

5. V části **Nastavení > Zálohování**, klikněte na tlačítko **povoleno** se zprovoznit nastavení konfigurace zálohy. Můžete přijmout výchozí hodnoty a klikněte na tlačítko **OK** na stránce nastavení pokračovat do souhrnná stránka pro vytvoření virtuálního počítače. Pokud chcete změnit hodnoty, proveďte další kroky.  

6. Vytvořte nebo vyberte trezor služeb zotavení, který obsahuje zálohy virtuálního počítače. Při vytváření trezoru služeb zotavení, můžete skupinu prostředků pro úložiště.  

    ![Konfigurace zálohování na virtuální počítač vytvořit stránku](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Skupinu prostředků pro trezor služeb zotavení může být jiná než skupina prostředků pro virtuální počítač.  
    > 
    > 

7. Ve výchozím nastavení je zaškrtnuté zásady zálohování můžete rychle zajistit ochranu virtuálního počítače. Zásady zálohování Určuje, jak často se bude trvat snímků zálohy a jak dlouho chcete uchovat tyto záložní kopie. Můžete přijmout výchozí zásady, nebo můžete vytvořit nebo vybrat jiné zásady zálohování. Chcete-li upravit zásady zálohování, vyberte **zásady zálohování** a změnit hodnoty zásad.  

8. Pokud jste radostí hodnotami konfigurace zálohování na stránce nastavení klikněte na tlačítko **OK**.  

9. Na stránce Souhrn po uplynutí ověřování, klikněte na tlačítko **vytvořit** k vytvoření virtuálního počítače, který používá nakonfigurované nastavení zálohování. 

## <a name="initiate-a-backup-after-creating-the-vm"></a>Spustit zálohování, po vytvoření virtuálního počítače 

I když byly vytvořeny zásady zálohování, je vhodné vytvořit prvotní zálohování. Chcete-li zobrazit podrobnosti zálohování pro virtuální počítač po dokončení šablony vytvoření virtuálního počítače, z **operace** nastavit nabídky na levé straně, klikněte na tlačítko **zálohování**. To můžete spustit zálohování na vyžádání, obnovte úplnou virtuálního počítače nebo všechny disky, obnovit soubory ze zálohy virtuálního počítače nebo změnit zásady zálohování, které jsou spojené s virtuálním počítačem.  

## <a name="using-a-resource-manager-template-to-deploy-a-protected-vm"></a>Pomocí šablony Resource Manageru k nasazení chráněných virtuálních počítačů

Předchozí kroky vysvětlují, jak používat portál Azure k vytvoření virtuálního počítače a chránit do trezoru služeb zotavení. Pokud chcete rychle nasadit jeden nebo více virtuálních počítačů a chránily do trezoru služeb zotavení, uvidí šablonu, [nasadit virtuální počítač s Windows a povolení zálohování](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>Které Image virtuálních počítačů povolit zálohování v době vytvoření virtuálního počítače? 

K povolení zálohování při vytváření virtuálních počítačů jsou podporovány v následujícím seznamu základní Image vydaná společností Microsoft. U ostatních virtuálních počítačů můžete povolit zálohování po vytvoření virtuálního počítače. Další informace [povolit zálohování po vytvoření virtuálního počítače](quick-backup-vm-portal.md) 

- **Windows** -Windows Server 2016 datového centra, jádra Windows Server 2016 datového centra, Windows Server 2012 DataCenter, Windows Server 2012 R2 DataCenter, Windows Server 2008 R2 SP1 
- **Ubuntu** - Ubuntu Server 1710, Ubuntu Server 1704, UUbuntu Server 1604(LTS), Ubuntu Server 1404(LTS) 
- **Red Hat** -RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE** - SUSE Linux Enterprise Server 11 SP4, 12 SP2, 12 SP3 
- **Debian** -Debian 8, Debian 9 
- **CentOS** - CentOS 6.9, CentOS 7.3 
- **Oracle Linux** -Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>Je zálohování náklady součástí náklady na virtuální počítač? 

Ne, zálohování náklady jsou samostatný, nebo se liší od náklady na virtuální počítače. Další informace o cenách zálohování najdete v tématu [cenách zálohování lokality](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Oprávnění, která jsou potřeba k povolení zálohování na virtuálním počítači? 

Pokud jste Přispěvatel virtuálních počítačů, můžete povolit zálohování na virtuálním počítači. Pokud používáte vlastní roli, potřebujete následující oprávnění k úspěšně povolit zálohování na virtuálním počítači. 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Pokud váš trezor služeb zotavení a virtuální počítač různé skupiny prostředků, ujistěte se, že máte oprávnění k zápisu ve skupině prostředků trezoru služeb zotavení.  

## <a name="next-steps"></a>Další postup 

Teď, když chráníte virtuální počítač, najdete v následujících článcích Další informace o úlohách správy virtuálních počítačů a obnovení virtuálních počítačů. 

- [Správa a monitorování virtuálních počítačů](backup-azure-manage-vms.md) 
- [Obnovení virtuálních počítačů](backup-azure-arm-restore-vms.md) 
