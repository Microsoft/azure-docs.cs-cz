---
title: Trezory služeb zotavení přehled | Microsoft Docs
description: Přehled a porovnání trezory služeb zotavení a trezory Azure Backup.
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.assetid: 38d4078b-ebc8-41ff-9bc8-47acf256dc80
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/15/2017
ms.author: markgal;arunak;sogup
ms.openlocfilehash: 2d25782ef73f8899587636b65ccd2d7870f89e6f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="recovery-services-vaults-overview"></a>Přehled trezory služeb zotavení

Tento článek popisuje funkce trezoru služeb zotavení. Trezor služeb zotavení je entita úložiště v Azure, kde data. Data jsou obvykle kopie dat, nebo informace o konfiguraci pro virtuální počítače (VM), úlohy, servery nebo pracovní stanice. Trezory služeb zotavení můžete použít pro uložení zálohy dat pro různé služby Azure, jako jsou virtuální počítače IaaS (Linux nebo Windows) a databází Azure SQL. Obnovení služby trezory podporu System Center DPM, Windows Server, Server pro zálohování Azure a další. Trezory služby Recovery Services usnadňují uspořádání dat záloh a současně minimalizují režii spojenou s jejich správou. 

V rámci předplatného Azure můžete vytvořit až pro 25 trezorů služeb zotavení každou oblast.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Trezory porovnáním různých služeb zotavení a trezory Backup

Pokud máte trezory Backup, že probíhá automaticky upgradují trezory služeb zotavení. 2017 listopadu mají všechny trezory Backup upgradována, aby trezory služeb zotavení. 

Trezory služeb zotavení jsou založené na modelu Azure Resource Manager ve službě Azure, zatímco trezory Backup jsou založené na modelu Azure Service Manager. Když upgradujete trezor Backup do trezoru služeb zotavení, zůstane beze změn zálohovaná data, během a po upgradu. Trezory služeb zotavení zadejte funkce není k dispozici pro trezory Backup, jako třeba:

- **Rozšířené možnosti zabezpečení zálohování dat**: trezory služeb zotavení s, Azure Backup poskytuje funkce zabezpečení k ochraně zálohování do cloudu. Funkce zabezpečení zajišťují můžete zabezpečit záloh a bezpečně obnovit data, i když jsou v ohrožení produkčního prostředí a zálohování serverů. [Další informace](backup-azure-security-feature.md)

- **Centrální monitorování pro vaše prostředí IT hybridní**: trezory služeb zotavení s, můžete monitorovat nejenom vaše [virtuální počítače Azure IaaS](backup-azure-manage-vms.md) , ale také vaše [místní prostředky](backup-azure-manage-windows-server.md#manage-backup-items) z centrální portálu. [Další informace](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Řízení přístupu na základě rolí (RBAC)**: RBAC poskytuje správu řízení podrobných přístupu v Azure. [Azure poskytuje různé integrované role](../role-based-access-control/built-in-roles.md), a zálohování Azure má tři [předdefinovaných rolí ke správě body obnovení](backup-rbac-rs-vault.md). Trezory služeb zotavení jsou kompatibilní s RBAC, který omezuje zálohování a obnovení přístupu k definovanou sadu rolí uživatele. [Další informace](backup-rbac-rs-vault.md)

- **Chránit všechny konfigurace virtuálních počítačů Azure**: trezory služeb zotavení ochrana založené na správci prostředků virtuálních počítačů včetně prémiové disky, spravované disky a virtuální počítače šifrovaná. Upgrade trezor Backup do trezoru služeb zotavení vám dává příležitost k upgradu virtuální počítače založené na portálu Service Manager na virtuální počítače na základě Resource Manager. Při upgradu trezoru, můžete zachovat body obnovení virtuálních počítačů na bázi portálu Service Manager a nakonfigurovat ochranu upgradovaný virtuálních počítačů (Resource Manager povoleno). [Další informace](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Rychlé obnovení pro virtuální počítače IaaS**: trezory služeb zotavení pomocí, můžete obnovit soubory a složky z virtuálního počítače IaaS bez obnovení celý virtuální počítač, který umožňuje rychlejší obnovení. Rychlé obnovení pro virtuální počítače IaaS je k dispozici pro systém Windows a virtuální počítače s Linuxem. [Další informace](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Správa vašeho trezory služeb zotavení na portálu
Protože služba zálohování je integrována do nabídky nastavení Azure, je snadné vytváření a správa trezorů služeb zotavení na portálu Azure. Tato integrace znamená můžete vytvořit ani spravovat trezoru služeb zotavení *v kontextu cílovou službu*. Například zobrazit body obnovení pro virtuální počítač, vyberte ho a klikněte na tlačítko **zálohování** v nabídce nastavení. Zobrazí se zálohování informace specifické pro tento virtuální počítač. V následujícím příkladu **ContosoVM** je název virtuálního počítače. **ContosoVM demovault** je název trezoru služeb zotavení. Nemusíte pamatovat název trezoru služeb zotavení, která ukládá body obnovení, tyto informace můžete získat přístup z virtuálního počítače.  

![Podrobnosti trezoru služeb zotavení virtuálních počítačů](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context.png)

Pokud několik serverů jsou chráněné pomocí stejné trezor služeb zotavení, může být více logických podívat se na trezor služeb zotavení. Můžete vyhledat všechny trezory služeb zotavení v odběru a vyberte jednu ze seznamu.

Následující části obsahují odkazy na články, které vysvětlují, jak používat Trezor služeb zotavení v každý typ aktivity.

### <a name="back-up-data"></a>Zálohování dat
- [Zálohování virtuálního počítače Azure](backup-azure-vms-first-look-arm.md)
- [Zálohování Windows serveru nebo pracovní stanice systému Windows](backup-try-azure-backup-in-10-mins.md)
- [Zálohování úloh DPM do Azure](backup-azure-dpm-introduction.md)
- [Příprava zálohování úloh pomocí serveru Azure Backup](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Správa bodů obnovení
- [Spravovat zálohy virtuálního počítače Azure](backup-azure-manage-vms.md)
- [Správa souborů a složek](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Obnovení dat z trezoru
- [Obnovit jednotlivé soubory z virtuálního počítače Azure](backup-azure-restore-files-from-vm.md)
- [Obnovení virtuálního počítače Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Zabezpečené úložiště
- [Zabezpečení cloudu zálohování dat v trezory služeb zotavení](backup-azure-security-feature.md)



## <a name="next-steps"></a>Další kroky
Použijte v těchto článcích:</br>
[Zálohování virtuálních počítačů IaaS](backup-azure-arm-vms-prepare.md)</br>
[Zálohování serveru Azure Backup](backup-azure-microsoft-azure-backup.md)</br>
[Zálohování serveru Windows](backup-configure-vault.md)
