---
title: Odpovědi na běžné dotazy týkající se funkcí Azure Backup
description: 'Odpovědi na běžné otázky týkající se: Azure Backup funkce, včetně trezorů Recovery Services, co může zálohovat, jak to funguje, šifrování a omezení. '
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/07/2019
ms.author: dacurwin
ms.openlocfilehash: 415a25dbe63b8942509827cd8434cc0f50fde87a
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954817"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup – často kladené otázky
Tento článek obsahuje odpovědi na nejčastější dotazy týkající se služby Azure Backup.

## <a name="recovery-services-vault"></a>Trezor Recovery Services

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Je v rámci předplatného Azure nějak omezený počet trezorů, které lze vytvořit?
Ano. Pro každou podporovanou oblast Azure Backup můžete pro každé předplatné vytvořit až 500 Recovery Services trezory. Pokud potřebujete další trezory, vytvořte další předplatné.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Je nějak omezený počet serverů nebo počítačů, které lze zaregistrovat k trezoru?
Pro každý trezor můžete zaregistrovat až 1000 virtuálních počítačů Azure. Pokud používáte agenta Microsoft Azure Backup, můžete zaregistrovat až 50 agentů MAB na jeden trezor. A můžete registrovat 50 serverů MAB nebo serverů DPM do trezoru.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Kolik zdrojů/položek lze chránit v trezoru? 
V trezoru můžete chránit až 2000 zdrojů dat a položek napříč všemi úlohami (IaaS VM, SQL, AFS atd.).<br>  
Pokud jste třeba v trezoru už chránili 500 virtuálních počítačů a 400 souborů Azure, můžete v ní 1100 chránit jenom databáze SQL. 

### <a name="how-many-policies-can-i-create-per-vault"></a>Kolik zásad můžu vytvořit pro každý trezor? 
Na jeden trezor můžete mít až 200 zásad.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Pokud má moje organizace jeden trezor, jak můžu během obnovování dat izolovat data z různých serverů v trezoru?
Data serveru, která chcete obnovit společně, by měla při nastavování zálohy používat stejné přístupové heslo. Chcete-li izolovat obnovení na konkrétní server nebo servery, použijte přístupové heslo pouze pro tento server nebo servery. Například servery lidských zdrojů mohou používat jedno šifrovací heslo, účetní servery jiné a servery úložiště ještě jiné.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Můžu přesunout trezor mezi předplatnými?
Ano. Pokud chcete přesunout trezor Recovery Services, přečtěte si tento [článek](backup-azure-move-recovery-services-vault.md) .

### <a name="can-i-move-backup-data-to-another-vault"></a>Můžu přesunout zálohovaná data do jiného trezoru?
Ne. Zálohovaná data uložená v trezoru nejde přesunout do jiného trezoru.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Můžu po zálohování změnit z GRS na LRS?
Ne. Trezor Recovery Services může změnit jenom možnosti úložiště před uložením jakýchkoli záloh.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Můžu provést obnovení na úrovni položky (ILR) pro virtuální počítače zálohované do trezoru Recovery Services?
- ILR se podporuje pro virtuální počítače Azure zálohované zálohováním virtuálních počítačů Azure. Další informace najdete v [článku](backup-azure-restore-files-from-vm.md) .
- ILR se nepodporuje pro body obnovení online místních virtuálních počítačů zálohovaných službou Azure Backup Server nebo System Center DPM.


## <a name="azure-backup-agent"></a>Agent Azure Backup

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Kde najdu běžné otázky týkající se Azure Backup agenta pro zálohování virtuálních počítačů Azure?

- V případě agenta běžícího na virtuálních počítačích Azure si přečtěte tyto [Nejčastější dotazy](backup-azure-vm-backup-faq.md).
- V případě agenta používaného k zálohování složek souborů Azure si přečtěte tyto [Nejčastější dotazy](backup-azure-file-folder-backup-faq.md).


## <a name="general-backup"></a>Obecné zálohování

### <a name="are-there-limits-on-backup-scheduling"></a>Existují pro plánování zálohování omezení?
Ano.
- Počítače s Windows serverem nebo Windows můžete zálohovat až třikrát denně. Můžete nastavit zásady plánování na denní nebo týdenní plány.
- DPM můžete zálohovat až dvakrát denně. Zásadu plánování můžete nastavit na denní, týdenní, měsíční a roční.
- Virtuální počítače Azure zálohujete jednou denně.

### <a name="what-operating-systems-are-supported-for-backup"></a>Jaké operační systémy jsou podporované pro zálohování?
Azure Backup podporuje tyto operační systémy pro zálohování souborů a složek a aplikací chráněných pomocí Azure Backup Server a DPM.

