---
title: "Nejčastější dotazy k zálohování Souborů Azure"
description: "Tento článek obsahuje podrobnosti o ochraně Souborů Azure v Azure. Tento souhrn se zobrazí ve výsledcích vyhledávání."
services: backup
keywords: "Nepřidávejte ani neupravujte klíčová slova, aniž byste se poradili se svým odborníkem na SEO."
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: 3d09914c93d0f48b8f6bed405202682aaf925a5f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="questions-about-backing-up-azure-files"></a>Dotazy týkající se zálohování Souborů Azure
V tomto článku najdete odpovědi na běžné dotazy týkající se zálohování Souborů Azure. Některé odpovědi zahrnují odkazy na články obsahující komplexní informace. Otázky týkající se služby Azure Backup můžete také publikovat na [diskusním fóru](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

K rychlému procházení částmi tohoto článku použijte odkazy vpravo v části **V tomto článku**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurace úlohy zálohování pro Soubory Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-file-shares-br"></a>Proč se nezobrazují některé účty úložiště, které chci chránit a které obsahují platné sdílené složky? <br/>
Během období Preview zálohování sdílených složek Azure nepodporuje všechny typy účtů úložiště. Seznam podporovaných účtů úložiště najdete [tady](troubleshoot-azure-files.md#preview-boundaries).

### <a name="why-cant-i-see-some-of-my-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>Proč se nezobrazují některé sdílené složky v účtu úložiště, když se pokouším nakonfigurovat zálohování? <br/>
Zkontrolujte, jestli daná sdílená složka již není chráněná ve stejném trezoru služby Recovery Services nebo jestli nebyla nedávno odstraněná.

### <a name="why-cant-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>Proč nemůžu chránit sdílené složky připojené ke skupině synchronizace ve službě Azure File Sync? <br/>
Ochrana sdílených složek Azure připojených ke skupinám synchronizace je ve verzi Limited Preview. Pokud chcete požádat o přístup, obraťte se na e-mail [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) a uveďte ID vašeho předplatného. 

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
-   Indie – střed (INC) 
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

### <a name="how-many-file-shares-can-i-protect-in-a-vaultbr"></a>Kolik sdílených složek můžu chránit v jednom trezoru?<br/>
Během období Preview můžete v jednom trezoru chránit sdílené složky až z 25 účtů úložiště. Zároveň můžete v jednom trezoru chránit až 200 sdílených složek. 

## <a name="backup"></a>Backup

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>Kolik záloh na vyžádání jedné sdílené složky můžu vytvořit? <br/>
V jakémkoli okamžiku můžete mít až 200 snímků jedné sdílené složky. Toto omezení zahrnuje snímky pořízené službou Azure Backup podle definice ve vašich zásadách. Pokud po dosažení omezení začne zálohování selhávat, zajistěte úspěch budoucích zálohování odstraněním bodů obnovení na vyžádání.

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>Po povolení virtuálních sítí v účtu úložiště začalo při zálohování sdílených složek v tomto účtu docházet k chybám. Proč?
Zálohování sdílených složek Azure nepodporuje účty úložiště s povolenými virtuálními sítěmi. Zakažte v účtech úložiště virtuální sítě, abyste umožnili úspěšné zálohování. 

## <a name="restore"></a>Obnovení

### <a name="can-i-recover-from-a-deleted-file-share-br"></a>Můžu obnovit odstraněnou sdílenou složku? <br/>
Při odstraňování sdílené složky se zobrazí seznam záloh, které se také odstraní, a výzva k potvrzení. Odstraněnou sdílenou složku není možné obnovit.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-a-file-share-br"></a>Můžu provést obnovení ze zálohy po zastavení ochrany sdílené složky? <br/>
Ano. Pokud jste při zastavování ochrany zvolili možnost **Zachovat zálohovaná data**, můžete provést obnovení ze všech stávajících bodů obnovení.

## <a name="manage-backup"></a>Správa zálohování

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>Můžu získat přístup ke snímkům pořízeným službou Azure Backup a připojit je? <br/>
Přístup ke všem snímkům pořízeným službou Azure Backup je možný přes zobrazení snímků na portálu, v PowerShellu nebo v rozhraní příkazového řádku. Připojit je můžete podle [tohoto](../storage/files/storage-how-to-use-files-snapshots.md#mount-a-file-share) postupu.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>Jakou maximální dobu uchovávání záloh můžu nakonfigurovat? <br/>
Zálohování sdílených složek Azure nabízí možnost uchovávat denní zálohy po dobu až 120 dnů.

### <a name="what-happens-when-i-change-the-backup-policy-for-a-file-share-br"></a>Co se stane, když že se pro sdílenou složku změní zásady zálohování? <br/>
Pokud se pro sdílené složky použije nová zásada, plán a uchovávání se budou řídit touto novou zásadou. Pokud se doba uchovávání prodlouží, existující body obnovení se označí k zachování pro novou zásadu. Pokud se doba uchovávání zkrátí, označí se k vyřazení v rámci další úlohy čištění a následně se odstraní.


## <a name="see-also"></a>Viz také
Tyto informace se týkají pouze zálohování Souborů Azure. Další informace o dalších oblastech služby Azure Backup najdete v některém z těchto témat s nejčastějšími dotazy ke službě Backup:
-  [Nejčastější dotazy k trezoru služby Recovery Services](backup-azure-backup-faq.md)
-  [Nejčastější dotazy k zálohování virtuálních počítačů Azure](backup-azure-vm-backup-faq.md)
-  [Nejčastější dotazy k agentovi Azure Backup](backup-azure-file-folder-backup-faq.md)
