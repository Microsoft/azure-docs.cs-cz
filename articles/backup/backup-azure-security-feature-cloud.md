---
title: Funkce zabezpečení, které vám pomůžou chránit cloudové úlohy
description: Naučte se používat funkce zabezpečení v Azure Backup k zajištění většího zabezpečení záloh.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: f89d9ab3ef373ecd9a7d15ef4ec9b2109750f8de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248070"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Funkce zabezpečení, které vám pomůžou chránit cloudové úlohy, které používají Azure Backup

Problémy se zabezpečením, jako je malware, ransomwarem a vniknutí, se zvyšují. Tyto problémy se zabezpečením můžou být nákladné, s ohledem na peníze i data. Pro ochranu proti takovým útokům Azure Backup nyní poskytuje funkce zabezpečení, které vám pomůžou chránit zálohovaná data i po odstranění.

Jednou z těchto funkcí je obnovitelné odstranění. Pomocí obnovitelného odstranění, a to i v případě, že škodlivý objekt actor odstraní zálohu virtuálního počítače (nebo se data záloh nechtěně odstraní), zálohovaná data se uchovávají po dobu 14 dalších dnů, což umožňuje obnovení této zálohované položky bez ztráty dat. Další 14 dní uchovávání zálohovaných dat ve stavu "obnovitelné odstranění" neúčtují žádné náklady na zákazníka. Azure také šifruje všechna zálohovaná data v klidovém režimu pomocí [šifrování služby Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) k dalšímu zabezpečení vašich dat.

Ochrana před odstraněním pro virtuální počítače Azure je všeobecně dostupná.

>[!NOTE]
>Obnovitelné odstranění pro SQL Server na virtuálním počítači Azure a obnovitelné odstranění pro SAP HANA v úlohách virtuálních počítačů Azure je teď dostupné ve verzi Preview.<br>
>Pokud si chcete zaregistrovat verzi Preview, napište nám na AskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>Obnovitelné odstranění

### <a name="soft-delete-for-vms"></a>Obnovitelné odstranění pro virtuální počítače

Obnovitelné odstranění pro virtuální počítače chrání zálohy virtuálních počítačů před nezamýšleným odstraněním. I po odstranění záloh se tyto dny uchovávají ve stavu obnovitelného odstranění po dobu 14 dalších dnů.

> [!NOTE]
> Obnovitelné odstranění chrání pouze Odstraněná data zálohy. Pokud se virtuální počítač odstraní bez zálohy, funkce obnovitelného odstranění nebude data uchovávat. Všechny prostředky by měly být chráněné pomocí Azure Backup, aby se zajistila plná odolnost.
>

### <a name="supported-regions"></a>Podporované oblasti

Obnovitelné odstranění se v současné době podporuje v Středozápadní USA, Východní Asie, Kanadě – střed, Kanada – východ, Francie – střed, Francie – jih, Jižní Korea, Korea – jih, Velká Británie – jih, Velká Británie – západ, Austrálie – východ, Austrálie – jihovýchod, Severní Evropa, Západní USA, západní USA 2, Střed USA, jih Východní Asie, Střed USA – sever, Střed USA – jih, Japonsko – východ, Japonsko – západ, Indie – jih, Indie – střed, Indie – západ, Východní USA 2, Švýcarsko – sever, Švýcarsko – západ a všechny národní oblasti.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Obnovitelné odstranění pro virtuální počítače pomocí Azure Portal

