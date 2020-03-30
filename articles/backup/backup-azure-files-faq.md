---
title: Nejčastější dotazy k zálohování Souborů Azure
description: V tomto článku se dozvíte odpovědi na běžné otázky o tom, jak chránit sdílené složky Azure se službou Azure Backup.
ms.date: 07/29/2019
ms.topic: conceptual
ms.openlocfilehash: c69d4642aefbd599d3783dcdfa059a0cd9d129d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302538"
---
# <a name="questions-about-backing-up-azure-files"></a>Dotazy týkající se zálohování Souborů Azure

V tomto článku najdete odpovědi na běžné dotazy týkající se zálohování Souborů Azure. Některé odpovědi zahrnují odkazy na články obsahující komplexní informace. Dotazy k Azure Backup také můžete pokládat v [diskuzním fóru](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

K rychlému procházení částmi tohoto článku použijte odkazy vpravo v části **V tomto článku**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurace úlohy zálohování pro Soubory Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares"></a>Proč se nezobrazují některé účty úložiště, které chci chránit a které obsahují platné sdílené složky Azure?

Během období Preview nepodporuje zálohování sdílených složek Azure všechny typy účtů úložiště. Seznam podporovaných účtů úložiště najdete [tady](troubleshoot-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Je taky možné, že účet úložiště, který hledáte, se už chrání nebo je zaregistrovaný v jiném trezoru. Pokud chcete účet úložiště najít v jiných trezorech, abyste ho mohli chránit, [zrušte registraci](troubleshoot-azure-files.md#configuring-backup) v trezoru.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Proč se nezobrazují některé sdílené složky Azure v účtu úložiště, když se pokouším nakonfigurovat zálohování?

Zkontrolujte, jestli už příslušná sdílená složka Azure není chráněná ve stejném trezoru služby Recovery Services nebo jestli nebyla nedávno odstraněna.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Můžu chránit sdílené složky připojené ke skupině synchronizace ve službě Azure File Sync?

Ano. Ochrana sdílených složek Azure připojených ke skupinám synchronizace je povolená a ve verzi Preview.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Při pokusu o zálohování sdílených složek jsem kliknul/a na účet úložiště, abych zjistil/a, které sdílené složky obsahuje. Nenastavil/a jsem však jejich ochranu. Jak nastavím ochranu těchto sdílených složek pomocí jiného trezoru?

Při pokusu o zálohování se výběrem účtu úložiště kvůli zjištění, které sdílené složky obsahuje, zaregistruje účet úložiště do trezoru, ze kterého se tato akce provede. Pokud se rozhodnete chránit sdílené složky pomocí jiného trezoru, [zrušte registraci](troubleshoot-azure-files.md#configuring-backup) zvoleného účtu úložiště v tomto trezoru.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Mohu změnit trezor, do kterého zálohovat sdílené složky?

Ano. Budete však muset [zastavit ochranu ve sdílené složce](manage-afs-backup.md#stop-protection-on-a-file-share) z připojeného úložiště, [zrušit registraci](troubleshoot-azure-files.md#configuring-backup) tohoto účtu úložiště a pak jej chránit před jiným trezorem.

### <a name="in-which-geos-can-i-back-up-azure-file-shares"></a>Ve kterých geografických službách můžu zálohovat sdílené složky Azure?

Zálohování sdílených složek Azure je aktuálně ve verzi Preview a je k dispozici pouze v následujících zeměpisných oblastech:

- Austrálie – východ (AE)
- Austrálie – jihovýchod (ASE)
- Brazílie – jih (BRS)
- Kanada – střed (CNC)
- Kanada – východ (CE)
- USA – střed (CUS)
- Východní Asie (EA)
- USA – východ (EUS)
- USA – východ 2 (EUS2)
- Japonsko – východ (JPE)
- Japonsko – západ (JPW)
- Indie – střed (INC)
- Indie – jih (INS)
- Jižní Korea – střed (KRC)
- Jižní Korea – jih (KRS)
- USA – středosever (NCUS)
- Severní Evropa (NE)
- USA – středojih (SCUS)
- Jihovýchodní Asie (SEA)
- Velká Británie – jih (UKS)
- Velká Británie – západ (UKW)
- Západní Evropa (WE)
- USA – západ (WUS)
- USA – středozápad (WCUS)
- USA – západ 2 (WUS 2)
- Us Gov Arizona (UGA)
- Us Gov Texas (UGT)
- Americká vláda Virginie (UGV)
- Austrálie Centrální (ACL)
- Indie Západ (INW)
- Jižní Afrika – sever(SAN)
- SAE Sever (UAN)
- Francie – střed (FRC)
- Německo – sever (GN)                       
- Německo – západ –střed (GWC)
- Jižní Afrika Západ (SAW)
- Centrální SAE (UAC)
- NWE (Norsko –východ)     
- NWW (Norsko – západ)
- SZN (Sever Švýcarska)

Zapište, [AskAzureBackupTeam@microsoft.com](mailto:askazurebackupteam@microsoft.com) pokud potřebujete použít v konkrétní geo, která není uvedena výše.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>Kolik sdílených složek Azure můžu chránit v jednom trezoru?

Během období Preview můžete v jednom trezoru chránit sdílené složky Azure až z 50 účtů úložiště. Zároveň můžete v jednom trezoru chránit až 200 sdílených složek Azure.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Můžu chránit dvě různé sdílené složky ze stejného účtu úložiště v jiných trezorech?

Ne. Všechny sdílené složky v účtu úložiště je možné chránit pouze ve stejném trezoru.

## <a name="backup"></a>Zálohování

### <a name="how-many-scheduled-backups-can-i-configure-per-file-share"></a>Kolik naplánovaných záloh lze nakonfigurovat na složku sdílené složky?

Azure Backup v současné době podporuje konfiguraci naplánované honosné zálohování sdílených složek Azure jednou denně.

### <a name="how-many-on-demand-backups-can-i-take-per-file-share"></a>Kolik záloh na vyžádání jedné sdílené složky můžu vytvořit?

V jakémkoli okamžiku můžete mít až 200 snímků jedné sdílené složky. Toto omezení zahrnuje snímky pořízené službou Azure Backup podle definice ve vašich zásadách. Pokud po dosažení omezení začne zálohování selhávat, zajistěte úspěch budoucích zálohování odstraněním bodů obnovení na vyžádání.

## <a name="restore"></a>Obnovení

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Můžu obnovit odstraněnou sdílenou složku Azure?

Při odstraňování sdílené složky Azure se zobrazí seznam záloh, které se odstraní, a výzva k potvrzení. Odstraněnou sdílenou složku Azure není možné obnovit.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Můžu provést obnovení ze zálohy po zastavení ochrany sdílené složky Azure?

Ano. Pokud jste při zastavování ochrany zvolili možnost **Zachovat zálohovaná data**, můžete provést obnovení ze všech stávajících bodů obnovení.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Co se stane, když zruším probíhající úlohu obnovení?

Pokud je úloha průběžného obnovení zrušena, proces obnovení se zastaví a všechny soubory budou obnoveny před zrušením, zůstane v nakonfigurovaném cíli (původní nebo alternativní umístění) bez vrácení zpět.

## <a name="manage-backup"></a>Správa zálohování

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Můžu použít PowerShell ke konfiguraci nebo správě a obnovení záloh sdílených složek Azure?

Ano. Naleznete v podrobné dokumentaci [zde](backup-azure-afs-automation.md)

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it"></a>Můžu získat přístup ke snímkům pořízeným službou Azure Backup a připojit je?

Přístup ke všem snímkům pořízeným službou Azure Backup je možný přes zobrazení snímků na portálu, v PowerShellu nebo v rozhraní příkazového řádku. Další informace o snímcích sdílených složek Azure najdete v tématu [Přehled snímků sdílených složek u souborů Azure (Preview)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Jaká je maximální uchování, kterou lze nakonfigurovat pro zálohování?

Zálohování pro sdílené složky Azure nabízí možnost konfigurovat zásady s uchováváním až 180 dní. Pomocí [možnosti "Zálohování na vyžádání" v prostředí PowerShell](backup-azure-afs-automation.md#trigger-an-on-demand-backup)však můžete zachovat bod obnovení i po dobu 10 let.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Co se stane, když u sdílené složky Azure změním zásady zálohování?

Pokud se pro sdílené složky použije nová zásada, plán a uchovávání se budou řídit touto novou zásadou. Pokud se doba uchovávání prodlouží, existující body obnovení se označí k zachování pro novou zásadu. Pokud se doba uchovávání zkrátí, označí se k vyřazení v rámci další úlohy čištění a budou odstraněny.

## <a name="next-steps"></a>Další kroky

Další informace o dalších oblastech služby Azure Backup najdete v následujících nejčastějších dotazech k zálohování:

- [Nejčastější dotazy k trezoru služby Recovery Services](backup-azure-backup-faq.md)
- [Nejčastější dotazy k zálohování virtuálních počítačů Azure](backup-azure-vm-backup-faq.md)
- [Nejčastější dotazy k agentovi Azure Backup](backup-azure-file-folder-backup-faq.md)
