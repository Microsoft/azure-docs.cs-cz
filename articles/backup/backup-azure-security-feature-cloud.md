---
title: Funkce zabezpečení pomáhají chránit cloudové úlohy
description: Naučte se používat funkce zabezpečení v Azure Backup, aby se zálohování bezpečnější.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: bd7c86e18114513a264a0f9252589533fb7ff2d3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668743"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Funkce zabezpečení pomáhají chránit cloudové úlohy, které používají Azure Backup

Obavy z bezpečnostních problémů, jako je malware, ransomware a vniknutí, se zvyšují. Tyto bezpečnostní otázky mohou být nákladné, a to jak z hlediska peněz, tak z hlediska dat. Služba Azure Backup nyní poskytuje funkce zabezpečení, které pomáhají chránit záložní data i po odstranění, aby se před takovými útoky chránily.

Jednou z takových funkcí je měkké odstranění. Při odstranění pomocí softwaru, i když škodlivý objekt actor odstraní zálohu virtuálního počítače (nebo se omylem odstraní data zálohy), budou záložní data zachována po dobu dalších 14 dnů, což umožní obnovení této položky zálohování bez ztráty dat. Dalších 14 dnů uchovávání záložních dat ve stavu "obnovitelné odstranění" nevznikají žádné náklady pro zákazníka. Azure také šifruje všechna zálohovaná data v klidovém stavu pomocí [šifrování služby Storage Service pro](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) další zabezpečení dat.

Ochrana proti softwarovým odstraněním pro virtuální počítače Azure je obecně dostupná.

>[!NOTE]
>Obnovitelné odstranění pro SQL server v Azure VM a obnovitelné odstranění pro SAP HANA v úlohách virtuálních virtuálních počítače Azure je teď dostupné ve verzi Preview.<br>
>Chcete-li se zaregistrovat do náhledu, napište nám naAskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>Obnovitelné odstranění

### <a name="soft-delete-for-vms"></a>Obnovitelné odstranění pro virtuální měna

Obnovitelné odstranění pro virtuální počítače chrání zálohy virtuálních počítačů před nechtěnou odstranění. I po odstranění záloh jsou zachovány ve stavu obnovitelného odstranění po dobu dalších 14 dnů.

> [!NOTE]
> Obnovitelné odstranění chrání pouze odstraněná záložní data. Pokud virtuální počítače je odstraněn bez zálohy, funkce obnovitelného odstranění nezachová data. Všechny prostředky by měly být chráněny pomocí služby Azure Backup, aby byla zajištěna plná odolnost.
>

### <a name="supported-regions"></a>Podporované oblasti

Obnovitelné odstranění je v současné době podporováno v západních středních USA, východní Asii, Kanadě – střed, Kanadě – východ, Francie – střed, Francie – střed, Koreji – Jih, Velká Británie – jih, Velká Británie – západ, Austrálie – východ, Austrálie – jihovýchod, Severní Evropa, Západní USA, Západní USA2, Střed USA, Jihovýchodní Asie, Severní Střed USA, Jižní Střed USA, Japonsko – západ, Indie – jih, Indie – západ, Východní USA 2 , Švýcarsko Sever, Švýcarsko Západ, Norsko Západ, Norsko východ a všechny národní regiony.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Obnovitelné odstranění pro virtuální počítače pomocí portálu Azure

