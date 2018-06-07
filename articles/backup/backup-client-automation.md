---
title: Pomocí prostředí PowerShell pro zálohování Windows serveru do Azure
description: Zjistěte, jak nasadit a spravovat Azure Backup pomocí prostředí PowerShell
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/24/2018
ms.author: saurse
ms.openlocfilehash: f69975fc30dfdfbcdd801bcdb552e8b4be948607
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606266"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Nasazení a správa zálohování do Azure pro servery Windows / klienty Windows pomocí PowerShellu
Tento článek ukazuje, jak pomocí prostředí PowerShell pro nastavení služby Azure Backup na Windows serveru nebo klienta Windows a Správa zálohování a obnovení.

## <a name="install-azure-powershell"></a>Instalace prostředí Azure PowerShell
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Tento článek se zaměřuje na Azure Resource Manager (ARM) a rutiny MS Online zálohování Powershellu, které vám umožní používat Trezor služeb zotavení ve skupině prostředků.

Října 2015 byla vydána Azure PowerShell 1.0. Tato verze 0.9.8 bylo úspěšně dokončeno. Uvolněte a uvést do režimu o významné změny, zejména v vzoru pro pojmenovávání rutin. Rutiny verze 1.0 dodržují formát pojmenování {sloveso}-AzureRm {podstatné jméno}, kdežto názvy ve verzi 0.9.8 neobsahují označení **Rm** (třeba New-AzureRmResourceGroup místo New-AzureResourceGroup). Pokud používáte prostředí Azure PowerShell 0.9.8, musíte nejdřív spuštěním příkazu **Switch-AzureMode AzureResourceManager** spustit režim Resource Manager. Tento příkaz není nutné v 1.0 nebo novější.

Pokud chcete použít skripty napsané pro verzi 0.9.8 prostředí v prostředí 1.0 nebo novější, měli byste pečlivě aktualizace a testovat skripty v předprodukčním prostředí před jejich používání v produkčním prostředí předejdete neočekávané dopad.

