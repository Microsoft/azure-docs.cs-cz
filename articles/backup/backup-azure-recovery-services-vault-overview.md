---
title: Přehled trezorů služby Recovery Services
description: Přehled a porovnání mezi trezory Recovery Services a trezory Azure Backup.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 2b292a39e38ef5e298f45c2babbee9fbd20c39ea
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2020
ms.locfileid: "88261867"
---
# <a name="recovery-services-vaults-overview"></a>Přehled trezorů služby Recovery Services

Tento článek popisuje funkce trezoru Recovery Services. Recovery Services trezor je entita úložiště v Azure, která slouží k ukládání dat. Data obvykle kopírují data nebo konfigurační informace pro virtuální počítače, úlohy, servery nebo pracovní stanice. Pomocí služby Recovery Services trezory můžete uchovávat data záloh pro různé služby Azure, jako jsou například virtuální počítače IaaS (Linux nebo Windows) a databáze SQL Azure. Trezory Recovery Services podporují System Center DPM, Windows Server, Azure Backup Server a další. Trezory služby Recovery Services usnadňují uspořádání dat záloh a současně minimalizují režii spojenou s jejich správou. Trezory Recovery Services jsou založené na modelu Azure Resource Manager Azure, který poskytuje funkce, jako například:

- **Rozšířené možnosti, které vám pomůžou zabezpečit**zálohovaná data: pomocí Recovery Services trezorů Azure Backup poskytuje možnosti zabezpečení pro ochranu cloudových záloh. Funkce zabezpečení zajistí, že budete moci zabezpečit zálohy a bezpečně obnovit data, i když dojde k ohrožení produkčního a záložního serveru. [Další informace](backup-azure-security-feature.md)

- **Centrální monitorování pro vaše hybridní IT prostředí**: Díky trezorům Recovery Services můžete monitorovat nejen [virtuální počítače Azure s IaaS](backup-azure-manage-vms.md) , ale i vaše místní [prostředky](backup-azure-manage-windows-server.md#manage-backup-items) z centrálního portálu. [Další informace](backup-azure-monitoring-built-in-monitor.md)

- **Access Control na základě rolí (RBAC)**: RBAC zajišťuje v Azure jemně odstupňované řízení přístupu. [Azure poskytuje různé předdefinované role](../role-based-access-control/built-in-roles.md)a Azure Backup má tři [předdefinované role pro správu bodů obnovení](backup-rbac-rs-vault.md). Trezory Recovery Services jsou kompatibilní s RBAC, což omezuje přístup k nástroji pro zálohování a obnovení na definovanou sadu rolí uživatele. [Další informace](backup-rbac-rs-vault.md)

- **Obnovitelné odstranění**: u obnovitelného odstranění, i když škodlivý objekt actor odstraní zálohu (nebo se zálohovaná data nechtěně odstraní), zálohovaná data se uchovávají po dobu 14 dalších dnů, což umožňuje obnovení této zálohované položky bez ztráty dat. Další 14 dní uchovávání zálohových dat ve stavu "obnovitelné odstranění" neúčtují žádné náklady na zákazníka. [Další informace](backup-azure-security-feature-cloud.md).

- **Obnovení mezi oblastmi**: obnovení mezi oblastmi (crr) vám umožňuje obnovení virtuálních počítačů Azure v sekundární oblasti, která je spárována s oblastí Azure. Pokud Azure deklaruje havárie v primární oblasti, data replikovaná v sekundární oblasti jsou dostupná k obnovení v sekundární oblasti, aby se zmírnila Doba výpadku reálného výpadku v primární oblasti pro jejich prostředí. [Další informace](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Nastavení úložiště v trezoru Recovery Services

Recovery Services trezor je entita, která ukládá zálohy a body obnovení vytvořené v průběhu času. Trezor Recovery Services obsahuje také zásady zálohování, které jsou přidruženy k chráněným virtuálním počítačům.

- Azure Backup automaticky zpracovává úložiště pro trezor. Podívejte se, jak [můžete změnit nastavení úložiště](./backup-create-rs-vault.md#set-storage-redundancy).

- Další informace o redundanci úložiště najdete v těchto článcích o [geografické](../storage/common/storage-redundancy.md) a [místní](../storage/common/storage-redundancy.md) redundanci.

### <a name="additional-resources"></a>Další zdroje

- [Úložiště – podporované a nepodporované scénáře](backup-support-matrix.md#vault-support)
- [Nejčastější dotazy k trezoru](backup-azure-backup-faq.md)

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) je přizpůsobený cloudový poradce, který pomáhá optimalizovat používání Azure. Analyzuje využití Azure a poskytuje včasné doporučení, které vám pomůžou optimalizovat a zabezpečit vaše nasazení. Poskytuje doporučení ve čtyřech kategoriích: vysoká dostupnost, zabezpečení, výkon a náklady.

Azure Advisor poskytuje hodinová [doporučení](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) pro virtuální počítače, které nejsou zálohované, takže nikdy nebudete zálohovat důležité virtuální počítače. Doporučení můžete také ovládat tím, že je odkládáte.  Můžete vybrat doporučení a povolit zálohování na virtuálních počítačích v řádku zadáním trezoru (kde se budou ukládat zálohy) a zásad zálohování (plán zálohování a uchovávání záložních kopií).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>Další kroky

Následující články použijte k následujícím akcím:</br>
[Zálohování virtuálního počítače s IaaS](backup-azure-arm-vms-prepare.md)</br>
[Zálohování Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Zálohování Windows serveru](backup-windows-with-mars-agent.md)
