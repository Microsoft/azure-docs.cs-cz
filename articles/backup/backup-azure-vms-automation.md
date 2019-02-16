---
title: Nasazení a správa zálohování virtuálních počítačů nasazených Resource Managerem pomocí PowerShellu
description: Použití Powershellu k nasazení a správa záloh v Azure u virtuálních počítačů nasazených Resource Managerem
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: raynew
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b9c3dadb328bb49f3cb1897bb64390ed75620b3d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311881"
---
# <a name="use-powershell-to-back-up-and-restore-virtual-machines"></a>Použití Powershellu k zálohování a obnovení virtuálních počítačů

Tento článek ukazuje, jak pomocí rutin Azure Powershellu k zálohování a obnovení Azure virtuálního počítače (VM) z trezoru služby Recovery Services. Trezor služby Recovery Services je prostředek Azure Resource Manageru použít k ochraně dat a assetů ve službě Azure Backup a Azure Site Recovery services.

> [!NOTE]
> Azure nabízí dva modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek je určený pro použití s virtuálními počítači vytvořené pomocí modelu Resource Manageru.
>
>

Tento článek vás provede pomocí prostředí PowerShell k ochraně virtuálního počítače a obnovení dat z bodu obnovení.

## <a name="concepts"></a>Koncepty

Pokud nejste obeznámeni se službou Azure Backup, získáte přehled o službě, přečtěte si článek, [co je Azure Backup?](backup-introduction-to-azure-backup.md) Než začnete, ujistěte se, že jste zahrnuli součásti potřebné Azure Backup a omezení aktuálního řešení zálohování virtuálního počítače.

Jak efektivně pomocí prostředí PowerShell, je nezbytné pro zjištění hierarchie objektů a ze které se mají spustit.

