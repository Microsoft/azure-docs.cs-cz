---
title: Použití PowerShellu k zálohování Windows serveru do Azure
description: V tomto článku se naučíte, jak pomocí PowerShellu nastavit Azure Backup pro Windows Server nebo klienta Windows a spravovat zálohování a obnovení.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 6285b7fc6493090ab0bead5f00124a6eaa02dc7e
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172450"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Nasazení a správa zálohování do Azure pro servery Windows / klienty Windows pomocí PowerShellu

V tomto článku se dozvíte, jak pomocí PowerShellu nastavit Azure Backup pro Windows Server nebo klienta Windows a spravovat zálohování a obnovení.

## <a name="install-azure-powershell"></a>Instalace prostředí Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Začněte tím, [že nainstalujete nejnovější verzi PowerShellu](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services

Následující kroky vás provedou vytvořením trezoru Recovery Services. Recovery Services trezor se liší od trezoru záloh.

1. Pokud používáte Azure Backup poprvé, musíte pomocí rutiny **Register-AzResourceProvider** zaregistrovat poskytovatele služby Azure Recovery Services s vaším předplatným.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Recovery Services trezor je prostředek ARM, takže ho budete muset umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořit novou. Při vytváření nové skupiny prostředků zadejte název a umístění pro skupinu prostředků.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. K vytvoření nového trezoru použijte rutinu **New-AzRecoveryServicesVault** . Nezapomeňte zadat stejné umístění úložiště, jaké bylo použito pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Zadejte typ redundance úložiště, který se má použít. můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy-grs.md). Následující příklad ukazuje možnost-BackupStorageRedundancy pro testVault je nastavená na geograficky redundantní.

   > [!TIP]
   > Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Recovery Services do proměnné.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Zobrazení trezorů v předplatném

K zobrazení seznamu všech trezorů v rámci aktuálního předplatného použijte **příkaz Get-AzRecoveryServicesVault** . Tento příkaz můžete použít ke kontrole vytvoření nového trezoru nebo k zobrazení, které trezory jsou k dispozici v rámci předplatného.

V seznamu je spuštěný příkaz, **Get-AzRecoveryServicesVault**a všechny trezory v rámci předplatného.

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

