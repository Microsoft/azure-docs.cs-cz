---
title: Nejčastější dotazy k zálohování Souborů Azure
description: V tomto článku najdete odpovědi na běžné otázky týkající se ochrany sdílených složek Azure pomocí služby Azure Backup.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: c62f8376b220911edd26edbe18955d0103440b81
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89377416"
---
# <a name="questions-about-backing-up-azure-files"></a>Dotazy týkající se zálohování Souborů Azure

V tomto článku najdete odpovědi na běžné dotazy týkající se zálohování Souborů Azure. Některé odpovědi zahrnují odkazy na články obsahující komplexní informace. Dotazy týkající se Azure Backup služby můžete také publikovat na stránce s [dotazem Microsoft Q&pro diskuzi](/answers/topics/azure-backup.html).

K rychlému procházení částmi tohoto článku použijte odkazy vpravo v části **V tomto článku**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurace úlohy zálohování pro Soubory Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Proč se mi nezobrazují některé účty úložiště, které chci chránit a které obsahují platné sdílené složky Azure?

Pokud chcete zajistit, aby účet úložiště patřil do jednoho z podporovaných typů účtů úložiště, přečtěte si část [Podpora pro zálohování sdílených složek Azure](azure-file-share-support-matrix.md) . Je také možné, že účet úložiště, který hledáte, je již chráněný nebo zaregistrovaný v jiném trezoru. [Zrušením registrace účtu úložiště](manage-afs-backup.md#unregister-a-storage-account) z trezoru zjistíte účet úložiště v jiných trezorech pro ochranu.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Proč se nezobrazují některé sdílené složky Azure v účtu úložiště, když se pokouším nakonfigurovat zálohování?

Zkontrolujte, jestli už příslušná sdílená složka Azure není chráněná ve stejném trezoru služby Recovery Services nebo jestli nebyla nedávno odstraněna.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Můžu chránit sdílené složky připojené ke skupině synchronizace ve službě Azure File Sync?

Yes. Ochrana sdílených složek Azure připojených ke skupinám synchronizace je povolená.

### <a name="when-trying-to-back-up-file-shares-i-selected-a-storage-account-to-discover-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Při pokusu o zálohování sdílených složek jsem vybrali účet úložiště, ve kterém zjistíte sdílené složky. Nechrání se však. Návody chránit tyto sdílené složky pomocí jiného trezoru?

Při pokusu o zálohování vyberte účet úložiště, ve kterém se budou objevovat sdílené složky. zaregistruje účet úložiště s trezorem, ze kterého se tato akce provádí. Pokud se rozhodnete chránit sdílené složky pomocí jiného trezoru, [zrušte registraci](manage-afs-backup.md#unregister-a-storage-account) zvoleného účtu úložiště z tohoto trezoru.

### <a name="why-cant-i-change-the-vault-to-configure-backup-for-the-file-share"></a>Proč nemohu změnit trezor, aby se nakonfigurovala záloha pro sdílenou složku?

Pokud je účet úložiště už zaregistrovaný v trezoru nebo jiné sdílené složky v účtu úložiště jsou chráněné pomocí trezoru, nebudete mít možnost ho změnit. Všechny sdílené složky v účtu úložiště je možné chránit jenom pomocí stejného trezoru. Pokud chcete změnit trezor, budete muset [Zastavit ochranu všech sdílených složek v účtu úložiště](manage-afs-backup.md#stop-protection-on-a-file-share) z připojeného trezoru, [zrušit registraci](manage-afs-backup.md#unregister-a-storage-account) účtu úložiště a pak zvolit jiný trezor pro ochranu.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Můžu změnit trezor, do kterého zálohujte své sdílené složky?

Yes. Z připojeného trezoru ale budete muset [Zastavit ochranu sdílené složky](manage-afs-backup.md#stop-protection-on-a-file-share) , zrušit [registraci](manage-afs-backup.md#unregister-a-storage-account) tohoto účtu úložiště a pak ho chránit z jiného trezoru.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Můžu chránit dvě různé sdílené složky ze stejného účtu úložiště v jiných trezorech?

No. Všechny sdílené složky v účtu úložiště je možné chránit pouze ve stejném trezoru.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>Co mám dělat, když se moje zálohy začnou zdařit kvůli překročení maximálního limitu?

V jakémkoli okamžiku můžete mít až 200 snímků jedné sdílené složky. Toto omezení zahrnuje snímky pořízené službou Azure Backup podle definice ve vašich zásadách. Pokud zálohování po dosažení limitu selže, odstraňte snímky na vyžádání pro úspěšné budoucí zálohy.

## <a name="restore"></a>Obnovení

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Můžu obnovit odstraněnou sdílenou složku Azure?

Pokud je sdílená složka ve stavu tichého odstranění, je třeba nejprve zrušit odstranění sdílené složky, aby se operace obnovení mohla provést. Operace zrušení odstranění zařadí sdílenou složku do aktivního stavu, ve kterém se můžete kdykoli vrátit k libovolnému bodu v čase. Informace o tom, jak zrušit odstranění sdílené složky, najdete na [tomto odkazu](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) nebo v tématu [zrušení odstranění sdílení souborů](./scripts/backup-powershell-script-undelete-file-share.md). Pokud je sdílená složka trvale smazána, nebudete moci obnovit obsah a snímky.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Můžu provést obnovení ze zálohy po zastavení ochrany sdílené složky Azure?

Yes. Pokud jste při zastavování ochrany zvolili možnost **Zachovat zálohovaná data**, můžete provést obnovení ze všech stávajících bodů obnovení.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Co se stane, když zruším probíhající úlohu obnovení?

Pokud je probíhající úloha obnovení zrušena, proces obnovení se zastaví a všechny soubory obnovené před zrušením zůstávají v nakonfigurovaném cíli (původní nebo alternativní umístění) bez vrácení zpět.

## <a name="manage-backup"></a>Správa zálohování

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Můžu ke konfiguraci/správě a obnovení záloh sdílených složek Azure použít PowerShell?

Yes. Informace [najdete v podrobné dokumentaci.](backup-azure-afs-automation.md)

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Můžu získat přístup k snímkům pořízeným službou Azure Backup a připojit je?

Ke všem snímkům pořízeným pomocí Azure Backup lze přihlédnout zobrazením snímků na portálu, PowerShellu nebo rozhraní příkazového řádku. Další informace o snímcích sdílených složek Azure najdete v tématu [Přehled snímků sdílených složek pro soubory Azure](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Jaká je maximální doba uchování, kterou je možné nakonfigurovat pro zálohování?

Podrobnosti o maximálním uchování najdete v části [support Matrix](azure-file-share-support-matrix.md) . Azure Backup provádí výpočet počtu snímků v reálném čase, když při konfiguraci zásad zálohování zadáte hodnoty uchování. Jakmile počet snímků odpovídajících definovaným hodnotám uchování překročí 200, zobrazí se na portálu upozornění požadující úpravu hodnot uchování. To je proto, že nepřekračuje limit maximálního počtu snímků, které soubory Azure podporuje pro každou sdílenou složku v jakémkoli okamžiku.

### <a name="what-is-the-impact-on-existing-recovery-points-and-snapshots-when-i-modify-the-backup-policy-for-an-azure-file-share-to-switch-from-daily-policy-to-gfs-policy"></a>Jaký je dopad na existující body obnovení a snímky, když změní zásady zálohování pro sdílenou složku Azure tak, aby přešly ze "každodenních zásad" na "zásady GFS"?

Když upravíte zásady denního zálohování na zásady GFS (přidání týdně/měsíčně/ročního uchování), chování je následující:

- **Uchovávání**: Pokud přidáváte jako součást změny zásad týdenní/měsíční/roční uchování, všechny budoucí body obnovení vytvořené jako součást plánovaného zálohování budou označeny podle nových zásad. Všechny existující body obnovení budou stále považovány za denní body obnovení, a proto nebudou označeny jako týdně a měsíčně/za rok.

- **Vyčištění snímků a bodů obnovení**:

  - Pokud je denní uchovávání prodlouženo, datum vypršení platnosti stávajících bodů obnovení se aktualizuje podle denní hodnoty uchování nakonfigurované v nových zásadách.
  - Pokud se denní uchovávání sníží, existující body obnovení a snímky jsou označené k odstranění v další úloze spuštění čištění podle hodnoty denního uchování nakonfigurovaného v nové zásadě a pak se odstraní.

Tady je příklad toho, jak to funguje:

#### <a name="existing-policy-p1"></a>Existující zásady [P1]

|Typ uchování |Plán |Uchovávání  |
|---------|---------|---------|
|Každý den    |    Každý den v 8 ODP.    |  100 dní       |

#### <a name="new-policy-modified-p1"></a>Nové zásady [upraveno P1]

| Typ uchování | Plán                       | Uchovávání |
| -------------- | ------------------------------ | --------- |
| Každý den          | Každý den v 9 ODP.              | 50 dní   |
| Každý týden         | V neděli v 9 ODP.              | 3 týdny   |
| Měsíčně        | Poslední pondělí v 9 ODP.         | 1 měsíc   |
| Ročně         | V lednu na třetí neděli v 9 ODP. | 4 roky   |

#### <a name="impact"></a>Dopad

1. Datum vypršení platnosti stávajících bodů obnovení se upraví podle hodnoty denního uchování nové zásady: to znamená 50 dnů. Takže všechny body obnovení, které jsou starší než 50 dní, budou označeny k odstranění.

2. Existující body obnovení nebudou v závislosti na nových zásadách označeny jako týdenní/měsíční/roční.

3. Všechna budoucí zálohování se aktivují podle nového plánu: to znamená 9 ODP.

4. Datum vypršení platnosti všech budoucích bodů obnovení bude zarovnáno s novou zásadou.

>[!NOTE]
>Změny zásad budou mít vliv pouze na body obnovení vytvořené v rámci spuštění naplánované úlohy zálohování. V případě zálohování na vyžádání je doba uchování určena hodnotou **zachovat** pozici určenou v době pořízení zálohy.

### <a name="what-is-the-impact-on-existing-recovery-points-when-i-modify-an-existing-gfs-policy"></a>Jaký je dopad na existující body obnovení při úpravách existujících zásad GFS?

Když se u sdílených složek použije nová zásada, všechna budoucí naplánovaná zálohování se budou považovat podle plánu nakonfigurovaného ve upravených zásadách.  Uchovávání všech existujících bodů obnovení je zarovnáno podle nových nakonfigurovaných hodnot pro uchování. Takže pokud je zachování rozšířené, existující body obnovení jsou označeny tak, aby byly v souladu s novou zásadou. Pokud se uchování zmenší, označí se pro vyčištění v další úloze čištění a pak se odstraní.

Tady je příklad toho, jak to funguje:

#### <a name="existing-policy-p2"></a>Existující zásada [P2]

| Typ uchování | Plán           | Uchovávání |
| -------------- | ------------------ | --------- |
| Každý den          | Každý den v 8 ODP. | 50 dní   |
| Každý týden         | V pondělí po 8 ODP.  | 3 týdny   |

#### <a name="new-policy-modified-p2"></a>Nové zásady [upraveno P2]

| Typ uchování | Plán               | Uchovávání |
| -------------- | ---------------------- | --------- |
| Každý den          | Každý den v 9 ODP.     | 10 dní   |
| Každý týden         | V pondělí v 9 ODP.      | 2 týdny   |
| Měsíčně        | Poslední pondělí v 9 ODP. | 2 měsíce  |

#### <a name="impact-of-change"></a>Dopad změny

1. Datum vypršení platnosti existujících denních bodů obnovení bude zarovnáno podle nové hodnoty denního uchování, což je 10 dní. Proto se odstraní každý denní bod obnovení, který je starší než 10 dní.

2. Datum vypršení platnosti stávajících týdenních bodů obnovení bude zarovnáno podle nové týdenní hodnoty uchovávání, která je dva týdny. Proto se odstraní každý týdenní bod obnovení, který je starší než dva týdny.

3. Měsíční body obnovení budou vytvořeny pouze jako součást budoucích záloh na základě nové konfigurace zásad.

4. Datum vypršení platnosti všech budoucích bodů obnovení bude zarovnáno s novou zásadou.

>[!NOTE]
>Změny zásad budou mít vliv pouze na body obnovení vytvořené v rámci plánovaného zálohování. V případě zálohování na vyžádání se uchování určuje podle hodnoty **zachovat** do, která je určená v době pořízení zálohy.

## <a name="next-steps"></a>Další kroky

- [Řešení potíží při zálohování sdílených složek Azure](troubleshoot-azure-files.md)
