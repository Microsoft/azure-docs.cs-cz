---
title: Zálohování Windows Serveru do Azure pomocí PowerShellu
description: V tomto článku se dozvíte, jak používat PowerShell k nastavení Zálohování Azure na Windows Serveru nebo v klientovi Windows a ke správě zálohování a obnovení.
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: efe0b93fe1e37990422ffbd2256e38c12401dca5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673197"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Nasazení a správa zálohování do Azure pro servery Windows / klienty Windows pomocí PowerShellu

Tento článek ukazuje, jak používat PowerShell k nastavení Azure Backup na Windows Serveru nebo v klientovi Windows a ke správě zálohování a obnovení.

## <a name="install-azure-powershell"></a>Instalace prostředí Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li začít, [nainstalujte nejnovější verzi prostředí PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services

Následující kroky vás provedou vytvořením trezoru služby Recovery Services. Trezor služby Recovery Services se liší od trezoru zálohování.

1. Pokud používáte Azure Backup poprvé, musíte použít rutinu **Register-AzResourceProvider** k registraci poskytovatele služby Azure Recovery Service s vaším předplatným.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Trezor služby Recovery Services je prostředek ARM, takže jej musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořit novou. Při vytváření nové skupiny prostředků zadejte název a umístění skupiny prostředků.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. K vytvoření nového trezoru použijte rutinu **New-AzRecoveryServicesVault.** Nezapomeňte zadat stejné umístění pro úschovnu, jaké bylo použito pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Zadejte typ redundance úložiště, která se má použít. můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantní úložiště (GRS).](../storage/common/storage-redundancy-grs.md) Následující příklad ukazuje -BackupStorageRedundancy možnost testVault je nastavena na GeoRedundant.

   > [!TIP]
   > Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Recovery Services do proměnné.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Zobrazení trezorů v předplatném

Pomocí **příkazu Get-AzRecoveryServicesVault** zobrazíte seznam všech trezorů v aktuálním předplatném. Pomocí tohoto příkazu můžete zkontrolovat, zda byl vytvořen nový trezor, nebo zjistit, jaké trezory jsou k dispozici v předplatném.

Spusťte příkaz **Get-AzRecoveryServicesVault**a všechny trezory v předplatném jsou uvedeny.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Instalace agenta Azure Backup

