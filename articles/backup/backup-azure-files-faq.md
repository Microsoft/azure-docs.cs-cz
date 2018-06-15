---
title: Nejčastější dotazy k zálohování Souborů Azure
description: Tento článek obsahuje podrobnosti o ochraně sdílených složek Azure.
services: backup
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: f6dcaee1ac328a9bafce0561f421b772b7e1d119
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605164"
---
# <a name="questions-about-backing-up-azure-files"></a>Dotazy týkající se zálohování Souborů Azure
V tomto článku najdete odpovědi na běžné dotazy týkající se zálohování Souborů Azure. Některé odpovědi zahrnují odkazy na články obsahující komplexní informace. Otázky týkající se služby Azure Backup můžete také publikovat na [diskusním fóru](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

K rychlému procházení částmi tohoto článku použijte odkazy vpravo v části **V tomto článku**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurace úlohy zálohování pro Soubory Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares-br"></a>Proč se nezobrazují některé účty úložiště, které chci chránit a které obsahují platné sdílené složky Azure? <br/>
Během období Preview nepodporuje zálohování sdílených složek Azure všechny typy účtů úložiště. Seznam podporovaných účtů úložiště najdete [tady](troubleshoot-azure-files.md#preview-boundaries). Je taky možné, že účet úložiště, který hledáte, se už chrání nebo je zaregistrovaný v jiném trezoru. Pokud chcete účet úložiště najít v jiných trezorech, abyste ho mohli chránit, [zrušte registraci](troubleshoot-azure-files.md#configuring-backup) v trezoru.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>Proč se nezobrazují některé sdílené složky Azure v účtu úložiště, když se pokouším nakonfigurovat zálohování? <br/>
Zkontrolujte, jestli už příslušná sdílená složka Azure není chráněná ve stejném trezoru služby Recovery Services nebo jestli nebyla nedávno odstraněna.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>Můžu chránit sdílené složky připojené ke skupině synchronizace ve službě Azure File Sync? <br/>
Ano. Ochrana sdílených složek Azure připojených ke skupinám synchronizace je povolená a ve verzi Preview.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Při pokusu o zálohování sdílených složek jsem kliknul/a na účet úložiště, abych zjistil/a, které sdílené složky obsahuje. Nenastavil/a jsem však jejich ochranu. Jak nastavím ochranu těchto sdílených složek pomocí jiného trezoru?
Při pokusu o zálohování se výběrem účtu úložiště kvůli zjištění, které sdílené složky obsahuje, zaregistruje účet úložiště do trezoru, ze kterého se tato akce provede. Pokud se rozhodnete chránit sdílené složky pomocí jiného trezoru, [zrušte registraci](troubleshoot-azure-files.md#configuring-backup) zvoleného účtu úložiště v tomto trezoru.

### <a name="can-i-change-the-vault-to-which-i-backup-my-file-shares"></a>Můžu změnit trezor, do kterého zálohuji své sdílené složky?
Ano. Budete však muset [zastavit ochranu](backup-azure-files.md#stop-protecting-an-azure-file-share) v připojeném trezoru, [zrušit registraci](troubleshoot-azure-files.md#configuring-backup) tohoto účtu úložiště a pak nastavit ochranu v jiném trezoru.

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>Ve kterých zeměpisných oblastech můžu zálohovat sdílené složky Azure? <br/>
Zálohování sdílených složek Azure je aktuálně ve verzi Preview a je k dispozici pouze v následujících zeměpisných oblastech: 
-   Austrálie – jihovýchod (ASE) 
- Brazílie – jih (BRS)
- Kanada – střed (CNC)
-   Kanada – východ (CE)
-   USA – střed (CUS)
-   Východní Asie (EA)
-   Východní Austrálie (AE) 
-   USA – východ (EUS)
-   USA – východ 2 (EUS2)
- Japonsko – východ (JPE)
- Japonsko – západ (JPW)
-   Indie – střed (INC) 
- Indie – jih (INS)
- Korea – střed (KRC)
- Korea – jih (KRS)
-   Střed USA – sever (NCUS) 
-   Severní Evropa (NE) 
-   Střed USA – jih (SCUS) 
-   Jihovýchodní Asie (SEA)
-   Velká Británie – jih (UKS) 
-   Velká Británie – západ (UKW) 
-   Západní Evropa (WE) 
-   USA – západ (WUS)
-   Střed USA – západ (WCUS)
-   USA – západ 2 (WUS 2)

Pokud potřebujete zálohování použít v konkrétní zeměpisné oblasti, která není uvedená výše, obraťte se na e-mail [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com).

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vaultbr"></a>Kolik sdílených složek Azure můžu chránit v jednom trezoru?<br/>
Během období Preview můžete v jednom trezoru chránit sdílené složky Azure až z 25 účtů úložiště. Zároveň můžete v jednom trezoru chránit až 200 sdílených složek Azure.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Můžu chránit dvě různé sdílené složky ze stejného účtu úložiště v jiných trezorech?
Ne. Všechny sdílené složky v účtu úložiště je možné chránit pouze ve stejném trezoru.

## <a name="backup"></a>Backup

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>Kolik záloh na vyžádání jedné sdílené složky můžu vytvořit? <br/>
V jakémkoli okamžiku můžete mít až 200 snímků jedné sdílené složky. Toto omezení zahrnuje snímky pořízené službou Azure Backup podle definice ve vašich zásadách. Pokud po dosažení omezení začne zálohování selhávat, zajistěte úspěch budoucích zálohování odstraněním bodů obnovení na vyžádání.

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>Po povolení virtuálních sítí v účtu úložiště začalo při zálohování sdílených složek v tomto účtu docházet k chybám. Proč?
Zálohování sdílených složek Azure nepodporuje účty úložiště s povolenými virtuálními sítěmi. Zakažte v účtech úložiště virtuální sítě, abyste umožnili úspěšné zálohování. 

## <a name="restore"></a>Obnovení

### <a name="can-i-recover-from-a-deleted-azure-file-share-br"></a>Můžu obnovit odstraněnou sdílenou složku Azure? <br/>
Při odstraňování sdílené složky Azure se zobrazí seznam záloh, které se odstraní, a výzva k potvrzení. Odstraněnou sdílenou složku Azure není možné obnovit.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share-br"></a>Můžu provést obnovení ze zálohy po zastavení ochrany sdílené složky Azure? <br/>
Ano. Pokud jste při zastavování ochrany zvolili možnost **Zachovat zálohovaná data**, můžete provést obnovení ze všech stávajících bodů obnovení.

## <a name="manage-backup"></a>Správa zálohování

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>Můžu získat přístup ke snímkům pořízeným službou Azure Backup a připojit je? <br/>
Přístup ke všem snímkům pořízeným službou Azure Backup je možný přes zobrazení snímků na portálu, v PowerShellu nebo v rozhraní příkazového řádku. Další informace o snímcích sdílených složek Azure najdete v tématu [Přehled snímků sdílených složek u souborů Azure (Preview)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>Jakou maximální dobu uchovávání záloh můžu nakonfigurovat? <br/>
Zálohování sdílených složek Azure nabízí možnost uchovávat denní zálohy po dobu až 120 dnů.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share-br"></a>Co se stane, když u sdílené složky Azure změním zásady zálohování? <br/>
Pokud se pro sdílené složky použije nová zásada, plán a uchovávání se budou řídit touto novou zásadou. Pokud se doba uchovávání prodlouží, existující body obnovení se označí k zachování pro novou zásadu. Pokud se doba uchovávání zkrátí, označí se k vyřazení v rámci další úlohy čištění a budou odstraněny.

## <a name="see-also"></a>Viz také
Tyto informace se týkají pouze zálohování Souborů Azure. Další informace o dalších oblastech služby Azure Backup najdete v některém z těchto témat s nejčastějšími dotazy ke službě Backup:
-  [Nejčastější dotazy k trezoru služby Recovery Services](backup-azure-backup-faq.md)
-  [Nejčastější dotazy k zálohování virtuálních počítačů Azure](backup-azure-vm-backup-faq.md)
-  [Nejčastější dotazy k agentovi Azure Backup](backup-azure-file-folder-backup-faq.md)
