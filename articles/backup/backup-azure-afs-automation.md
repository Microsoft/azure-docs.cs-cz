---
title: Zálohování souborů Azure pomocí PowerShellu
description: V tomto článku se dozvíte, jak zálohovat soubory Azure pomocí služby Azure Backup a PowerShellu.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273537"
---
# <a name="back-up-azure-files-with-powershell"></a>Zálohování souborů Azure pomocí PowerShellu

Tento článek popisuje, jak pomocí Azure PowerShellu zálohovat sdílenou složku souborů Azure pomocí trezoru [služby Azure Backup](backup-overview.md) Recovery Services.

Tento článek vysvětluje, jak:

> [!div class="checklist"]
>
> * Nastavte PowerShell a zaregistrujte zprostředkovatele služeb azure recovery services.
> * Vytvořte trezor služby Recovery Services.
> * Nakonfigurujte zálohování pro sdílenou složku Azure.
> * Spusťte úlohu zálohování.

## <a name="before-you-start"></a>Než začnete

* [Přečtěte si další informace](backup-azure-recovery-services-vault-overview.md) o trezorech služby Recovery Services.
* Přečtěte si o možnostech náhledu pro [zálohování sdílených složek Azure](backup-afs.md).
* Zkontrolujte hierarchii objektů prostředí PowerShell pro služby recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Hierarchie objektů služby Recovery Services

Hierarchie objektů je shrnuta v následujícím diagramu.

