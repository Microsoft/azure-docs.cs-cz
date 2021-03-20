---
title: Přehled trezorů služby Recovery Services
description: Přehled trezorů Recovery Services.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 0ed37446e1ccf0780f924143c8f063964adf0004
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755112"
---
# <a name="recovery-services-vaults-overview"></a>Přehled trezorů služby Recovery Services

Tento článek popisuje funkce trezoru Recovery Services. Recovery Services trezor je entita úložiště v Azure, která slouží k ukládání dat. Data obvykle kopírují data nebo konfigurační informace pro virtuální počítače, úlohy, servery nebo pracovní stanice. Pomocí služby Recovery Services trezory můžete uchovávat data záloh pro různé služby Azure, jako jsou například virtuální počítače IaaS (Linux nebo Windows) a databáze SQL Azure. Trezory Recovery Services podporují System Center DPM, Windows Server, Azure Backup Server a další. Trezory služby Recovery Services usnadňují uspořádání dat záloh a současně minimalizují režii spojenou s jejich správou. Trezory Recovery Services jsou založené na modelu Azure Resource Manager Azure, který poskytuje funkce, jako například:

- **Rozšířené možnosti, které vám pomůžou zabezpečit** zálohovaná data: pomocí Recovery Services trezorů Azure Backup poskytuje možnosti zabezpečení pro ochranu cloudových záloh. Funkce zabezpečení zajistí, že budete moci zabezpečit zálohy a bezpečně obnovit data, i když dojde k ohrožení produkčního a záložního serveru. [Další informace](backup-azure-security-feature.md)

- **Centrální monitorování pro vaše hybridní IT prostředí**: Díky trezorům Recovery Services můžete monitorovat nejen [virtuální počítače Azure s IaaS](backup-azure-manage-vms.md) , ale i vaše místní [prostředky](backup-azure-manage-windows-server.md#manage-backup-items) z centrálního portálu. [Další informace](backup-azure-monitoring-built-in-monitor.md)

- **Řízení přístupu na základě role v Azure (Azure RBAC)**: Azure RBAC zajišťuje v Azure jemně odstupňované řízení přístupu. [Azure poskytuje různé předdefinované role](../role-based-access-control/built-in-roles.md)a Azure Backup má tři [předdefinované role pro správu bodů obnovení](backup-rbac-rs-vault.md). Trezory Recovery Services jsou kompatibilní s Azure RBAC, což omezuje přístup k zálohování a obnovení na definovanou sadu rolí uživatele. [Další informace](backup-rbac-rs-vault.md)

- **Obnovitelné odstranění**: u obnovitelného odstranění, i když škodlivý objekt actor odstraní zálohu (nebo se zálohovaná data nechtěně odstraní), zálohovaná data se uchovávají po dobu 14 dalších dnů, což umožňuje obnovení této zálohované položky bez ztráty dat. Další 14 dní uchovávání zálohových dat ve stavu "obnovitelné odstranění" za vás neúčtují žádné náklady. [Další informace](backup-azure-security-feature-cloud.md).

- **Obnovení mezi oblastmi**: obnovení mezi oblastmi (crr) vám umožňuje obnovení virtuálních počítačů Azure v sekundární oblasti, která je spárována s oblastí Azure. Povolením této funkce na [úrovni trezoru](backup-create-rs-vault.md#set-cross-region-restore)můžete kdykoli obnovit replikovaná data v sekundární oblasti, když zvolíte. To vám umožní obnovit data sekundární oblasti pro účely dodržování předpisů pro audit a během výpadků, aniž byste čekali na to, že Azure deklaruje havárií (na rozdíl od nastavení GRS trezoru). [Další informace](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Nastavení úložiště v trezoru Recovery Services

Recovery Services trezor je entita, která ukládá zálohy a body obnovení vytvořené v průběhu času. Trezor Recovery Services obsahuje také zásady zálohování, které jsou přidruženy k chráněným virtuálním počítačům.

- Azure Backup automaticky zpracovává úložiště pro trezor. Podívejte se, jak [můžete změnit nastavení úložiště](./backup-create-rs-vault.md#set-storage-redundancy).

- Další informace o redundanci úložiště najdete v těchto článcích o [geograficky](../storage/common/storage-redundancy.md#geo-zone-redundant-storage), [místní](../storage/common/storage-redundancy.md#locally-redundant-storage) a [Zona](../storage/common/storage-redundancy.md#zone-redundant-storage) redundanci.

## <a name="encryption-settings-in-the-recovery-services-vault"></a>Nastavení šifrování v trezoru Recovery Services

Tato část popisuje možnosti, které jsou k dispozici pro šifrování zálohovaných dat uložených v úložišti Recovery Services.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Šifrování zálohovaných dat pomocí klíčů spravovaných platformou

Ve výchozím nastavení se všechna vaše data šifrují pomocí klíčů spravovaných platformou. Pro povolení tohoto šifrování není nutné provádět žádnou explicitní akci z vašeho konce. Platí pro všechny úlohy, které se zálohují do vašeho trezoru Recovery Services.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Šifrování zálohovaných dat s využitím klíčů spravovaných zákazníkem

Data můžete šifrovat pomocí šifrovacích klíčů vlastněných a spravovaných vámi. Azure Backup umožňuje používat klíče RSA uložené v Azure Key Vault k šifrování záloh. Šifrovací klíč, který se používá k šifrování záloh, může být jiný než ten, který se používá pro zdroj. Data jsou chráněná pomocí šifrovacího klíče založeného na standardu AES 256 (klíč DEK), který je zase chráněn pomocí vašich klíčů. Díky tomu máte plnou kontrolu nad daty a klíči. Aby bylo šifrování povoleno, musí mít Recovery Services trezoru udělen přístup k šifrovacímu klíči v Azure Key Vault. Kdykoli je to potřeba, můžete klíč zakázat nebo odvolat přístup. Před tím, než se pokusíte ochránit jakékoli položky do trezoru, je však nutné povolit šifrování pomocí vašich klíčů.

Přečtěte si další informace o šifrování zálohovaných dat [pomocí klíčů spravovaných zákazníkem](encryption-at-rest-with-cmk.md).

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) je přizpůsobený cloudový poradce, který pomáhá optimalizovat používání Azure. Analyzuje využití Azure a poskytuje včasné doporučení, které vám pomůžou optimalizovat a zabezpečit vaše nasazení. Poskytuje doporučení ve čtyřech kategoriích: vysoká dostupnost, zabezpečení, výkon a náklady.

Azure Advisor poskytuje hodinová [doporučení](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) pro virtuální počítače, které nejsou zálohované, takže nikdy nebudete zálohovat důležité virtuální počítače. Doporučení můžete také ovládat tím, že je odkládáte.  Můžete vybrat doporučení a povolit zálohování na virtuálních počítačích v řádku zadáním trezoru (kde se budou ukládat zálohy) a zásad zálohování (plán zálohování a uchovávání záložních kopií).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>Další zdroje informací

- [Úložiště – podporované a nepodporované scénáře](backup-support-matrix.md#vault-support)
- [Nejčastější dotazy k trezoru](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Další kroky

Následující články použijte k následujícím akcím:

- [Zálohování virtuálního počítače s IaaS](backup-azure-arm-vms-prepare.md)
- [Zálohování Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [Zálohování Windows serveru](backup-windows-with-mars-agent.md)
