---
title: Řešení potíží se zálohováním sdílených složek Azure
description: Tento článek obsahuje informace o řešení potíží, ke kterým dochází při ochraně sdílených složek Azure.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4c934d2295fa702425e8df0a03636b9f9208cfa4
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515069"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Řešení potíží při zálohování sdílených složek Azure

Tento článek obsahuje informace o řešení potíží, které řeší všechny problémy, které jste procházeli při konfiguraci zálohování nebo obnovení sdílených složek Azure pomocí služby Azure Backup.

## <a name="common-configuration-issues"></a>Běžné problémy s konfigurací

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Nepovedlo se najít účet úložiště pro konfiguraci zálohování sdílené složky Azure.

- Počkejte na dokončení zjišťování.
- Ověřte, jestli je už žádná sdílená složka v účtu úložiště chráněná jiným úložištěm Recovery Services.

  >[!NOTE]
  >Všechny sdílené složky v účtu úložiště je možné chránit jenom v rámci jednoho trezoru Recovery Services. Pomocí [tohoto skriptu](scripts/backup-powershell-script-find-recovery-services-vault.md) můžete najít Recovery Services trezor, ve kterém je váš účet úložiště zaregistrovaný.

- Ujistěte se, že sdílená složka není přítomna v žádném z nepodporovaných účtů úložiště. Podporované účty úložiště můžete najít v tématu [Podpora pro zálohování sdílených složek Azure](azure-file-share-support-matrix.md) .
- Zajistěte, aby celková délka názvu účtu úložiště a názvu skupiny prostředků překročila 84 znaků v případě nových účtů úložiště a 77 znaků v případě klasických účtů úložiště.
- Zkontrolujte nastavení brány firewall účtu úložiště, abyste měli jistotu, že je povolená možnost Povolit důvěryhodné služby Microsoftu přístup k účtu úložiště.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>Chyba na portálu hlásí, že zjišťování účtů úložiště selhalo

Pokud máte partnerské předplatné (s podporou CSP), ignorujte tuto chybu. Pokud vaše předplatné není povolené CSP a vaše účty úložiště nejde zjistit, obraťte se na podporu.

### <a name="selected-storage-account-validation-or-registration-failed"></a>Nepovedlo se ověřit nebo zaregistrovat vybraný účet úložiště.

Zkuste registraci zopakovat. Pokud se problém opakuje, obraťte se na podporu.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>Nepovedlo se vypsat nebo najít sdílené složky ve vybraném účtu úložiště.

- Ujistěte se, že účet úložiště existuje ve skupině prostředků a že se po posledním ověření nebo registraci v trezoru neodstranil ani nepřesunul.
- Zajistěte, aby sdílená složka, kterou chcete chránit, nebyla Odstraněná.
- Ujistěte se, že účet úložiště je pro zálohování sdílených složek podporovaný účet úložiště. Podporované účty úložiště můžete najít v tématu [Podpora pro zálohování sdílených složek Azure](azure-file-share-support-matrix.md) .
- Ověřte, jestli je sdílená složka už chráněná ve stejném trezoru Recovery Services.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>Konfigurace záložní sdílené složky (nebo konfigurace zásad ochrany) selhává

- Pokud potíže potrvají, zkontrolujte konfiguraci znovu.
- Zajistěte, aby se sdílená složka, kterou chcete chránit, neodstranila.
- Pokud se pokoušíte chránit více sdílených složek najednou a některé sdílené složky selžou, zkuste znovu nakonfigurovat zálohování pro neúspěšné sdílené složky.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Po zrušení ochrany sdílené složky nejde odstranit Recovery Services trezor.

V Azure Portal otevřete   >    >  **účty úložiště** infrastruktury zálohování trezoru. Vyberte zrušit **registraci** pro odebrání účtů úložiště z Recovery Services trezoru.

>[!NOTE]
>Trezor Recovery Services je možné odstranit až po zrušení registrace všech účtů úložiště zaregistrovaných v trezoru.

## <a name="common-backup-or-restore-errors"></a>Běžné chyby zálohování nebo obnovení

>[!NOTE]
>[Tento dokument](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) obsahuje informace o tom, abyste měli jistotu, že máte dostatečná oprávnění k provádění operací zálohování nebo obnovení.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound – operace se nezdařila, protože se nenašla sdílená složka.

Kód chyby: FileShareNotFound

Chybová zpráva: operace se nezdařila, protože se nenašla sdílená složka.

Zajistěte, aby se sdílená složka, kterou se pokoušíte chránit, neodstranila.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable – účet úložiště se nenašel nebo se nepodporuje.

Kód chyby: UserErrorFileShareEndpointUnreachable

Chybová zpráva: účet úložiště se nenašel nebo se nepodporuje.