![Hierarchie objektů služby Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Zkontrolujte odkaz [na rutinu rutiny](/powershell/module/az.recoveryservices) **Az.RecoveryServices** v knihovně Azure.

## <a name="set-up-and-install"></a>Nastavení a instalace

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nastavení prostředí PowerShell takto:

1. [Stáhněte si nejnovější verzi prostředí Az PowerShell](/powershell/azure/install-az-ps). Minimální požadovaná verze je 1.0.0.

> [!WARNING]
> Minimální verze PS potřebné pro náhled byl 'Az 1.0.0'. Vzhledem k nadcházejícím změnám pro GA bude minimální požadovaná verze PS "Az.RecoveryServices 2.6.0". Na tuto verzi je velmi důležité upgradovat všechny stávající verze systému PS. V opačném případě se existující skripty po GA přeruší. Nainstalujte minimální verzi s následujícími příkazy PS

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. Najděte rutiny Azure Backup PowerShell pomocí tohoto příkazu:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Zkontrolujte aliasy a rutiny pro Azure Backup, Azure Site Recovery a trezor služby Recovery Services. Zde je příklad toho, co můžete vidět. Není to úplný seznam rutin.

    ![Seznam rutin služby Obnovení](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Přihlaste se ke svému účtu Azure pomocí **služby Connect-AzAccount**.
5. Na zobrazené webové stránce budete vyzváni k zadání přihlašovacích údajů k účtu.

    * Případně můžete zahrnout pověření účtu jako parametr v rutině **Connect-AzAccount** s **-Credential**.
    * Pokud jste partner csp, který pracuje jménem tenanta, zadejte zákazníka jako tenanta pomocí jejich id tenanta nebo primárního názvu domény klienta. Příkladem je **Connect-AzAccount -Tenant** fabrikam.com.

6. Přidružte předplatné, které chcete použít, k účtu, protože účet může mít několik předplatných.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Pokud používáte Azure Backup poprvé, použijte rutinu **Register-AzResourceProvider** k registraci poskytovatele služby Azure Recovery Services s vaším předplatným.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Ověřte, zda se poskytovatelé úspěšně zaregistrovali:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Ve výstupu příkazu ověřte, že **RegistrationState** změny **Registered**. Pokud tomu tak není, spusťte rutinu **Register-AzResourceProvider** znovu.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Trezor služby Recovery Services je prostředek Správce prostředků, takže jej musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo můžete vytvořit skupinu prostředků s rutinou **New-AzResourceGroup.** Při vytváření skupiny prostředků zadejte název a umístění skupiny prostředků.

Podle těchto kroků vytvořte trezor služby Recovery Services.

1. Úschovna je umístěna ve skupině prostředků. Pokud nemáte existující skupinu prostředků, vytvořte novou skupinu s [new-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). V tomto příkladu vytvoříme novou skupinu prostředků v oblasti Západní USA.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. K vytvoření úschovny použijte rutinu [New-AzRecoveryServicesVault.](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) Zadejte stejné umístění pro úschovnu, jaké bylo použito pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Zadejte typ redundance, který se má použít pro úložiště trezoru.

   * Můžete použít [místně redundantní úložiště](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantní úložiště](../storage/common/storage-redundancy-grs.md).
   * Následující příklad nastaví možnost **-BackupStorageRedundancy** pro cmd[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) pro **testvault** nastavenou na **georedundantní**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Zobrazení trezorů v předplatném

Chcete-li zobrazit všechny trezory v předplatném, použijte [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Výstup je podobný následujícímu. Všimněte si, že přidružené skupiny prostředků a umístění jsou k dispozici.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Nastavení kontextu úschovny

Uložte objekt úschovny do proměnné a nastavte kontext úschovny.

* Mnoho rutin Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services, takže je vhodné uložit objekt trezoru v proměnné.
* Kontext trezoru představuje typ chráněných dat v trezoru. Nastavte ji pomocí [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Po nastavení kontextu se vztahuje na všechny následné rutiny.

Následující příklad nastaví kontext úložiště pro **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Načtení ID trezoru

Plánujeme zanesit nastavení kontextu trezoru v souladu s pokyny Azure PowerShellu. Místo toho můžete ID úschovny uložit nebo načíst a předat ho příslušným příkazům. Pokud jste tedy nenastavili kontext úložiště nebo chcete zadat příkaz ke spuštění určitého trezoru, předejte ID úschovny jako "-vaultID" všem relevantním příkazům takto:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Konfigurace zásad zálohování

Zásada zálohování určuje plán zálohování a jak dlouhé mají být zachovány body obnovení zálohy:

* Zásady zálohování jsou přidruženy alespoň k jedné zásadě uchovávání informací. Zásady uchovávání informací definují, jak dlouho je bod obnovení uchováván před jeho odstraněním.
* Zobrazení výchozího uchovávání zásad zálohování pomocí [objektu Get-AzRecoveryServicesBackupBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Zobrazení výchozího plánu zásad zálohování pomocí objektu [Get-AzRecoveryServicesBackupBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Rutina [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) slouží k vytvoření nové zásady zálohování. Zadejte objekty zásad plánu a uchovávání informací.

Ve výchozím nastavení je čas zahájení definován v objektu zásad plánu. Pomocí následujícího příkladu můžete změnit čas zahájení na požadovaný čas zahájení. Požadovaný čas zahájení by měl být také v UTC. Níže uvedený příklad předpokládá, že požadovaný čas zahájení je 01:00 AM UTC pro denní zálohování.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Je třeba zadat čas zahájení pouze v násobcích 30 minut. Ve výše uvedeném příkladu může být pouze "01:00:00" nebo "02:30:00". Čas zahájení nemůže být "01:15:00"

Následující příklad ukládá zásady plánu a zásady uchovávání informací v proměnných. Potom používá tyto proměnné jako parametry pro novou zásadu **(NewAFSPolicy**). **NewAFSPolicy** trvá denní zálohování a uchovává ji po dobu 30 dnů.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Výstup je podobný následujícímu.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Povolit zálohování

Po definování zásad zálohování můžete povolit ochranu sdílené složky Azure pomocí zásad.

### <a name="retrieve-a-backup-policy"></a>Načtení zásad zálohování

Příslušný objekt zásad načtete pomocí [zásad Get-AzRecoveryServicesBackupProtectionProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Pomocí této rutiny získat konkrétní zásady nebo zobrazit zásady přidružené k typu pracovního vytížení.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Načtení zásady pro typ pracovního vytížení

Následující příklad načte zásady pro typ pracovního vytížení **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Výstup je podobný následujícímu.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Časové pásmo pole **BackupTime** v prostředí PowerShell je univerzální koordinovaný čas (UTC). Když se na webu Azure Portal zobrazí čas zálohování, čas se přizpůsobí místnímu časovému pásmu.

### <a name="retrieve-a-specific-policy"></a>Načtení konkrétní zásady

Následující zásady načte zásady zálohování s názvem **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Povolení zálohování a použití zásad

Povolte ochranu pomocí [ochrany Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Po přidružení zásady k trezoru se zálohy aktivují v souladu s plánem zásad.

Následující příklad umožňuje ochranu pro test sdílené složky **AzureAzureFileShare** v testu účtu **úložištěStorageAcct**, s **dailyafs**zásady .

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Příkaz čeká, dokud není dokončena úloha ochrany konfigurace a poskytuje podobný výstup, jak je znázorněno.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>Důležité upozornění – identifikace položky zálohování pro zálohování AFS

Tato část popisuje důležitou změnu v zálohování AFS v rámci přípravy na GA.

Při povolení zálohování pro AFS uživatel zadá zákazníkům popisný název sdílené složky jako název entity a vytvoří se položka zálohy. Název položky zálohy je jedinečný identifikátor vytvořený službou Azure Backup. Identifikátor obvykle zahrnuje uživatelsky přívětivý název. Ale pro zpracování důležitého scénáře obnovitelného odstranění, kde lze odstranit sdílenou složku a vytvořit jinou sdílenou složku se stejným názvem, bude jedinečná identita sdílené složky Azure nyní ID namísto názvu pro zákazníky. Chcete-li znát jedinečnou identitu/název každé položky, stačí spustit ```Get-AzRecoveryServicesBackupItem``` příkaz s příslušnými filtry pro backupManagementType a WorkloadType získat všechny příslušné položky a pak sledovat název pole v vrácené ps objekt/odpověď. Vždy se doporučuje vypsat položky a pak načíst jejich jedinečný název z pole 'název' v odpovědi. Tato hodnota slouží k filtrování položek s parametrem "Název". V opačném případě použijte parametr FriendlyName k načtení položky s popisným názvem nebo identifikátorem zákazníka.

> [!WARNING]
> Ujistěte se, že verze PS je upgradována na minimální verzi pro "Az.RecoveryServices 2.6.0" pro zálohy AFS. V této verzi je pro ```Get-AzRecoveryServicesBackupItem``` příkaz k dispozici filtr "friendlyName". Předajte název sdílené složky Azure parametru friendlyName. Pokud předáte název sdílené složky Azure parametru "Name", tato verze vyvolá upozornění předat tento popisný název parametru popisný název. Neinstalace této minimální verze může mít za následek selhání existujících skriptů. Nainstalujte minimální verzi systému PS pomocí následujícího příkazu.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>Spuštění zálohy na vyžádání

Pomocí [položky Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) spusťte zálohu na vyžádání pro chráněnou sdílenou složku Azure.

1. Načtěte účet úložiště z kontejneru v trezoru, který obsahuje vaše záložní data pomocí [get-azRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Chcete-li spustit úlohu zálohování, získáte informace o sdílené složce Azure s [položkou Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Spusťte zálohu na vyžádání pomocí[položky Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Zálohování na vyžádání spusťte následujícím způsobem:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Příkaz vrátí úlohu s ID, které má být sledováno, jak je znázorněno v následujícím příkladu.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Snímky sdílené složky Azure se používají při zálohování, takže obvykle úloha dokončí v době, kdy příkaz vrátí tento výstup.

### <a name="using-on-demand-backups-to-extend-retention"></a>Použití záloh na vyžádání k rozšíření uchovávání informací

Zálohy na vyžádání lze použít k uchování snímků po dobu 10 let. Plánovače lze použít ke spuštění skriptů prostředí PowerShell na vyžádání s vybranou uchování a proto pořizovat snímky v pravidelných intervalech každý týden, měsíc nebo rok. Při pořizování pravidelných snímků se podívejte na [omezení záloh na vyžádání](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) pomocí zálohování Azure.

Pokud hledáte ukázkové skripty, můžete odkazovat na ukázkový skript na GitHubu (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>) pomocí runbooku Azure Automation, který umožňuje naplánovat zálohování v pravidelných intervalech a zachovat je i až 10 let.

> [!WARNING]
> Ujistěte se, že verze PS je upgradována na minimální verzi pro "Az.RecoveryServices 2.6.0" pro zálohy AFS v runbookech automatizace. Budete muset nahradit starý modul "AzureRM" modulem "Az". V této verzi je pro ```Get-AzRecoveryServicesBackupItem``` příkaz k dispozici filtr "friendlyName". Předajte název sdílené složky azure parametru friendlyName. Pokud předáte název sdílené složky azure parametru "Name", tato verze vyvolá upozornění předat tento popisný název parametru popisný název.

## <a name="next-steps"></a>Další kroky

[Přečtěte si o](backup-afs.md) zálohování souborů Azure na webu Azure Portal.
