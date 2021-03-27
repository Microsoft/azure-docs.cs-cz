---
title: Co je nového ve službě Azure Backup
description: Seznamte se s novými funkcemi v Azure Backup.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: c5e6734c6a962fa43d79fc90fdfaa85923b6339f
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612479"
---
# <a name="whats-new-in-azure-backup"></a>Co je nového ve službě Azure Backup

Azure Backup neustále vylepšuje a uvolňuje nové funkce, které zvyšují ochranu vašich dat v Azure. Tyto nové funkce rozšiřují ochranu dat na nové typy úloh, zvyšují zabezpečení a zlepšují dostupnost zálohovaných dat. Také přidávají nové možnosti správy, monitorování a automatizace.

Další informace o nových vydaných verzích najdete v tématu popisujícím tuto stránku nebo se můžete [přihlásit k odběru aktualizací](https://azure.microsoft.com/updates/?query=backup).

## <a name="updates-summary"></a>Souhrn aktualizací

- Březen 2021
  - [Zálohování disku Azure je teď všeobecně dostupné.](#azure-disk-backup-is-now-generally-available)
  - [Centrum zálohování je teď všeobecně dostupné](#backup-center-is-now-generally-available)
  - [Podpora archivní vrstvy pro Azure Backup (ve verzi Preview)](#archive-tier-support-for-azure-backup-in-preview)
- Únor 2021
  - [Zálohování objektů blob Azure (ve verzi Preview)](#backup-for-azure-blobs-in-preview)
- Leden 2021
  - [Zálohování disku Azure (ve verzi Preview)](#azure-disk-backup-in-preview)
  - [Šifrování v klidovém formátu pomocí klíčů spravovaných zákazníkem (všeobecně dostupné)](#encryption-at-rest-using-customer-managed-keys)
- Listopad 2020
  - [Šablona Azure Resource Manager pro zálohování ve službě Azure File Share (AFS)](#azure-resource-manager-template-for-afs-backup)
  - [Přírůstkové zálohování pro databáze SAP HANA na virtuálních počítačích Azure (ve verzi Preview)](#incremental-backups-for-sap-hana-databases-in-preview)
- Září 2020
  - [Centrum zálohování (ve verzi Preview)](#backup-center-in-preview)
  - [Záložní Azure Database for PostgreSQL (ve verzi Preview)](#backup-azure-database-for-postgresql-in-preview)
  - [Zálohování a obnovení selektivního disku](#selective-disk-backup-and-restore)
  - [Obnovení mezi oblastmi pro databáze SQL Server a SAP HANA na virtuálních počítačích Azure (ve verzi Preview)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [Podpora pro zálohování virtuálních počítačů s až 32 disky (obecně dostupné)](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Zjednodušené možnosti konfigurace zálohování pro SQL ve virtuálních počítačích Azure](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Zálohování SAP HANA v RHEL Azure Virtual Machines (ve verzi Preview)](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [Redundantní úložiště zóny (ZRS) pro zálohovaná data (ve verzi Preview)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Obnovitelné odstranění pro úlohy SQL Server a SAP HANA na virtuálních počítačích Azure](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-disk-backup-is-now-generally-available"></a>Zálohování disku Azure je teď všeobecně dostupné.

Azure Backup nabízí správu životního cyklu snímků do Azure Managed Disks automatizací pravidelného vytváření snímků a jejich zachování pro nakonfigurované doby trvání pomocí zásad zálohování.

Další informace najdete v tématu [Přehled zálohování disku Azure](disk-backup-overview.md).

## <a name="backup-center-is-now-generally-available"></a>Centrum zálohování je teď všeobecně dostupné

Centrum zálohování zjednodušuje správu ochrany dat ve velkém měřítku tím, že umožňuje zjišťovat, řídit, monitorovat, pracovat a optimalizovat správu zálohování z jedné centrální konzoly.

Další informace najdete v tématu [Přehled centra zálohování](backup-center-overview.md).

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Podpora archivní vrstvy pro Azure Backup (ve verzi Preview)

Azure Backup teď umožňuje snížit náklady na dlouhodobé zálohy uchovávání s využitím úrovně archivace pro virtuální počítače Azure a SQL Server na virtuálních počítačích Azure.

Další informace najdete v tématu [Podpora úrovně archivu (Preview)](archive-tier-support.md).

## <a name="backup-for-azure-blobs-in-preview"></a>Zálohování objektů blob Azure (ve verzi Preview)

Provozní záloha pro objekty BLOB je spravované místní řešení pro ochranu dat, které umožňuje chránit objekty blob bloku z různých scénářů ztráty dat, jako jsou poškození, odstranění objektů BLOB a náhodné odstranění účtu úložiště. Data jsou uložená místně v rámci samotného zdrojového účtu úložiště a v každém případě je můžete kdykoli obnovit do vybraného bodu v čase. Poskytuje tak jednoduché, bezpečné a nákladově efektivní prostředky pro ochranu objektů BLOB.

Provozní záloha pro objekty BLOB se integruje do centra zálohování, a to mimo jiné možnosti správy zálohování, aby poskytovala samostatné podokno se skleněným řízením, které vám umožní řídit, monitorovat, provozovat a analyzovat zálohy ve velkém měřítku.

Další informace najdete v tématu [Přehled provozní zálohy pro objekty blob Azure (ve verzi Preview)](blob-backup-overview.md).

## <a name="azure-disk-backup-in-preview"></a>Zálohování disku Azure (ve verzi Preview)

Azure disk Backup nabízí řešení klíč, které poskytuje správu životního cyklu snímků pro [Azure Managed disks](../virtual-machines/managed-disks-overview.md) automatizací pravidelného vytváření snímků a jejich zachování po nakonfigurované době pomocí zásad zálohování. Můžete spravovat snímky disků s nulovými náklady na infrastrukturu a nemusíte mít vlastní skriptování ani nároky na správu. Jedná se o řešení zálohování konzistentní vzhledem k selháním, které využívá přírůstkové zálohování spravovaného disku pomocí [přírůstkových snímků](../virtual-machines/disks-incremental-snapshots.md) s podporou více záloh za den. Je to také řešení bez agentů a nemá vliv na výkon produkčních aplikací. Podporuje zálohování a obnovení disků s operačním systémem i datové disky (včetně sdílených disků), ať už jsou aktuálně připojené ke spuštěnému virtuálnímu počítači Azure.

Další informace najdete v tématu [zálohování disku Azure (ve verzi Preview)](disk-backup-overview.md).

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Šifrování v klidovém formátu pomocí klíčů spravovaných zákazníkem

Podpora šifrování v klidovém formátu pomocí klíčů spravovaných zákazníkem je teď všeobecně dostupná. Díky tomu máte možnost šifrovat data záloh ve vašich úložištích Recovery Services pomocí vlastních klíčů uložených v trezorech klíčů Azure. Šifrovací klíč, který se používá k šifrování záloh v trezoru Recovery Services, se může lišit od těch, které se použily k šifrování zdroje. Data jsou chráněná pomocí šifrovacího klíče založeného na standardu AES 256 (klíč DEK), který je zase chráněn pomocí klíčů uložených v Key Vault. V porovnání s šifrováním pomocí klíčů spravovaných platformou (které jsou ve výchozím nastavení k dispozici) vám to umožní získat větší kontrolu nad klíči a může vám pomoci lépe splnit požadavky na dodržování předpisů.

Další informace najdete v tématu [šifrování zálohovaných dat pomocí klíčů spravovaných zákazníkem](encryption-at-rest-with-cmk.md).

## <a name="azure-resource-manager-template-for-afs-backup"></a>Šablona Azure Resource Manager pro zálohování AFS

Azure Backup teď podporuje konfiguraci zálohování pro existující sdílené složky Azure pomocí šablony Azure Resource Manager (ARM). Šablona konfiguruje ochranu pro existující sdílenou složku Azure tím, že určí příslušné podrobnosti pro Recovery Services trezor a zásady zálohování. Volitelně vytvoří nový trezor Recovery Services a zásadu zálohování a zaregistruje účet úložiště obsahující sdílenou složku do trezoru Recovery Services.

Další informace najdete v tématu [šablony Azure Resource Manager pro Azure Backup](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>Přírůstkové zálohování pro databáze SAP HANA (ve verzi Preview)

Azure Backup teď podporuje přírůstkové zálohování pro databáze SAP HANA hostované na virtuálních počítačích Azure. To umožňuje rychlejší a efektivnější zálohování dat SAP HANA.

Další informace najdete v tématu [různé možnosti, které jsou dostupné během vytváření zásady zálohování](sap-hana-faq-backup-azure-vm.md#policy) , a [Postup vytvoření zásad zálohování pro databáze SAP HANA](tutorial-backup-sap-hana-db.md#creating-a-backup-policy).

## <a name="backup-center-in-preview"></a>Centrum zálohování (ve verzi Preview)

Azure Backup povolili novou nativní možnost správy pro správu celé zálohy z centrální konzoly. Centrum zálohování poskytuje možnost monitorování, provozování, řízení a optimalizace ochrany dat ve velkém měřítku jednotným způsobem konzistentním s využitím nativního prostředí pro správu Azure.

Pomocí služby Backup Center získáte agregované zobrazení inventáře mezi předplatnými, umístěními, skupinami prostředků, trezory a i klienty pomocí Azure Lighthouse. Centrum služby Backup je také centrem akcí, kde můžete aktivovat aktivity související se zálohováním, jako je například konfigurace zálohování, obnovení, vytváření zásad nebo trezorů, z jednoho místa. Kromě toho s bezproblémovou integrací do Azure Policy se teď můžete řídit prostředí a sledovat dodržování předpisů v perspektivě zálohování. Integrované zásady Azure, které jsou specifické pro Azure Backup, vám také umožní konfigurovat zálohy ve velkém měřítku.

Další informace najdete v tématu [Přehled centra zálohování](backup-center-overview.md).

## <a name="backup-azure-database-for-postgresql-in-preview"></a>Záložní Azure Database for PostgreSQL (ve verzi Preview)

Služba Azure Backup a Azure Database Services společně vytvořila řešení zálohování na podnikové úrovni pro Azure PostgreSQL (teď ve verzi Preview). Nyní můžete splnit požadavky na ochranu dat a dodržování předpisů pomocí zásad zálohování řízených zákazníkem, které umožňují uchovávání záloh po dobu až 10 let. Díky tomu máte podrobnější kontrolu nad správou operací zálohování a obnovení na úrovni jednotlivých databází. Podobně můžete snadno obnovit napříč PostgreSQL verzemi nebo do úložiště objektů BLOB.

Další informace najdete v tématu [Azure Database for PostgreSQL Backup](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Zálohování a obnovení selektivního disku

Azure Backup podporuje zálohování všech disků (operačního systému a dat) na virtuálním počítači společně s použitím řešení zálohování virtuálních počítačů. Teď můžete pomocí funkce zálohování a obnovení selektivních disků zálohovat podmnožinu datových disků ve virtuálním počítači. To poskytuje efektivní a nákladově efektivní řešení pro potřeby zálohování a obnovení. Každý bod obnovení obsahuje pouze disky, které jsou součástí operace zálohování.

Další informace najdete v tématu [selektivní zálohování disku a obnovení pro virtuální počítače Azure](selective-disk-backup-restore.md).

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>Obnovení mezi oblastmi pro SQL Server a SAP HANA (ve verzi Preview)

Díky zavedení obnovení mezi oblastmi teď můžete začít obnovovat v sekundární oblasti. tím se sníží riziko problémů v reálném výpadku v primární oblasti vašeho prostředí. Tím se v sekundární oblasti obnoví plně řízené zákazníky. Azure Backup používá zálohovaná data replikovaná do sekundární oblasti pro taková obnovení.

Nyní se tato funkce rozšířila i na podporu obnovení pro virtuální počítače Azure v různých oblastech, a to i na obnovení databází SQL a SAP HANAch ve virtuálních počítačích Azure.

Další informace najdete v tématu [obnovení v různých oblastech pro databáze SQL](restore-sql-database-azure-vm.md#cross-region-restore) a [obnovení mezi oblastmi pro SAP HANA databáze](sap-hana-db-restore.md#cross-region-restore).

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Podpora pro zálohování virtuálních počítačů s až 32 disky

Dokud teď Azure Backup podporuje 16 spravovaných disků na jeden virtuální počítač. Nyní Azure Backup podporuje zálohování až 32 spravovaných disků na jeden virtuální počítač.

Další informace najdete v tématu [matice podpory úložiště virtuálních počítačů](backup-support-matrix-iaas.md#vm-storage-support).

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Jednodušší konfigurace zálohování pro SQL ve virtuálních počítačích Azure

Konfigurace záloh pro vaše SQL Server ve virtuálních počítačích Azure je teď ještě jednodušší díky vložené konfiguraci zálohování integrované do podokna virtuálního počítače v Azure Portal. V několika krocích můžete povolit zálohování SQL Server a chránit tak všechny existující databáze a také ty, které se přidají do budoucna.

Další informace najdete v tématu [zálohování SQL Server v podokně virtuálního počítače](backup-sql-server-vm-from-vm-pane.md).

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>Zálohování SAP HANA ve virtuálních počítačích Azure s RHEL (ve verzi Preview)

Azure Backup je nativní řešení zálohování pro Azure a je BackInt certifikováno pomocí SAP. Azure Backup teď přidal podporu pro Red Hat Enterprise Linux (RHEL), jeden z nejpoužívanějších operačních systémů Linux, na kterých běží SAP HANA.

Další informace najdete v tématu [matice podpory scénářů zálohování databáze SAP HANA](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>Redundantní úložiště zóny (ZRS) pro zálohovaná data (ve verzi Preview)

Azure Storage poskytuje vynikající rovnováhu vysokého výkonu, vysoké dostupnosti a vysoké odolnosti dat s využitím různých možností redundance. Azure Backup vám umožní tyto výhody také využít k zálohování dat a možnosti ukládat zálohy do místně redundantního úložiště (LRS) a geograficky redundantního úložiště (GRS). Nyní existují další možnosti trvanlivosti s přidanou podporou pro zónu redundantního úložiště (ZRS).

Další informace najdete v tématu [Nastavení redundance úložiště pro Recovery Services trezor](backup-create-rs-vault.md#set-storage-redundancy).

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>Obnovitelné odstranění pro úlohy SQL Server a SAP HANA

Problémy se zabezpečením, jako je malware, ransomwarem a vniknutí, se zvyšují. Tyto problémy se zabezpečením můžou být nákladné, s ohledem na peníze i data. Pro ochranu proti takovým útokům Azure Backup poskytuje funkce zabezpečení, které vám pomůžou chránit zálohovaná data i po odstranění.

Jednou z těchto funkcí je obnovitelné odstranění. Pomocí obnovitelného odstranění, a to i v případě, že škodlivý objekt actor odstraní zálohu (nebo se data záloh nechtěně odstraní), uchovávají data zálohy 14 dalších dnů, což umožňuje obnovení této zálohované položky bez ztráty dat. Další 14 dní uchovávání zálohových dat ve stavu "obnovitelné odstranění" za vás neúčtují žádné náklady.

Kromě toho, že podpora obnovitelného odstranění pro virtuální počítače Azure, SQL Server a SAP HANA úlohy ve virtuálních počítačích Azure, jsou také chráněné pomocí obnovitelného odstranění.

Další informace najdete v tématu [obnovitelné odstranění pro SQL Server na virtuálním počítači Azure a SAP HANA v úlohách virtuálních počítačů Azure](soft-delete-sql-saphana-in-azure-vm.md).

## <a name="next-steps"></a>Další kroky

- [Pokyny Azure Backup a osvědčené postupy](guidance-best-practices.md)