---
title: Podpora archivní úrovně (Preview)
description: Další informace o podpoře vrstev archivů pro Azure Backup
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 6c597d640f24dc4c680bfd5db16f9df09017ee54
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609848"
---
# <a name="archive-tier-support-preview"></a>Podpora archivní úrovně (Preview)

Zákazníci spoléhají na Azure Backup pro ukládání zálohovaných dat, včetně dat zálohy jejich Long-Term uchovávání (LTR) s požadavky na uchování definovanými pravidly dodržování předpisů organizace. Ve většině případů se starší data záloh otevírají zřídka a ukládají se jenom pro potřeby dodržování předpisů.

Azure Backup podporuje zálohování dlouhodobých bodů uchovávání dat v archivní úrovni kromě snímků a úrovně Standard.

## <a name="scope-for-preview"></a>Rozsah pro verzi Preview

Podporované úlohy:

- Virtuální počítače Azure
  - Pouze měsíční a roční body obnovení. Denní a týdenní body obnovení nejsou podporovány.
  - Stáří >= 3 měsíce v Vault-Standard úrovni
  - Zbývající >uchování = 6 měsíců
  - Žádné aktivní denní a týdenní závislosti
- SQL Server ve virtuálních počítačích Azure
  - Pouze úplné body obnovení. Protokoly a rozdíly nejsou podporovány.
  - Stáří >= 45 dní v Vault-Standard úrovni
  - Zbývající >uchování = 6 měsíců
  - Žádné závislosti

Podporovaní klienti:

- Tato funkce je k dispozici v prostředí PowerShell.