Před instalací agenta Azure Backup je třeba nechat instalační program stáhnout a prezentovat na Windows Serveru. Nejnovější verzi instalačního programu můžete získat ze [služby Stažení softwaru společnosti Microsoft](https://aka.ms/azurebackup_agent) nebo ze stránky řídicího panelu úložiště služby Recovery Services. Uložte instalační program do snadno přístupného umístění,\*jako je *C:\Downloads .

Případně můžete pomocí prostředí PowerShell získat stahovač:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Chcete-li agenta nainstalovat, spusťte v konzole PowerShell u sil se zvýšenými oprávněními následující příkaz:

```powershell
MARSAgentInstaller.exe /q
```

Tím nainstalujete agenta se všemi výchozími možnostmi. Instalace trvá několik minut na pozadí. Pokud nezadáte */nu* možnost, pak **windows update** okno se otevře na konci instalace ke kontrole všech aktualizací. Po instalaci se agent zobrazí v seznamu nainstalovaných programů.

Seznam nainstalovaných programů naleznete v **Ovládacích panelech** > **Programy** > **programy a funkce**.

![Nainstalovaný agent](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Možnosti instalace

Chcete-li zobrazit všechny možnosti dostupné prostřednictvím příkazového řádku, použijte následující příkaz:

```powershell
MARSAgentInstaller.exe /?
```

Dostupné možnosti zahrnují:

| Možnost | Podrobnosti | Výchozí |
| --- | --- | --- |
| /q |Tichá instalace |- |
| /p:"umístění" |Cesta k instalační složce pro agenta Azure Backup. |C:\Program Files\Agent služby Microsoft Azure Recovery Services |
| /s:"umístění" |Cesta ke složce mezipaměti pro agenta Azure Backup. |C:\Program Files\Agent služby Microsoft Azure Recovery Services\Scratch |
| /m |Přihlášení k webu Microsoft Update |- |
| /nu |Po dokončení instalace nekontrolujte aktualizace |- |
| /d |Odinstaluje agenta služby Microsoft Azure Recovery Services |- |
| /ph |Adresa hostitele proxy serveru |- |
| /po |Číslo portu hostitele proxy serveru |- |
| /pu |Uživatelské jméno hostitele proxy serveru |- |
| /pw |Heslo proxy |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Registrace klientského počítače se systémem Windows Server nebo Windows do úložiště služby Recovery Services

Po vytvoření trezoru služby Recovery Services stáhněte nejnovějšího agenta a přihlašovací údaje trezoru a uložte je na vhodném místě, jako je C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>Registrace pomocí modulu PS Az

> [!NOTE]
> Chyba s generováním certifikátu trezoru je opravena ve verzi Az 3.5.0. Ke stažení certifikátu trezoru použijte verzi az 3.5.0 nebo vyšší.

V nejnovějším modulu Az prostředí PowerShell z důvodu základních omezení platformy vyžaduje stažení přihlašovacích údajů trezoru certifikát podepsaný svým držitelem. Následující příklad ukazuje, jak poskytnout certifikát podepsaný svým držitelem a stáhnout přihlašovací údaje trezoru.

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

V klientském počítači se systémem Windows Server nebo Windows spusťte rutinu [Start-OBRegistration](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) a zaregistrujte počítač pomocí trezoru.
Toto a další rutiny používané pro zálohování jsou z modulu MSONLINE, který byl přidán jako součást procesu instalace.

Instalační program agenta neaktualizuje proměnnou $Env:PSModulePath. To znamená, že automatické načtení modulu se nezdaří. Chcete-li tento problém vyřešit, můžete provést následující kroky:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Případně můžete modul do skriptu načíst ručně následujícím způsobem:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Po načtení rutin online zálohování zaregistrujete přihlašovací údaje trezoru:

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> K určení souboru pověření v úschovně nepoužívejte relativní cesty. Je nutné zadat absolutní cestu jako vstup do rutiny.
>
>

## <a name="networking-settings"></a>Nastavení sítě

Pokud je připojení počítače se systémem Windows k internetu prostřednictvím proxy serveru, může být agentovi poskytnuto také nastavení proxy serveru. V tomto příkladu neexistuje žádný proxy server, takže explicitně vyklízení všech informací souvisejících s proxy serverem.

Využití šířky pásma lze `work hour bandwidth` také `non-work hour bandwidth` řídit pomocí možností a pro danou sadu dnů v týdnu.

Nastavení podrobností o serveru proxy a šířce pásma se provádí pomocí rutiny [Set-OBMachineSetting:](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obmachinesetting?view=winserver2012-ps)

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Nastavení šifrování

Záložní data odeslaná do služby Azure Backup jsou šifrovaná, aby byla chráněna důvěrnost dat. Heslo pro šifrování je "heslo" pro dešifrování dat v době obnovení.

Pin zabezpečení musíte vygenerovat tak, že vyberete **Generovat** **v** > části Nastavení nastavení zabezpečení**vlastností** > **v** části **trezoru služby Recovery Services** na webu Azure Portal. Potom použijte jako `generatedPIN` v příkazu:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Po nastavení uchovávejte informace o přístupové mši v bezpečí. Bez této přístupové fráze nelze obnovit data z Azure.
>
>

## <a name="back-up-files-and-folders"></a>Zálohování souborů a složek

Všechny zálohy ze serverů Windows a klientů na Azure Backup se řídí zásadami. Politika se skládá ze tří částí:

1. **Plán zálohování,** který určuje, kdy je třeba provést zálohy a synchronizovat se službou.
2. **Plán uchovávání informací,** který určuje, jak dlouho chcete zachovat body obnovení v Azure.
3. **Specifikace zahrnutí/vyloučení souboru,** která určuje, co by mělo být zálohováno.

V tomto dokumentu, protože jsme automatizaci zálohování, budeme předpokládat, že nic nebylo nakonfigurováno. Začneme vytvořením nové zásady zálohování pomocí rutiny [New-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obpolicy?view=winserver2012-ps)

```powershell
$NewPolicy = New-OBPolicy
```

V tomto okamžiku je zásada prázdná a další rutiny jsou potřebné k definování, jaké položky budou zahrnuty nebo vyloučeny, kdy budou spuštěny zálohy a kde budou zálohy uloženy.

### <a name="configuring-the-backup-schedule"></a>Konfigurace plánu zálohování

První ze tří částí zásady je plán zálohování, který je vytvořen pomocí rutiny [New-OBSchedule.](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obschedule?view=winserver2012-ps) Plán zálohování definuje, kdy je třeba provést zálohování. Při vytváření plánu je třeba zadat dva vstupní parametry:

* **Dny v týdnu,** které by měly být spuštěny zálohy. Úlohu zálohování můžete spustit pouze jeden den nebo každý den v týdnu nebo libovolnou kombinaci mezi nimi.
* **Časy dne,** kdy by měla být spuštěna záloha. Můžete definovat až tři různé denní doby, kdy bude záloha spuštěna.

Můžete například nakonfigurovat zásady zálohování, které běží každou sobotu a neděli v 16:00.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

Plán zálohování musí být přidružen k zásadě, což lze dosáhnout pomocí rutiny [Set-OBSchedule.](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obschedule?view=winserver2012-ps)

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>Konfigurace zásad uchovávání informací

Zásady uchovávání informací definují, jak dlouho jsou zachovány body obnovení vytvořené ze zálohovacích úloh. Při vytváření nové zásady uchovávání informací pomocí rutiny [New-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obretentionpolicy?view=winserver2012-ps) můžete určit počet dní, po které je třeba zachovat body obnovení zálohy pomocí služby Azure Backup. Následující příklad nastaví zásady uchovávání informací sedm dní.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

Zásady uchovávání informací musí být přidruženy k hlavní zásady pomocí rutiny [Set-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obretentionpolicy?view=winserver2012-ps):

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="including-and-excluding-files-to-be-backed-up"></a>Zahrnutí a vyloučení souborů, které mají být zálohovány

Objekt `OBFileSpec` definuje soubory, které mají být zahrnuty a vyloučeny do zálohy. Toto je sada pravidel, která obor z chráněných souborů a složek v počítači. Můžete mít libovolný počet pravidel pro zahrnutí nebo vyloučení souborů a přidružit je k zásadě. Při vytváření nového objektu OBFileSpec můžete:

* Určení souborů a složek, které mají být zahrnuty
* Určení souborů a složek, které mají být vyloučeny
* Zadejte rekurzivní zálohování dat ve složce (nebo) zda mají být zálohovány pouze soubory nejvyšší úrovně v zadané složce.

Ten je dosaženo pomocí -NonRecursive příznak v New-OBFileSpec příkazu.

V níže uvedeném příkladu budeme zálohovat svazek C: a D: a vyloučit binární soubory operačního systému ve složce Windows a všechny dočasné složky. Chcete-li tak učinit, vytvoříme dvě specifikace souboru pomocí rutiny [New-OBFileSpec](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obfilespec?view=winserver2012-ps) - jeden pro zahrnutí a jeden pro vyloučení. Jakmile jsou specifikace souboru vytvořeny, jsou přidruženy k zásadě pomocí rutiny [Add-OBFileSpec.](https://docs.microsoft.com/powershell/module/msonlinebackup/add-obfilespec?view=winserver2012-ps)

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>Použití zásady

Nyní je objekt zásad dokončen a má přidružený plán zálohování, zásady uchovávání informací a seznam souborů zahrnutí/vyloučení. Tato zásada může být nyní potvrzena pro Azure Backup používat. Před použitím nově vytvořené zásady se ujistěte, že neexistují žádné existující zásady zálohování přidružené k serveru pomocí rutiny [Remove-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/remove-obpolicy?view=winserver2012-ps) Odebráním zásady vyzvete k potvrzení. Chcete-li potvrzení přeskočit, použijte `-Confirm:$false` příznak s rutinou.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Potvrzení objektu zásad se provádí pomocí rutiny [Set-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obpolicy?view=winserver2012-ps) To bude také požádat o potvrzení. Chcete-li potvrzení přeskočit, použijte `-Confirm:$false` příznak s rutinou.

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Můžete zobrazit podrobnosti o existující zásady zálohování pomocí [rutiny Get-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obpolicy?view=winserver2012-ps) Můžete přejít k podrobnostem dále pomocí [rutiny Get-OBSchedule](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obschedule?view=winserver2012-ps) pro plán zálohování a [rutina Get-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obretentionpolicy?view=winserver2012-ps) pro zásady uchovávání informací

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-on-demand-backup"></a>Provedení zálohy na vyžádání

Po nastavení zásady zálohování dojde k zálohování podle plánu. Spuštění zálohy na vyžádání je také možné pomocí rutiny [Start-OBBackup:](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obbackup?view=winserver2012-ps)

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="back-up-windows-server-system-state-in-mabs-agent"></a>Zálohování stavu systému Windows Server v agentovi MABS

Tato část popisuje příkaz prostředí PowerShell pro nastavení stavu systému v agentovi MABS

### <a name="schedule"></a>Plán

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Uchovávání

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Konfigurace plánu a uchovávání informací

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>Ověření zásady

```powershell
Get-OBSystemStatePolicy
 ```

## <a name="restore-data-from-azure-backup"></a>Obnovení dat ze služby Azure Backup

Tato část vás provede kroky pro automatizaci obnovení dat z Azure Backup. To zahrnuje následující kroky:

1. Výběr zdrojového svazku
2. Zvolte záložní bod, který chcete obnovit.
3. Určení položky, kterou chcete obnovit
4. Spuštění procesu obnovení

### <a name="picking-the-source-volume"></a>Výběr zdrojového svazku

Chcete-li obnovit položku z Azure Backup, musíte nejprve identifikovat zdroj položky. Vzhledem k tomu, že provádíme příkazy v kontextu systému Windows Server nebo klienta systému Windows, je počítač již identifikován. Dalším krokem při identifikaci zdroje je identifikace svazku, který jej obsahuje. Seznam svazků nebo zdrojů zálohovaných z tohoto počítače lze načíst spuštěním rutiny [Get-OBRecoverableSource.](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverablesource?view=winserver2012-ps) Tento příkaz vrátí pole všech zdrojů zálohovaných z tohoto serveru/klienta.

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Výběr záložního bodu, ze kterého chcete obnovit

Načíst seznam záložních bodů spuštěním [rutiny Get-OBRecoverableItem](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverableitem?view=winserver2012-ps) s příslušnými parametry. V našem příkladu vybereme nejnovější bod zálohování pro zdrojový svazek *C:* a použijeme ho k obnovení konkrétního souboru.

```powershell
$Rps = Get-OBRecoverableItem $Source[0]
$Rps
```

```Output

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/16/2019 7:00:19 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :
```

Objekt `$Rps` je pole záložních bodů. První prvek je nejnovější bod a Nth element je nejstarší bod. Chcete-li vybrat nejnovější bod, budeme používat `$Rps[0]`.

### <a name="specifying-an-item-to-restore"></a>Určení položky, kterou chcete obnovit

Chcete-li obnovit určitý soubor, zadejte název souboru vzhledem ke kořenovému svazku. Chcete-li například načíst c:\Test\Cat.job, proveďte následující příkaz.

```powershell
$Item = New-OBRecoverableItem $Rps[0] "Test\cat.jpg" $FALSE
$Item
```

```Output
IsDir                : False
ItemNameFriendly     : C:\Test\cat.jpg
ItemNameGuid         :
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : cat.jpg
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM

```

### <a name="triggering-the-restore-process"></a>Spuštění procesu obnovení

Chcete-li spustit proces obnovení, musíme nejprve zadat možnosti obnovení. To lze provést pomocí rutiny [New-OBRecoveryOption.](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obrecoveryoption?view=winserver2012-ps) V tomto příkladu předpokládejme, že chceme obnovit soubory do *c:\temp*. Předpokládejme také, že chceme přeskočit soubory, které již existují v cílové složce *C:\temp*. Chcete-li vytvořit takovou možnost obnovení, použijte následující příkaz:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Nyní spusťte proces obnovení pomocí příkazu [Start-OBRecovery](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obrecovery?view=winserver2012-ps) na vybraném `$Item` výstupu `Get-OBRecoverableItem` rutiny:

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>Odinstalace agenta azure backup

Odinstalace agenta Azure Backup lze provést pomocí následujícího příkazu:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Odinstalování binárních souborů agentů ze stroje má některé důsledky, které je třeba zvážit:

* Odebere filtr souborů ze zařízení a sledování změn je zastaveno.
* Všechny informace o zásadách jsou odebrány z počítače, ale informace o zásadách jsou nadále uloženy ve službě.
* Všechny plány zálohování jsou odebrány a žádné další zálohy jsou převzaty.

Data uložená v Azure však zůstanou a zůstanou zachována podle nastavení zásad uchovávání informací, které jste nastavili. Starší body jsou automaticky zráty.

## <a name="remote-management"></a>Vzdálená správa

Veškerou správu kolem agenta Azure Backup, zásad y a zdrojů dat lze provádět vzdáleně prostřednictvím prostředí PowerShell. Počítač, který bude spravován vzdáleně, musí být připraven správně.

Ve výchozím nastavení je služba WinRM nakonfigurována pro ruční spuštění. Typ spuštění musí být nastaven na *automatické* a služba by měla být spuštěna. Chcete-li ověřit, zda je spuštěna služba WinRM, měla by být *spuštěna*hodnota vlastnosti Stav .

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

Prostředí PowerShell by mělo být nakonfigurováno pro vzdálené hlasování.

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

Stroj lze nyní spravovat vzdáleně - od instalace agenta. Například následující skript zkopíruje agenta do vzdáleného počítače a nainstaluje jej.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure Backup for Windows Server/Client:

* [Seznámení s Azure Backup](backup-introduction-to-azure-backup.md)
* [Zálohování serverů Windows](backup-windows-with-mars-agent.md)