1. K odstranění zálohovaných dat virtuálního počítače je třeba zastavit zálohování. V Azure Portal přejděte do trezoru služby Recovery Services, klikněte pravým tlačítkem na zálohovanou položku a vyberte **Zastavit zálohování**.

   ![Snímek obrazovky Azure Portal zálohované položky](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. V následujícím okně budete mít možnost odstranit nebo zachovat data záloh. Pokud zvolíte možnost **Odstranit zálohovaná data** a pak **Zastavit zálohování**, zálohování virtuálního počítače se trvale neodstraní. Místo toho se zálohovaná data uchovávají 14 dnů ve stavu tichého odstranění. Pokud je zvolená možnost **Odstranit zálohovaná data** , pošle se na nakonfigurované ID e-mailu e-mailové upozornění s informací o uživateli, že pro zálohovaná data zbývá 14 dní rozšířené uchovávání dat. E-mailová výstraha se také pošle na 12. den, který vychází ze zbývajících dvou dnů, než se odobnovení Odstraněná data. Odstranění se odsadí do patnáctého dne, kdy dojde k trvalému odstranění a pošle se poslední e-mailová výstraha informující o trvalém odstranění dat.

   ![Snímek obrazovky Azure Portal, zastavení záložní obrazovky](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Během těchto 14 dnů se v Recovery Services trezoru u obnovitelného odstraněného virtuálního počítače zobrazí červená ikona "slabého odstranění" vedle něho.

   ![Snímek obrazovky Azure Portal, virtuální počítač ve stavu tichého odstranění](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Pokud se v trezoru nacházejí nějaké záložní zálohované položky, trezor se v této době nedá odstranit. Zkuste prosím odstranit trezor, až se zálohované položky trvale odstraní, a v trezoru se neodstraní žádná položka v tichém odstraněném stavu.

4. Aby bylo možné obnovit částečný odstraněný virtuální počítač, musí být nejprve obnoven. Pokud ho chcete obnovit, vyberte virtuální počítač s příjemným odstraněnou a potom vyberte možnost **obnovit**.

   ![Snímek obrazovky Azure Portal, odstranění virtuálního počítače](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Zobrazí se okno upozornění, že pokud je zvoleno možnost zrušit odstranění, všechny body obnovení pro virtuální počítač budou neodstraněny a budou k dispozici pro operaci obnovení. Virtuální počítač se zachová ve stavu zastavit ochranu se zachováním dat a zálohy se pozastaví a zálohovaná data se trvale zachovají beze zásad zálohování.

   ![Snímek obrazovky Azure Portal, potvrďte odstranění virtuálního počítače.](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   V tuto chvíli můžete virtuální počítač obnovit taky tak, že z vybraného bodu obnovení vyberete **obnovit virtuální počítač** .  

   ![Snímek obrazovky Azure Portal, obnovení možnosti virtuálního počítače](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Systém uvolňování paměti spustí a vyčistí body obnovení s vypršenou platností až poté, co uživatel provede operaci **obnovení zálohy** .

5. Po dokončení procesu zrušení odstranění se stav vrátí do části zastavit zálohování s daty zachovat a pak můžete zvolit **pokračovat v zálohování**. Operace **obnovit zálohování** vrátí zpět zálohovanou položku v aktivním stavu, která je přidružená k zásadám zálohování vybraným uživatelem, který definuje plány zálohování a uchovávání.

   ![Snímek obrazovky Azure Portal, obnovit možnost zálohování](./media/backup-azure-security-feature-cloud/resume-backup.png)

Tento vývojový diagram zobrazuje různé kroky a stavy zálohované položky, když je povolené obnovitelné odstranění:

![Životní cyklus záložní položky odstraněné položky](./media/backup-azure-security-feature-cloud/lifecycle.png)

Další informace najdete v části [Nejčastější dotazy](backup-azure-security-feature-cloud.md#frequently-asked-questions) níže.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Obnovitelné odstranění pro virtuální počítače pomocí Azure PowerShellu

> [!IMPORTANT]
> Verze AZ. RecoveryServices, která je nutná k použití obnovitelného odstranění pomocí Azure PS, je min 2.2.0. Nejnovější verzi získáte pomocí ```Install-Module -Name Az.RecoveryServices -Force```.

Jak je uvedeno výše pro Azure Portal, sekvence kroků je stejná i při použití Azure PowerShellu.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Odstranění zálohované položky pomocí Azure PowerShellu

Odstraňte zálohovanou položku pomocí rutiny [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

' DeleteState ' zálohované položky se změní z ' NotDeleted ' na ' ToBeDeleted '. Data zálohy se uchovávají 14 dnů. Chcete-li obnovit operaci odstranění, je třeba provést příkaz zpět a odstranit.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Zrušení operace odstranění pomocí Azure PowerShellu

Nejdřív načtěte příslušnou zálohovanou položku, která je ve stavu tichého odstranění (tj., chystá se odstranit).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Pak proveďte operaci vrácení zpět se změnami pomocí rutiny [Undo-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

' DeleteState ' zálohované položky se vrátí na ' NotDeleted '. Ochrana se ale pořád zastaví. Chcete-li znovu povolit ochranu, musíte [pokračovat v zálohování](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) .

### <a name="soft-delete-for-vms-using-rest-api"></a>Obnovitelné odstranění pro virtuální počítače pomocí REST API

- Odstraňte zálohy pomocí REST API, jak je uvedeno [zde](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Pokud si uživatel přeje tyto operace odstranění zrušit, přečtěte si postup uvedený [tady](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).

## <a name="disabling-soft-delete"></a>Zákaz obnovitelného odstranění

Obnovitelné odstranění je ve výchozím nastavení povolené u nově vytvořených trezorů za účelem ochrany zálohovaných dat před náhodnými nebo škodlivými odstraněními.  Zakázání této funkce se nedoporučuje. Jediným případem, kdy byste měli zvážit zakázání obnovitelného odstranění, je, že plánujete přesunutí chráněných položek do nového trezoru a nemůžete počkat 14 dní před odstraněním a obnovením ochrany (například v testovacím prostředí). Tuto funkci může zakázat pouze vlastník trezoru. Pokud tuto funkci zakážete, všechna budoucí odstranění chráněných položek budou mít za následek okamžité odebrání bez možnosti obnovení. Zálohovaná data, která existují v tichém odstraněném stavu před zakázáním této funkce, zůstanou v tichém odstraněném stavu po dobu 14 dnů. Pokud chcete tyto okamžité odstranění trvale odstranit, musíte je znovu odstranit a znovu odstranit, abyste se mohli trvale odstranit.

### <a name="disabling-soft-delete-using-azure-portal"></a>Zákaz obnovitelného odstranění pomocí Azure Portal

Chcete-li zakázat obnovitelné odstranění, použijte následující postup:

1. V Azure Portal přejdete do svého trezoru a pak přejdete na **nastavení** -> **vlastnosti**.
2. V podokně Vlastnosti vyberte **nastavení zabezpečení** -> **aktualizovat**.  
3. V podokně nastavení zabezpečení v části **obnovitelné odstranění**vyberte **Zakázat**.

![Zakázat obnovitelné odstranění](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Vypnutí obnovitelného odstranění pomocí Azure PowerShellu

> [!IMPORTANT]
> Verze AZ. RecoveryServices, která je nutná k použití obnovitelného odstranění pomocí Azure PS, je min 2.2.0. Nejnovější verzi získáte pomocí ```Install-Module -Name Az.RecoveryServices -Force```.

Pokud ho chcete zakázat, použijte rutinu [set-AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS.

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

Postupujte následovně:

1. Chcete-li [Zakázat obnovitelné odstranění](#disabling-soft-delete), postupujte podle pokynů.
2. V Azure Portal přejdete do trezoru, přejdete na **položku zálohované položky**a zvolíte odstraněný virtuální počítač.

   ![Výběr obnovitelného odstraněného virtuálního počítače](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Vyberte možnost zrušit **odstranění**.

   ![Zvolit možnost zrušit odstranění](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Zobrazí se okno. Vyberte možnost zrušit **odstranění**.

   ![Vybrat zrušit odstranění](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Chcete-li trvale odstranit data zálohy, vyberte možnost **Odstranit zálohovaná data** .

   ![Zvolit odstranění zálohovaných dat](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Zadejte název zálohované položky, abyste potvrdili, že chcete body obnovení odstranit.

   ![Zadejte název zálohované položky.](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Chcete-li odstranit data zálohy pro položku, vyberte možnost **Odstranit**. Zpráva s oznámením vám poskytne informace o odstranění zálohovaných dat.

### <a name="using-azure-powershell"></a>Používání Azure PowerShellu

Pokud byly položky odstraněny před vypnutím obnovitelného odstranění, pak budou v neodstraněném stavu. Aby je bylo možné okamžitě odstranit, operace odstranění musí být obrácená a následně provedena znovu.

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

Pokud byly položky odstraněny před vypnutím obnovitelného odstranění, pak budou v neodstraněném stavu. Aby je bylo možné okamžitě odstranit, operace odstranění musí být obrácená a následně provedena znovu.

1. Nejprve vraťte operace odstranění se kroky uvedenými [zde](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).
2. Pak pomocí REST API podle kroků uvedených [tady](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)zakažte funkci obnovitelného odstranění.
3. Pak zálohy odstraňte pomocí REST API, jak je uvedeno [zde](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="encryption"></a>Šifrování

### <a name="encryption-of-backup-data-using-microsoft-managed-keys"></a>Šifrování zálohovaných dat pomocí spravovaných klíčů společnosti Microsoft

Zálohovaná data se šifrují automaticky pomocí Azure Storage šifrování. Šifrování chrání vaše data a pomáhá splnit závazky zabezpečení a dodržování předpisů vaší organizace. Data se šifrují a dešifrují transparentně pomocí 256 šifrování AES, což je jedna z nejúčinnějších šifrovacích šifr, která jsou kompatibilní se standardem FIPS 140-2. Šifrování Azure Storage se v systému Windows podobá šifrování BitLockeru.

V rámci Azure jsou data přenášená mezi službou Azure Storage a trezorem chráněná protokolem HTTPS. Tato data zůstávají v páteřní síti Azure.

Další informace najdete v tématu [Azure Storage šifrování pro](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)neaktivní neaktivní data. V tématu [Azure Backup – Nejčastější dotazy](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) , které vám pomohou zodpovědět případné dotazy týkající se šifrování.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Šifrování zálohovaných dat pomocí klíčů spravovaných zákazníkem

Při zálohování Azure Virtual Machines máte také možnost šifrovat zálohovaná data v Recovery Services trezoru pomocí šifrovacích klíčů uložených v Azure Key Vault.

>[!NOTE]
>Tato funkce je aktuálně v brzkém použití. Pokud chcete data záloh šifrovat pomocí zákaznických klíčů, vyplňte [Tento průzkum](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) . Všimněte si, že možnost použití této funkce podléhá schválení Azure Backup služby.

### <a name="backup-of-managed-disk-vm-encrypted-using-customer-managed-keys"></a>Zálohování virtuálního počítače se spravovaným diskem zašifrovaným pomocí klíčů spravovaných zákazníkem

Azure Backup umožňuje zálohovat Azure Virtual Machines obsahující disky šifrované pomocí zákaznických spravovaných klíčů. Podrobnosti najdete v tématu [šifrování spravovaných disků pomocí klíčů spravovaných zákazníkem](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys).

### <a name="backup-of-encrypted-vms"></a>Zálohování šifrovaných virtuálních počítačů

Pomocí služby Azure Backup můžete zálohovat a obnovovat virtuální počítače Azure s Windows nebo Linuxem pomocí šifrovaných disků. Pokyny najdete v tématu [zálohování a obnovení šifrovaných virtuálních počítačů pomocí Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="private-endpoints"></a>Soukromé koncové body

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>Další funkce zabezpečení

### <a name="protection-of-azure-backup-recovery-points"></a>Ochrana Azure Backup bodů obnovení

Účty úložiště používané trezory služby Recovery Services jsou izolované a uživatelé k nim nemají k dispozici žádné škodlivé účely. Přístup je povolený jenom prostřednictvím operací správy Azure Backup, jako je například obnovení. Tyto operace správy se řídí pomocí Access Control na základě rolí (RBAC).

Další informace najdete v tématu [použití Access Control na základě rolí ke správě Azure Backup bodů obnovení](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="for-soft-delete"></a>Pro obnovitelné odstranění

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Musím u každého trezoru povolit funkci obnovitelného odstranění?

Ne, ve výchozím nastavení je pro všechny trezory služby Recovery Services postavené a povolené.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Můžu nakonfigurovat počet dní, po které se po dokončení operace odstranění budou uchovávat data ve stavu tichého odstranění?

Ne, je po operaci odstranění pevně nastavená na 14 dní dalšího uchování.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Musím platit náklady za další 14 dní v tomto případě?

Ne, toto 14. další uchovávání dat se zaznamená zdarma jako součást funkcí obnovitelného odstranění.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Můžu provést operaci obnovení, když jsou moje data ve stavu tichého odstranění?

Ne, chcete-li obnovit, je nutné obnovit neodstraněný prostředek. Operace zrušení odstranění vrátí prostředek zpátky do **stavu zastavit ochranu se stavem zachovat data** , kde se můžete kdykoli vrátit k libovolnému bodu v čase. Systém uvolňování paměti zůstává v tomto stavu pozastaven.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Budou se moje snímky řídit stejným životním cyklem jako body obnovení v trezoru?

Ano.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Jak můžu znovu aktivovat naplánované zálohy u prostředku odstraněného z nějakého obnovitelného?

Operace zrušit odstranění, po které následuje operace pokračovat, bude chránit prostředek znovu. Operace obnovení přidruží zásadu zálohování, která spustí plánované zálohy s vybranou dobou uchovávání. Systém uvolňování paměti se také spouští ihned po dokončení operace obnovení. Pokud chcete provést obnovení z bodu obnovení, který je po datu vypršení platnosti, doporučujeme to provést před aktivací operace obnovení.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Můžu odstranit svůj trezor, pokud jsou v trezoru obnovitelné odstraněné položky?

Trezor Recovery Services nelze odstranit, pokud jsou v trezoru nějaké zálohované položky. Odstraněné položky jsou trvale odstraněny 14 dnů po operaci odstranění. Pokud nemůžete počkat na 14 dní, pak [zakažte obnovitelné odstranění](#disabling-soft-delete), odstrante obnovitelné odstraněné položky a znovu je odstraňte, aby se trvale odstranily. Po zajistěte, aby nedocházelo k žádným chráněným položkám a žádné tiché odstraněné položky, Trezor je možné odstranit.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Můžu odstranit data starší než 14 dní, po jejichž uplynutí bude období obnovitelného odstranění?

Ne. Nelze odstranit dočasně odstraněné položky, budou automaticky odstraněny po 14 dnech. Tato funkce zabezpečení má povolenou ochranu zálohovaných dat před náhodnými nebo škodlivými odstraněními.  Měli byste počkat na 14 dní před provedením jakékoli jiné akce na virtuálním počítači.  Odstraněné položky se nebudou účtovat.  Pokud potřebujete znovu zapnout ochranu virtuálních počítačů označených pro obnovitelné odstranění do 14 dní do nového trezoru, obraťte se na podporu Microsoftu.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Může dojít k obnovitelnému odstranění operací v PowerShellu nebo rozhraní příkazového řádku?

Obnovitelné operace odstranění lze provádět pomocí [prostředí PowerShell](#soft-delete-for-vms-using-azure-powershell). V současné době se rozhraní příkazového řádku nepodporuje.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Podporuje se obnovitelné odstranění pro jiné cloudové úlohy, jako je SQL Server ve virtuálních počítačích Azure a SAP HANA na virtuálních počítačích Azure?

Ne. V současné době je obnovitelné odstranění podporováno pouze pro virtuální počítače Azure.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [ovládacích prvcích zabezpečení pro Azure Backup](backup-security-controls.md).
