---
title: Přehled trezorů služby Recovery Services
description: Přehled a porovnání mezi trezory služby Recovery Services a trezory Zálohování Azure.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: e6a359287533c9ffdd688b5285b24b9c70fa7b7f
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436959"
---
# <a name="recovery-services-vaults-overview"></a>Přehled trezorů služby Recovery Services

Tento článek popisuje funkce trezoru služby Recovery Services. Trezor služby Recovery Services je entita úložiště v Azure, ve které jsou data umístěna. Data jsou obvykle kopie dat nebo informace o konfiguraci pro virtuální počítače (VMs), úlohy, servery nebo pracovní stanice. Trezory služby Recovery Services můžete použít k uložení záložních dat pro různé služby Azure, jako jsou virtuální počítače IaaS (Linux nebo Windows) a databáze Azure SQL. Trezory služby Recovery Services podporují system center dpm, windows server, server Zálohování Azure a další. Trezory služby Recovery Services usnadňují uspořádání dat záloh a současně minimalizují režii spojenou s jejich správou.

V rámci předplatného Azure můžete vytvořit až 500 trezorů služby Recovery Services na jedno předplatné na oblast.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Porovnání trezorů služby Recovery Services a trezorů zálohování

Pokud máte stále trezory zálohování, jsou automaticky upgradovány na trezory služby Recovery Services. Do listopadu 2017 byly všechny trezory zálohování upgradovány na trezory služby Recovery Services.

Trezory služby Recovery Services jsou založené na modelu Azure Resource Manageru, zatímco trezory zálohování byly založené na modelu Azure Service Manager. Při upgradu trezoru zálohování do trezoru služby Recovery Services zůstanou záložní data během procesu upgradu a po něm neporušená. Trezory služby Recovery Services poskytují funkce, které nejsou k dispozici pro trezory zálohování, například:

- **Rozšířené funkce pro zabezpečení zálohovaných dat**: Díky trezorům služby Recovery Services poskytuje služba Azure Backup funkce zabezpečení pro ochranu záloh v cloudu. Funkce zabezpečení zajišťují zabezpečení záloh a bezpečné obnovení dat, a to i v případě, že jsou ohroženy produkční a zálohovací servery. [Další informace](backup-azure-security-feature.md)

