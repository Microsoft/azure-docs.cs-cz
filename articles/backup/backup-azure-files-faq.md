---
title: Nejčastější dotazy k zálohování Souborů Azure
description: V tomto článku najdete odpovědi na běžné otázky týkající se ochrany sdílených složek Azure pomocí služby Azure Backup.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: d7b19fd11e6784a188a18f6a613eef5ff4f77764
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82105637"
---
# <a name="questions-about-backing-up-azure-files"></a>Dotazy týkající se zálohování Souborů Azure

V tomto článku najdete odpovědi na běžné dotazy týkající se zálohování Souborů Azure. Některé odpovědi zahrnují odkazy na články obsahující komplexní informace. Dotazy k Azure Backup také můžete pokládat v [diskuzním fóru](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

K rychlému procházení částmi tohoto článku použijte odkazy vpravo v části **V tomto článku**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurace úlohy zálohování pro Soubory Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Proč se mi nezobrazují některé účty úložiště, které chci chránit a které obsahují platné sdílené složky Azure?

Pokud chcete zajistit, aby účet úložiště patřil do jednoho z podporovaných typů účtů úložiště, přečtěte si část [Podpora pro zálohování sdílených složek Azure](azure-file-share-support-matrix.md) . Je také možné, že účet úložiště, který hledáte, je již chráněný nebo zaregistrovaný v jiném trezoru. [Zrušením registrace účtu úložiště](manage-afs-backup.md#unregister-a-storage-account) z trezoru zjistíte účet úložiště v jiných trezorech pro ochranu.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Proč se nezobrazují některé sdílené složky Azure v účtu úložiště, když se pokouším nakonfigurovat zálohování?

Zkontrolujte, jestli už příslušná sdílená složka Azure není chráněná ve stejném trezoru služby Recovery Services nebo jestli nebyla nedávno odstraněna.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Můžu chránit sdílené složky připojené ke skupině synchronizace ve službě Azure File Sync?

Ano. Ochrana sdílených složek Azure připojených ke skupinám synchronizace je povolená.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Při pokusu o zálohování sdílených složek jsem kliknul/a na účet úložiště, abych zjistil/a, které sdílené složky obsahuje. Nechrání se však. Návody chránit tyto sdílené složky pomocí jiného trezoru?

Při pokusu o zálohování vyberte účet úložiště, ve kterém se budou objevovat sdílené složky. zaregistruje účet úložiště s trezorem, ze kterého se tato akce provádí. Pokud se rozhodnete chránit sdílené složky pomocí jiného trezoru, [zrušte registraci](manage-afs-backup.md#unregister-a-storage-account) zvoleného účtu úložiště z tohoto trezoru.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Můžu změnit trezor, do kterého zálohujte své sdílené složky?

Ano. Z připojeného trezoru ale budete muset [Zastavit ochranu sdílené složky](manage-afs-backup.md#stop-protection-on-a-file-share) , zrušit [registraci](manage-afs-backup.md#unregister-a-storage-account) tohoto účtu úložiště a pak ho chránit z jiného trezoru.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>Kolik sdílených složek Azure můžu chránit v jednom trezoru?

Sdílené složky Azure můžete chránit od až 50 účtů úložiště na jeden trezor. Zároveň můžete v jednom trezoru chránit až 200 sdílených složek Azure.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Můžu chránit dvě různé sdílené složky ze stejného účtu úložiště v jiných trezorech?

Ne. Všechny sdílené složky v účtu úložiště je možné chránit pouze ve stejném trezoru.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>Co mám dělat, když se moje zálohy začnou zdařit kvůli překročení maximálního limitu?

V jakémkoli okamžiku můžete mít až 200 snímků jedné sdílené složky. Toto omezení zahrnuje snímky pořízené službou Azure Backup podle definice ve vašich zásadách. Pokud zálohování po dosažení limitu selže, odstraňte snímky na vyžádání pro úspěšné budoucí zálohy.

## <a name="restore"></a>Obnovení

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Můžu obnovit odstraněnou sdílenou složku Azure?

Po odstranění sdílené složky Azure se zobrazí seznam záloh, které se odstraní, a žádost o potvrzení. V současné době nelze obnovit odstraněnou sdílenou složku Azure.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Můžu provést obnovení ze zálohy po zastavení ochrany sdílené složky Azure?

Ano. Pokud jste při zastavování ochrany zvolili možnost **Zachovat zálohovaná data**, můžete provést obnovení ze všech stávajících bodů obnovení.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Co se stane, když zruším probíhající úlohu obnovení?

Pokud je probíhající úloha obnovení zrušena, proces obnovení se zastaví a všechny soubory obnovené před zrušením zůstávají v nakonfigurovaném cíli (původní nebo alternativní umístění) bez vrácení zpět.

## <a name="manage-backup"></a>Správa zálohování

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Můžu ke konfiguraci/správě a obnovení záloh sdílených složek Azure použít PowerShell?

Ano. Informace [najdete v podrobné dokumentaci.](backup-azure-afs-automation.md)

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Můžu získat přístup k snímkům pořízeným službou Azure Backup a připojit je?

Ke všem snímkům pořízeným pomocí Azure Backup lze přihlédnout zobrazením snímků na portálu, PowerShellu nebo rozhraní příkazového řádku. Další informace o snímcích sdílených složek Azure najdete v tématu [Přehled snímků sdílených složek pro soubory Azure](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Jaká je maximální doba uchování, kterou je možné nakonfigurovat pro zálohování?

Podrobnosti o maximálním uchování najdete v části [support Matrix](azure-file-share-support-matrix.md) . Azure Backup provádí výpočet počtu snímků v reálném čase, když při konfiguraci zásad zálohování zadáte hodnoty uchování. Jakmile počet snímků odpovídajících definovaným hodnotám uchování překročí 200, zobrazí se na portálu upozornění požadující úpravu hodnot uchování. To je proto, že nepřekračuje limit maximálního počtu snímků, které soubory Azure podporuje pro každou sdílenou složku v jakémkoli okamžiku.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Co se stane, když u sdílené složky Azure změním zásady zálohování?

Pokud se pro sdílené složky použije nová zásada, plán a uchovávání se budou řídit touto novou zásadou. Pokud se doba uchovávání prodlouží, existující body obnovení se označí k zachování pro novou zásadu. Pokud se doba uchovávání zkrátí, označí se k vyřazení v rámci další úlohy čištění a budou odstraněny.

## <a name="next-steps"></a>Další kroky

Další informace o dalších oblastech Azure Backup najdete v některých dalších nejčastějších dotazech k zálohování:

- [Nejčastější dotazy k trezoru služby Recovery Services](backup-azure-backup-faq.md)
- [Nejčastější dotazy k zálohování virtuálních počítačů Azure](backup-azure-vm-backup-faq.md)
- [Nejčastější dotazy k agentovi Azure Backup](backup-azure-file-folder-backup-faq.md)