[Stáhněte si nejnovější verzi prostředí PowerShell](https://github.com/Azure/azure-powershell/releases) (minimální požadovaná verze je: 1.0.0)

[!INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services
Následující kroky vás provedou vytvoření trezoru služeb zotavení. Trezor služeb zotavení se liší od úložiště záloh.

1. Pokud používáte Azure Backup poprvé, musíte použít **Register-AzureRMResourceProvider** rutiny k registraci poskytovatele služeb zotavení Azure s vaším předplatným.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Trezor služeb zotavení je prostředek ARM, proto musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořte novou. Když vytváříte novou skupinu prostředků, zadejte název a umístění pro skupinu prostředků.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "WestUS"
    ```
3. Použití **New-AzureRmRecoveryServicesVault** vytvořte nový trezor. Ujistěte se, že zadejte stejné umístění pro úložiště, jako byl použit pro skupinu prostředků.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```
4. Zadejte typ redundance úložiště se použije. můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy-grs.md). Následující příklad ukazuje, že je možnost - BackupStorageRedundancy pro testVault nastavena na GeoRedundant.

   > [!TIP]
   > Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Recovery Services do proměnné.
   >
   >

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Zobrazit trezorů v předplatném.
Použití **Get-AzureRmRecoveryServicesVault** Chcete-li zobrazit seznam všech trezorů v aktuálním předplatném. Tento příkaz můžete použít, chcete-li zkontrolovat, zda byl vytvořen nový trezor nebo jaké trezory jsou k dispozici v rámci předplatného.

Spusťte příkaz, **Get-AzureRmRecoveryServicesVault**, a jsou uvedeny všechny trezorů v rámci předplatného.

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


[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Instalace agenta Azure Backup
Před instalací agenta Azure Backup, musíte mít instalační program stažené a existuje v systému Windows Server. Můžete získat nejnovější verzi instalačního programu z [Microsoft Download Center](http://aka.ms/azurebackup_agent) nebo ze stránky řídicího panelu trezoru služeb zotavení. Instalační program uložte na snadno dostupném místě jako * C:\Downloads\*.

Chcete-li získat nástroj pro stažení programu Alternativně pomocí prostředí PowerShell:
 
 ```
 $MarsAURL = 'Http://Aka.Ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Pokud chcete nainstalovat agenta, spusťte v konzolu prostředí PowerShell se zvýšenými oprávněními následující příkaz:

```
PS C:\> MARSAgentInstaller.exe /q
```

Tím se nainstaluje agent s výchozími možnostmi. Instalace trvá několik minut na pozadí. Pokud nezadáte */nu* možnost potom **Windows Update** otevře se okno na konci instalace k vyhledání všech aktualizací. Po instalaci agenta se zobrazí v seznamu nainstalovaných programů.

Chcete-li zobrazit seznam nainstalovaných programů, přejděte na **ovládací panely** > **programy** > **programy a funkce**.

![Agent nainstalován](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Možnosti instalace
Pokud chcete zobrazit všechny možnosti, které jsou k dispozici prostřednictvím příkazového řádku, použijte následující příkaz:

```
PS C:\> MARSAgentInstaller.exe /?
```

Mezi dostupné možnosti patří:

| Možnost | Podrobnosti | Výchozí |
| --- | --- | --- |
| /q |Tichá instalace |- |
| / p: "umístění" |Cesta ke složce instalace pro agenta Azure Backup. |C:\Program Files\Microsoft Azure Recovery Services agenta |
| / s: "umístění" |Cesta ke složce mezipaměti pro agenta Azure Backup. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Výslovný souhlas ke službě Microsoft Update |- |
| /nu |Nekontrolovat aktualizace po dokončení instalace |- |
| /d |Odinstaluje Agenta Microsoft Azure Recovery Services. |- |
| /pH |Adresa proxy hostitele |- |
| /Po |Číslo portu proxy hostitele |- |
| /Pu |Uživatelské jméno proxy hostitele |- |
| /pw |Heslo pro proxy server |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Registrace systému Windows Server a klientský počítač systému Windows do trezoru služeb zotavení
Po vytvoření trezoru služeb zotavení, stáhněte si nejnovější verzi agenta a přihlašovací údaje trezoru a uložte ho do vhodného umístění jako C:\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
```

V systému Windows Server nebo klientský počítač systému Windows, spusťte [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) rutiny počítač zaregistrovat v trezoru.
Tato a další rutiny používané pro zálohování, jsou z MSONLINE modul, který Mars AgentInstaller přidat jako součást procesu instalace. 

Instalační program agenta nelze aktualizovat $Env: PSModulePath proměnné. To znamená, že automaticky načíst modul selže. Tento problém vyřešíte tak můžete provést následující:

```
PS C:\>  $Env:psmodulepath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Alternativně můžete ručně načíst modul ve vašem skriptu následujícím způsobem:

```
PS C:\>  Import-Module  'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'

```

Po načtení rutiny Online zálohování, zaregistrujte se přihlašovací údaje úložiště:


```

PS C:\> Start-OBRegistration -VaultCredentials $credsfilename.FilePath -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Nepoužívejte relativní cesty k určení souboru s přihlašovacími údaji. Jako vstup do rutiny, musí zadat absolutní cestu.
>
>

## <a name="networking-settings"></a>Nastavení sítě
Pokud připojení Windows počítače k Internetu prostřednictvím proxy serveru, nastavení proxy serveru se dá zadat i k agentovi. V tomto příkladu neexistuje žádný proxy server, takže jsme jsou explicitně vymazání údaje související s proxy serverem.

Využití šířky pásma se dá taky nastavit pomocí možnosti ```work hour bandwidth``` a ```non-work hour bandwidth``` pro danou sadu dny v týdnu.

Nastavení serveru proxy a šířky pásma podrobnosti se provádí pomocí [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) rutiny:

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Nastavení šifrování
Zálohování data přenášená do služby Azure Backup je zašifrovaná chránit jejich důvěrnost dat. Šifrovací přístupové heslo je 'heslo' k dešifrování dat v době obnovení.

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
PS C:\> $PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force 
PS C:\> $PassCode   = 'AzureR0ckx'
PS C:\> Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase
Server properties updated successfully
```

> [!IMPORTANT]
> Zachování informací o přístupové heslo bezpečném, po nastavení. Nelze obnovit data ze služby Azure bez tohoto hesla.
>
>

## <a name="back-up-files-and-folders"></a>Zálohování souborů a složek
Zásady se řídí všechny zálohy ze Windows serverů a klientů do služby Azure Backup. Zásady se skládá ze tří částí:

1. A **plán zálohování** určující při zálohování muset provést a synchronizovat se službou.
2. A **plán uchovávání informací** který určuje, jak dlouho chcete zachovat body obnovení v Azure.
3. A **zadání zahrnutí nebo vyloučení souboru** který určuje, co by měl být zálohovány.

V tomto dokumentu protože jsme se automatizaci zálohování, budete předpokládáme, že nic nebyl nakonfigurován. Začneme vytvořením nové zásady zálohování pomocí [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) rutiny.

```
PS C:\> $newpolicy = New-OBPolicy
```

V tuto chvíli je prázdné zásady a ostatní rutiny jsou potřebné k definování položky, které je budou zahrnuté ani vyloučené, při zálohování se spustí, a tam, kde se uloží v zálohování.

### <a name="configuring-the-backup-schedule"></a>Konfigurace plánu zálohování
První den 3 části zásada je plán zálohování, která je vytvořena pomocí [New-OBSchedule](https://technet.microsoft.com/library/hh770401) rutiny. Plán zálohování definuje, kdy je třeba přijmout zálohy. Při vytváření plánu je třeba zadat 2 vstupní parametry:

* **Dny v týdnu** , má-li spustit zálohování. Můžete spustit úlohu zálohování na právě jeden den nebo každý den v týdnu nebo libovolnou kombinaci mezi nimi.
* **Časy dne** spuštění zálohování. Můžete definovat až 3 různou dobu, kdy se spustí zálohování.

Například může nakonfigurovat zásady zálohování, který se spustí na 16: 00 každou sobotu a neděli.

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

Plán zálohování musí být přidružený k zásadě, a to jde dosáhnout pomocí [Set-OBSchedule](https://technet.microsoft.com/library/hh770407) rutiny.

```
PS C:> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>Konfigurace zásad uchovávání
Zásady uchovávání informací definuje, jak dlouho se uchovají body obnovení vytvořené z úlohy zálohování. Při vytváření nové zásady uchovávání informací pomocí [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) rutiny, můžete zadat počet dnů, které je třeba body obnovení záloh pro zachování s Azure Backup. Následující příklad nastaví zásady uchovávání informací 7 dní.

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

Zásady uchovávání informací musí být přidružené hlavní zásadám, pomocí rutiny [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405):

```
PS C:\> Set-OBRetentionPolicy -Policy $newpolicy -RetentionPolicy $retentionpolicy

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
### <a name="including-and-excluding-files-to-be-backed-up"></a>Zahrnutí a vyloučení souborů k zálohování
```OBFileSpec``` Objekt definuje soubory zahrnout a vyloučit zálohu. To je sada pravidel, která obor chráněné soubory a složky na počítači. Můžete mít mnoho souboru pravidla zahrnutí nebo vyloučení podle potřeby a přidružovat je k zásadě. Při vytváření nového objektu OBFileSpec, můžete:

* Zadejte soubory a složky, které mají být zahrnuty
* Zadejte soubory a složky, které se mají vyloučit
* Zadejte rekurzivní zálohování dat v složku (nebo) zda pouze nejvyšší úrovně soubory v zadané složce by měl být zálohuje nahoru.

K tomu se dosáhne použitím příznaku - nerekurzivní v příkazu New-OBFileSpec.

V následujícím příkladu jsme budete zálohování svazku C: a D: a vyloučit binární soubory operačního systému ve složce Windows a všechny dočasné složky. K tomu vytvoříme dvě specifikace pomocí souboru [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) rutiny – jeden pro zahrnutí a jeden pro vyloučení. Po vytvoření souboru specifikace jsou spojené s použitím zásad [přidat OBFileSpec](https://technet.microsoft.com/library/hh770424) rutiny.

```
PS C:\> $inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")

PS C:\> $exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude

PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $inclusions

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


PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $exclusions

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

### <a name="applying-the-policy"></a>Použití zásad
Objekt zásad teď dokončení a má přidružené plánu zálohování, zásady uchovávání informací a seznam souborů zahrnutí nebo vyloučení. Tato zásada může být nyní potvrdit pro zálohování Azure používat. Před použitím nově vytvořenou zásadu zajistěte, aby žádné existující zásady zálohování, které jsou přidružené k serveru pomocí [odebrat OBPolicy](https://technet.microsoft.com/library/hh770415) rutiny. Odebrání zásady zobrazí výzvu k potvrzení. Přeskočit použití potvrzení ```-Confirm:$false``` příznak pomocí rutiny.

```
PS C:> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Potvrzení objektu zásad se provádí pomocí [Set-OBPolicy](https://technet.microsoft.com/library/hh770421) rutiny. Také to požádá o potvrzení. Přeskočit použití potvrzení ```-Confirm:$false``` příznak pomocí rutiny.

```
PS C:> Set-OBPolicy -Policy $newpolicy
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

Můžete zobrazit podrobnosti o existující zásady zálohování pomocí [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) rutiny. Podrobnostem další pomocí [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) rutina pro plán zálohování a [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) rutina pro zásady uchovávání informací

```
PS C:> Get-OBPolicy | Get-OBSchedule
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing

PS C:> Get-OBPolicy | Get-OBRetentionPolicy
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing

PS C:> Get-OBPolicy | Get-OBFileSpec
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

### <a name="performing-an-ad-hoc-backup"></a>Provádění ad hoc zálohy
Jakmile byla nastavena zásada zálohování dojde k zálohování podle plánu. Aktivuje ad hoc zálohy je také možné pomocí [Start-OBBackup](https://technet.microsoft.com/library/hh770426) rutiny:

```
PS C:> Get-OBPolicy | Start-OBBackup
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

## <a name="restore-data-from-azure-backup"></a>Obnovení dat z Azure Backup
Tato část vás provede kroky pro automatizaci obnovení dat z Azure Backup. Díky tomu zahrnuje následující kroky:

1. Vyberte zdrojový svazek
2. Vyberte bod obnovení zálohy
3. Vyberte položku, kterou chcete obnovit
4. Aktivační událost v procesu obnovení

### <a name="picking-the-source-volume"></a>Výběr zdrojovém svazku
Chcete-li obnovit položky z Azure Backup, musíte nejprve k identifikaci zdroje položky. Vzhledem k tomu, že jsme se provádění příkazů v kontextu systému Windows Server nebo klienta Windows, počítač je už definovaný. Dalším krokem při identifikaci zdroj je k identifikaci svazku, který ji obsahuje. Seznam svazků nebo zdrojů zálohovaných z tohoto počítače se dá načíst spuštěním [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) rutiny. Tento příkaz vrátí pole všech zdrojů zálohovat z tohoto serveru nebo klienta.

```
PS C:> $source = Get-OBRecoverableSource
PS C:> $source
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Výběr bodu zálohy pro obnovení
Načtení seznamu body zálohy spuštěním [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) rutiny s příslušnými parametry. V našem příkladu jsme vybrali nejnovější bod zálohy pro zdrojový svazek *D:* a použít ho k obnovení konkrétní soubor.

```
PS C:> $rps = Get-OBRecoverableItem -Source $source[1]
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
Objekt ```$rps``` je pole body zálohy. První prvek je poslední bod a n-tou elementu bude nejstarší bod. Zvolte nejnovější bod, použijeme ```$rps[0]```.

### <a name="choosing-an-item-to-restore"></a>Vybrat položku, kterou chcete obnovit
K identifikaci přesný soubor nebo složku pro obnovení, použijte rekurzivně [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) rutiny. Tímto způsobem lze procházet hierarchii složek výhradně pomocí ```Get-OBRecoverableItem```.

V tomto příkladu, pokud chcete obnovit soubor *finances.xls* jsme může odkazovat, který používá objekt ```$filesFolders[1]```.

```
PS C:> $filesFolders = Get-OBRecoverableItem $rps[0]
PS C:> $filesFolders
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

PS C:> $filesFolders = Get-OBRecoverableItem $filesFolders[0]
PS C:> $filesFolders
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

Můžete také vyhledat položky k obnovení pomocí ```Get-OBRecoverableItem``` rutiny. V našem příkladu, k vyhledání *finances.xls* nám může získat popisovač v souboru tak, že spustíte tento příkaz:

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>Spuštění procesu obnovení
K aktivaci v procesu obnovení, musíme nejprve nastavte možnosti obnovení. To můžete provést pomocí [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) rutiny. Pro tento příklad předpokládejme, že chceme obnovit soubory *C:\temp*. Také Předpokládejme, že chceme přeskočit soubory, které již existují na cílovou složku *C:\temp*. Pokud chcete vytvořit možnost obnovení, použijte následující příkaz:

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Teď spustit proces obnovení pomocí [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) příkaz na vybraném ```$item``` z výstupu ```Get-OBRecoverableItem``` rutiny:

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```


## <a name="uninstalling-the-azure-backup-agent"></a>Odinstalace agenta Azure Backup
Odinstalace agenta Azure Backup lze provést pomocí následujícího příkazu:

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

Odinstalace binárních souborů agenta z počítače má některé důsledky vzít v úvahu:

* Odebere filtr souborů z počítače a sledování změn je zastavena.
* Všechny informace o zásadách se odebere z počítače, ale informace o zásadách dále uložena ve službě.
* Se odeberou všechny plány zálohování a jsou provedeny žádné další zálohy.

Však data uložená v Azure zůstane a se uchovávají podle nastavení zásad uchovávání informací, které jste. Starší body jsou automaticky stará.

## <a name="remote-management"></a>Vzdálená správa
Veškerá správa, kolem agenta Azure Backup, zásady a zdroje dat lze provést vzdáleně pomocí prostředí PowerShell. Na počítač, který bude spravovat vzdáleně musí být správně připravena.

Ve výchozím nastavení je Služba WinRM nakonfigurován pro ruční spuštění. Typ spuštění musí být nastavený na *automatické* a musí být spuštěna. Pokud chcete ověřit, že Služba WinRM je spuštěná, by měla být hodnota vlastnosti stav *systémem*.

```
PS C:\> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

Prostředí PowerShell by měl být nakonfigurovaný pro vzdálenou komunikaci.

```
PS C:\> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:\> Set-ExecutionPolicy unrestricted -force
```

Tento počítač teď můžete spravovat vzdáleně – od instalace agenta. Například následující skript zkopíruje agenta ke vzdálenému počítači a ho nainstaluje.

```
PS C:\> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## <a name="next-steps"></a>Další postup
Další informace o Azure Backup pro Windows Server nebo klienta najdete v tématu

* [Seznámení s Azure Backup](backup-introduction-to-azure-backup.md)
* [Zálohování serverů Windows](backup-configure-vault.md)
