---
title: Přehled trezorů služby Recovery Services
description: Přehled a porovnání trezory služby Recovery Services a trezorů Azure Backup.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/10/2018
ms.author: markgal
ms.openlocfilehash: 38190cc613ea9d3723cc3f0f563842e8416689aa
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241340"
---
# <a name="recovery-services-vaults-overview"></a>Přehled trezory služby Recovery Services

Tento článek popisuje funkce trezoru služby Recovery Services. Trezor služby Recovery Services je entita úložiště v Azure, které jsou uloženy data. Data jsou obvykle kopií dat, nebo informace o konfiguraci pro virtuální počítače (VM), úlohy, serverech nebo pracovních stanic. Trezory služby Recovery Services můžete použít k uložení zálohovaných dat pro různé služby Azure, jako jsou virtuální počítače IaaS (Linux nebo Windows) a databáze Azure SQL. Obnovení služby trezory podporu systému System Center DPM, Windows Server, Azure Backup serveru a další. Trezory služby Recovery Services usnadňují uspořádání dat záloh a současně minimalizují režii spojenou s jejich správou.

V rámci předplatného Azure můžete vytvořit až 500 trezorů služby Recovery Services na předplatné a oblast.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Trezory Recovery Services porovnání služeb zotavení a trezory Backup

Pokud máte stále trezory služby Backup, že se automaticky upgradovány na trezory služby Recovery Services. . Listopadu 2017 se upgradovaly na trezory služby Recovery Services všechny trezory služby Backup.

Trezory služby Recovery Services jsou založené na modelu Azure Resource Manageru v Azure, zatímco trezory služby Backup jsou založené na modelu Azure Service Manager. Při upgradu trezoru služby Backup na trezor služby Recovery Services záložní data zůstanou beze změny během a po procesu upgradu. Trezory služby Recovery Services poskytují funkce není k dispozici pro trezory služby Backup, jako například:

- **Rozšířené možnosti zabezpečení zálohování dat**: trezory Recovery Services pomocí služby Recovery Services, Azure Backup poskytuje funkce zabezpečení k ochraně cloudové zálohy. Funkce zabezpečení zajistit zabezpečení vašich záloh a bezpečně obnovit data, i v případě, že dojde k ohrožení produkční a zálohování serverů. [Další informace](backup-azure-security-feature.md)

- **Centrální monitorování pro vaši hybridní IT prostředí**: trezory Recovery Services pomocí služby Recovery Services, můžete monitorovat nejenom vaše [virtuálních počítačů Azure IaaS](backup-azure-manage-vms.md) , ale také vaše [místní prostředky](backup-azure-manage-windows-server.md#manage-backup-items) z centrální portál. [Další informace](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Řízení přístupu na základě rolí (RBAC)**: RBAC poskytuje jemně odstupňované řízení řízení přístupu v Azure. [Azure nabízí různé předdefinované role](../role-based-access-control/built-in-roles.md), a Azure Backup obsahuje tři [předdefinovaných rolí ke správě body obnovení](backup-rbac-rs-vault.md). Trezory služby Recovery Services, musí být kompatibilní s RBAC, který omezuje zálohování a obnovení přístupu k definovanou sadu rolí uživatelů. [Další informace](backup-rbac-rs-vault.md)

- **Chránit všechny konfigurace virtuálních počítačů Azure**: trezory služby Recovery Services ochrany založené na správci prostředků virtuálních počítačů, včetně disků Premium Managed Disks a šifrovaných virtuálních počítačů. Upgrade úložiště záloh do trezoru služby Recovery Services vám dává příležitost k upgradu vašich virtuálních počítačů založené na portálu Service Manager na virtuální počítače založené na Resource Manageru. Při upgradu trezoru, můžete zachovat body obnovení virtuálního počítače založené na portálu Service Manager a nakonfigurujte ochranu pro upgradovaný virtuální počítače (Resource Manager s podporou). [Další informace](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Okamžitá obnova pro virtuální počítače IaaS**: trezory Recovery Services pomocí služby Recovery Services, můžete obnovit soubory a složky z virtuálního počítače IaaS bez obnovení celého virtuálního počítače, který umožňuje rychlejší obnovení. Okamžitá obnova pro virtuální počítače IaaS je k dispozici pro virtuální počítače s Linuxem i Windows. [Další informace](https://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Správa vašich trezorech služby Recovery Services na portálu
Vytváření a správa trezorů služby Recovery Services na webu Azure Portal je snadné, protože služba Backup integruje se s dalšími službami Azure. Tato integrace znamená, že můžete vytvářet a Spravovat trezor služby Recovery Services *v kontextu cílovou službu*. Chcete-li zobrazit body obnovení pro virtuální počítač, například vyberte svůj virtuální počítač a klikněte na tlačítko **zálohování** v nabídce operace.

![Podrobnosti trezoru služeb zotavení virtuálního počítače](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Pokud virtuální počítač nemá zálohování je nakonfigurované a potom vás vyzve ke konfiguraci zálohování. Pokud zálohování je nakonfigurované, zobrazí se vám záložních informací o virtuálním počítači, včetně seznamu bodů obnovení.  

![Podrobnosti trezoru služeb zotavení virtuálního počítače](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

V předchozím příkladu **ContosoVM** je název virtuálního počítače. **Názvy ContosoVM-demovault** je název trezoru služby Recovery Services. Nemusíte pamatovat název trezoru služby Recovery Services, který uchovává body obnovení, můžete přistupovat k těmto informacím z virtuálního počítače.  

Pokud jeden trezor služby Recovery Services chrání více serverů, může být více logických podívat se na trezor služby Recovery Services. Můžete vyhledat všechny trezory služby Recovery Services v rámci předplatného a vyberte ho ze seznamu.

Následující části obsahují odkazy na články, které popisují, jak použít trezor služby Recovery Services v příslušném typu aktivity.

> [!NOTE]
> Se stejným názvem nelze vytvořit trezor služby Recovery Services, pokud byl odstraněn během 24 hodin. Použít jiný název prostředku nebo zvolte jinou skupinu prostředků nebo zkuste znovu za 24 hodin.

### <a name="back-up-data"></a>Zálohování dat
- [Zálohování virtuálního počítače Azure](backup-azure-vms-first-look-arm.md)
- [Zálohování Windows serveru nebo pracovní stanice Windows](backup-try-azure-backup-in-10-mins.md)
- [Zálohování úloh DPM do Azure](backup-azure-dpm-introduction.md)
- [Příprava zálohování úloh pomocí Azure Backup serveru](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Správa bodů obnovení
- [Správa záloh virtuálních počítačů Azure](backup-azure-manage-vms.md)
- [Správa souborů a složek](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Obnovení dat z trezoru
- [Obnovení jednotlivých souborů z virtuálního počítače Azure](backup-azure-restore-files-from-vm.md)
- [Obnovení virtuálního počítače Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Zabezpečení trezoru
- [Zabezpečení cloudu data záloh v trezorech služby Recovery Services](backup-azure-security-feature.md)



## <a name="next-steps"></a>Další kroky
Pomocí těchto článcích:</br>
[Zálohování virtuálního počítače s IaaS](backup-azure-arm-vms-prepare.md)</br>
[Zálohování Azure Backup serveru](backup-azure-microsoft-azure-backup.md)</br>
[Zálohování Windows serveru](backup-configure-vault.md)
