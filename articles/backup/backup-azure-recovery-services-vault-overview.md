---
title: Přehled trezorů služby Recovery Services
description: Přehled a porovnání mezi trezory Recovery Services a trezory Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/10/2018
ms.author: dacurwin
ms.openlocfilehash: 78f35f30503a6d4f5ead222799f160a88fe2c1d5
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639827"
---
# <a name="recovery-services-vaults-overview"></a>Přehled trezorů Recovery Services

Tento článek popisuje funkce trezoru Recovery Services. Recovery Services trezor je entita úložiště v Azure, která slouží k ukládání dat. Data obvykle kopírují data nebo konfigurační informace pro virtuální počítače, úlohy, servery nebo pracovní stanice. Pomocí služby Recovery Services trezory můžete uchovávat data záloh pro různé služby Azure, jako jsou například virtuální počítače IaaS (Linux nebo Windows) a databáze SQL Azure. Trezory Recovery Services podporují System Center DPM, Windows Server, Azure Backup Server a další. Trezory služby Recovery Services usnadňují uspořádání dat záloh a současně minimalizují režii spojenou s jejich správou.

V rámci předplatného Azure můžete vytvořit až 500 Recovery Services trezorů pro každé předplatné na oblast.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Porovnávání Recovery Services trezorů a trezorů služby Backup

Pokud stále máte trezory služby Backup, budou automaticky upgradovány na Recovery Services trezory. Od listopadu 2017 byly všechny trezory služby Backup upgradovány na Recovery Services trezory.

Trezory Recovery Services jsou založené na modelu Azure Resource Manager Azure, zatímco trezory služby Backup byly založené na modelu Azure Service Manager. Když provedete upgrade trezoru služby Backup na Recovery Services trezor, data zálohování zůstanou během procesu upgradu i po něm beze změny. Trezory Recovery Services poskytují funkce, které nejsou k dispozici pro trezory služby Backup, například:

- **Rozšířené možnosti, které vám pomůžou zabezpečit zálohovaná data**: S Recovery Services trezory poskytuje Azure Backup možnosti zabezpečení pro ochranu cloudových záloh. Funkce zabezpečení zajistí, že budete moci zabezpečit zálohy a bezpečně obnovit data, i když dojde k ohrožení produkčního a záložního serveru. [Víc se uč](backup-azure-security-feature.md)

- **Centrální monitorování pro vaše hybridní IT prostředí**: S Recovery Services trezory můžete monitorovat nejen [virtuální počítače Azure s IaaS](backup-azure-manage-vms.md) , ale i místní [prostředky](backup-azure-manage-windows-server.md#manage-backup-items) z centrálního portálu. [Víc se uč](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Access Control na základě rolí (RBAC)** : RBAC poskytuje podrobné řízení přístupu v Azure. [Azure poskytuje různé předdefinované role](../role-based-access-control/built-in-roles.md)a Azure Backup má tři [předdefinované role pro správu bodů obnovení](backup-rbac-rs-vault.md). Trezory Recovery Services jsou kompatibilní s RBAC, což omezuje přístup k nástroji pro zálohování a obnovení na definovanou sadu rolí uživatele. [Víc se uč](backup-rbac-rs-vault.md)

- **Chraňte všechny konfigurace Azure Virtual Machines**: Recovery Services trezory chrání virtuální počítače založené na Správce prostředků, včetně prémiových disků, Managed Disks a šifrovaných virtuálních počítačů. Upgrade trezoru služby Backup na Recovery Services trezor vám dává možnost upgradovat vaše virtuální počítače založené na Service Manager na Správce prostředků virtuální počítače na bázi. Během upgradu trezoru můžete zachovat body obnovení virtuálních počítačů s Service Manager a nakonfigurovat ochranu pro upgradované virtuální počítače s podporou Správce prostředků. [Víc se uč](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Okamžité obnovení pro virtuální počítače s IaaS**: Pomocí Recovery Services trezorů můžete obnovit soubory a složky z virtuálního počítače s IaaS bez obnovení celého virtuálního počítače, který umožňuje rychlejší obnovení. Pro virtuální počítače s Windows i Linuxem je k dispozici okamžité obnovení pro virtuální počítače s IaaS. [Víc se uč](https://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Správa trezorů Recovery Services na portálu
Vytváření a Správa trezorů Recovery Services v Azure Portal je snadné, protože služba zálohování je integrovaná do jiných služeb Azure. Tato integrace znamená, že můžete vytvořit nebo spravovat trezor Recovery Services *v kontextu cílové služby*. Pokud například chcete zobrazit body obnovení pro virtuální počítač, vyberte svůj virtuální počítač a v nabídce operace klikněte na **zálohovat** .

![Virtuální počítač s podrobnostmi o trezoru Recovery Services](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Pokud virtuální počítač nemá nakonfigurovanou zálohu, zobrazí se výzva ke konfiguraci zálohování. Pokud jste nakonfigurovali zálohování, zobrazí se informace o zálohování virtuálního počítače, včetně seznamu bodů obnovení.  

![Virtuální počítač s podrobnostmi o trezoru Recovery Services](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

V předchozím příkladu je **ContosoVM** název virtuálního počítače. **ContosoVM-demovault** je název trezoru Recovery Services. Nemusíte si pamatovat název trezoru Recovery Services, ve kterém jsou uložené body obnovení, k těmto informacím můžete získat přístup z virtuálního počítače.  

Pokud jeden Recovery Services trezor chrání více serverů, může být více logickým pro zobrazení trezoru Recovery Services. V rámci předplatného můžete hledat všechny Recovery Services trezory a vybrat je ze seznamu.

Následující části obsahují odkazy na články, které vysvětlují použití trezoru Recovery Services u každého typu aktivity.

> [!NOTE]
> Recovery Services Trezor nelze vytvořit se stejným názvem, pokud byl odstraněn během 24 hodin. Použijte jiný název prostředku nebo vyberte jinou skupinu prostředků, nebo to zkuste znovu za 24 hodin.

### <a name="back-up-data"></a>Zálohování dat
- [Zálohování virtuálního počítače Azure](backup-azure-vms-first-look-arm.md)
- [Zálohování Windows serveru nebo pracovní stanice Windows](backup-try-azure-backup-in-10-mins.md)
- [Zálohování úloh DPM do Azure](backup-azure-dpm-introduction.md)
- [Příprava na zálohování úloh pomocí Azure Backup Server](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Správa bodů obnovení
- [Správa záloh virtuálních počítačů Azure](backup-azure-manage-vms.md)
- [Správa souborů a složek](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Obnovení dat z trezoru
- [Obnovení jednotlivých souborů z virtuálního počítače Azure](backup-azure-restore-files-from-vm.md)
- [Obnovení virtuálního počítače Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Zabezpečení trezoru
- [Zabezpečení cloudových zálohovaných dat v Recovery Services trezorech](backup-azure-security-feature.md)



## <a name="next-steps"></a>Další kroky
Následující články použijte k následujícím akcím:</br>
[Zálohování virtuálního počítače s IaaS](backup-azure-arm-vms-prepare.md)</br>
[Zálohování Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Zálohování Windows serveru](backup-configure-vault.md)