- Ujistěte se, že účet úložiště existuje ve skupině prostředků a že se po posledním ověření neodstranil nebo neodebral ze skupiny prostředků.

- Ujistěte se, že účet úložiště je pro zálohování sdílených složek podporovaný účet úložiště.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached – dosáhli jste maximálního počtu snímků pro tuto sdílenou složku; po vypršení platnosti starších verzí bude možné provést další akce.

Kód chyby: AFSMaxSnapshotReached

Chybová zpráva: dosáhli jste maximálního počtu snímků pro tuto sdílenou složku. po vypršení platnosti starších verzí bude možné provést další akce.

- K této chybě může dojít, když vytvoříte více záloh na vyžádání pro sdílenou složku.
- Pro každou sdílenou složku je povolený limit 200 snímků, včetně souborů pořízených Azure Backup. Starší naplánované zálohy (nebo snímky) se čistí automaticky. Zálohy (nebo snímky) na vyžádání se musí odstranit, pokud dojde k dosažení maximálního omezení.

Odstraňte zálohy na vyžádání (snímky sdílené složky Azure) na portálu Soubory Azure.

>[!NOTE]
> Pokud odstraníte snímky vytvořené pomocí Azure Backup, ztratíte body obnovení.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound – operace se nezdařila, protože zadaný účet úložiště už neexistuje.

Kód chyby: UserErrorStorageAccountNotFound

Chybová zpráva: operace se nezdařila, protože zadaný účet úložiště už neexistuje.

Ujistěte se, že účet úložiště stále existuje a že není odstraněný.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound – zadané podrobnosti o účtu úložiště jsou nesprávné.

Kód chyby: UserErrorDTSStorageAccountNotFound

Chybová zpráva: zadané podrobnosti o účtu úložiště jsou nesprávné.

Ujistěte se, že účet úložiště stále existuje a že není odstraněný.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound – skupina prostředků neexistuje.

Kód chyby: UserErrorResourceGroupNotFound

Chybová zpráva: Skupina prostředků neexistuje.

Vyberte existující skupinu prostředků nebo vytvořte novou skupinu prostředků.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest – úloha zálohování už pro tuto sdílenou složku probíhá.

Kód chyby: ParallelSnapshotRequest

Chybová zpráva: pro tuto sdílenou složku již probíhá úloha zálohování.

- Zálohování sdílené složky nepodporuje paralelní požadavky snímku na stejnou sdílenou složku.

- Počkejte, až se dokončí stávající úloha zálohování, a pak to zkuste znovu. Pokud nemůžete najít úlohu zálohování v trezoru Recovery Services, podívejte se na jiné trezory Recovery Services ve stejném předplatném.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>Zálohování nebo obnovení sdílené složky FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling se nezdařilo z důvodu omezení služby úložiště. Důvodem může být to, že služba úložiště je zaneprázdněná zpracováním jiných požadavků pro daný účet úložiště.

Kód chyby: FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling

Chybová zpráva: zálohování nebo obnovení sdílené složky se nezdařilo, protože došlo k omezení služby úložiště. Pravděpodobnou příčinou je zaneprázdněnost služby úložiště zpracováním jiných požadavků pro daný účet úložiště.

Zkuste operaci zálohování nebo obnovení provést později.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound – cílová sdílená složka se nenašla.

Kód chyby: TargetFileShareNotFound

Chybová zpráva: cílová sdílená složka se nenašla.

- Ujistěte se, že vybraný účet úložiště existuje a cílová sdílená složka se neodstraní.

- Ujistěte se, že účet úložiště je pro zálohování sdílených složek podporovaný účet úložiště.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked – úlohy zálohování nebo obnovení selhaly, protože účet úložiště je v uzamčeném stavu.

Kód chyby: UserErrorStorageAccountIsLocked

Chybová zpráva: úlohy zálohování nebo obnovení se nezdařily, protože účet úložiště je v uzamčeném stavu.

Odeberte zámek na účtu úložiště, nebo místo **zámku pro čtení** použijte **Zámek proti odstranění** a zkuste operaci zálohování nebo obnovení zopakovat.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached – obnovení nebylo úspěšné, protože počet neúspěšných souborů je vyšší než prahová hodnota.

Kód chyby: DataTransferServiceCoFLimitReached

Chybová zpráva: obnovení nebylo úspěšné, protože počet neúspěšných souborů je vyšší než prahová hodnota.

- Důvody selhání obnovení jsou uvedeny v souboru (cesta je uvedena v podrobnostech úlohy). Vyřešte chyby a opakujte operaci obnovení pouze pro soubory, které selhaly.

- Běžné důvody selhání obnovení souborů:

  - soubory, které se nezdařily, se momentálně používají.
  - v nadřazeném adresáři existuje adresář se stejným názvem jako soubor, který selhal.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover – obnovení nebylo úspěšné, protože se nepovedlo obnovit žádný soubor.

