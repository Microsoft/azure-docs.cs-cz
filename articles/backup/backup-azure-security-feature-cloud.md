---
title: Obnovitelné odstranění pro Azure Backup
description: Naučte se používat funkce zabezpečení v Azure Backup k zajištění většího zabezpečení záloh.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: d7998c7f9def6ce9965ded3b6ec700f7975891eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91271541"
---
# <a name="soft-delete-for-azure-backup"></a>Obnovitelné odstranění pro Azure Backup

Problémy se zabezpečením, jako je malware, ransomwarem a vniknutí, se zvyšují. Tyto problémy se zabezpečením můžou být nákladné, s ohledem na peníze i data. Pro ochranu proti takovým útokům Azure Backup nyní poskytuje funkce zabezpečení, které vám pomůžou chránit zálohovaná data i po odstranění.

Jednou z těchto funkcí je obnovitelné odstranění. Pomocí obnovitelného odstranění, a to i v případě, že škodlivý objekt actor odstraní zálohu (nebo se data záloh nechtěně odstraní), uchovávají data zálohy 14 dalších dnů, což umožňuje obnovení této zálohované položky bez ztráty dat. Další 14 dní uchovávání zálohových dat ve stavu "obnovitelné odstranění" za vás neúčtují žádné náklady.

Ochrana před odstraněním je k dispozici pro tyto služby:

- [Obnovitelné odstranění pro virtuální počítače Azure](soft-delete-virtual-machines.md)
- [Obnovitelné odstranění pro SQL Server na virtuálním počítači Azure a obnovitelné odstranění pro SAP HANA v úlohách virtuálních počítačů Azure](soft-delete-sql-saphana-in-azure-vm.md)

Tento vývojový diagram zobrazuje různé kroky a stavy zálohované položky, když je povolené obnovitelné odstranění:

![Životní cyklus záložní položky odstraněné položky](./media/backup-azure-security-feature-cloud/lifecycle.png)

## <a name="enabling-and-disabling-soft-delete"></a>Povolení a zákaz obnovitelného odstranění

Obnovitelné odstranění je ve výchozím nastavení povolené u nově vytvořených trezorů za účelem ochrany zálohovaných dat před náhodnými nebo škodlivými odstraněními.  Zakázání této funkce se nedoporučuje. Jediným případem, kdy byste měli zvážit zakázání obnovitelného odstranění, je, že plánujete přesunutí chráněných položek do nového trezoru a nemůžete počkat 14 dní před odstraněním a obnovením ochrany (například v testovacím prostředí). Tuto funkci může zakázat pouze vlastník trezoru. Pokud tuto funkci zakážete, všechna budoucí odstranění chráněných položek budou mít za následek okamžité odebrání bez možnosti obnovení. Zálohovaná data, která existují v tichém odstraněném stavu před zakázáním této funkce, zůstanou v tichém odstraněné stavu po dobu 14 dnů. Pokud chcete tyto okamžité odstranění trvale odstranit, musíte je znovu odstranit a znovu odstranit, abyste se mohli trvale odstranit.

Je důležité si uvědomit, že jakmile je obnovitelné odstranění zakázané, funkce se zakáže pro všechny typy úloh. Nemůžete například zakázat obnovitelné odstranění jenom pro SQL Server nebo SAP HANA databáze a přitom ponechat povolený virtuální počítač ve stejném trezoru. Pro podrobné řízení můžete vytvořit samostatné trezory.

### <a name="disabling-soft-delete-using-azure-portal"></a>Zákaz obnovitelného odstranění pomocí Azure Portal

Chcete-li zakázat obnovitelné odstranění, použijte následující postup:

1. V Azure Portal přejdete do svého trezoru a pak přejdete na **Settings**  ->  **vlastnosti**nastavení.
2. V podokně Vlastnosti vyberte možnost aktualizace **nastavení zabezpečení**  ->  **Update**.  
3. V podokně nastavení zabezpečení v části **obnovitelné odstranění**vyberte **Zakázat**.