**OS** | **SKU** | **Podrobnosti**
--- | --- | ---
Pracovní stanice | |
Bitová verze Windows 10 64 | Enterprise, Pro, Home | V počítačích by měly být spuštěny nejnovější aktualizace a aktualizace služeb.
Bit Windows 8.1 64 | Enterprise, Pro | V počítačích by měly být spuštěny nejnovější aktualizace a aktualizace služeb.
Bitová verze Windows 8 64 | Enterprise, Pro | V počítačích by měly být spuštěny nejnovější aktualizace a aktualizace služeb.
Bitová verze Windows 7 64 | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | V počítačích by měly být spuštěny nejnovější aktualizace a aktualizace služeb.
Server | |
Windows Server 2019 64 – bit | Standard, Datacenter, Essentials | S nejnovějšími aktualizacemi nebo aktualizacemi Service Pack.
Windows Server 2016 64 – bit | Standard, Datacenter, Essentials | S nejnovějšími aktualizacemi nebo aktualizacemi Service Pack.
Windows Server 2012 R2 64 bitů | Standard, Datacenter, Foundation | S nejnovějšími aktualizacemi nebo aktualizacemi Service Pack.
Windows Server 2012 64 – bit | Datacenter, Foundation, Standard | S nejnovějšími aktualizacemi nebo aktualizacemi Service Pack.
Windows Storage Server 2016 64 – bit | Standard, Workgroup | S nejnovějšími aktualizacemi nebo aktualizacemi Service Pack.
Windows Storage Server 2012 R2 64 bit | Standard, Workgroup, Essential | S nejnovějšími aktualizacemi nebo aktualizacemi Service Pack.
Windows Storage Server 2012 64 – bit | Standard, Workgroup | S nejnovějšími aktualizacemi nebo aktualizacemi Service Pack.
Windows Server 2008 R2 SP1 64 bit | Standard, Enterprise, Datacenter, Foundation | S nejnovějšími aktualizacemi.
Windows Server 2008 64 – bit | Standard, Enterprise, Datacenter | S nejnovějšími aktualizacemi.

Pro zálohování virtuálních počítačů Azure se Azure Backup podporuje [seznam distribucí, které Azure schválil](../virtual-machines/linux/endorsed-distros.md), s výjimkou základního operačního systému Linux a 32. Další vlastní distribuce systému Linux mohou fungovat, pokud je na virtuálním počítači k dispozici agent virtuálního počítače a podpora Pythonu existuje.


### <a name="are-there-size-limits-for-data-backup"></a>Existují pro zálohování dat omezení velikosti?
Omezení velikosti jsou následující:

Operační systém/počítač | Omezení velikosti zdroje dat
--- | ---
Windows 8 nebo novější | 54 400 GB
Windows 7 |1700 GB
Windows Server 2012 nebo novější | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Azure VM | 16 datových disků<br/><br/> Datový disk až do 4095 GB

### <a name="how-is-the-data-source-size-determined"></a>Jak se určuje velikost zdroje dat?
Následující tabulka vysvětluje, jak je určená velikost jednotlivých zdrojů dat.

**Zdroj dat** | **Podrobnosti**
--- | ---
Svazek |Množství zálohovaných dat z zálohovaného virtuálního počítače s jedním svazkem.
Databáze systému SQL Server |Velikost samostatné velikosti databáze SQL.
SharePoint | Součet databází s obsahem a konfigurací v rámci farmy služby SharePoint, která se zálohuje.
Exchange |Součet všech databází serveru Exchange v zálohovaném serveru Exchange.
BMR nebo stav systému |Každou jednotlivou kopii BMR nebo stavu systému zálohovaného počítače.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Existuje omezení množství zálohovaných dat pomocí Recovery Services trezoru?
K dispozici není žádné omezení množství dat, které můžete zálohovat pomocí Recovery Services trezoru.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Proč je velikost dat přenesených do trezoru Recovery Services menší než data vybraná k zálohování?
Data zálohovaná od agenta Azure Backup, DPM a Azure Backup Server se před přenosem komprimují a šifrují. Při použití komprese a šifrování jsou data v trezoru menší než 30-40%.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Můžu odstranit jednotlivé soubory z bodu obnovení v trezoru?
Ne, Azure Backup nepodporuje odstraňování nebo vyprazdňování jednotlivých položek z uložených záloh.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Když po spuštění zruším úlohu zálohování, dojde k odstranění přenesených zálohovaných dat?
Ne. Všechna data přenesená do trezoru před zrušením úlohy zálohování zůstanou v trezoru.

- Azure Backup používá mechanismus kontrolních bodů k příležitostnému přidávání kontrolních bodů do zálohovaných dat během zálohování.
- Díky kontrolním bodům v zálohovaných datech je možné při dalším procesu zálohování ověřit integritu souborů.
- Následující zálohování proběhne jako přírůstkové vzhledem k naposledy zálohovaným datům. Přírůstkové zálohování přenáší jenom nová nebo změněná data, což je rovno lepšímu využití šířky pásma.

Když ve virtuálním počítači Azure zrušíte úlohu zálohování, budou dosud přenesená data ignorována. Při následujícím přírůstkovém zálohování se přenesou data, která se změnila od poslední úspěšně dokončené úlohy zálohování.

