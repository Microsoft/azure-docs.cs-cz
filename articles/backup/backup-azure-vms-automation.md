---
title: Nasazení a správa zálohování virtuálních počítačů nasazených Resource Managerem pomocí PowerShellu
description: Pomocí prostředí PowerShell můžete nasadit a spravovat zálohy v Azure pro virtuálních počítačů nasazených Resource Manager
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: markgal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4d3c0d08b2a34313c10ab89f2972894ffabe19d2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606235"
---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>Pomocí rutin AzureRM.RecoveryServices.Backup zálohování virtuálních počítačů

Tento článek ukazuje, jak používat rutiny prostředí Azure PowerShell k zálohování a obnovení z trezoru služeb zotavení Azure virtuální počítač (VM). Trezor služeb zotavení je prostředek Azure Resource Manager a slouží k ochraně dat a prostředků služby Azure Backup a Azure Site Recovery. Trezor služeb zotavení můžete použít k ochraně virtuálních počítačů nasazených Azure Service Manager a virtuální počítače nasazené Azure Resource Manager.

> [!NOTE]
> Azure obsahuje dva modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek je pro použití s virtuálními počítači, které jsou vytvořené pomocí modelu Resource Manager.
>
>

Tento článek vás provede pomocí prostředí PowerShell k ochraně virtuálního počítače a obnovení dat z bodu obnovení.

## <a name="concepts"></a>Koncepty
Pokud nejste obeznámeni s služby Azure Backup Přehled služby, podívejte se na [co je Azure Backup?](backup-introduction-to-azure-backup.md) Než začnete, ujistěte se, že, zahrnují essentials o součásti potřebné pro práci s Azure Backup a omezení aktuálního řešení zálohování virtuálních počítačů.

Pomocí prostředí PowerShell efektivně, je potřeba pochopit hierarchii objektů a odkud spustit.