Před instalací agenta Azure Backup musíte mít Instalační program stažený a přítomen na Windows serveru. Nejnovější verzi instalačního programu můžete získat z webu [Microsoft Download Center](https://aka.ms/azurebackup_agent) nebo ze stránky řídicího panelu Recovery Servicesového trezoru. Uložte instalační program do snadno přístupného umístění, jako je například * C:\Downloads\*.

K získání tohoto stahovacího prostředí použijte taky PowerShell:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Pokud chcete nainstalovat agenta, spusťte v konzole PowerShellu se zvýšenými oprávněními následující příkaz:

```powershell
MARSAgentInstaller.exe /q
```

Tím se nainstaluje Agent se všemi výchozími možnostmi. Instalace na pozadí trvá několik minut. Pokud nezadáte možnost */Nu* , otevře se na konci instalace okno **web Windows Update** , kde můžete vyhledat jakékoli aktualizace. Po nainstalování se Agent zobrazí v seznamu nainstalovaných programů.

Seznam nainstalovaných programů zobrazíte tak, že přejdete do **ovládacích panelů** > **programy** > **programy a funkce**.

![Agent nainstalován](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Možnosti instalace

Chcete-li zobrazit všechny možnosti, které jsou k dispozici prostřednictvím příkazového řádku, použijte následující příkaz:

```powershell
MARSAgentInstaller.exe /?
```

K dispozici jsou tyto možnosti:

| Možnost | Podrobnosti | Výchozí |
| --- | --- | --- |
| /q |Tichá instalace |- |
| /p: "umístění" |Cesta k instalační složce pro agenta Azure Backup. |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s: umístění |Cesta ke složce mezipaměti pro agenta Azure Backup. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Výslovný souhlas s Microsoft Update |- |
| /nu |Po dokončení instalace Nekontrolovat aktualizace |- |
| /d |Odinstaluje agenta Microsoft Azure Recovery Services. |- |
| /pH |Adresa hostitele proxy serveru |- |
| /po |Číslo portu hostitele proxy serveru |- |
| /pu |Uživatelské jméno hostitele proxy serveru |- |
| /pw |Heslo proxy |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Registrace klientského počítače se systémem Windows Server nebo Windows do trezoru Recovery Services

Po vytvoření trezoru Recovery Services Stáhněte nejnovějšího agenta a přihlašovací údaje trezoru a uložte je do vhodného umístění, jako je C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>Registrace pomocí nástroje PS AZ Module

Z důvodu základních omezení platformy, stažení přihlašovacích údajů k trezoru vyžaduje certifikát podepsaný svým držitelem, a to v nejnovějším modulu AZ Module of PowerShell. Následující příklad ukazuje, jak poskytnout certifikát podepsaný svým držitelem a stáhnout přihlašovací údaje trezoru.

```powershell
$Vault = Get-AzRecoveryServicesVault -ResourceGroupName $rgName -Name $VaultName
$cert = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname xxxxxxxxxxxxx
$certificate =[System.Convert]::ToBase64String($cert.RawData)
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Certificate $certificate -Vault $vault -Backup -Path $CredsPath
```

Na klientském počítači s Windows serverem nebo Windows spusťte rutinu [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) , která zaregistruje počítač do trezoru.
Tato a další rutiny, které se používají pro zálohování, jsou z modulu MSONLINE, který AgentInstaller Mars přidal jako součást procesu instalace.

Instalační program agenta neaktualizuje proměnnou $Env:P SModulePath. To znamená, že automatické načtení modulu se nezdařilo. K vyřešení tohoto problému můžete postupovat takto:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Případně můžete modul ve skriptu ručně načíst následujícím způsobem:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Po načtení rutin pro online zálohování zaregistrujete přihlašovací údaje trezoru:

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
> Nepoužívejte relativní cesty k určení souboru s přihlašovacími údaji trezoru. Je nutné zadat absolutní cestu jako vstup do rutiny.
>
>

## <a name="networking-settings"></a>Nastavení sítě

Když je připojení počítače se systémem Windows k Internetu prostřednictvím proxy server, může být pro agenta k dispozici také nastavení proxy serveru. V tomto příkladu není proxy server, takže explicitně vymažeme všechny informace týkající se proxy serveru.

Využití šířky pásma se dá řídit také pomocí možností `work hour bandwidth` a `non-work hour bandwidth` v dané sadě dnů v týdnu.

Nastavení podrobností o proxy serveru a šířce pásma se provádí pomocí rutiny [set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) :

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

Zálohovaná data odesílaná do Azure Backup jsou šifrovaná za účelem ochrany důvěrnosti dat. Šifrovací heslo je "heslo", které dešifruje data v době obnovení.

Musíte **vygenerovat bezpečnostní kód PIN, a**to tak, že v části **nastavení** > **vlastnosti** > **bezpečnostní kód PIN** v Azure Portal **Recovery Services trezoru** . Pak tuto akci použijte jako `generatedPIN` v příkazu:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Po nastavení ponechte informace pro heslo bezpečně a zabezpečené. Nemůžete obnovit data z Azure bez tohoto přístupového hesla.
>
>

## <a name="back-up-files-and-folders"></a>Zálohování souborů a složek

Všechny zálohy ze serverů a klientů Windows do Azure Backup se řídí zásadami. Zásady se skládá ze tří částí:

1. **Plán zálohování** , který určuje, kdy je potřeba považovat zálohy a synchronizovat se službou.
2. **Plán uchovávání informací** , který určuje, jak dlouho mají být body obnovení v Azure uchovávány.
3. **Specifikace zahrnutí/vyloučení souborů** , která určuje, co se má zálohovat

V tomto dokumentu, protože Automatizujeme zálohování, Nepředpokládáme, že se nic nenakonfigurovalo. Začneme vytvořením nové zásady zálohování pomocí rutiny [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) .

```powershell
$NewPolicy = New-OBPolicy
```

V tuto chvíli je zásada prázdná a k definování položek, které se mají zahrnout nebo vyloučit, se budou potřebovat další rutiny, které se budou spouštět a kde se budou ukládat zálohy.

### <a name="configuring-the-backup-schedule"></a>Konfigurace plánu zálohování

První ze tří částí zásad je plán zálohování, který je vytvořený pomocí rutiny [New-OBSchedule](https://technet.microsoft.com/library/hh770401) . Plán zálohování definuje, kdy je potřeba považovat zálohy. Při vytváření plánu musíte zadat dva vstupní parametry:

* **Dny v týdnu** , kdy se má záloha spustit Úlohu zálohování můžete spustit pouze na jeden den nebo každý den v týdnu nebo libovolnou kombinaci mezi.
* **Denní dobu** , kdy se má záloha spustit. V případě, že se bude aktivovat zálohování, můžete definovat až tři různé časy dne.

Můžete například nakonfigurovat zásady zálohování, které běží na 16:00 každé sobotu a neděli.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

Plán zálohování je potřeba přidružit k zásadě a to lze dosáhnout pomocí rutiny [set-OBSchedule](https://technet.microsoft.com/library/hh770407) .

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>Konfigurace zásad uchovávání informací

Zásady uchovávání informací definují, jak dlouho se budou uchovávat body obnovení vytvořené z úloh zálohování. Při vytváření nových zásad uchovávání informací pomocí rutiny [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) můžete zadat počet dní, po které musí být body obnovení zálohy Azure Backup uchovávány. Následující příklad nastaví zásady uchovávání informací po dobu sedmi dnů.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

Zásady uchovávání informací musí být přidružené k hlavním zásadám pomocí rutiny [set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405):

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

Objekt `OBFileSpec` definuje soubory, které mají být zahrnuty a vyloučeny v záloze. Toto je sada pravidel, která vychází z chráněných souborů a složek v počítači. Podle potřeby můžete mít tolik pravidel pro zahrnutí nebo vyloučení souborů a přidružit je k zásadám. Při vytváření nového objektu OBFileSpec můžete:

* Zadejte soubory a složky, které se mají zahrnout.
* Zadejte soubory a složky, které chcete vyloučit.
* Zadejte rekurzivní zálohu dat ve složce (nebo), zda mají být zálohovány pouze soubory nejvyšší úrovně v zadané složce.

Druhý je dosaženo použitím příznaku-nerekurzivní v příkazu New-OBFileSpec.

V následujícím příkladu provedeme zálohování svazku C: a D: a vyloučíme binární soubory operačního systému ve složce Windows a v jakýchkoli dočasných složkách. K tomu vytvoříme dvě specifikace souborů pomocí rutiny [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) -One pro začlenění a jednu pro vyloučení. Po vytvoření specifikací souborů jsou tyto zásady přidruženy k zásadě pomocí rutiny [Add-OBFileSpec](https://technet.microsoft.com/library/hh770424) .

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

### <a name="configuring-schedule-and-retention"></a>Konfigurace plánu a uchovávání

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>Ověřují se zásady.

```powershell
Get-OBSystemStatePolicy
 ```

### <a name="applying-the-policy"></a>Použití zásad

Nyní je objekt zásad dokončený a má přidružený plán zálohování, zásady uchovávání informací a seznam souborů pro zahrnutí a vyloučení. Tato zásada se teď dá považovat za Azure Backup k použití. Než použijete nově vytvořenou zásadu, ujistěte se, že neexistují žádné existující zásady zálohování přidružené k serveru pomocí rutiny [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415) . Po odebrání zásady se zobrazí výzva k potvrzení. K přeskočení potvrzení použijte příznak `-Confirm:$false` s rutinou.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Potvrzování objektu zásad se provádí pomocí rutiny [set-OBPolicy](https://technet.microsoft.com/library/hh770421) . Zobrazí se také dotaz na potvrzení. K přeskočení potvrzení použijte příznak `-Confirm:$false` s rutinou.

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

Podrobnosti o existující zásadě zálohování můžete zobrazit pomocí rutiny [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) . Pomocí rutiny [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) pro plán zálohování a rutiny [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) pro zásady uchovávání informací můžete přejít k dalším podrobnostem.

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

### <a name="performing-an-on-demand-backup"></a>Provádění zálohování na vyžádání

Po nastavení zásady zálohování se budou zálohy vyskytnout podle plánu. Spuštění zálohování na vyžádání je také možné pomocí rutiny [Start-OBBackup](https://technet.microsoft.com/library/hh770426) :

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

## <a name="restore-data-from-azure-backup"></a>Obnovit data z Azure Backup

Tato část vás provede jednotlivými kroky pro automatizaci obnovení dat z Azure Backup. V takovém případě zahrnuje následující kroky:

1. Výběr zdrojového svazku
2. Vyberte bod zálohování, který chcete obnovit.
3. Vyberte položku, kterou chcete obnovit.
4. Aktivace procesu obnovení

### <a name="picking-the-source-volume"></a>Výběr zdrojového svazku

Aby bylo možné obnovit položku z Azure Backup, nejprve je nutné určit zdroj položky. Vzhledem k tomu, že spouštíme příkazy v kontextu Windows serveru nebo klienta Windows, je počítač už identifikovaný. Dalším krokem při identifikaci zdroje je identifikace svazku, který ho obsahuje. Seznam svazků nebo zdrojů zálohovaných z tohoto počítače se dá načíst spuštěním rutiny [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) . Tento příkaz vrátí pole všech zdrojů zálohovaných z tohoto serveru nebo klienta.

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

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Výběr bodu zálohování, ze kterého se má obnovit

Seznam bodů zálohování načtete spuštěním rutiny [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) s příslušnými parametry. V našem příkladu vybereme nejnovější bod zálohování pro zdrojový svazek *D:* a použijete ho k obnovení konkrétního souboru.

```powershell
$Rps = Get-OBRecoverableItem -Source $Source[1]
```

```Output
IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :

IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 17-Jun-15 6:31:31 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :
```

Objekt `$Rps` je pole záložních bodů. První prvek je poslední bod a n-tý prvek je nejstarším bodem. K výběru nejnovějšího bodu použijeme `$Rps[0]`.

### <a name="choosing-an-item-to-restore"></a>Výběr položky k obnovení

Chcete-li identifikovat přesný soubor nebo složku pro obnovení, rekurzivně použijte rutinu [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) . Tímto způsobem lze hierarchii složek procházet výhradně pomocí `Get-OBRecoverableItem`.

Pokud v tomto příkladu chceme obnovit soubor *finance. xls* , můžeme na něj odkazovat pomocí `$FilesFolders[1]`objektu.

```powershell
$FilesFolders = Get-OBRecoverableItem $Rps[0]
$FilesFolders
```

```Output
IsDir : True
ItemNameFriendly : D:\MyData\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\
LocalMountPoint : D:\
MountPointName : D:\
Name : MyData
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime : 15-Jun-15 8:49:29 AM
```

```powershell
$FilesFolders = Get-OBRecoverableItem $FilesFolders[0]
$FilesFolders
```

```Output
IsDir : False
ItemNameFriendly : D:\MyData\screenshot.oxps
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps
LocalMountPoint : D:\
MountPointName : D:\
Name : screenshot.oxps
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 228313
ItemLastModifiedTime : 21-Jun-14 6:45:09 AM

IsDir : False
ItemNameFriendly : D:\MyData\finances.xls
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls
LocalMountPoint : D:\
MountPointName : D:\
Name : finances.xls
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 96256
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

Můžete také vyhledat položky, které chcete obnovit, pomocí rutiny ```Get-OBRecoverableItem```. V našem příkladu můžete vyhledat *finance. xls.* k souboru můžeme získat popisovač spuštěním tohoto příkazu:

```powershell
$Item = Get-OBRecoverableItem -RecoveryPoint $Rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>Aktivace procesu obnovení

Pro aktivaci procesu obnovení musíme nejdřív zadat možnosti obnovení. To se dá udělat pomocí rutiny [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) . V tomto příkladu předpokládáme, že chceme soubory obnovit do *C:\Temp*. Můžeme také předpokládat, že chceme Přeskočit soubory, které už existují v cílové složce *C:\Temp*. Chcete-li vytvořit takovou možnost obnovení, použijte následující příkaz:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Nyní spusťte proces obnovení pomocí příkazu [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) na vybraném `$Item` z výstupu rutiny `Get-OBRecoverableItem`:

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

## <a name="uninstalling-the-azure-backup-agent"></a>Odinstalace agenta Azure Backup

Odinstalaci agenta Azure Backup můžete provést pomocí následujícího příkazu:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Odinstalace binárních souborů agenta z počítače má nějaký důsledek v úvahu:

* Odebere z počítače filtr souboru a sledování změn se zastaví.
* Z počítače se odeberou všechny informace o zásadách, ale informace o zásadách se v této službě budou dál ukládat.
* Všechny plány zálohování se odeberou a neprovádí se žádné další zálohy.

Data uložená v Azure ale zůstanou a v rámci nastavení zásad uchovávání informací se uchovávají. Starší body jsou automaticky zastaraly.

## <a name="remote-management"></a>Vzdálená správa

Veškerou správu Azure Backup agenta, zásad a zdrojů dat je možné provádět vzdáleně prostřednictvím prostředí PowerShell. Počítač, který se bude spravovat vzdáleně, musí být připravený správně.

Ve výchozím nastavení je služba WinRM nakonfigurovaná na ruční spuštění. Typ spuštění musí být nastaven na hodnotu *automaticky* a služba by měla být spuštěna. Chcete-li ověřit, zda je spuštěna služba WinRM, hodnota vlastnosti status by měla být *spuštěna*.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell by měl být nakonfigurovaný pro vzdálenou komunikaci.

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

Počítač se teď dá spravovat vzdáleně – od instalace agenta. Například následující skript zkopíruje agenta do vzdáleného počítače a nainstaluje ho.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Další kroky

Další informace o Azure Backup pro Windows Server/klienta:

* [Seznámení s Azure Backup](backup-introduction-to-azure-backup.md)
* [Zálohování serverů Windows](backup-configure-vault.md)