Kód chyby: DataTransferServiceAllFilesFailedToRecover

Chybová zpráva: obnovení nebylo úspěšné, protože se nepovedlo obnovit žádný soubor.

- Důvody selhání obnovení jsou uvedeny v souboru (cesta je uvedena v podrobnostech úlohy). Vyřešte chyby a zopakujte operaci obnovení pouze pro soubory, u kterých došlo k chybě.

- Běžné důvody selhání obnovení souborů:

  - soubory, které se nezdařily, se momentálně používají.
  - v nadřazeném adresáři existuje adresář se stejným názvem jako soubor, který selhal.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid – obnovení selhalo, protože jeden ze souborů ve zdroji neexistuje.

Kód chyby: DataTransferServiceSourceUriNotValid

Chybová zpráva: operace obnovení se nezdařila, protože jeden ze souborů ve zdroji neexistuje.

- Data bodu obnovení neobsahují vybrané položky. Pokud chcete obnovit soubory, zadejte správný seznam souborů.
- Snímek sdílené složky odpovídající bodu obnovení se ručně odstranil. Vyberte jiný bod obnovení zkuste operaci obnovení zopakovat.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked – probíhá zpracování úlohy obnovení do stejného cíle.

Kód chyby: UserErrorDTSDestLocked

Chybová zpráva: probíhá zpracování úlohy obnovení do stejného cíle.

- Zálohování sdílené složky nepodporuje paralelní obnovení do stejné cílové sdílené složky.

- Počkejte na dokončení stávajícího obnovení a zkuste to znovu. Pokud nemůžete najít úlohu obnovení v trezoru Recovery Services, podívejte se na jiné trezory Recovery Services ve stejném předplatném.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>Operace UserErrorTargetFileShareFull-Restore se nezdařila, protože cílová sdílená složka je plná.

Kód chyby: UserErrorTargetFileShareFull

Chybová zpráva: operace obnovení se nezdařila, protože cílová sdílená složka je plná.

Zvyšte kvótu velikosti cílové sdílené složky, aby odpovídala datům pro obnovení, a zkuste operaci obnovení zopakovat.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient – cílová sdílená složka nemá dostatečnou kvótu velikosti úložiště pro obnovení.

Kód chyby: UserErrorTargetFileShareQuotaNotSufficient

Chybová zpráva: cílová sdílená složka nemá dostatečnou kvótu velikosti úložiště pro obnovení.

Zvyšte kvótu velikosti cílové sdílené složky, aby vyhovovala datům pro obnovení, a zkuste operaci zopakovat.

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>Operace Synchronizace souborů PreRestoreFailed-Restore se nezdařila, protože došlo k chybě při provádění operací před obnovením u prostředků služby Synchronizace souborů přidružených k cílové sdílené složce.

Kód chyby: Synchronizace souborů PreRestoreFailed

Chybová zpráva: operace obnovení se nezdařila, protože došlo k chybě při provádění operací před obnovením u prostředků služby Synchronizace souborů přidružených k cílové sdílené složce.

Zkuste data obnovit později. Pokud potíže trvají, obraťte se na podporu Microsoftu.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress-Synchronizace souborů Azure zjišťování změn služby probíhá pro cílovou sdílenou složku. Detekci změn aktivovala předchozí obnovení do cílové sdílené složky.

Kód chyby: AzureFileSyncChangeDetectionInProgress

Chybová zpráva: pro cílovou sdílenou složku probíhá zjišťování změn služby Synchronizace souborů Azure. Detekci změn aktivovala předchozí obnovení do cílové sdílené složky.

Použijte jinou cílovou sdílenou složku. Alternativně můžete počkat na dokončení Synchronizace souborů Azure zjišťování změn služby pro cílovou sdílenou složku před opakováním pokusu o obnovení.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored – jeden nebo víc souborů se nepovedlo úspěšně obnovit. Další informace najdete v seznamu neúspěšných souborů v cestě uvedené výše.

Kód chyby: UserErrorAFSRecoverySomeFilesNotRestored

Chybová zpráva: jeden nebo více souborů nelze úspěšně obnovit. Další informace najdete v seznamu souborů, u kterých došlo k chybě, v cestě uvedené výše.

- Důvody selhání obnovení jsou uvedeny v souboru (cesta je uvedena v podrobnostech úlohy). Vyřešte důvody a opakujte operaci obnovení pouze pro soubory, které selhaly.
- Běžné důvody selhání obnovení souborů:

  - soubory, které se nezdařily, se momentálně používají.
  - v nadřazeném adresáři existuje adresář se stejným názvem jako soubor, který selhal.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound – nenašel se snímek sdílené složky Azure odpovídající bodu obnovení.

Kód chyby: UserErrorAFSSourceSnapshotNotFound