## <a name="retention-and-recovery"></a>Uchovávání a obnovení

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Jsou zásady uchovávání pro počítače DPM a Windows bez aplikace DPM stejné?
Ano, obě mají denní, týdenní, měsíční a roční zásady uchovávání.

### <a name="can-i-customize-retention-policies"></a>Můžu přizpůsobit zásady uchovávání informací?
Ano, budete přizpůsobovat zásady. Můžete například nakonfigurovat týdenní a denní požadavky na uchovávání, ale ne roční a měsíční.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Můžu použít jiné časy pro plánování zálohování a zásady uchovávání informací?
Ne. Zásady uchovávání informací lze aplikovat pouze na body záloh. Tento obrázek například ukazuje zásady uchovávání informací pro zálohy vytvořené v 12am a 18:00.

![Plánování zálohování a uchovávání](./media/backup-azure-backup-faq/Schedule.png)


### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Pokud je zálohování po dlouhou dobu zachované, trvá obnovení staršího datového bodu déle? <br/>
Ne. Čas pro obnovení nejstarší nebo nejnovější bod je stejný. Každý bod obnovení se chová jako úplný bod.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Jestliže se každý bod obnovení chová jako úplný bod, ovlivní to celkové fakturovatelné úložiště zálohování?
Typické produkty s dlouhodobými body uchování ukládají zálohovaná data jako úplné body.

- Úplné body jsou *neefektivní* z hlediska úložiště, ale jejich obnovení je snadnější a rychlejší.
- Přírůstkové kopie jsou *efektivní* pro úložiště, ale vyžadují obnovení řetězce dat, který ovlivňuje dobu obnovení.

Architektura úložiště Azure Backup nabízí to nejlepší z obou světů – optimální ukládání dat pro rychlé obnovení a nízké poplatky za úložiště. Tím zajistíte efektivní využití šířky pásma příchozího a odchozího přenosu dat. Velikost úložiště dat a čas potřebný k obnovení dat se uchovávají minimálně. Přečtěte si [](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/)Další informace o přírůstkových zálohách.

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Je nějak omezený počet bodů obnovení, které lze vytvořit?
Na jednu chráněnou instanci je možné vytvořit až 9 999 bodů obnovení. Chráněná instance je počítač, Server (fyzický nebo virtuální) nebo zatížení, které se zálohuje do Azure.

- Přečtěte si další informace o [zálohování a uchovávání](./backup-overview.md#backup-and-retention).


### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Kolikrát můžu obnovit data zálohovaná do Azure?
Počet obnovení z Azure Backup není omezený.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Platí se za výchozí přenos z Azure při obnovování dat?
Ne. Obnovení je bezplatné a neúčtují se vám poplatky za odchozí provoz.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Co se stane, když změním zásady zálohování?
Při použití nové zásady se sleduje plán a uchování nové zásady.

- Pokud se doba uchovávání prodlouží, existující body obnovení se označí k zachování pro novou zásadu.
- Pokud se doba uchovávání zkrátí, označí se k vyřazení v rámci další úlohy čištění a následně se odstraní.

## <a name="encryption"></a>Šifrování

### <a name="is-the-data-sent-to-azure-encrypted"></a>Jsou data odesílaná do Azure šifrovaná?
Ano. Data se šifrují na místním počítači pomocí AES256. Data se odesílají prostřednictvím zabezpečeného spojení HTTPS. Data přenášená v cloudu jsou chráněná propojením HTTPS jenom mezi službou Storage a Recovery Service. protokol iSCSI zabezpečuje data přenášená mezi službou obnovení a uživatelským počítačem. Zabezpečené tunelování se používá k ochraně kanálu iSCSI.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Jsou šifrovaná i zálohovaná data v Azure?
Ano. Data v Azure jsou zašifrovaná – v klidovém stavu.

- V případě místního zálohování se šifrování v klidovém prostředí zajišťuje pomocí hesla, které zadáte při zálohování do Azure.
- Pro virtuální počítače Azure se data zašifrují – na REST pomocí Šifrování služby Storage (SSE).

Microsoft nikdy nedešifruje zálohovaná data.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Jaká je minimální délka šifrování klíče používaného k šifrování zálohovaných dat?
Pokud používáte agenta Azure Backup, šifrovací klíč by měl obsahovat alespoň 16 znaků. Pro virtuální počítače Azure neplatí žádné omezení délky klíčů, které používá služba Azure Key Vault.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Co se stane, když ztratím šifrovací klíč? Můžu obnovit data? Může Microsoft obnovit data?
Klíč, který se používá k šifrování zálohovaných dat, je k dispozici pouze na vašem webu. Microsoft neudržuje jeho kopii v Azure a nemá ke klíči žádný přístup. Pokud klíč vložíte nesprávně, Microsoft nemůže obnovit zálohovaná data.

## <a name="next-steps"></a>Další kroky

Přečtěte si další Nejčastější dotazy:

- [Běžné dotazy](backup-azure-vm-backup-faq.md) k zálohování virtuálních počítačů Azure.
- [Běžné otázky](backup-azure-file-folder-backup-faq.md) týkající se agenta Azure Backup