>[!NOTE]
>Podpora archivní vrstvy pro virtuální počítače Azure a SQL Server ve virtuálních počítačích Azure je ve verzi Public Preview s omezeným zápisem. Pokud se chcete zaregistrovat ke službě Archive support, použijte tento [odkaz](https://aka.ms/ArchivePreviewInterestForm).

## <a name="get-started-with-powershell"></a>Začínáme s PowerShellem

1. V PowerShellu spusťte následující příkaz:
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.0.0-preview -AllowPrerelease -force
    ```

1. Připojte se k Azure pomocí rutiny [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .
1. Přihlaste se k předplatnému:

   `Set-AzContext -Subscription "SubscriptionName"`

1. Získat trezor:

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. Získat seznam zálohovaných položek:

    `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM/AzureWorkload" -WorkloadType "AzureVM/MSSQL"`

1. Získat zálohovanou položku

    - Pro virtuální počítače Azure:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - Pro SQL Server ve virtuálních počítačích Azure:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<dbName>' -and $_.ContainerName -match '<vmName>'}`

## <a name="use-powershell"></a>Použití prostředí PowerShell

### <a name="check-archivable-recovery-points"></a>Kontrolovat archivní body obnovení

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm  -IsReadyForMove $true -TargetTier VaultArchive
```

Zobrazí se seznam všech bodů obnovení přidružených ke konkrétní zálohované položce, které jsou připraveny k přesunu do archivu.

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>Zjistit, proč nelze přesunout bod obnovení do archivu

```azurepowershell
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

Kde `$rp[0]` je bod obnovení, pro který chcete zjistit, proč nelze archivovat.

Ukázkový výstup:

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Kontrolovat doporučenou sadu archivačních bodů (jenom pro virtuální počítače Azure)

Body obnovení přidružené k virtuálnímu počítači jsou přírůstkové. Když se určitý bod obnovení přesune do archivu, převede se na úplnou zálohu a pak se přesune na archivní. Úspora nákladů spojených s přesunem do archivu proto závisí na měnícím se způsobu, jakým je zdroj dat.

Takže Azure Backup se zavedla s doporučovanou sadou bodů obnovení, které by mohly vést k úsporám nákladů při jejich přesunu dohromady.

>[!NOTE]
>Úspora nákladů závisí na nejrůznějších důvodech a nemusí být stejná pro všechny dvě instance.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>Přesunout do archivu

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[2] -SourceTier VaultStandard -DestinationTier VaultArchive
```

Tento příkaz přesune archivní bod obnovení do archivní. Vrátí úlohu, kterou lze použít ke sledování operace přesunutí z portálu i pomocí prostředí PowerShell.

### <a name="view-archived-recovery-points"></a>Zobrazit archivované body obnovení

Tento příkaz vrátí všechny archivované body obnovení.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive
```

### <a name="restore-with-powershell"></a>Obnovení pomocí PowerShellu

V případě bodů obnovení v archivu poskytuje Azure Backup integrovanou metodologii obnovení.

Integrované obnovení je proces se dvěma kroky. Prvním krokem je dehydratovanéní bodů obnovení uložených v archivu a jejich dočasné uložení do trezoru – standardní vrstva po dobu trvání (označované také jako doba trvání vysazování) v rozmezí od 10 do 30 dnů. Výchozí hodnota je 15 dní. K dispozici jsou dvě různé priority pro resazování – Standard a vysoká priorita. Přečtěte si další informace o [prioritě pro vysazování](https://docs.microsoft.com/azure/storage/blobs/storage-blob-rehydration#rehydrate-an-archived-blob-to-an-online-tier).

>[!NOTE]
>
>- Vybraná doba trvání pro vyvýšení se nedá změnit a provedené body obnovení zůstanou na úrovni Standard pro dobu trvání vysazování.
>- V dalším kroku vysazování se účtují náklady.

Další informace o různých metodách obnovení virtuálních počítačů Azure najdete v tématu [obnovení virtuálního počítače Azure pomocí PowerShellu](backup-azure-vms-automation.md#restore-an-azure-vm).

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

Chcete-li obnovit SQL Server, postupujte podle [těchto kroků](backup-azure-sql-automation.md#restore-sql-dbs). Další požadované parametry jsou **RehydrationPriority** a **RehydrationDuration**.

### <a name="view-jobs-from-powershell"></a>Zobrazení úloh z PowerShellu

Chcete-li zobrazit úlohy přesunutí a obnovení, použijte následující rutinu prostředí PowerShell:

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="use-the-portal"></a>Použití portálu

### <a name="check-archived-recovery-point"></a>Zkontrolování archivovaného bodu obnovení

Nyní můžete zobrazit všechny body obnovení, které byly přesunuty do archivu.

![Všechny body obnovení.](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>Obnovení na portálu

Pro body obnovení, které byly přesunuty do archivu, je nutné přidat parametry pro dobu trvání vysazování a prioritu pro vysazování.

![Obnovit na portálu.](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>Zobrazení úloh na portálu

![Zobrazit úlohy na portálu.](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>Upravit ochranu

Existují dva způsoby, jak můžete upravit ochranu pro zdroj dat:

- Změna existující zásady
- Ochrana zdroje dat pomocí nové zásady

V obou případech se nové zásady aplikují na všechny starší body obnovení, které jsou na úrovni Standard a na úrovni archivace. Takže se starší body obnovení můžou odstranit, pokud dojde ke změně v zásadách.

Když se body obnovení přesunou do archivu, vztahují se na období předčasného odstranění 180 dnů. Poplatky se účtují poměrnou rychlostí. Pokud se odstraní bod obnovení, který se nechali v archivu po dobu 180 dnů, bude se účtovat cena ekvivalentní hodnotě 180 minus počet dní strávený na úrovni Standard.

Body obnovení, které se nechali v archivu po dobu nejméně šesti měsíců, budou mít při odstranění náklady na předčasné odstranění.

## <a name="stop-protection-and-delete-data"></a>Zastavení ochrany a odstranění dat

Zastavení ochrany a odstranění dat odstraní všechny body obnovení. V případě bodů obnovení v archivu, které se nechali po dobu trvání 180 dní v archivní úrovni, bude odstranění bodů obnovení vést k nákladům na předčasné odstranění.

## <a name="error-codes-and-troubleshooting-steps"></a>Chybové kódy a postup řešení potíží

Existuje několik chybových kódů, které se zobrazí, když se bod obnovení nedá přesunout do archivní.

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**Chybová zpráva** -Recovery-Point typ není způsobilý pro přesunutí archivu

**Popis** – tento kód chyby se zobrazí, když vybraný typ bodu obnovení nemá nárok na přesunutí do archivní.

**Doporučená akce** – [tady](#scope-for-preview) ověřte způsobilost bodu obnovení.

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**Chybová zpráva** -Recovery-Point, že aktivní závislosti pro obnovení nejsou vhodné pro přesunutí archivu

**Popis –** Vybraný bod obnovení má aktivní závislosti a nelze jej přesunout do archivu.

**Doporučená akce** – [tady](#scope-for-preview) ověřte způsobilost bodu obnovení.

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**Chybová zpráva** – Recovery-Point nemá nárok na přesunutí archivu, protože životnost je vyčerpána v trezoru – úroveň Standard je nižší než požadované minimum.

**Popis** – bod obnovení musí zůstat na standardní úrovni po dobu minimálně tří měsíců pro virtuální počítače azure a 45 dní pro SQL Server ve virtuálních počítačích Azure.

**Doporučená akce** – [tady](#scope-for-preview) ověřte způsobilost bodu obnovení.

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**Chybová zpráva** – Recovery-Point zbývající životnost je menší než požadované minimum.

**Popis** – minimální životnost nutná pro bod obnovení pro nárok na přesunutí archivu je šest měsíců.

**Doporučená akce** – [tady](#scope-for-preview) ověřte způsobilost bodu obnovení.

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**Chybová zpráva** – Recovery-Point nemá nárok na přesun archivu, protože už je přesunutý do archivní úrovně.

**Popis** – vybraný bod obnovení je již v archivu. Proto není vhodné ho přesunout do archivní.

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**Chybová zpráva** – typ zdroje dat nemá nárok na doporučení API.

**Popis** – rozhraní API pro doporučení platí jenom pro virtuální počítače Azure. Nedá se použít pro vybraný typ DataSource.

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**Chybová zpráva** – bod obnovení je již znovu vysušen. Pro tento RP není povoleno vysazování.

**Popis** – vybraný bod obnovení je již znovu vysušen.

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**Chybová zpráva** – bod obnovení není způsobilý pro přesunutí archivu.

**Popis** – vybraný bod obnovení nemá nárok na přesunutí archivu.

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**Chybová** **zpráva** – bod obnovení archivu není znovu vysušen. Po dokončení opětovného nasazování v archivu RP se znovu pokuste provést obnovení.

**Popis** – bod obnovení není znovu vysušen. Po dehydratovaném bodu obnovení zkuste provést obnovení.

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**Chybová** **zpráva**– při vysazování se podporuje jenom pro body obnovení archivu – k vysazování se podporuje jenom pro archivní body obnovení.

**Popis** – při vysazování není pro vybraný bod obnovení povoleno.

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**Chybová zpráva** – pro bod obnovení je již In-Progress pro vysazování.

**Popis** – již probíhá dosazování pro vybraný bod obnovení.

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**Chybová zpráva** : bod obnovení nelze přesunout do archivní úrovně z důvodu nedostatečné doby uchování zadané v zásadě.

**Doporučená akce** – zásady aktualizace pro chráněnou položku s příslušným nastavením uchovávání a zkuste to znovu.

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**Chybová zpráva** – pořád se zjišťuje, jestli se tento bod obnovení dá přesunout.

**Popis** – je ještě možné určit připravenost přesunutí bodu obnovení.

**Doporučená akce** – po nějaké době zkuste kontrolu zopakovat.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>Co se stane s archivací bodů obnovení, když zastavím ochranu a zachovám data?

Bod obnovení zůstane v archivu stále. Další informace najdete v tématu [dopad zastavení ochrany na body obnovení](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points).

## <a name="next-steps"></a>Další kroky

- [Ceny služby Azure Backup](azure-backup-pricing.md)
