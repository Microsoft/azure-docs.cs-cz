---
title: Řešení potíží se zálohováním ve službě Azure disk Backup
description: Postup řešení potíží se zálohováním ve službě Azure disk Backup
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: f6e9bcbbd618cf820ce91f8a3092d5be4aa045cb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105107240"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup"></a>Řešení potíží se zálohováním ve službě Azure disk Backup

Tento článek poskytuje informace o řešení potíží s problémy zálohování a obnovení s využitím disku Azure. Další informace o dostupnosti oblasti [Azure disk Backup](disk-backup-overview.md) , podporovaných scénářích a omezeních najdete v tématu věnovaném [maticům podpory](disk-backup-support-matrix.md).

## <a name="common-issues-faced-with-azure-disk-backup"></a>Běžné problémy s Azure disk Backup

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>Kód chyby: UserErrorSnapshotRGSubscriptionMismatch

Chybová zpráva: pro úložiště dat snímku je vybrané neplatné předplatné.

Doporučená akce: disky a snímky disků se ukládají do stejného předplatného. Můžete zvolit libovolnou skupinu prostředků, do které se mají ukládat snímky disků v rámci předplatného. Vyberte stejné předplatné jako zdrojový disk. Další informace najdete v tématu [matice podpory](disk-backup-support-matrix.md).

### <a name="error-code-usererrorsnapshotrgnotfound"></a>Kód chyby: UserErrorSnapshotRGNotFound

Chybová zpráva: tuto operaci nelze provést, protože skupina prostředků úložiště dat snímku neexistuje.