![Zakázat obnovitelné odstranění](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Zákaz obnovitelného odstranění pomocí Azure PowerShell

> [!IMPORTANT]
> Verze AZ. RecoveryServices, která je nutná k použití obnovitelného odstranění pomocí Azure PowerShell, je minimální 2.2.0. Použijte ```Install-Module -Name Az.RecoveryServices -Force``` k získání nejnovější verze.

Pokud ho chcete zakázat, použijte rutinu [set-AzRecoveryServicesVaultBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) prostředí PowerShell.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Zákaz obnovitelného odstranění pomocí REST API

Pokud chcete zakázat funkce obnovitelného odstranění pomocí REST API, přečtěte si postup uvedený [tady](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Trvale se odstraňují obnovitelné položky odstraněné zálohy

Zálohovaná data v tichém odstraněném stavu před zakázáním této funkce zůstanou v tichém stavu odstranění. Pokud je chcete trvale odstranit, odstraňte je znovu a znovu je odstraňte, abyste je mohli trvale odstranit.

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal

Postupujte takto:

1. Chcete-li [Zakázat obnovitelné odstranění](#enabling-and-disabling-soft-delete), postupujte podle pokynů.

2. V Azure Portal přejdete do trezoru, přejdete na **zálohované položky**a zvolíte položku neodstraněno.

   ![Zvolit dostraněnou položku](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Vyberte možnost zrušit **odstranění**.

   ![Zvolit možnost zrušit odstranění](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Zobrazí se okno. Vyberte možnost zrušit **odstranění**.

   ![Vybrat zrušit odstranění](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Chcete-li trvale odstranit data zálohy, vyberte možnost **Odstranit zálohovaná data** .

   ![Zvolit odstranění zálohovaných dat](/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Zadejte název zálohované položky, abyste potvrdili, že chcete body obnovení odstranit.

   ![Zadejte název zálohované položky.](/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Chcete-li odstranit data zálohy pro položku, vyberte možnost **Odstranit**. Zpráva s oznámením vám poskytne informace o odstranění zálohovaných dat.

### <a name="using-azure-powershell"></a>Použití Azure Powershell

Pokud byly položky odstraněny před tím, než byl zakázán částečný-DELETE, budou v neodstraněném stavu. Aby je bylo možné okamžitě odstranit, operace odstranění musí být obrácená a následně provedena znovu.

Identifikujte položky, které jsou ve stavu nepodmíněného odstranění.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Pak změňte operaci odstranění, kterou jste provedli v případě, že bylo povoleno obnovitelné odstranění.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Vzhledem k tomu, že obnovitelné odstranění je teď zakázané, bude výsledkem operace odstranění okamžité odebrání zálohovaných dat.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Pomocí rozhraní REST API

Pokud byly položky odstraněny před tím, než byl zakázán částečný-DELETE, budou v neodstraněném stavu. Aby je bylo možné okamžitě odstranit, operace odstranění musí být obrácená a následně provedena znovu.

1. Nejprve vraťte operace odstranění se kroky uvedenými [zde](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion).
2. Pak pomocí REST API podle kroků uvedených [tady](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)zakažte funkci obnovitelného odstranění.
3. Pak zálohy odstraňte pomocí REST API, jak je uvedeno [zde](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Musím u každého trezoru povolit funkci obnovitelného odstranění?

Ne, je ve výchozím nastavení integrovaná a povolená pro všechny trezory Recovery Services.

### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-the-delete-operation-is-complete"></a>Můžu nakonfigurovat počet dní, po které se po dokončení operace odstranění budou uchovávat data ve stavu tichého odstranění?

Ne, je po operaci odstranění vyřešeno 14 dní dalšího uchování.

### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Musím platit náklady za další 14 dní v tomto případě?

Ne, toto 14. další uchovávání dat v rámci funkce obnovitelného odstranění přináší náklady zdarma.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Můžu provést operaci obnovení, když jsou moje data ve stavu tichého odstranění?

Ne, chcete-li obnovit, je nutné obnovit neodstraněný prostředek. Operace zrušení odstranění vrátí prostředek zpátky do **stavu zastavit ochranu se stavem zachovat data** , kde se můžete kdykoli vrátit k libovolnému bodu v čase. Systém uvolňování paměti zůstává v tomto stavu pozastaven.

### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Budou se moje snímky řídit stejným životním cyklem jako body obnovení v trezoru?

Ano.

### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Jak můžu znovu aktivovat naplánované zálohy u prostředku odstraněného z nějakého obnovitelného?

Operace zrušit odstranění, po které následuje operace obnovení, bude chránit prostředek znovu. Operace obnovení přidružuje zásadu zálohování, která spustí naplánované zálohy s vybranou dobou uchovávání. Systém uvolňování paměti se také spouští ihned po dokončení operace obnovení. Pokud chcete provést obnovení z bodu obnovení, který uplynul po datu vypršení platnosti, doporučujeme to provést před aktivací operace obnovení.

### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Můžu odstranit svůj trezor, pokud jsou v trezoru obnovitelné odstraněné položky?

Trezor Recovery Services nelze odstranit, pokud jsou v trezoru nějaké zálohované položky. Odstraněné položky jsou trvale odstraněny 14 dnů po operaci odstranění. Pokud nemůžete počkat na 14 dní, pak [zakažte obnovitelné odstranění](#enabling-and-disabling-soft-delete), odstrante obnovitelné odstraněné položky a znovu je odstraňte, aby se trvale odstranily. Po zajistěte, aby nedocházelo k žádným chráněným položkám a žádné tiché odstraněné položky, Trezor je možné odstranit.  

### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Můžu odstranit data starší než 14 dní, po jejichž uplynutí bude období obnovitelného odstranění?

Ne. Odstranit neodstraněné položky nelze vynutit. Automaticky se odstraní po 14 dnech. Tato funkce zabezpečení má povolenou ochranu zálohovaných dat před náhodnými nebo škodlivými odstraněními.  Před provedením jakékoli jiné akce s položkou byste měli počkat na 14 dní.  Odstraněné položky se nebudou účtovat.  Pokud potřebujete znovu nastavit ochranu položek označených pro obnovitelné odstranění do 14 dní v novém trezoru, obraťte se na podporu Microsoftu.

### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Může dojít k obnovitelnému odstranění operací v PowerShellu nebo rozhraní příkazového řádku?

Obnovitelné operace odstranění lze provádět pomocí prostředí PowerShell. V současné době se rozhraní příkazového řádku nepodporuje.

## <a name="next-steps"></a>Další kroky

- [Přehled funkcí zabezpečení v Azure Backup](security-overview.md)