Chybová zpráva: nenašel se snímek sdílené složky Azure odpovídající bodu obnovení.

- Zajistěte, aby byl snímek sdílené složky odpovídající bodu obnovení, který se snažíte použít pro obnovení, stále existuje.

  >[!NOTE]
  >Pokud odstraníte snímek sdílené složky, který byl vytvořen Azure Backup, odpovídající body obnovení se stanou nepoužitelnými. Doporučujeme, abyste neodstranili snímky, abyste zajistili zaručené obnovení.

- Zkuste pro obnovení dat vybrat jiný bod obnovení.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget – probíhá jiná úloha obnovení ve stejné cílové sdílené složce.

Kód chyby: UserErrorAnotherRestoreInProgressOnSameTarget

Chybová zpráva: probíhá jiná úloha obnovení ve stejné cílové sdílené složce.

Použijte jinou cílovou sdílenou složku. Případně můžete operaci zrušit a počkat, než se druhé obnovení dokončí.

## <a name="common-modify-policy-errors"></a>Běžné chyby zásad úprav

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation – pro tuto položku probíhá jiná operace konfigurace ochrany.

Kód chyby: BMSUserErrorConflictingProtectionOperation

Chybová zpráva: pro tuto položku probíhá jiná operace konfigurace ochrany.

Počkejte na dokončení předchozí operace Upravit zásadu a zkuste to znovu později.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked – u vybrané položky probíhá jiná operace.

Kód chyby: BMSUserErrorObjectLocked

Chybová zpráva: u vybrané položky probíhá jiná operace.

Počkejte na dokončení jiné probíhající operace a zkuste to znovu později.

## <a name="common-soft-delete-related-errors"></a>Běžné chyby související s tichým odstraněním

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState – tento bod obnovení není k dispozici, protože snímek přidružený k tomuto bodu je ve sdílené složce, která je v nepodmíněném stavu odstranění.

Kód chyby: UserErrorRestoreAFSInSoftDeleteState

Chybová zpráva: Tento bod obnovení není k dispozici, protože snímek přidružený k tomuto bodu je ve sdílené složce, která je ve stavu bez přístupného odstranění.

Operaci obnovení nelze provést, pokud je sdílená složka ve stavu tichého odstranění. Obnovte sdílenou složku ze souboru portálu Files nebo pomocí [skriptu Undelete](scripts/backup-powershell-script-undelete-file-share.md) a zkuste provést obnovení.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState – uvedené body obnovení nejsou k dispozici, protože se trvale odstranila přidružená sdílená složka obsahující snímky bodu obnovení.

Kód chyby: UserErrorRestoreAFSInDeleteState

Chybová zpráva: uvedené body obnovení nejsou k dispozici, protože se trvale odstranila přidružená sdílená složka obsahující snímky bodu obnovení.

Ověřte, zda je odstraněna zálohovaná sdílená složka. Pokud byl ve stavu undeleteded, ověřte, zda je doba uchování měkkého odstranění vyšší a nebyla obnovena zpět. V obou těchto případech ztratíte všechny snímky trvale a nebudete moct data obnovit.

>[!NOTE]
> Nedoporučujeme odstranit zálohovanou sdílenou složku, nebo pokud je v tichém odstraněných stavech, neodstraňujte před uplynutím doby uchování krátkého odstranění, abyste se vyhnuli ztrátě všech bodů obnovení.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState – zálohování se nepovedlo, protože sdílená složka Azure je ve stavu nepodmíněného odstranění.

Kód chyby: UserErrorBackupAFSInSoftDeleteState

Chybová zpráva: zálohování se nepovedlo, protože sdílená složka Azure je ve stavu unsoft-Deleted.

Pokud chcete pokračovat v zálohování a zabránit trvalému odstranění dat, odstraňte sdílenou složku z **portálu souborů** nebo pomocí [skriptu Undelete](scripts/backup-powershell-script-undelete-file-share.md) .

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState – zálohování se nepovedlo, protože přidružená sdílená složka Azure je trvale Odstraněná.

Kód chyby: UserErrorBackupAFSInDeleteState

Chybová zpráva: zálohování se nepovedlo, protože přidružená sdílená složka Azure je trvale Odstraněná.

Ověřte, jestli je zálohovaná sdílená složka trvale Odstraněná. Pokud ano, zastavte zálohování sdílené složky, aby se předešlo opakovaným chybám zálohování. Informace o zastavení ochrany najdete v tématu [zastavení ochrany sdílené složky Azure](./manage-afs-backup.md#stop-protection-on-a-file-share) .

## <a name="next-steps"></a>Další kroky

Další informace o zálohování sdílených složek Azure najdete v těchto tématech:

- [Zálohování sdílených složek Azure](backup-afs.md)
- [Nejčastější dotazy k zálohování sdílených složek Azure](backup-azure-files-faq.yml)
