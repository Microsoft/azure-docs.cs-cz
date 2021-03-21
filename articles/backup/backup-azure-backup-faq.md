---
title: Odpovědi na časté dotazy
description: 'Odpovědi na běžné dotazy týkající se funkcí služby Azure Backup, včetně trezorů služby Recovery Services, co může zálohovat, jak to funguje, šifrování a omezení. '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: 79ff404192de481965f3971f00328c49a591dd41
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583373"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup – Nejčastější dotazy

V tomto článku najdete odpovědi na běžné dotazy týkající se služby Azure Backup.

## <a name="recovery-services-vault"></a>Trezor služby Recovery Services

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Je v rámci předplatného Azure nějak omezený počet trezorů, které lze vytvořit?

Ano. Na jedno předplatné můžete vytvořit až 500 trezorů služby Recovery Services pro každou podporovanou oblast služby Azure Backup. Pokud potřebujete další trezory, vytvořte další předplatné.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Je nějak omezený počet serverů nebo počítačů, které lze zaregistrovat k trezoru?

K jednomu trezoru můžete zaregistrovat až 1000 počítačů. Pokud používáte agenta Microsoft Azure Backup, můžete pro každý trezor zaregistrovat až 50 agentů MARS. A můžete registrovat 50 serverů MABS nebo serverů DPM do trezoru.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Kolik zdrojů dat nebo položek je možné chránit v jednom trezoru?

V trezoru můžete chránit až 2000 zdrojů dat a položek napříč všemi úlohami (například virtuální počítač IaaS, SQL, AFS).
Pokud jste například už chránili 500 virtuálních počítačů a 400 sdílené složky Azure Files v trezoru, můžete v ní 1100 chránit jenom databáze SQL.

### <a name="how-many-policies-can-i-create-per-vault"></a>Kolik zásad je možné vytvořit pro každý trezor?

Na jeden trezor můžete mít až 200 zásad.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Pokud má moje organizace jeden trezor, jak se při obnovování dat dají izolovat data jednoho serveru od jiného?

Data serveru, která chcete obnovovat dohromady, by při nastavování zálohování měla používat stejné přístupové heslo. Pokud chcete obnovení izolovat na konkrétní server nebo servery, použijte přístupové heslo jenom pro tento server nebo servery. Například servery lidských zdrojů mohou používat jedno šifrovací heslo, účetní servery jiné a servery úložiště ještě jiné.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Dá se přesunout trezor mezi předplatnými?

Ano. Informace o přesunu trezoru služby Recovery Services najdete v tomto [článku](backup-azure-move-recovery-services-vault.md).

### <a name="can-i-move-backup-data-to-another-vault"></a>Dají se zálohovaná data přesunout do jiného trezoru?

No. Zálohovaná data uložená v trezoru se nedají přesunout do jiného trezoru.

### <a name="can-i-change-the-storage-redundancy-setting-after-a-backup"></a>Můžu po zálohování změnit nastavení redundance úložiště?

Typ replikace úložiště je ve výchozím nastavení nastaven na geograficky redundantní úložiště (GRS). Po konfiguraci zálohování je možnost upravit zakázána a nelze ji změnit.

![Typ replikace úložiště](./media/backup-azure-backup-faq/storage-replication-type.png)

