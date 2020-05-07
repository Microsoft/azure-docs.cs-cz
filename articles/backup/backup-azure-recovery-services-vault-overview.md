---
title: Přehled trezorů služby Recovery Services
description: Přehled a porovnání mezi trezory Recovery Services a trezory Azure Backup.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: a0dacd82b7cf4258c0147bbaf9dc39ee6fc0fa25
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597942"
---
# <a name="recovery-services-vaults-overview"></a>Přehled trezorů služby Recovery Services

Tento článek popisuje funkce trezoru Recovery Services. Recovery Services trezor je entita úložiště v Azure, která slouží k ukládání dat. Data obvykle kopírují data nebo konfigurační informace pro virtuální počítače, úlohy, servery nebo pracovní stanice. Pomocí služby Recovery Services trezory můžete uchovávat data záloh pro různé služby Azure, jako jsou například virtuální počítače IaaS (Linux nebo Windows) a databáze SQL Azure. Trezory Recovery Services podporují System Center DPM, Windows Server, Azure Backup Server a další. Trezory služby Recovery Services usnadňují uspořádání dat záloh a současně minimalizují režii spojenou s jejich správou.

V rámci předplatného Azure můžete vytvořit až 500 Recovery Services trezorů pro každé předplatné na oblast.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Porovnávání Recovery Services trezorů a trezorů služby Backup

Pokud stále máte trezory služby Backup, jsou automaticky upgradovány na Recovery Services trezory. Od listopadu 2017 byly všechny trezory služby Backup upgradovány na Recovery Services trezory.

Trezory Recovery Services jsou založené na modelu Azure Resource Manager Azure, zatímco trezory služby Backup byly založené na modelu Azure Service Manager. Když provedete upgrade trezoru služby Backup na Recovery Services trezor, data zálohování zůstanou během procesu upgradu i po něm beze změny. Trezory Recovery Services poskytují funkce, které nejsou k dispozici pro trezory služby Backup, například:

- **Rozšířené možnosti, které vám pomůžou zabezpečit**zálohovaná data: pomocí Recovery Services trezorů Azure Backup poskytuje možnosti zabezpečení pro ochranu cloudových záloh. Funkce zabezpečení zajistí, že budete moci zabezpečit zálohy a bezpečně obnovit data, i když dojde k ohrožení produkčního a záložního serveru. [Další informace](backup-azure-security-feature.md)

- **Centrální monitorování pro vaše hybridní IT prostředí**: Díky trezorům Recovery Services můžete monitorovat nejen [virtuální počítače Azure s IaaS](backup-azure-manage-vms.md) , ale i vaše místní [prostředky](backup-azure-manage-windows-server.md#manage-backup-items) z centrálního portálu. [Další informace](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Access Control na základě rolí (RBAC)**: RBAC zajišťuje v Azure jemně odstupňované řízení přístupu. [Azure poskytuje různé předdefinované role](../role-based-access-control/built-in-roles.md)a Azure Backup má tři [předdefinované role pro správu bodů obnovení](backup-rbac-rs-vault.md). Trezory Recovery Services jsou kompatibilní s RBAC, což omezuje přístup k nástroji pro zálohování a obnovení na definovanou sadu rolí uživatele. [Další informace](backup-rbac-rs-vault.md)

- **Chraňte všechny konfigurace Azure Virtual Machines**: Recovery Services trezory chrání správce prostředků virtuálních počítačů, včetně prémiových disků, Managed disks a šifrovaných virtuálních počítačů. Upgrade trezoru služby Backup na Recovery Services trezor vám dává možnost upgradovat vaše virtuální počítače založené na Service Manager na Správce prostředků virtuální počítače na bázi. Během upgradu trezoru můžete zachovat body obnovení virtuálních počítačů s Service Manager a nakonfigurovat ochranu pro upgradované virtuální počítače s podporou Správce prostředků. [Další informace](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Okamžité obnovení pro virtuální počítače s IaaS**: pomocí trezorů Recovery Services můžete obnovit soubory a složky z virtuálního počítače s IaaS bez obnovení celého virtuálního počítače, který umožňuje rychlejší obnovení. Pro virtuální počítače s Windows i Linuxem je k dispozici okamžité obnovení pro virtuální počítače s IaaS. [Další informace](backup-instant-restore-capability.md)

## <a name="storage-settings-in-the-recovery-services-vault"></a>Nastavení úložiště v trezoru Recovery Services

Recovery Services trezor je entita, která ukládá zálohy a body obnovení vytvořené v průběhu času. Trezor Recovery Services obsahuje také zásady zálohování, které jsou přidruženy k chráněným virtuálním počítačům.

Azure Backup automaticky zpracovává úložiště pro trezor. Podívejte se, jak [můžete změnit nastavení úložiště](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy).

Další informace o redundanci úložiště najdete v těchto článcích o [geografické](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs) a [místní](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) redundanci.

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Správa trezorů Recovery Services na portálu

Vytváření a Správa trezorů Recovery Services v Azure Portal je snadné, protože služba zálohování je integrovaná do jiných služeb Azure. Tato integrace znamená, že můžete vytvořit nebo spravovat trezor Recovery Services *v kontextu cílové služby*. Pokud například chcete zobrazit body obnovení pro virtuální počítač, vyberte svůj virtuální počítač a v nabídce operace klikněte na **zálohovat** .

![Virtuální počítač s podrobnostmi o Recovery Services trezoru](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

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

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](https://docs.microsoft.com/azure/advisor/) je přizpůsobený cloudový poradce, který pomáhá optimalizovat používání Azure. Analyzuje využití Azure a poskytuje včasné doporučení, které vám pomůžou optimalizovat a zabezpečit vaše nasazení. Poskytuje doporučení ve čtyřech kategoriích: vysoká dostupnost, zabezpečení, výkon a náklady.

Azure Advisor poskytuje hodinová [doporučení](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations#protect-your-virtual-machine-data-from-accidental-deletion) pro virtuální počítače, které nejsou zálohované, takže nikdy nebudete zálohovat důležité virtuální počítače. Doporučení můžete také ovládat tím, že je odkládáte.  Můžete kliknout na doporučení a povolit zálohování na virtuálních počítačích v řádku zadáním trezoru (kde se budou ukládat zálohy) a zásad zálohování (plán zálohování a uchovávání záložních kopií).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>Další kroky

Následující články použijte k následujícím akcím:</br>
[Zálohování virtuálního počítače s IaaS](backup-azure-arm-vms-prepare.md)</br>
[Zálohování Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Zálohování Windows serveru](backup-windows-with-mars-agent.md)