- **Centrální monitorování pro hybridní IT prostředí**: S trezory služby Recovery Services můžete monitorovat nejen virtuální počítače Azure [IaaS,](backup-azure-manage-vms.md) ale také [místní prostředky](backup-azure-manage-windows-server.md#manage-backup-items) z centrálního portálu. [Další informace](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Řízení přístupu na základě rolí (RBAC):** RBAC poskytuje jemně odstupňované řízení správy přístupu v Azure. [Azure poskytuje různé předdefinované role](../role-based-access-control/built-in-roles.md)a Azure Backup má tři [předdefinované role pro správu bodů obnovení](backup-rbac-rs-vault.md). Trezory služby Recovery Services jsou kompatibilní s nástrojem RBAC, který omezuje zálohování a obnovení přístupu k definované sadě uživatelských rolí. [Další informace](backup-rbac-rs-vault.md)

- **Chraňte všechny konfigurace virtuálních počítačů Azure**: trezory služby Recovery Services chrání virtuální počítače založené na Správci prostředků, včetně disků Premium, spravovaných disků a šifrovaných virtuálních počítačů. Upgrade trezoru zálohování do trezoru služby Recovery Services vám dává možnost upgradovat virtuální počítače založené na Správci služeb na virtuální počítače založené na Správci prostředků. Při upgradu trezoru můžete zachovat body obnovení virtuálních zařízení založené na Správci služeb a nakonfigurovat ochranu upgradovaných virtuálních počítačů (s povolenou správcem prostředků). [Další informace](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Okamžité obnovení pro virtuální aplikace IaaS:** Pomocí trezorů služby Recovery Services můžete obnovit soubory a složky z virtuálního zařízení IaaS bez obnovení celého virtuálního zařízení, což umožňuje rychlejší obnovení. Okamžité obnovení pro virtuální počítače IaaS je k dispozici pro virtuální počítače s Windows i Linux. [Další informace](backup-instant-restore-capability.md)

## <a name="storage-settings-in-the-recovery-services-vault"></a>Nastavení úložiště v trezoru služby Recovery Services

Trezor služby Recovery Services je entita, která ukládá zálohy a body obnovení vytvořené v průběhu času. Trezor služby Recovery Services také obsahuje zásady zálohování, které jsou přidruženy k chráněným virtuálním počítačům.

Azure Backup automaticky zpracovává úložiště pro trezor. Podívejte se, jak [lze změnit nastavení úložiště](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy).

Další informace o redundanci úložiště najdete v těchto článcích o [geografické](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs) a [místní](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) redundanci.

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Správa trezorů služby Recovery Services na portálu

Vytvoření a správa trezorů služby Recovery Services na webu Azure Portal je snadné, protože služba Backup se integruje do jiných služeb Azure. Tato integrace znamená, že můžete vytvořit nebo spravovat trezor služby Recovery Services *v kontextu cílové služby*. Chcete-li například zobrazit body obnovení pro virtuální počítač, vyberte virtuální počítač a v nabídce Operace klikněte na **Zálohování.**

![Podrobnosti trezoru služby Recovery Services VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Pokud virtuální počítače nemá nakonfigurované zálohy, zobrazí se výzva ke konfiguraci zálohování. Pokud byla nakonfigurovaná záloha, zobrazí se informace o zálohování virtuálního počítače, včetně seznamu bodů obnovení.  

![Podrobnosti trezoru služby obnovení VM](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

V předchozím příkladu **ContosoVM** je název virtuálního počítače. **ContosoVM-demovault** je název trezoru služby Recovery Services. Nemusíte si pamatovat název trezoru služby Recovery Services, který ukládá body obnovení, můžete k těmto informacím přistupovat z virtuálního počítače.  

Pokud jeden trezor služby Recovery Services chrání více serverů, může být logičtější podívat se na trezor služby Recovery Services. Můžete vyhledat všechny trezory služby Recovery Services v předplatném a vybrat jeden ze seznamu.

Následující části obsahují odkazy na články, které vysvětlují, jak používat trezor služby Recovery Services v každém typu aktivity.

> [!NOTE]
> Trezor služby Recovery Services nelze vytvořit se stejným názvem, pokud byl odstraněn do 24 hodin. Použijte jiný název prostředku nebo zvolte jinou skupinu prostředků nebo opakujte akci po 24 hodinách.

### <a name="back-up-data"></a>Zálohování dat

- [Zálohování virtuálního počítače Azure](backup-azure-vms-first-look-arm.md)
- [Zálohování pracovní stanice systému Windows Server nebo Windows](backup-try-azure-backup-in-10-mins.md)
- [Zálohování úloh aplikace DPM do Azure](backup-azure-dpm-introduction.md)
- [Příprava na zálohování úloh pomocí Azure Backup Serveru](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Správa bodů obnovení

- [Správa záloh virtuálních počítačů Azure](backup-azure-manage-vms.md)
- [Správa souborů a složek](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Obnovení dat z úložiště

- [Obnovení jednotlivých souborů z virtuálního počítače Azure](backup-azure-restore-files-from-vm.md)
- [Obnovení virtuálního počítače Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Zabezpečit trezor

- [Zabezpečení dat zálohování v cloudu v trezorech služby Recovery Services](backup-azure-security-feature.md)

## <a name="next-steps"></a>Další kroky

Následující články slouží k následujícím:</br>
[Zálohování virtuálního mísu IaaS](backup-azure-arm-vms-prepare.md)</br>
[Zálohování zálohovacího serveru Azure](backup-azure-microsoft-azure-backup.md)</br>
[Zálohování systému Windows Server](backup-windows-with-mars-agent.md)