![Hierarchie objektů služby obnovení](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Odkazu na rutiny Powershellu AzureRm.RecoveryServices.Backup najdete v tématu [Azure Backup - rutin služby obnovení](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) v knihovně Azure.

## <a name="setup-and-registration"></a>Instalace a registrace
Chcete-li začít:

1. [Stáhněte si nejnovější verzi prostředí PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (minimální požadovaná verze je: 1.4.0)

2. Rutiny prostředí PowerShell zálohování Azure k dispozici najděte tak, že zadáte následující příkaz:
    ```PS
    PS C:\> Get-Command *azurermrecoveryservices*
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
    Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
    Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
    Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
    Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
    Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
    Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
    Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
    ```
3. Účet přihlášení pro vaši Azure pomocí **Connect-AzureRmAccount**. Tato rutina se vyvolá na webové stránce vás vyzve k zadání přihlašovacích údajů účtu: 
    - Alternativně můžete použít přihlašovací údaje účtu jako parametr v **Connect-AzureRmAccount** rutiny, pomocí **-přihlašovacích údajů** parametr.
    - Pokud jste poskytovatel CSP partnera práce jménem klienta, zadejte zákazníka jako klient, pomocí jejich název primární domény tenantID nebo klienta. Příklad: **Connect-AzureRmAccount-klienta "fabrikam.com"**
4. Přidružte odběr, který chcete používat s účtem, protože účet může mít několik odběrů:

    ```PS
    PS C:\> Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Pokud používáte Azure Backup poprvé, musíte použít **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** rutiny k registraci poskytovatele služeb zotavení Azure s vaším předplatným.

    ```PS
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Můžete ověřit, že registrované zprostředkovatele úspěšně, použijte následující příkazy:
    ```PS
    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ``` 
Ve výstupu příkazu **RegistrationState** by se nastavit na **registrovaná**. Pokud ne, stačí znovu spustit **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** rutiny uvedené výše.

Následující úlohy je možné automatizovat pomocí prostředí PowerShell:

* [Vytvoření trezoru služby Recovery Services](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Zálohování virtuálních počítačů Azure](backup-azure-vms-automation.md#back-up-azure-vms)
* [Aktivuje úlohu zálohování](backup-azure-vms-automation.md#trigger-a-backup-job)
* [Monitorování úlohy zálohování](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Obnovení virtuálního počítače Azure](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services

Následující kroky vás provedou vytvoření trezoru služeb zotavení. Trezor služeb zotavení se liší od úložiště záloh.

1. Trezor služeb zotavení je prostředek Resource Manager, proto musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořte skupinu prostředků s **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** rutiny. Při vytváření skupiny prostředků, zadejte název a umístění pro skupinu prostředků.  

    ```PS
    PS C:\> New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Použití **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** rutiny pro vytvoření trezoru služeb zotavení. Ujistěte se, že zadejte stejné umístění pro úložiště, jako byl použit pro skupinu prostředků.

    ```PS
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Zadejte typ redundance úložiště se použije. můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy-grs.md). Následující příklad ukazuje, že je možnost - BackupStorageRedundancy pro testvault nastavena na GeoRedundant.

    ```PS
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Recovery Services do proměnné.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Zobrazit trezorů v předplatném.
Použití **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)** Chcete-li zobrazit seznam všech trezorů v aktuálním předplatném. Tento příkaz můžete použít, chcete-li zkontrolovat, zda byl vytvořen nový trezor nebo zobrazíte dostupné trezorů v rámci předplatného.

Spuštění příkazu Get-AzureRmRecoveryServicesVault, chcete-li zobrazit všechny trezorů v rámci předplatného. Následující příklad ukazuje informace zobrazené pro každý trezor.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Zálohování virtuálních počítačů Azure
Ochrana virtuálních počítačů pomocí trezoru služeb zotavení. Před použitím ochranu nastavit kontext trezoru (typ dat v úložišti) a ověřte zásady ochrany. Zásady ochrany je plán při spuštění úloh zálohování a jak dlouho mají být uchována každý snímek zálohy.

### <a name="set-vault-context"></a>Kontext sady trezoru
Než povolíte ochranu na virtuálním počítači, použijte **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** nastavit kontext úložiště. Po nastavení se kontext trezoru použije pro všechny další rutiny. Následující příklad nastaví kontext trezoru trezoru, *testvault*.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Vytvoření zásady ochrany
Při vytváření trezoru služby Recovery Services se vytvoří i výchozí zásady ochrany a uchovávání informací. Výchozí zásady ochrany aktivují úlohu zálohování každý den v určenou dobu. Výchozí zásady uchovávání informací uchovávají denní bod obnovení po dobu 30 dnů. Výchozí zásady můžete rychle zajistit ochranu virtuálního počítače a upravovat zásady později pomocí různých údajů.

Použití **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** Chcete-li zobrazit zásady ochrany v trezoru. Tuto rutinu můžete použít, chcete-li získat konkrétní zásadu, nebo pokud chcete zobrazit zásady přidružené typu úlohy. Následující příklad získá zásady pro typ pracovního vytížení, AzureVM.

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Časové pásmo BackupTime pole v prostředí PowerShell je UTC. Ale když čas zálohování se zobrazí na portálu Azure, čas se upraví na vaše místní časové pásmo.
>
>

Zásady zálohování ochrany je přidružen alespoň jeden zásady uchovávání informací. Zásady uchovávání informací definuje, jak dlouho bod obnovení je udržováno před odstraněním. Použití **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** zobrazení výchozí zásady uchovávání informací.  Podobně můžete použít **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** získat výchozí plán zásady. **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** rutina vytvoří objekt prostředí PowerShell, který obsahuje informace zásady zálohování. Objekty zásad plán a uchovávání dat se používají jako vstupy pro **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** rutiny. Následující příklad ukládá v proměnné plán zásad a zásad uchovávání informací. V příkladu používá k definici parametrů při vytváření zásady ochrany, tyto proměnné *NewPolicy*.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```


### <a name="enable-protection"></a>Povolení ochrany
Po definování zásad zálohování ochrany je stále nutné povolit zásady pro položku. Použití **[povolit AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** k povolení ochrany. Povolení ochrany vyžaduje dva objekty - položky a zásady. Jakmile zásady je už přidružená k trezoru, aktivuje se v době definované v plán zásad zálohování pracovního postupu.

Následující příklad povolí ochranu pro položku, V2VM, pomocí zásad, NewPolicy. K povolení ochrany na nešifrované virtuálních počítačích Resource Manager

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Pokud chcete povolit ochranu na šifrované virtuálních počítačích (zašifrovaný pomocí BEK a KEK), musíte poskytnout oprávnění služby Azure Backup ke čtení klíče a tajné klíče z trezoru klíčů.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Chcete-li povolit ochranu na šifrování virtuálních počítačů (šifrují pomocí BEK pouze), je potřeba udělit oprávnění služby Azure Backup pro čtení tajných klíčů z trezoru klíčů.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Pokud používáte cloudu Azure Government, použijte pro parametr hodnotu ff281ffe-705c-4f53-9f37-a40e6f2c68f3 **- ServicePrincipalName** v [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) rutiny.
>
>

Pro klasické virtuální počítače

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Upravit zásady ochrany
Chcete-li upravit zásady ochrany, použijte [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) upravit SchedulePolicy nebo RetentionPolicy objekty.

Následující příklad změní uchování bodu obnovení do 365 dní.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Spustit zálohu
Můžete použít **[zálohování AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** se spustit úlohu zálohování. Pokud je prvotní zálohování, je úplné zálohování. Následné zálohy trvat přírůstkové kopie. Nezapomeňte použít **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** nastavit kontext trezoru před spuštěním úlohy zálohování. V následujícím příkladu se předpokládá, že byl nastaven kontext úložiště.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Časové pásmo čas spuštění a čas ukončení polí v prostředí PowerShell je UTC. Pokud čas se zobrazí na portálu Azure, čas upraví na vaše místní časové pásmo.
>
>

## <a name="monitoring-a-backup-job"></a>Monitorování úlohy zálohování
Dlouhotrvající operace, jako je například úlohy zálohování, můžete monitorovat bez použití portálu Azure. Stav probíhající úlohy, použijte **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** rutiny. Tato rutina načte úlohy zálohování pro konkrétní úložiště a tento trezor je uveden v kontextu trezoru. Následující příklad získá stav úlohu v průběhu jako pole a ukládá stav v $joblist proměnné.

```
PS C:\> $joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
PS C:\> $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Místo dotazování tyto úlohy pro dokončení – což je zbytečné další kód – použít **[čekání AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** rutiny. Tato rutina pozastaví spuštění až do dokončení úlohy nebo zadaný časový limit.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Obnovení virtuálního počítače Azure
Je klíčové rozdíl mezi obnovení virtuálního počítače pomocí portálu Azure a obnovení virtuálního počítače pomocí prostředí PowerShell. V prostředí PowerShell je dokončena operace obnovení, jakmile disky a informace o konfiguraci z bodu obnovení se vytvoří. Pokud chcete obnovit nebo obnovit několik soubory ze zálohy virtuálního počítače Azure, podívejte se na [souboru části Obnovení](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)

> [!NOTE]
> Operace obnovení nevytvoří virtuálního počítače.
>
>

K vytvoření virtuálního počítače z disku, najdete v části [vytvoření virtuálního počítače z obnovené disků](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Toto jsou základní kroky k obnovení virtuálního počítače Azure:

* Vyberte virtuální počítač
* Vyberte bod obnovení
* Obnovení disky
* Vytvoření virtuálního počítače z uložené disků

Následující obrázek znázorňuje hierarchie objektů z RecoveryServicesVault dolů BackupRecoveryPoint.

![Hierarchie objektů služby obnovení zobrazující BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Chcete-li obnovit zálohovaná data, Identifikujte položku zálohovanou a bod obnovení, který obsahuje data v daném okamžiku. Použití **[obnovení AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** rutiny k obnovení dat z trezoru do účtu zákazníka.

### <a name="select-the-vm"></a>Vyberte virtuální počítač
GET pro objekt prostředí PowerShell, který označuje správné záložní položku, spusťte z kontejneru v trezoru a směrem dolů hierarchie objektů. Pokud chcete vybrat kontejner, který představuje virtuální počítač, použijte **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** rutiny a prostřednictvím kanálu, který chcete **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** rutiny.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Vyberte bod obnovení
Použití **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** rutiny seznam všech bodů obnovení pro položku zálohování. Zvolte bod obnovení pro obnovení. Pokud si nejste jisti, který bod obnovení používat, je dobrým zvykem zvolte nejnovější RecoveryPointType = AppConsistent bodu v seznamu.

V následující skript, proměnné **$rp**, je pole bodů obnovení pro vybranou položku zálohování, v posledních sedmi dnech. Toto pole je seřazen v obráceném pořadí času s indexem 0 nejnovější bod obnovení. Použijte standardní prostředí PowerShell pole indexování a vyberte bod obnovení. V příkladu $rp [0] vybere nejnovější bod obnovení.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```



### <a name="restore-the-disks"></a>Obnovení disky
Použití **[obnovení AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** rutiny k obnovení dat a konfigurace zálohování položek bodu obnovení. Jakmile jste našli bod obnovení, je využít jako hodnota **- RecoveryPoint** parametr. V předchozím ukázkovém kódu **$rp [0]** byl bod obnovení použít. V následujícím vzorovém kódu **$rp [0]** je bod obnovení pro obnovení disku.

Chcete-li obnovit disky a informace o konfiguraci:

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Použití **[čekání AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** rutiny čekání na dokončení úlohy obnovení.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Po dokončení úlohy obnovení, použijte **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** rutiny Získejte podrobnosti o operaci obnovení. Vlastnost JobDetails obsahuje informace potřebné pro virtuální počítač znovu sestavit.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Po obnovení disky, přejděte k další části vytvořte virtuální počítač.

## <a name="create-a-vm-from-restored-disks"></a>Vytvoření virtuálního počítače z obnovené disků
Po obnovení disky, tyto kroky použijte k vytvoření a konfiguraci virtuálního počítače z disku.

> [!NOTE]
> Pokud chcete vytvořit šifrovaný virtuální počítače z obnovené disků, musí mít vaše Azure role oprávnění k provedení akce, **Microsoft.KeyVault/vaults/deploy/action**. Pokud vaše role nemá toto oprávnění, vytvořte vlastní role pomocí této akce. Další informace najdete v tématu [vlastní role v Azure RBAC](../role-based-access-control/custom-roles.md).
>
>

1. Dotaz na vlastnosti obnovené disku podrobnosti úlohy.

  ```
  PS C:\> $properties = $details.properties
  PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
  PS C:\> $containerName = $properties["Config Blob Container Name"]
  PS C:\> $blobName = $properties["Config Blob Name"]
  ```

2. Nastavit kontext úložiště Azure a obnovte konfiguračního souboru JSON.

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. Použijte konfigurační soubor JSON pro vytvoření konfigurace virtuálního počítače.

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
    ```

4. Připojte disk operačního systému a datové disky. V závislosti na konfiguraci virtuálních počítačů naleznete v příslušné části zobrazíte příslušné rutiny:

    #### <a name="non-managed-non-encrypted-vms"></a>Nespravované, bez šifrování virtuálních počítačů

    Použijte následující příklad nespravované, bez šifrování virtuálních počítačů.

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
    PS C:\> foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
    {
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="non-managed-encrypted-vms-bek-only"></a>Nespravované, šifrované virtuální počítače (pouze BEK)

    Nespravované, šifrované virtuálních počítačů (šifrují pomocí BEK pouze) musíte k obnovení tajný klíč pro trezor klíčů, než je možné připojit disky. Další informace najdete v článku [obnovení šifrovaných virtuálního počítače z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Následující příklad ukazuje, jak připojit operačního systému a datové disky pro virtuální počítače šifrovaná.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="non-managed-encrypted-vms-bek-and-kek"></a>Nespravované, šifrované virtuálních počítačů (BEK a KEK)

    Nespravované, šifrované virtuálních počítačů (zašifrovaný pomocí BEK a KEK) budete muset obnovit klíč a tajný klíč do trezoru klíčů, než připojíte disky. Další informace najdete v článku [obnovení šifrovaných virtuálního počítače z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Následující příklad ukazuje, jak připojit operačního systému a datové disky pro virtuální počítače šifrovaná.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="managed-non-encrypted-vms"></a>Spravovaných, bez šifrování virtuálních počítačů

    Pro spravovaných bez šifrování virtuálních počítačů budete muset vytvořit spravované disky z úložiště objektů blob a pak připojte disky. Podrobné informace najdete v článku [připojit datový disk pro virtuální počítač s Windows pomocí prostředí PowerShell](../virtual-machines/windows/attach-disk-ps.md). Následující vzorový kód ukazuje, jak připojit datových disků pro virtuální počítače spravované bez šifrování.

    ```
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="managed-encrypted-vms-bek-only"></a>Spravovat, šifrované virtuální počítače (pouze BEK)

    Pro spravované šifrované virtuální počítače (šifrují pomocí BEK pouze) budete muset vytvořit spravované disky z úložiště objektů blob a pak připojte disky. Podrobné informace najdete v článku [připojit datový disk pro virtuální počítač s Windows pomocí prostředí PowerShell](../virtual-machines/windows/attach-disk-ps.md). Následující vzorový kód ukazuje, jak připojit datových disků pro virtuální počítače spravované šifrované.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="managed-encrypted-vms-bek-and-kek"></a>Spravovaných, šifrované virtuálních počítačů (BEK a KEK)

    Pro spravované šifrované virtuální počítače (zašifrovaný pomocí BEK a KEK) budete muset vytvořit spravované disky z úložiště objektů blob a pak připojte disky. Podrobné informace najdete v článku [připojit datový disk pro virtuální počítač s Windows pomocí prostředí PowerShell](../virtual-machines/windows/attach-disk-ps.md). Následující vzorový kód ukazuje, jak připojit datových disků pro virtuální počítače spravované šifrované.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

5. Nakonfigurujte nastavení sítě.

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Vytvořte virtuální počítač.

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Obnovit soubory ze zálohy virtuálního počítače Azure

Kromě obnovování disků, můžete také obnovit jednotlivé soubory ze zálohy virtuálního počítače Azure. Funkce obnovení souborů poskytuje přístup ke všem souborům v bodu obnovení a je můžete spravovat pomocí Průzkumníka souborů a jako byste to udělali pro normální soubory.

Toto jsou základní kroky obnovit soubor ze zálohy virtuálního počítače Azure:

* Vyberte virtuální počítač
* Vyberte bod obnovení
* Připojit disky bodu obnovení
* Zkopírujte požadované soubory
* Odpojení disku


### <a name="select-the-vm"></a>Vyberte virtuální počítač
GET pro objekt prostředí PowerShell, který označuje správné záložní položku, spusťte z kontejneru v trezoru a směrem dolů hierarchie objektů. Pokud chcete vybrat kontejner, který představuje virtuální počítač, použijte **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** rutiny a prostřednictvím kanálu, který chcete **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** rutiny.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Vyberte bod obnovení
Použití **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** rutiny seznam všech bodů obnovení pro položku zálohování. Zvolte bod obnovení pro obnovení. Pokud si nejste jisti, který bod obnovení používat, je dobrým zvykem zvolte nejnovější RecoveryPointType = AppConsistent bodu v seznamu.

V následující skript, proměnné **$rp**, je pole bodů obnovení pro vybranou položku zálohování, v posledních sedmi dnech. Toto pole je seřazen v obráceném pořadí času s indexem 0 nejnovější bod obnovení. Použijte standardní prostředí PowerShell pole indexování a vyberte bod obnovení. V příkladu $rp [0] vybere nejnovější bod obnovení.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Připojit disky bodu obnovení

Použití **[Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)** rutiny skript, který chcete připojit všechny disky bodu obnovení.

> [!NOTE]
> Disky jsou připojené jako disky připojené přes iSCSI na počítači, kde se spouští skript. Proto je téměř okamžitou a nedojde všech poplatků
>
>

```
PS C:\> Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]

OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```
Spusťte skript na počítači, ve které chcete obnovit soubory. Budete muset zadat heslo uvedené výše se spustit skript. Poté, co jsou disky připojené, pomocí Průzkumníka Windows souboru procházet nové svazky a soubory. Další informace najdete v části [souboru obnovení dokumentace](backup-azure-restore-files-from-vm.md)

### <a name="unmount-the-disks"></a>Odpojení disky
Po zkopírování požadované soubory odpojit disky pomocí **[zakázat AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** rutiny. Se důrazně doporučuje jako zajišťuje, že k souborům bod obnovení je odebrání přístupu k

```
PS C:\> Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```


## <a name="next-steps"></a>Další postup
Pokud dáváte přednost zapojit vašich prostředků Azure pomocí prostředí PowerShell, najdete v článku prostředí PowerShell [nasadit a spravovat zálohy pro Windows Server](backup-client-automation.md). Pokud budete spravovat zálohy aplikace DPM, najdete v článku [nasadit a spravovat zálohy pro DPM](backup-dpm-automation.md). Mají obě z těchto článků verze pro nasazení Resource Manager a nasazení Classic.  