Pokud jste už zálohu nakonfigurovali a musíte se přesunout z GRS na LRS, přečtěte si téma [Jak změnit z GRS na LRS po konfiguraci zálohování](backup-create-rs-vault.md#how-to-change-from-grs-to-lrs-after-configuring-backup).

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Dá se pro virtuální počítače zálohované v trezoru služby Recovery Services použít obnovování na úrovni položek (Item Level Restore, ILR)?

- ILR se podporuje pro virtuální počítače Azure zálohované pomocí funkce zálohování virtuálních počítačů Azure. Další informace najdete v tomto [článku](backup-azure-restore-files-from-vm.md).
- ILR se nepodporuje u online bodů obnovení místních virtuálních počítačů zálohovaných nástrojem Azure Backup Server (MABS) nebo System Center DPM.

### <a name="how-can-i-move-data-from-the-recovery-services-vault-to-on-premises"></a>Jak můžu přesunout data z trezoru služby Recovery Services do místního prostředí?

Export dat přímo z Recovery Services trezoru do místního prostředí pomocí Data Box není podporován. Data musí být obnovena do účtu úložiště a pak je lze přesunout do místního prostředí prostřednictvím [data box](../databox/data-box-overview.md) nebo [Import/export](../import-export/storage-import-export-service.md).

### <a name="what-is-the-difference-between-a-geo-redundant-storage-grs-vault-with-and-without-the-cross-region-restore-crr-capability-enabled"></a>Jaký je rozdíl mezi trezorem geograficky redundantního úložiště (GRS) s povolenou funkcí obnovení mezi oblastmi (CRR) a bez nich?

V případě trezoru [GRS](azure-backup-glossary.md#grs) bez povoleného [crr](azure-backup-glossary.md#cross-region-restore-crr) funkce není dostupná žádná data v sekundární oblasti, dokud Azure nedeklaruje havárie v primární oblasti. V takovém scénáři Probíhá obnovení ze sekundární oblasti. Když je povolený CRR, i když je primární oblast v provozu, můžete spustit obnovení v sekundární oblasti.

### <a name="can-i-move-a-subscription-that-contains-a-vault-to-a-different-azure-active-directory"></a>Můžu přesunout předplatné, které obsahuje trezor, na jiný Azure Active Directory?

Ano. Pokud chcete přesunout předplatné (které obsahuje trezor) do jiné Azure Active Directory (AD), přečtěte si téma [přenos předplatného do jiného adresáře](../role-based-access-control/transfer-subscription.md).

>[!IMPORTANT]
>Po přesunu předplatného se ujistěte, že provedete následující akce:<ul><li>Oprávnění řízení přístupu na základě rolí a vlastní role nejsou voditelné. V nové službě Azure AD musíte znovu vytvořit oprávnění a role.</li><li>Spravovanou identitu trezoru musíte znovu vytvořit tak, že ji zakážete a znovu povolíte. Také je nutné vyhodnotit a znovu vytvořit oprávnění MI.</li><li>Pokud trezor používá funkce, které využívají MI, například [soukromé koncové body](private-endpoints.md#before-you-start) a [spravované klíče zákazníka](encryption-at-rest-with-cmk.md#before-you-start), je nutné tyto funkce překonfigurovat.</li></ul>

### <a name="can-i-move-a-subscription-that-contains-a-recovery-services-vault-to-a-different-tenant"></a>Můžu přesunout předplatné, které obsahuje Recovery Services trezor, do jiného tenanta?

Ano. Ujistěte se, že provedete následující akce: 

>[!IMPORTANT]
>Po přesunu předplatného se ujistěte, že provedete následující akce:<ul><li>Pokud trezor používá CMK (spravované klíče zákazníka), musíte aktualizovat trezor. Díky tomu může trezor znovu vytvořit a znovu nakonfigurovat spravovanou identitu úložiště a CMK (která se bude nacházet v novém tenantovi), jinak se operace zálohování a obnovení nezdaří.</li><li>V předplatném musíte překonfigurovat oprávnění RBAC, protože existující oprávnění nejde přesunout.</li></ul>

## <a name="azure-backup-agent"></a>Agent Azure Backup

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Kde najdu běžné otázky týkající se agenta služby Azure Backup pro zálohování virtuálních počítačů Azure?

- Informace o agentovi spuštěném na virtuálních počítačích Azure najdete v těchto [nejčastějších dotazech](backup-azure-vm-backup-faq.yml).
- Informace o agentovi použitém k zálohování složek Azure Files najdete v těchto [nejčastějších dotazech](backup-azure-file-folder-backup-faq.md).

## <a name="general-backup"></a>Obecné zálohování

### <a name="are-there-limits-on-backup-scheduling"></a>Existují pro plánování zálohování nějaká omezení?

Ano.

- Počítače s Windows Serverem nebo Windows můžete zálohovat až třikrát denně. Pro zásady plánování můžete nastavit denní nebo týdenní plány.
- DPM můžete zálohovat až dvakrát denně. Pro zásady plánování můžete nastavit denní, týdenní, měsíční nebo roční plány.
- Virtuální počítače Azure zálohujete jednou denně.

### <a name="what-operating-systems-are-supported-for-backup"></a>Které operační systémy se pro zálohování podporují?

Azure Backup podporuje pro zálohování souborů a složek a aplikací chráněných pomocí Azure Backup Serveru a DPM následující operační systémy.

**OS** | **SKU** | **Podrobnosti**
--- | --- | ---
Pracovní stanice | |
Windows 10, 64bitová verze | Enterprise, Pro, Home | Na počítačích by měly být spuštěné nejnovější aktualizace Service Pack a aktualizace služeb.
Windows 8.1, 64bitová verze | Enterprise, Pro | Na počítačích by měly být spuštěné nejnovější aktualizace Service Pack a aktualizace služeb.
Windows 8, 64bitová verze | Enterprise, Pro | Na počítačích by měly být spuštěné nejnovější aktualizace Service Pack a aktualizace služeb.
Windows 7, 64bitová verze | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Na počítačích by měly být spuštěné nejnovější aktualizace Service Pack a aktualizace služeb.
Server | |
Windows Server 2019, 64bitová verze | Standard, Datacenter, Essentials | S nejnovějšími aktualizacemi Service Pack / aktualizacemi služeb.
Windows Server 2016, 64bitová verze | Standard, Datacenter, Essentials | S nejnovějšími aktualizacemi Service Pack / aktualizacemi služeb.
Windows Server 2012 R2, 64bitová verze | Standard, Datacenter, Foundation | S nejnovějšími aktualizacemi Service Pack / aktualizacemi služeb.
Windows Server 2012, 64bitová verze | Datacenter, Foundation, Standard | S nejnovějšími aktualizacemi Service Pack / aktualizacemi služeb.
Windows Storage Server 2016, 64bitová verze | Standard, Workgroup | S nejnovějšími aktualizacemi Service Pack / aktualizacemi služeb.
Windows Storage Server 2012 R2, 64bitová verze | Standard, Workgroup, Essential | S nejnovějšími aktualizacemi Service Pack / aktualizacemi služeb.
Windows Storage Server 2012, 64bitová verze | Standard, Workgroup | S nejnovějšími aktualizacemi Service Pack / aktualizacemi služeb.
Windows Server 2008 R2 SP1, 64bitová verze | Standard, Enterprise, Datacenter, Foundation | S nejnovějšími aktualizacemi.
Windows Server 2008, 64bitová verze | Standard, Enterprise, Datacenter | S nejnovějšími aktualizacemi.

Azure Backup nepodporuje 32bitové operační systémy.

Pro zálohy virtuálních počítačů Azure s Linuxem Azure Backup podporuje [seznam distribucí schválených pro Azure](../virtual-machines/linux/endorsed-distros.md), kromě základního OS Linux a 32bitového operačního systému. Další vlastní distribuce Linuxu mohou také fungovat, pokud je ve virtuálním počítači dostupný agent virtuálního počítače a pokud je podporovaný Python.

### <a name="are-there-size-limits-for-data-backup"></a>Existují nějaká omezení velikosti pro zálohování dat?

Platí tato omezení velikosti:

OS/počítač | Omezení velikosti zdroje dat
--- | ---
Windows 8 nebo novější | 54 400 GB
Windows 7 |1700 GB
Windows Server 2012 nebo novější | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Virtuální počítač Azure | Viz [matice podpory pro zálohování virtuálních počítačů Azure](./backup-support-matrix-iaas.md#vm-storage-support) .

### <a name="how-is-the-data-source-size-determined"></a>Jak se určuje velikost zdroje dat?

Následující tabulka vysvětluje, jak je určená velikost jednotlivých zdrojů dat.

**Zdroj dat** | **Podrobnosti**
--- | ---
Svazek |Množství dat zálohovaných z jednoho svazku virtuálního počítače
Databáze SQL Serveru |Velikost databáze s jedinou zálohovanou velikostí.
SharePoint | Součet databází obsahu a konfiguračních databází v rámci zálohované farmy služby SharePoint
Výměna |Součet všech databází systému Exchange na zálohovaném serveru Exchange
BMR a stav systému |Každá jednotlivá kopie BMR nebo stavu systému zálohovaného počítače

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Existuje nějaké omezení množství dat zálohovaných s využitím trezoru služby Recovery Services?

K dispozici není žádné omezení celkového množství dat, která můžete zálohovat pomocí Recovery Services trezoru. Jednotlivé zdroje dat (kromě virtuálních počítačů Azure) můžou mít velikost maximálně 54 400 GB. Další informace o omezeních najdete [v části omezení trezoru v matici podpory](./backup-support-matrix.md#vault-support).

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Proč je velikost dat přenášených do trezoru služby Recovery Services menší než velikost dat vybraných k zálohování?

Data zálohovaná z agenta služby Azure Backup, DPM nebo Azure Backup Serveru projdou před odesláním kompresí a šifrováním. Po kompresi a šifrování jsou data v trezoru o 30–40 % menší.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Dají se z bodu obnovení v trezoru odstranit jednotlivé soubory?

Ne, Azure Backup nepodporuje odstraňování nebo mazání jednotlivých položek z uložených záloh.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Když zruším úlohu zálohování poté, co už se spustila, dojde k odstranění přenášených dat?

No. Všechna data přenášená do trezoru před zrušením úlohy zálohování zůstanou v trezoru.

- Azure Backup používá mechanismus kontrolních bodů k příležitostnému přidávání kontrolních bodů do zálohovaných dat během zálohování.
- Díky kontrolním bodům v zálohovaných datech je možné při dalším procesu zálohování ověřit integritu souborů.
- Následující zálohování proběhne jako přírůstkové vzhledem k naposledy zálohovaným datům. Přírůstkové zálohování přenáší jen nová nebo změněná data, což znamená lepší využití přenosové kapacity.

Když ve virtuálním počítači Azure zrušíte úlohu zálohování, budou dosud přenesená data ignorována. Při následujícím přírůstkovém zálohování se přenesou data, která se změnila od poslední úspěšně dokončené úlohy zálohování.

## <a name="retention-and-recovery"></a>Uchovávání a obnovení

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Jsou zásady uchovávání pro počítače s DPM a počítače s Windows bez DPM stejné?

Ano, oba tyto typy mohou mít denní, týdenní, měsíční nebo roční zásady uchovávání.

### <a name="can-i-customize-retention-policies"></a>Dají se zásady uchovávání přizpůsobit?

Ano, zásady můžete přizpůsobovat. Můžete například nakonfigurovat požadavky na týdenní a denní uchovávání, ale ne na roční a měsíční.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Dají se pro plánování zálohování a zásady uchovávání používat jiné časy?

No. Zásady uchovávání informací lze aplikovat pouze na body záloh. Tento obrázek například ukazuje zásady uchovávání informací pro zálohy vytvořené ve 12:00 a 18:00.

![Plánování zálohování a uchovávání](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>Pokud se záloha uchovává po dlouhou dobu, trvá pak obnovení staršího datového bodu déle?

No. Obnovení nejstaršího i nejnovějšího bodu trvá stejně dlouho. Každý bod obnovení se chová jako úplný bod.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Jestliže se každý bod obnovení chová jako úplný bod, ovlivní to celkové fakturovatelné úložiště zálohování?

Typické produkty s dlouhodobými body uchování ukládají zálohovaná data jako úplné body. 

- Úplné body jsou *neefektivní* z hlediska úložiště, ale jejich obnovení je snadnější a rychlejší.
- Přírůstkové kopie jsou *efektivní* z hlediska úložiště, ale vyžadují obnovení řetězu dat, což ovlivňuje dobu obnovení.

Architektura úložiště Azure Backup nabízí to nejlepší z obou světů – optimální ukládání dat pro rychlé obnovení a nízké poplatky za úložiště. Tento přístup zajišťuje efektivní využití příchozí i odchozí šířky pásma. Velikost úložiště dat i čas potřebný k obnovení dat jsou omezeny na minimum. Další informace o [přírůstkovém zálohování](backup-architecture.md#backup-types)

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Je nějak omezený počet bodů obnovení, které lze vytvořit?

Na jednu chráněnou instanci je možné vytvořit až 9 999 bodů obnovení. Chráněná instance je počítač, server (fyzický nebo virtuální) nebo úloha zálohující do Azure.

- Další informace o [zálohování a uchovávání](./backup-support-matrix.md)

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Kolikrát se dají obnovit data zálohovaná do Azure?

Neexistuje žádné omezení počtu obnov z Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Platí se za výchozí přenos z Azure při obnovování dat?

No. Obnovení je zdarma a výchozí přenos se vám neúčtuje.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Co se stane, když změním zásady zálohování?

Pokud se použije nová zásada, plán a uchovávání se budou řídit touto novou zásadou.

- Pokud je uchovávání dat rozšířené, existující body obnovení jsou označeny tak, aby byly v souladu s novými zásadami.
- Pokud se doba uchovávání zkrátí, označí se k vyřazení v rámci další úlohy čištění a následně se odstraní.

### <a name="how-long-is-data-retained-when-stopping-backups-but-selecting-the-option-to-retain-backup-data"></a>Jak dlouho se data uchovávají při zastavování záloh, ale výběr možnosti uchovávání zálohovaných dat?

Když se zálohování zastaví a data se zachovají, stávající pravidla zásad pro vyřazování dat se zastaví a data se uchovávají po dobu neurčitou, dokud ji správce nespustí.

## <a name="encryption"></a>Šifrování

### <a name="is-the-data-sent-to-azure-encrypted"></a>Jsou data odesílaná do Azure šifrovaná?

Ano. Data se na místním počítači šifrují pomocí AES256. Data se odesílají prostřednictvím zabezpečeného spojení HTTPS. Data přenášená v cloudu jsou propojením HTTPS chráněná jenom mezi úložištěm a službou Recovery Service. Data přenášená mezi službou Recovery Service a uživatelským počítačem zabezpečuje protokol iSCSI. K ochraně tunelu iSCSI se využívá zabezpečené tunelové propojení.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Jsou šifrovaná i zálohovaná data v Azure?

Ano. Data v Azure jsou v klidovém stavu zašifrovaná.

- V případě místního zálohování se šifrování v klidovém stavu zajišťuje pomocí hesla, které zadáte při zálohování do Azure.
- Pro virtuální počítače Azure jsou neaktivní uložená data zašifrovaná pomocí Šifrování služby Storage (SSE).

Společnost Microsoft v žádném okamžiku nešifruje zálohovaná data.

### <a name="what-is-the-minimum-length-of-the-encryption-key-used-to-encrypt-backup-data"></a>Jaká je minimální délka šifrovacího klíče použitého k šifrování zálohovaných dat?

Šifrovací klíč používaný agentem služby Microsoft Azure Recovery Services (MARS) je odvozen z hesla, které by mělo být alespoň 16 znaků dlouhé. U virtuálních počítačů Azure není nijak omezená délka klíčů používaných službou Azure Key trezor.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Co se stane, když ztratím šifrovací klíč? Můžu data obnovit? Může je obnovit Microsoft?

Klíč, pomocí kterého se šifrují zálohovaná data, je k dispozici jenom u vás. Microsoft neuchovává kopii v Azure a nemá žádný přístup k tomuto klíči. Pokud tento klíč ztratíte, Microsoft nemůže zálohovaná data obnovit.

## <a name="next-steps"></a>Další kroky

Přečtěte si další nejčastější dotazy:

- [Běžné dotazy](backup-azure-vm-backup-faq.yml) týkající se zálohování virtuálních počítačů Azure
- [Běžné dotazy](backup-azure-file-folder-backup-faq.md) týkající se agenta Azure Backup