1. Chcete-li odstranit záložní data virtuálního počítače, musí být záloha zastavena. Na webu Azure Portal přejděte do trezoru služeb pro obnovení, klikněte pravým tlačítkem myši na položku zálohy a zvolte **Zastavit zálohování**.

   ![Snímek obrazovky se zálohovacími položkami portálu Azure Portal](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. V následujícím okně budete mít možnost odstranit nebo zachovat záložní data. Pokud zvolíte **Odstranit záložní data** a pak zastavit **zálohování**, záloha virtuálního počítače se trvale neodstraní. Zálohovací data budou spíše zachována po dobu 14 dnů v obnovitelném odstraněném stavu. Pokud je vybrána možnost **Odstranit záložní data,** je odeslána e-mailová výstraha odstranění na konfigurované ID e-mailu informující uživatele, že 14 dní zbývá rozšířené uchovávání pro záložní data. Také e-mailové upozornění je odesláno na 12 den informující, že zbývají další dva dny na oživení smazaných dat. Smazání je odloženo do 15.

   ![Snímek obrazovky s portálem Azure, obrazovka Zastavit zálohování](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Během těchto 14 dnů se v trezoru služby Recovery Services zobrazí měkký odstraněný virtuální virtuální ms s červenou ikonou "obnovitelného odstranění".

   ![Snímek obrazovky s portálem Azure, virtuální počítač ve stavu obnovitelného odstranění](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Pokud jsou v úschovně přítomny všechny položky zálohování s měkkým odstraněním, nelze úložiště v daném okamžiku odstranit. Po trvalém odstranění položek zálohy zkuste odstranit úschovnu a v úložišti nezůstane žádná položka v obnovitelném odstraněném stavu.

4. Chcete-li obnovit obnovitelné odstraněné virtuální ms, musí být nejprve zrušen. Chcete-li obnovit, zvolte pevně odstraněný virtuální ms a pak vyberte možnost **Zrušit smazání**.

   ![Snímek obrazovky s portálem Azure, Undelete VM](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Zobrazí se okno s upozorněním, že pokud je vybráno zrušení, všechny body obnovení pro virtuální ho stomatož budou zrušeny a budou k dispozici pro provedení operace obnovení. Virtuální počítače se uchovají ve stavu "stop protection with retain data" se zálohami pozastavenými a záložními daty uchovávanými navždy bez účinnosti zásad zálohování.

   ![Snímek obrazovky s portálem Azure, Potvrdit nesmazání virtuálního počítače](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   V tomto okamžiku můžete také obnovit virtuální ho správě výběrem **obnovení virtuálního virtuálního virtuálního mísy** z vybraného bodu obnovení.  

   ![Snímek obrazovky s portálem Azure, možnost Obnovení virtuálního počítače](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Systém uvolňování paměti se spustí a vyčistí body obnovení, jejichž platnost vypršela, až poté, co uživatel provede operaci **obnovení zálohování.**

5. Po dokončení procesu obnovení se stav vrátí na "Zastavit zálohování s uchováním dat" a pak můžete zvolit **pokračovat v zálohování**. Operace **Obnovení zálohování** přináší zpět položku zálohování v aktivním stavu, spojené se zásadami zálohování vybrané uživatelem definující plány zálohování a uchovávání.

   ![Snímek obrazovky s portálem Azure, možnost Pokračovat v zálohování](./media/backup-azure-security-feature-cloud/resume-backup.png)

Tento vývojový diagram zobrazuje různé kroky a stavy položky zálohování, pokud je povoleno obnovitelné odstranění:

![Životní cyklus položky zálohování s měkkým odstraněním](./media/backup-azure-security-feature-cloud/lifecycle.png)

Další informace naleznete v části [Nejčastější dotazy](backup-azure-security-feature-cloud.md#frequently-asked-questions) níže.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Obnovitelné odstranění pro virtuální počítače pomocí Azure PowerShellu

> [!IMPORTANT]
> Verze Az.RecoveryServices, která je vyžadována k použití obnovitelného odstranění pomocí služby Azure PS, je min 2.2.0. Slouží ```Install-Module -Name Az.RecoveryServices -Force``` k získání nejnovější verze.

Jak je uvedeno výše pro Azure Portal, posloupnost kroků je stejná při používání Azure PowerShellu také.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Odstranění položky zálohování pomocí Azure PowerShellu

Odstraňte položku zálohy pomocí rutiny [Ps Disable-AzRecoveryServicesBackupProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0)

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

"DeleteState" položky zálohy se změní z "NotDeleted" na "ToBeDeleted". Záložní data budou uchovávána po dobu 14 dnů. Pokud chcete vrátit operaci odstranění, pak zpět-delete by měla být provedena.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Zrušení operace odstranění pomocí Azure PowerShellu

Nejprve načíst příslušnou položku zálohy, která je ve stavu obnovitelného odstranění (to znamená, že má být odstraněn).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Potom proveďte operaci zpětného odstranění pomocí rutiny [PS Recovery-AzRecoveryServicesBackupItemDeletion.](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0)

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

"DeleteState" položky zálohování se vrátí na 'NotDeleted'. Ale ochrana je stále zastavena. [Chcete-li](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) znovu povolit ochranu, obnovte zálohování.

### <a name="soft-delete-for-vms-using-rest-api"></a>Obnovitelné odstranění pro virtuální aplikace pomocí rozhraní REST API

- Odstraňte zálohy pomocí rozhraní REST API, jak je uvedeno [zde](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Pokud si uživatel přeje tyto operace odstranění vrátit, přečtěte si kroky uvedené [zde](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).

## <a name="disabling-soft-delete"></a>Zakázání obnovitelného odstranění

Obnovitelné odstranění je ve výchozím nastavení povoleno u nově vytvořených trezorů, které chrání záložní data před náhodnými nebo škodlivými odstraněními.  Zakázání této funkce se nedoporučuje. Jediné okolnosti, kdy byste měli zvážit zakázání obnovitelného odstranění, je, pokud plánujete přesunutí chráněných položek do nového trezoru a nemůžete čekat 14 dní požadovaných před odstraněním a opětovnou ochranou (například v testovacím prostředí).) Tuto funkci může zakázat pouze vlastník trezoru. Pokud tuto funkci zakážete, všechny budoucí odstranění chráněných položek bude mít za následek okamžité odebrání, bez možnosti obnovení. Záložní data, která existují v obnovitelném odstraněném stavu před zakázáním této funkce, zůstanou v obnovitelném odstraněném stavu po dobu 14 dnů. Pokud chcete trvale odstranit okamžitě, pak je třeba je obnovit a odstranit znovu, abyste se trvale odstranili.

### <a name="disabling-soft-delete-using-azure-portal"></a>Zakázání obnovitelného odstranění pomocí portálu Azure

Chcete-li zakázat odstranění pomocí funkce Soft Delete, postupujte takto:

1. Na webu Azure Portal přejděte do trezoru a přejděte na **Nastavení** -> **vlastností**.
2. V podokně vlastností vyberte**možnost Aktualizace** **nastavení** -> zabezpečení .  
3. V podokně nastavení zabezpečení vyberte v části **Obnovitelné odstranění**možnost **Zakázat**.

![Zakázat obnovitelné odstranění](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Zakázání obnovitelného odstranění pomocí Azure PowerShellu

> [!IMPORTANT]
> Verze Az.RecoveryServices, která je vyžadována k použití obnovitelného odstranění pomocí služby Azure PS, je min 2.2.0. Slouží ```Install-Module -Name Az.RecoveryServices -Force``` k získání nejnovější verze.

Chcete-li tuto funkci zakázat, použijte rutinu [PS Set-AzRecoveryServicesVaultBackupProperty.](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0)

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Zakázání obnovitelného odstranění pomocí rozhraní REST API

Chcete-li zakázat funkci obnovitelného odstranění pomocí rozhraní REST API, podívejte se na kroky uvedené [zde](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Trvalé odstranění neodstraněných položek zálohování

Záložní data v obnovitelném odstraněném stavu před zakázáním této funkce zůstanou v obnovitelném odstraněném stavu. Pokud chcete trvale odstranit okamžitě, pak je zrušte a odstraňte znovu, abyste se trvale odstranili.

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal

Postupujte následovně:

1. Chcete-li [zakázat odstranění pomocí funkce soft delete,](#disabling-soft-delete)postupujte takto .
2. Na webu Azure Portal přejděte do trezoru, přejděte na **Položky zálohování**a zvolte obnovitelné odstraněné virtuální počítače.

   ![Volba obnovitelného odstraněný virtuálního virtuálního mísy](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Vyberte možnost **Zrušit smazání**.

   ![Zvolte Zrušit smazání.](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Zobrazí se okno. Vyberte **zrušit smazání**.

   ![Vybrat Zrušit odstranění](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Zvolte **Odstranit záložní data,** chcete-li trvale odstranit záložní data.

   ![Zvolte Odstranit záložní data.](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Zadejte název položky zálohy a potvrďte, že chcete odstranit body obnovení.

   ![Zadejte název položky zálohy.](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Chcete-li odstranit záložní data pro položku, vyberte **odstranit**. Oznámení vás dozví, že záložní data byla odstraněna.

### <a name="using-azure-powershell"></a>Použití Azure Powershell

Pokud byly položky odstraněny před zakázáním obnovitelného odstranění, budou ve stavu obnovitelného odstranění. Chcete-li je okamžitě odstranit, je třeba operaci odstranění vrátit zpět a provést znovu.

Identifikujte položky, které jsou ve stavu obnovitelného odstranění.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Potom stornovat operaci odstranění, která byla provedena, když bylo povoleno obnovitelné odstranění.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Vzhledem k tomu, že obnovitelné odstranění je nyní zakázáno, operace odstranění bude mít za následek okamžité odstranění záložních dat.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Pomocí rozhraní REST API

Pokud byly položky odstraněny před zakázáním obnovitelného odstranění, budou ve stavu obnovitelného odstranění. Chcete-li je okamžitě odstranit, je třeba operaci odstranění vrátit zpět a provést znovu.

1. Nejprve vrátit operace odstranění pomocí kroků uvedených [zde](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).
2. Potom zakažte funkci obnovitelného odstranění pomocí rozhraní REST API pomocí [kroků uvedených zde](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).
3. Potom odstraňte zálohy pomocí rozhraní REST API, jak je uvedeno [zde](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="encryption"></a>Šifrování

Všechna zálohovaná data se při ukládání v cloudu automaticky zašifrují pomocí šifrování Azure Storage, které vám pomůže splnit vaše závazky v oblasti zabezpečení a dodržování předpisů. Tato data v klidovém stavu jsou šifrována pomocí 256bitového šifrování AES, což je jedna z nejsilnějších dostupných blokových šifer a je kompatibilní s FIPS 140-2.

Kromě šifrování v klidovém stavu jsou všechna data zálohování při přenosu přenášena přes protokol HTTPS. Vždy zůstane v páteřní síti Azure.

Další informace najdete v tématu [šifrování Azure Storage pro data v klidovém stavu](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Najdete v [nejčastějších dotazech](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) k zálohování Azure a zodpovíte všechny otázky týkající se šifrování.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Šifrování záložních dat pomocí klíčů spravovaných platformou

Ve výchozím nastavení jsou všechna data šifrována pomocí klíčů spravovaných platformou. K povolení tohoto šifrování nemusíte provádět žádné explicitní akce z vaší strany a vztahuje se na všechny úlohy zálohované do trezoru služby Recovery Services.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Šifrování zálohovaných dat pomocí klíčů spravovaných zákazníkem

Při zálohování virtuálních počítačů Azure teď můžete šifrovat data pomocí klíčů, které vlastníte a spravujete. Azure Backup umožňuje používat klíče RSA uložené v trezoru klíčů Azure pro šifrování záloh. Šifrovací klíč používaný k šifrování záloh se může lišit od klíče použitého pro zdroj. Data jsou chráněna pomocí šifrovacího klíče (DEK) založeného na AES 256, který je zase chráněn pomocí vašich klíčů. To vám dává plnou kontrolu nad daty a klíči. Chcete-li povolit šifrování, je nutné, aby trezor služby Recovery Services byl udělen přístup k šifrovacímu klíči v trezoru klíčů Azure. Klíč můžete zakázat nebo kdykoli to odvolávat. Před pokusem o ochranu všech položek do trezoru je však nutné povolit šifrování pomocí klíčů.

>[!NOTE]
>Tato funkce je v současné době v omezené dostupnosti. Vyplňte [prosím tento průzkum](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) AskAzureBackupTeam@microsoft.com a pošlete nám e-mail, pokud chcete šifrovat vaše záložní data pomocí klíčů spravovaných zákazníkem. Všimněte si, že možnost používat tuto funkci podléhá schválení služby Azure Backup.

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Zálohování spravovaných diskových virtuálních počítačů zašifrovaných pomocí klíčů spravovaných zákazníkem

Azure Backup také umožňuje zálohovat virtuální počítače Azure, které používají váš klíč pro šifrování na straně serveru. Klíč používaný k šifrování disků je uložen v trezoru klíčů Azure a spravujete je vy. Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem se liší od Azure Disk Encryption, protože ADE využívá Nástroj BitLocker (pro Windows) a DM-Crypt (pro Linux) k provádění šifrování pro hosta, SSE šifruje data ve službě úložiště, což vám umožní používat libovolný operační systém nebo image pro vaše virtuální počítače. Další podrobnosti naleznete v části [Šifrování spravovaných disků pomocí klíčů spravovaných zákazníkem.](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)

### <a name="backup-of-vms-encrypted-using-ade"></a>Zálohování virtuálních počítače šifrovaných pomocí ADE

Pomocí Azure Backup můžete taky zálohovat virtuální počítače Azure, které mají svůj operační systém nebo datové disky zašifrované pomocí Azure Disk Encryption. ADE používá nástroj BitLocker pro virtuální počítače se systémem Windows a virtuální počítače DM-Crypt pro Linux k provádění šifrování pro hosta. Podrobnosti najdete v tématu [Zálohování a obnovení šifrovaných virtuálních počítačů pomocí Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="private-endpoints"></a>Soukromé koncové body

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>Další funkce zabezpečení

### <a name="protection-of-azure-backup-recovery-points"></a>Ochrana bodů obnovení azure backup

Účty úložiště používané trezory služeb pro obnovení jsou izolované a uživatelé k nim nemají přístup pro žádné škodlivé účely. Přístup je povolen pouze prostřednictvím operací správy zálohování Azure, jako je například obnovení. Tyto operace správy jsou řízeny prostřednictvím řízení přístupu na základě rolí (RBAC).

Další informace najdete [v tématu Správa bodů obnovení azure backup pomocí řízení přístupu na základě rolí](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="for-soft-delete"></a>Pro měkké odstranění

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Musím povolit funkci obnovitelného odstranění v každém trezoru?

Ne, je ve výchozím nastavení vytvořená a povolená pro všechny trezory služeb pro obnovení.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Lze nakonfigurovat počet dní, po které budou moje data po dokončení operace odstranění uchována v režimu obnovitelného odstranění?

Ne, je opravena na 14 dní další uchovávání po operaci odstranění.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Musím zaplatit náklady na toto další 14denní zadržení?

Ne, toto 14denní další uchovávání informací je zdarma jako součást funkce obnovitelného odstranění.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Mohu provést operaci obnovení, když jsou data ve stavu obnovitelného odstranění?

Ne, chcete-li obnovit, musíte obnovit obnovitelný odstraněný prostředek. Operace zrušení odstranění přenese prostředek zpět do **stavu Zastavit s uchováním stavu dat,** kde můžete obnovit do libovolného bodu v čase. Systém uvolňování paměti zůstává v tomto stavu pozastaven.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Budou moje snímky sledovat stejný životní cyklus jako body obnovení v trezoru?

Ano.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Jak lze znovu spustit naplánované zálohy pro zdroj s obnovitelným odstraněním?

Undelete následované obnovením operace bude prostředek znovu chránit. Pokračovat v operaci přidruží zásady zálohování k aktivaci naplánované zálohy s vybranou dobu uchování. Systém uvolňování paměti se také spustí, jakmile je dokončena operace obnovení. Pokud chcete provést obnovení z bodu obnovení, který je po datu vypršení platnosti, doporučujeme vám to udělat před spuštěním operace obnovení.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Mohu trezor odstranit, pokud jsou v úschovně obnovitelné odstraněné položky?

Trezor služby Recovery Services nelze odstranit, pokud jsou v úložišti položky zálohování ve stavu obnovitelného odstranění. Položky s měkkým odstraněním jsou trvale odstraněny 14 dní po operaci odstranění. Pokud nemůžete čekat 14 dní, [zakažte obnovitelné odstranění](#disabling-soft-delete), odstraňte obnovitelné odstraněné položky a odstraňte je znovu, abyste se trvale odstranili. Po zajištění, že neexistují žádné chráněné položky a žádné obnovitelné odstraněné položky, lze trezor odstranit.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Mohu data odstranit dříve než 14denní období obnovitelného odstranění po odstranění?

Ne. Odstranění položek s měkkým odstraněním nelze vynutit, po 14 dnech se automaticky odstraní. Tato funkce zabezpečení je povolena pro zabezpečení zálohovaných dat před náhodným nebo škodlivým odstraněním.  Měli byste počkat 14 den před provedením jakékoli jiné akce na virtuálním počítači.  Zpoplatněné položky s měkkým smazaným.  Pokud potřebujete znovu chránit virtuální servery označené pro obnovitelné odstranění do 14 dnů do nového trezoru, obraťte se na podporu microsoftu.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Lze operace s měkkým odstraněním provádět v prostředí PowerShell nebo CLI?

Operace s měkkým odstraněním lze provádět pomocí [prostředí PowerShell](#soft-delete-for-vms-using-azure-powershell). V současné době není podporováno vykreslování příkazů.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Je obnovitelné odstranění podporované pro jiné cloudové úlohy, jako je SQL Server ve virtuálních počítačích Azure a SAP HANA v virtuálních počítačích Azure?

Ne. V současné době je obnovitelné odstranění podporováno jenom pro virtuální počítače Azure.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [ovládacích prvcích zabezpečení pro azure backup](backup-security-controls.md).