![Hierarchie objektů Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Reference k rutinám Powershellu AzureRm.RecoveryServices.Backup, naleznete v tématu [Azure Backup – rutiny služby zotavení](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) v knihovně Azure.

## <a name="setup-and-registration"></a>Instalace a registrace

Chcete-li začít:

1. [Stáhněte si nejnovější verzi prostředí PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) (minimální požadovaná verze je: 1.4.0)

2. Vyhledání dostupných rutin Powershellu pro zálohování Azure tak, že zadáte následující příkaz:

    ```powershell
    Get-Command *azurermrecoveryservices*
    ```    
    Zobrazí aliasů a rutin pro trezor služby Recovery Services, Azure Backup a Azure Site Recovery. Na následujícím obrázku je příklad takhle. Není úplný seznam rutin.

    ![seznam služby Recovery Services](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Účet přihlášení pro aplikaci Azure pomocí **Connect-AzureRmAccount**. Tato rutina se vyvolá na webové stránce vás vyzve k zadání přihlašovacích údajů k účtu:

    * Alternativně můžete zahrnout přihlašovacích údajů k účtu jako parametr v **Connect-AzureRmAccount** rutiny, pomocí **-Credential** parametr.
    * Pokud jste partner CSP, který spolupracuje jménem klienta, určení zákazníka jako tenant, pomocí názvu primární doména tenanta nebo ID Tenanta. Příklad: **Connect-AzureRmAccount-Tenant "fabrikam.com"**

4. Přidružte předplatné, které chcete používat s účtem, protože účet může mít několik předplatných:

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Pokud používáte Azure Backup poprvé, je nutné použít **[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** rutiny zaregistrujte zprostředkovatele služby Azure Recovery s vaším předplatným.

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Můžete ověřit, že zprostředkovatele úspěšně zaregistrován, pomocí následujících příkazů:
    ```powershell
    Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    Ve výstupu tohoto příkazu **RegistrationState** měli změnit na **registrované**. Pokud ne, stačí spustit **[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** rutinu znovu.

Tyto úlohy je možné automatizovat pomocí prostředí PowerShell:

* [Vytvoření trezoru služby Recovery Services](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Zálohování virtuálních počítačů Azure](backup-azure-vms-automation.md#back-up-azure-vms)
* [Aktivace úlohy zálohování](backup-azure-vms-automation.md#trigger-a-backup)
* [Monitorování úlohy zálohování](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Obnovení virtuálního počítače Azure](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Následující kroky vás provedou vytvořením trezor služby Recovery Services. Trezor služby Recovery Services se liší od trezoru služby Backup.

1. Trezor služby Recovery Services je prostředek Resource Manageru, proto musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořte skupinu prostředků pomocí **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** rutiny. Při vytváření skupiny prostředků, zadejte název a umístění pro skupinu prostředků.  

    ```powershell
    New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Použití **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** rutina pro vytvoření trezoru služby Recovery Services. Ujistěte se, k určení stejného umístění trezoru, protože byl použit pro skupinu prostředků.

    ```powershell
    New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Zadejte typ redundance úložiště se použije. můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantního úložiště (GRS)](../storage/common/storage-redundancy-grs.md). Následující příklad ukazuje, že možnost - BackupStorageRedundancy pro testvault nastavená na GeoRedundant.

    ```powershell
    $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Recovery Services do proměnné.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Zobrazit tyto trezory v rámci předplatného

Chcete-li zobrazit všechny trezorů v předplatném, použijte  **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)**:

```powershell
Get-AzureRmRecoveryServicesVault
```

Výstup se podobá následujícímu příkladu si všimněte, že jsou k dispozici přidružený název skupiny prostředků a umístění.

```
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Zálohování virtuálních počítačů Azure

Ochrana virtuálních počítačů pomocí trezoru služby Recovery Services. Před použitím ochrany nastavte kontext trezoru (typ chráněných dat v trezoru) a ověřte zásady ochrany. Plán spouštění úloh zálohování a jak dlouho se uchovávají každý snímek zálohy, jsou zásady ochrany.

### <a name="set-vault-context"></a>Kontext trezoru set

Než povolíte ochranu virtuálního počítače, použijte **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** nastavit kontext trezoru. Po nastavení se kontext trezoru použije pro všechny další rutiny. Následující příklad nastaví kontext trezoru pro trezor, *testvault*.

```powershell
Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Vytvořit zásady ochrany.

Při vytváření trezoru služby Recovery Services se vytvoří i výchozí zásady ochrany a uchovávání informací. Výchozí zásady ochrany aktivují úlohu zálohování každý den v určenou dobu. Výchozí zásady uchovávání informací uchovávají denní bod obnovení po dobu 30 dnů. Výchozí zásady můžete použít k rychlému zajištění ochrany vašeho virtuálního počítače a upravovat zásady později pomocí různých údajů.

Použití **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** zobrazení zásad ochrany k dispozici v trezoru. Načte konkrétní zásady, nebo pro zobrazení zásad přidružený k typu úlohy, můžete použít tuto rutinu. Následující příklad získá zásady pro typ úlohy, AzureVM.

```powershell
Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

Výstup se podobá následujícímu příkladu:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Časové pásmo BackupTime pole v prostředí PowerShell je čas UTC. Když čas zálohování se zobrazí na webu Azure Portal, čas upraveny na místní časové pásmo.
>
>

Zásady zálohování ochrany je přidružená aspoň jednu zásadu uchovávání informací. Zásady uchovávání informací Určuje, jak dlouho bod obnovení je zachována před odstraněním. Použití **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** zobrazení výchozí zásady uchovávání informací.  Podobně můžete použít **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** získat výchozí plán zásady. **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** rutina vytvoří objekt prostředí PowerShell, který obsahuje informace o zásadách zálohování. Objekty zásad plán a uchovávání se používají jako vstupy **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** rutiny. Následující příklad ukládá v proměnné plán zásady a zásady uchovávání informací. V příkladu se používá k definici parametrů při vytváření zásad ochrany těchto proměnných *NewPolicy*.

```powershell
$schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Výstup se podobá následujícímu příkladu:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Povolení ochrany

Jakmile se zásady ochrany, které jste definovali, stále musíte povolit zásady pro položku. Použití **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** povolit ochranu. Povolení ochrany vyžaduje dva objekty - položka a zásady. Jakmile zásadu přidružená k trezoru, pracovního postupu zálohování se aktivuje v době definovaný v plánu zásady.

Následující příklady zapnout ochranu položky V2VM, pomocí zásad NewPolicy. V příkladech se liší podle toho, jestli je virtuální počítač zašifrovaný a jaký typ šifrování.

Povolení ochrany na **nešifrované virtuální počítače Resource Manageru**:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Povolení ochrany na **šifrovaných virtuálních počítačů (šifrované pomocí klíče BEK a KEK)**, musí udělit oprávnění služby Azure Backup čtení klíče a tajné kódy z trezoru klíčů.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Povolení ochrany na **šifrovaných virtuálních počítačů (šifrované pomocí klíče BEK pouze)**, musí udělit oprávnění služby Azure Backup pro čtení tajných klíčů z trezoru klíčů.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Pokud používáte cloud Azure Government, použijte pro parametr hodnotu ff281ffe-705c-4f53-9f37-a40e6f2c68f3 **- ServicePrincipalName** v [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) rutiny.
>
>

Povolení ochrany na klasickém virtuálním počítači:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Upravit zásady ochrany.

Chcete-li upravit zásady ochrany, použijte [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) upravit objekty SchedulePolicy nebo parametru RetentionPolicy.

Následující příklad změní až 365 dnů uchování bodu obnovení.

```powershell
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Aktivujte zálohování

Použití **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** k aktivaci úlohy zálohování. Pokud se jedná o prvotní zálohování, je úplná záloha. Následné zálohy trvat, než přírůstková kopie. Nezapomeňte použít **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** nastavit kontext trezoru před aktivací úlohy zálohování. V následujícím příkladu se předpokládá, že již byl nastaven kontext trezoru.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

Výstup se podobá následujícímu příkladu:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Časové pásmo polí StartTime a EndTime v prostředí PowerShell je čas UTC. Když čas se zobrazí na webu Azure Portal, čas upraveny na místní časové pásmo.
>
>

## <a name="monitoring-a-backup-job"></a>Monitorování úlohy zálohování

Dlouhotrvající operace, jako je například úlohy zálohování, můžete sledovat bez použití webu Azure portal. Chcete-li zjistit stav probíhající úlohy, použijte **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** rutiny. Tato rutina načte úlohy zálohování pro konkrétní trezor a tento trezor je zadán v kontext trezoru. Následující příklad získá stav probíhající úlohy jako pole a uloží stav $joblist proměnné.

```powershell
$joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

Výstup se podobá následujícímu příkladu:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Namísto dotazování těchto úloh pro dokončení – což je zbytečné další kód – použít **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** rutiny. Tato rutina pozastaví provádění až do dokončení úlohy nebo nebude dosaženo hodnoty zadaný časový limit.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Obnovení virtuálního počítače Azure

Je důležité rozdíl mezi obnovení virtuálního počítače pomocí webu Azure portal a obnovení virtuálního počítače pomocí Powershellu. Pomocí Powershellu operace obnovení byla dokončena, jakmile disky a informace o konfiguraci z bodu obnovení se vytvoří. Operace obnovení není vytvoření virtuálního počítače. K vytvoření virtuálního počítače z disku, naleznete v části [vytvořit virtuální počítač z obnovených disků](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Pokud nechcete obnovit celý virtuální počítač, ale chcete obnovit nebo obnovení několika souborů ze zálohy virtuálního počítače Azure, přečtěte si [souboru sekce obnovení](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> Operace obnovení není vytvoření virtuálního počítače.
>
>

Následující obrázek znázorňuje hierarchii objektů z RecoveryServicesVault dolů BackupRecoveryPoint.

![Hierarchie objektů služby zotavení zobrazující BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Chcete-li obnovit zálohovaná data, identifikujte zálohovaná položka a bod obnovení, který obsahuje data bodu v čase. Použití **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** k obnovení dat z trezoru ke svému účtu.

Toto jsou základní kroky k obnovení virtuálního počítače Azure:

* Vyberte virtuální počítač.
* Zvolte bod obnovení.
* Obnovení disků.
* Vytvoření virtuálního počítače z uložené disků.

### <a name="select-the-vm"></a>Vyberte virtuální počítač

Chcete-li získat objekt prostředí PowerShell, který identifikuje přímo zálohovaná položka, spusťte z kontejneru v trezoru a nahlíželi hierarchií objektu. Chcete-li vybrat kontejner, který představuje virtuální počítač, použijte **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** rutiny a kanálem, který se **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** rutiny.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Zvolte bod obnovení.

Použití **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** rutiny pro zobrazení seznamu všech bodů obnovení zálohované položky. Zvolte bod obnovení pro obnovení. Pokud si nejste jistí, který bod obnovení používat, je vhodné zvolte nejnovější RecoveryPointType = AppConsistent bod v seznamu.

V následujícím skriptu, proměnné, **$rp**, je pole bodů obnovení pro vybrané záložní položky z posledních sedmi dnů. Pole je v obráceném pořadí řazení čas nabízí nejnovější bod obnovení na pozici 0. Použijte standardní indexování pole prostředí PowerShell a vyberte bod obnovení. V tomto příkladu $rp [0] Vybere poslední bod obnovení.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Výstup se podobá následujícímu příkladu:

```powershell
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

### <a name="restore-the-disks"></a>Obnovit disky

Použití **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** rutiny obnovit data zálohovaná položka a konfigurace bodu obnovení. Jakmile identifikujete bod obnovení, použijte jako hodnotu **- RecoveryPoint** parametru. V ukázce výše **$rp [0]** byl bod obnovení použít. V následujícím ukázkovém kódu **$rp [0]** je bod obnovení pro obnovení disku.

Chcete-li obnovit disky a konfigurační informace:

```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>Obnovení spravovaných disků

> [!NOTE]
> Pokud chcete obnovit jako spravovaných disků zálohovaného virtuálního počítače má spravované disky, zavedli jsme možnost z modulu Azure RM Powershellu v 6.7.0. a vyšší
>
>

Zadejte další parametr **TargetResourceGroupName** k určení RG, ke kterému se obnovit spravované disky. 

> [!NOTE]
> Důrazně doporučujeme použít **TargetResourceGroupName** parametr pro obnovení spravovaných disků, protože to má za následek výrazné zlepšení výkonu. Navíc z prostředí Azure Powershell Az modulu 1.0 a vyšší, tento parametr je povinný v případě obnovení se spravovanými disky
>
>


```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

**VMConfig.JSON** soubor se obnoví do účtu úložiště a spravované disky se obnoví do zadané cílové RG.

Výstup se podobá následujícímu příkladu:

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Použití **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** rutiny čekat na dokončení úlohy obnovení.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Po dokončení úlohy obnovení, použijte **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** rutiny zobrazíte podrobnosti o operaci obnovení. Vlastnost JobDetails obsahuje informace potřebné k opětovnému sestavení virtuálního počítače.

```powershell
$restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Po obnovení disků, přejděte k další části, chcete-li vytvořit virtuální počítač.

## <a name="create-a-vm-from-restored-disks"></a>Vytvořte virtuální počítač z obnovených disků

Po obnovení disků použijte následující kroky k vytvoření a konfigurace virtuálního počítače z disku.

> [!NOTE]
> Vytvořit z obnovených disků šifrovaných virtuálních počítačů, Azure role musí mít oprávnění k provedení akce, **Microsoft.KeyVault/vaults/deploy/action**. Pokud vaše role toto oprávnění nemá, vytvořte vlastní roli s touto akcí. Další informace najdete v tématu [vlastní role Azure RBAC](../role-based-access-control/custom-roles.md).
>
>

> [!NOTE]
> Po obnovení disků, teď můžete získat šablonu nasazení, který můžete přímo použít k vytvoření nového virtuálního počítače. Žádné další různé rutiny PS k vytvoření virtuálních počítačů spravované nebo nespravované, které jsou šifrované a nešifrované.

Podrobnosti o výsledné úloze poskytuje šablona identifikátoru URI, které jde dotazovat a nasadit.

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

Nasadit šablonu, kterou chcete vytvořit nový virtuální počítač, jak je vysvětleno [tady](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy#deploy-a-template-from-an-external-source).

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

V následující části jsou uvedené kroky potřebné k vytvoření virtuálního počítače pomocí souboru "Funkce".

> [!NOTE]
> Důrazně doporučujeme použít šablonu nasazení, které jsou podrobně popsané výše vytvořte virtuální počítač. V této části (body 1 až 6) bude brzy přestanou používat.

1. Dotaz na vlastnosti obnoveného disku pro podrobnosti o úloze.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Nastavit kontext úložiště Azure a obnovení konfiguračního souboru JSON.

   ```powershell
   Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzureStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Použití konfiguračního souboru JSON pro vytvoření konfigurace virtuálního počítače.

   ```powershell
   $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Připojte disk s operačním systémem a datové disky. Tento krok obsahuje příklady různých spravované a zašifrovaný konfigurací virtuálních počítačů. Použijte tento příklad, který vyhovuje vaší konfigurace virtuálního počítače.

   * **Virtuální počítače spravované bez a nešifrované** – použijte následující ukázku pro nespravované, bez šifrování virtuálních počítačů.

       ```powershell
       Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **Nespravovaná a šifrovaných virtuálních počítačů pomocí Azure AD (pouze klíče BEK)** – nespravované, šifrovaných virtuálních počítačů s Azure AD (šifrované pomocí klíče BEK pouze), budete muset obnovit tajný kód k trezoru klíčů, než disky můžete připojit. Další informace najdete v tématu [obnovení šifrovaných virtuálních počítačů z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Následující příklad ukazuje, jak připojit operačního systému a datové disky pro šifrované virtuální počítače. Při nastavování disk s operačním systémem, ujistěte se, že jste zmínili odpovídající typ operačního systému.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Nespravovaná a šifrovaných virtuálních počítačů pomocí Azure AD (klíče BEK a KEK)** – nespravované, šifrovaných virtuálních počítačů pomocí Azure AD (šifrované pomocí klíče BEK a KEK), obnovit klíč a tajný kód trezoru klíčů před připojením disků. Další informace najdete v tématu [obnovení šifrovaných virtuálních počítačů z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Následující příklad ukazuje, jak připojit operačního systému a datové disky pro šifrované virtuální počítače.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
      ```

   * **Nespravovaná a šifrovaných virtuálních počítačů bez Azure AD (pouze klíče BEK)** – nespravované, šifrovaných virtuálních počítačů bez Azure AD (šifrované pomocí klíče BEK pouze), pokud zdroj **keyVault/tajný klíč nejsou k dispozici** obnovit tajné klíče do služby key vault pomocí postupu v [obnovení virtuálního počítače bez šifrování z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Potom spusťte tyto skripty pro nastavení šifrování podrobnosti u obnoveného objektu blob operačního systému (Tento krok není povinný pro datový objekt blob). $Dekurl můžete načíst z obnovené trezor klíčů.<br>

   Níže uvedený skript je třeba provést pouze v případě, že trezor klíčů/tajný klíč zdroj není k dispozici.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzureStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    Po **tajné klíče jsou k dispozici** a podrobnosti o šifrování jsou také nastavit u objektu Blob operačního systému, připojení disků pomocí skriptu uvedena níže.<br>

    Pokud se trezor klíčů/tajných klíčů zdroje jsou už k dispozici, nemusí výše skript spustí.

      ```powershell
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Nespravovaná a šifrovaných virtuálních počítačů bez Azure AD (klíče BEK a KEK)** – nespravované, šifrovaných virtuálních počítačů bez Azure AD (šifrované pomocí klíče BEK a KEK), pokud zdroj **trezor klíčů/klíč/tajný kód nejsou k dispozici** obnovení klíče a tajné klíče trezoru klíčů pomocí postupu v [obnovení virtuálního počítače bez šifrování z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Potom spusťte tyto skripty pro nastavení šifrování podrobnosti u obnoveného objektu blob operačního systému (Tento krok není povinný pro datový objekt blob). $Dekurl a $kekurl můžete načíst z obnovené trezor klíčů.

   Níže uvedený skript je třeba provést pouze v případě, že zdrojový trezor klíčů/klíč/tajný klíč není k dispozici.

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzureStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```
   Po **klíče/tajné klíče jsou k dispozici** a podrobnosti o šifrování jsou nastaveny na objekt Blob operačního systému, připojte disky pomocí skriptu uvedena níže.

    Pokud zdrojový trezor klíčů/klíče/tajné klíče jsou k dispozici, nemusí provést skript výše.

    ```powershell
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

  * **Spravovaný a bez šifrování virtuálního počítače** – spravované nešifrované virtuální počítače, připojit obnovenou spravované disky. Podrobné informace najdete v tématu [připojení datového disku k virtuálnímu počítači s Windows pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md).

  * **Spravovat a šifrovaných virtuálních počítačů s Azure AD (pouze klíče BEK)** – spravovaných šifrovaných virtuálních počítačů s Azure AD (šifrované pomocí klíče BEK pouze) a připojit obnovenou spravované disky. Podrobné informace najdete v tématu [připojení datového disku k virtuálnímu počítači s Windows pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md).

  * **Spravovat a šifrovaných virtuálních počítačů s Azure AD (klíče BEK a KEK)** – spravovaných šifrovaných virtuálních počítačů s Azure AD (šifrované pomocí klíče BEK a KEK), připojit obnovenou spravované disky. Podrobné informace najdete v tématu [připojení datového disku k virtuálnímu počítači s Windows pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md).

  * **Spravovat a šifrovaných virtuálních počítačů bez Azure AD (pouze klíče BEK)** -pro spravované, šifrované virtuální počítače bez Azure AD (šifrované pomocí klíče BEK pouze), pokud zdroj **keyVault/tajný klíč nejsou k dispozici** obnovit tajné klíče pomocí služby key vault Postup v [obnovení virtuálního počítače bez šifrování z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Potom spusťte tyto skripty pro nastavení šifrování podrobnosti na obnovený disk s operačním systémem (Tento krok není povinný pro datový disk). $Dekurl můžete načíst z obnovené trezor klíčů.

    Níže uvedený skript je třeba provést pouze v případě, že trezor klíčů/tajný klíč zdroj není k dispozici.  

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $diskupdateconfig = New-AzureRmDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzureRmDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzureRmDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

    Poté, co tajné klíče jsou k dispozici a podrobnosti o šifrování jsou nastaveny na disk s operačním systémem, připojit obnovenou spravované disky, najdete v článku [připojení datového disku k virtuálnímu počítači s Windows pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md).

  * **Spravovat a šifrovaných virtuálních počítačů bez Azure AD (klíče BEK a KEK)** – pro spravované, šifrované virtuální počítače bez Azure AD (šifrované pomocí klíče BEK a KEK), pokud zdroj **trezor klíčů/klíč/tajný kód nejsou k dispozici** obnovení klíče a tajné klíče na klíč pomocí postupu v trezoru [obnovení virtuálního počítače bez šifrování z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Potom spusťte tyto skripty pro nastavení šifrování podrobnosti na obnovený disk s operačním systémem (Tento krok není povinný pro datový disk). $Dekurl a $kekurl můžete načíst z obnovené trezor klíčů.

  Níže uvedený skript je třeba provést pouze v případě, že zdrojový trezor klíčů/klíč/tajný klíč není k dispozici.

  ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzureRmDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzureRmDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzureRmDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzureRmDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Poté, co klíče/tajné klíče jsou k dispozici a podrobnosti o šifrování jsou nastaveny na disk s operačním systémem, připojit obnovenou spravované disky, najdete v článku [připojení datového disku k virtuálnímu počítači s Windows pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md).

5. Nastavení sítě.

    ```powershell
    $nicName="p1234"
    $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzureRmVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzureRmVirtualNetwork
    $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Vytvořte virtuální počítač.

    ```powershell  
    New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Nabízená oznámení ADE rozšíření.

  * **Pro virtuální počítač s Azure AD** – použijte následující příkaz ručně povolit šifrování pro datové disky  

    **Klíč BEK pouze**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

    **Klíč BEK a KEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

  * **Pro virtuální počítač bez Azure AD** – použijte následující příkaz ručně povolit šifrování pro datové disky.

    Pokud incase během provádění příkazu požádá o AADClientID, pak je potřeba aktualizovat prostředí Azure PowerShell.

    **Klíč BEK pouze**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **Klíč BEK a KEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Obnovení souborů ze zálohy virtuálního počítače Azure

Kromě obnovení disků, můžete také obnovit jednotlivé soubory ze zálohy virtuálního počítače Azure. Funkce obnovení souborů poskytuje přístup ke všem souborům v bodu obnovení. Správa souborů pomocí Průzkumníka souborů, stejně jako u běžných souborů.

Toto jsou základní kroky obnovení souboru ze zálohy virtuálního počítače Azure:

* Vyberte virtuální počítač
* Zvolte bod obnovení.
* Připojte disky bod obnovení
* Zkopírujte požadované soubory
* Odpojení disku

### <a name="select-the-vm"></a>Vyberte virtuální počítač

Chcete-li získat objekt prostředí PowerShell, který identifikuje přímo zálohovaná položka, spusťte z kontejneru v trezoru a nahlíželi hierarchií objektu. Chcete-li vybrat kontejner, který představuje virtuální počítač, použijte **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** rutiny a kanálem, který se **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** rutiny.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Zvolte bod obnovení.

Použití **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** rutiny pro zobrazení seznamu všech bodů obnovení zálohované položky. Zvolte bod obnovení pro obnovení. Pokud si nejste jistí, který bod obnovení používat, je vhodné zvolte nejnovější RecoveryPointType = AppConsistent bod v seznamu.

V následujícím skriptu, proměnné, **$rp**, je pole bodů obnovení pro vybrané záložní položky z posledních sedmi dnů. Pole je v obráceném pořadí řazení čas nabízí nejnovější bod obnovení na pozici 0. Použijte standardní indexování pole prostředí PowerShell a vyberte bod obnovení. V tomto příkladu $rp [0] Vybere poslední bod obnovení.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Výstup se podobá následujícímu příkladu:

```
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

### <a name="mount-the-disks-of-recovery-point"></a>Připojte disky bod obnovení

Použití **[Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)** rutiny pro získání skript, který připojí všechny disky bodu obnovení.

> [!NOTE]
> Disky jsou připojené jako disky připojené přes iSCSI k počítači, ve kterém se skript spouští. Připojení začne okamžitě a nebudou účtovat žádné poplatky.
>
>

```powershell
Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

Výstup se podobá následujícímu příkladu:

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Spusťte skript na počítači, kam chcete soubory obnovit. Ke spuštění skriptu, je nutné zadat heslo k dispozici. Po disky jsou připojené, pomocí Průzkumníka souborů Windows k nalezení nové svazky a soubory. Další informace najdete v článku zálohování [obnovení souborů ze záloh virtuálních počítačů Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Odpojení disků

Po zkopírování požadovaných souborů použijte **[zakázat AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** k odpojení disků. Ujistěte se, že odpojení disků tak přístup k souborům bodu obnovení se odebere.

```powershell
Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>Další postup

Pokud chcete spolupracovat s prostředky Azure pomocí Powershellu, najdete v článku prostředí PowerShell, [nasazení a Správa zálohování pro Windows Server](backup-client-automation.md). Pokud budete spravovat zálohy aplikace DPM, najdete v článku, [nasazení a Správa zálohování aplikace DPM](backup-dpm-automation.md). Z těchto článků mají verze pro nasazení Resource Manager a klasickými nasazeními.  