Doporučená akce: Vytvořte skupinu prostředků a poskytněte jí požadovaná oprávnění. Další informace najdete v tématu [Konfigurace zálohování](backup-managed-disks.md#configure-backup).

### <a name="error-code-usererrormanageddisknotfound"></a>Kód chyby: UserErrorManagedDiskNotFound

Chybová zpráva: operaci nelze provést, protože spravovaný disk již neexistuje.

Doporučená akce: zálohování bude i nadále neúspěšné, protože zdrojový disk může být odstraněn nebo přesunut do jiného umístění. Použijte existující bod obnovení k obnovení disku, pokud je odstraněn omylem. Pokud je disk přesunut do jiného umístění, nakonfigurujte zálohování disku.

### <a name="error-code-usererrornotenoughpermissionondisk"></a>Kód chyby: UserErrorNotEnoughPermissionOnDisk

Chybová zpráva: Služba Azure Backup vyžaduje další oprávnění na disku pro provedení této operace.

Doporučená akce: Udělte spravované identitě úložiště záloh příslušná oprávnění na disku. [V dokumentaci](backup-managed-disks.md) najdete informace o tom, jaká oprávnění vyžaduje spravovaná identita úložiště záloh a jak je poskytnout.

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>Kód chyby: UserErrorNotEnoughPermissionOnSnapshotRG

Chybová zpráva: Služba Azure Backup vyžaduje pro tuto operaci další oprávnění ke skupině prostředků úložiště dat snímku.

Doporučená akce: Udělte spravované identitě trezoru záloh příslušná oprávnění ke skupině prostředků úložiště dat snímku. Skupina prostředků snímku úložiště dat je umístění, kde jsou uložené snímky disků. Podívejte se na [dokumentaci](backup-managed-disks.md) , která vám pomůže pochopit, která je skupina prostředků, jaká oprávnění vyžaduje spravovaná identita úložiště záloh a jak je poskytnout.

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>Kód chyby: UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

Chybová zpráva: neplatný disk nebo služba Azure Backup vyžaduje k provedení této operace další oprávnění na disku.

Doporučená akce: zálohování bude i nadále neúspěšné, protože zdrojový disk může být odstraněn nebo přesunut do jiného umístění. Použijte existující bod obnovení k obnovení disku, pokud je odstraněn omylem. Pokud je disk přesunut do jiného umístění, nakonfigurujte zálohování disku. Pokud disk není odstraněný nebo přesunutý, udělte spravované identitě úložiště záloh příslušná oprávnění na disku. Informace o tom, jaká oprávnění vyžaduje spravovaná identita trezoru záloh a jak je poskytnout, najdete [v dokumentaci](backup-managed-disks.md) .

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>Kód chyby: UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

Chybová zpráva: tuto operaci nelze provést, protože skupina prostředků úložiště dat snímku již neexistuje. Nebo služba Azure Backup vyžaduje pro tuto operaci další oprávnění ke skupině prostředků úložiště dat snímku.

Doporučená akce: Vytvořte skupinu prostředků a udělte spravované identitě úložiště záloh vhodná oprávnění pro skupinu prostředků úložiště dat snímku. Skupina prostředků snímku úložiště dat je umístění, kde jsou uložené snímky disků. Podívejte se na [dokumentaci](backup-managed-disks.md) , kde najdete informace o tom, co je to skupina prostředků, jaká oprávnění vyžaduje spravovaná identita trezoru záloh a jak je poskytnout.

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>Kód chyby: UserErrorDiskBackupAuthorizationFailed

Chybová zpráva: ve spravované identitě trezoru služby Backup chybí potřebná oprávnění k provedení této operace.

Doporučená akce: Udělte spravované identitě úložiště záloh příslušná oprávnění na disku, který se má zálohovat, a ve skupině prostředků snímků dat snímku, kde jsou snímky uložené. Informace o tom, jaká oprávnění vyžaduje spravovaná identita trezoru záloh a jak je poskytnout, najdete [v dokumentaci](backup-managed-disks.md) .

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>Kód chyby: UserErrorSnapshotRGOrMSIPermissionsNotPresent

Chybová zpráva: tuto operaci nelze provést, protože skupina prostředků úložiště dat snímku již neexistuje. Nebo Azure Backup služba vyžaduje pro tuto operaci další oprávnění ke skupině prostředků úložiště dat snímku.

Doporučená akce: Vytvořte skupinu prostředků a udělte spravované identitě úložiště záloh vhodná oprávnění pro skupinu prostředků úložiště dat snímku. Skupina prostředků úložiště dat snímku je umístění, kde jsou snímky uložené. Informace o tom, co je skupina prostředků, najdete [v dokumentaci](backup-managed-disks.md) . Tato oprávnění vyžaduje spravovaná identita trezoru záloh a jejich poskytnutí.

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>Kód chyby: UserErrorOperationalStoreParametersNotProvided

Chybová zpráva: nejde provést operaci, protože není zadaný parametr skupiny prostředků úložiště dat snímku.

Doporučená akce: zadejte parametr skupiny prostředků úložiště dat snímku. Skupina prostředků snímku úložiště dat je umístění, kde jsou uložené snímky disků. Další informace najdete [v dokumentaci](backup-managed-disks.md).

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>Kód chyby: UserErrorInvalidOperationalStoreResourceGroup

Chybová zpráva: poskytnutá skupina prostředků úložiště dat snímku není platná.

Doporučená akce: Zadejte platnou skupinu prostředků pro úložiště dat snímku. Skupina prostředků snímku úložiště dat je umístění, kde jsou uložené snímky disků. Další informace najdete [v dokumentaci](backup-managed-disks.md).

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>Kód chyby: UserErrorDiskBackupDiskTypeNotSupported

Chybová zpráva: nepodporovaný typ disku

Doporučená akce: informace o podporovaných scénářích a omezeních najdete [v tabulce podpory](disk-backup-support-matrix.md) .

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>Kód chyby: UserErrorSameNameDiskAlreadyExists

Chybová zpráva: nejde obnovit, protože disk se stejným názvem už ve vybrané cílové skupině prostředků existuje.

Doporučená akce: Zadejte jiný název disku pro obnovení. Další informace najdete v tématu [obnovení Azure Managed disks](restore-managed-disks.md).

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>Kód chyby: UserErrorRestoreTargetRGNotFound

Chybová zpráva: operace se nezdařila, protože cílová skupina prostředků neexistuje.

Doporučená akce: Zadejte platnou skupinu prostředků, kterou chcete obnovit. Další informace najdete v tématu [obnovení Azure Managed disks](restore-managed-disks.md).

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>Kód chyby: UserErrorNotEnoughPermissionOnTargetRG

Chybová zpráva: Služba Azure Backup vyžaduje další oprávnění pro cílovou skupinu prostředků, aby bylo možné tuto operaci provést.

Doporučená akce: Udělte spravované identitě úložiště záloh příslušná oprávnění pro cílovou skupinu prostředků. Cílová skupina prostředků je vybrané umístění, kam se má disk obnovit. V dokumentaci pro [obnovení](restore-managed-disks.md) najdete informace o tom, jaká oprávnění vyžaduje spravovaná identita trezoru záloh a jak je poskytnout.

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>Kód chyby: UserErrorSubscriptionDiskQuotaLimitReached

Chybová zpráva: operace se nezdařila, protože bylo dosaženo maximálního limitu kvóty disku v rámci předplatného.

Doporučená akce: Další informace najdete v [dokumentaci k předplatnému a omezením předplatného Azure a k kvótě](../azure-resource-manager/management/azure-subscription-service-limits.md) nebo kontaktnímu podpora Microsoftu.

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>Kód chyby: UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

Chybová zpráva: operace se nezdařila, protože cílová skupina prostředků neexistuje. Nebo služba Azure Backup vyžaduje k provedení této operace další oprávnění pro cílovou skupinu prostředků.

Doporučená akce: Zadejte platnou skupinu prostředků, kterou chcete obnovit, a udělte spravované identitě úložiště záloh příslušná oprávnění pro cílovou skupinu prostředků. Cílová skupina prostředků je vybrané umístění, kam se má disk obnovit. V dokumentaci pro [obnovení](restore-managed-disks.md) najdete informace o tom, jaká oprávnění vyžaduje spravovaná identita trezoru záloh a jak je poskytnout.

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>Kód chyby: UserErrorDESKeyVaultKeyDisabled

Chybová zpráva: klíč trezoru klíčů, který se používá pro sadu šifrování disků, není v povoleném stavu.

Doporučená akce: Povolte klíč trezoru klíčů, který se používá pro sadu šifrování disku. Přečtěte si omezení v [matici podpory](disk-backup-support-matrix.md).

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>Kód chyby: UserErrorMSIPermissionsNotPresentOnDES

Chybová zpráva: Služba Azure Backup potřebuje oprávnění pro přístup k sadě pro šifrování disků, která se používá s diskem.

Doporučená akce: Poskytněte přístup čtenáře ke spravované identitě trezoru záloh do sady Disk Encryption (DES).

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>Kód chyby: UserErrorDESKeyVaultKeyNotAvailable

Chybová zpráva: klíč trezoru klíčů, který se používá pro sadu šifrování disků, není k dispozici.

Doporučená akce: Ujistěte se, že klíč trezoru klíčů, který se používá pro sadu šifrování disku, není zakázaný ani odstraněný.

### <a name="error-code-usererrordisksnapshotnotfound"></a>Kód chyby: UserErrorDiskSnapshotNotFound

Chybová zpráva: snímek disku pro tento bod obnovení byl odstraněn.

Doporučená akce: snímky se ukládají do skupiny prostředků úložiště dat snímku v rámci vašeho předplatného. Je možné, že snímek související s vybraným bodem obnovení byl pravděpodobně odstraněn nebo přesunut z této skupiny prostředků. Zvažte použití jiného bodu obnovení k obnovení. Řiďte se taky doporučenými pokyny pro výběr skupiny prostředků snímků, kterou najdete v [dokumentaci pro obnovení](restore-managed-disks.md).

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>Kód chyby: UserErrorSnapshotMetadataNotFound

Chybová zpráva: metadata snímku disku pro tento bod obnovení byla odstraněna.

Doporučená akce: Zvažte použití jiného bodu obnovení, který chcete obnovit. Další informace najdete v [dokumentaci k obnovení](restore-managed-disks.md).

### <a name="error-code-backupagentpluginhostvalidateprotectionerror"></a>Kód chyby: BackupAgentPluginHostValidateProtectionError

Chybová zpráva: zálohování disku ještě není dostupné v oblasti trezoru záloh, v rámci které se pokoušíte konfigurovat ochranu.

Doporučená akce: úložiště záloh musí být v podporované oblasti. Dostupnost oblastí najdete v části [Podpora](disk-backup-support-matrix.md).

### <a name="error-code-usererrordppdatasourcealreadyhasbackupinstance"></a>Kód chyby: UserErrorDppDatasourceAlreadyHasBackupInstance

Chybová zpráva: disk, na který se pokoušíte nakonfigurovat zálohování, je už chráněný. Disk je už přidružený k instanci zálohy v úložišti záloh.

Doporučená akce: Tento disk je už přidružený k instanci zálohy v úložišti záloh. Pokud chcete tento disk znovu chránit, odstraňte instanci zálohování z trezoru záloh, kde je aktuálně chráněná, a znovu ho Zabezpečte v jakémkoli jiném trezoru.

### <a name="error-code-usererrordppdatasourcealreadyprotected"></a>Kód chyby: UserErrorDppDatasourceAlreadyProtected

Chybová zpráva: disk, na který se pokoušíte nakonfigurovat zálohování, je už chráněný. Disk je už přidružený k instanci zálohy v úložišti záloh.

Doporučená akce: Tento disk je už přidružený k instanci zálohy v úložišti záloh. Pokud chcete tento disk znovu chránit, odstraňte instanci zálohování z trezoru služby Backup, kde je aktuálně chráněná, a znovu ho Zabezpečte v jakémkoli jiném trezoru.

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>Kód chyby: UserErrorMaxConcurrentOperationLimitReached

Chybová zpráva: operaci nelze spustit, protože bylo dosaženo maximálního počtu povolených souběžných záloh.

Doporučená akce: Počkejte na dokončení předchozí spuštěné zálohy.

## <a name="next-steps"></a>Další kroky

- [Matice podpory pro zálohování disků Azure](disk-backup-support-matrix.md)