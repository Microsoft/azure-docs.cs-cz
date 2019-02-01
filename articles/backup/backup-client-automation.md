---
title: Použití Powershellu k zálohování Windows serveru do Azure
description: Zjistěte, jak nasadit a spravovat Azure Backup pomocí Powershellu
services: backup
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/24/2018
ms.author: pvrk
ms.openlocfilehash: d430f6252157c5d34aa236ef88f8490b4ad6a184
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497940"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Nasazení a správa zálohování do Azure pro servery Windows / klienty Windows pomocí PowerShellu
V tomto článku se dozvíte, jak pomocí prostředí PowerShell pro nastavení zálohování Azure ve Windows serveru nebo klienta Windows a Správa zálohování a obnovení.

## <a name="install-azure-powershell"></a>Instalace prostředí Azure PowerShell
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Tento článek se zaměřuje na Azure Resource Manageru (ARM) a rutiny Powershellu MS Online zálohování, která vám umožní použít trezor služby Recovery Services ve skupině prostředků.

Října 2015 byla vydána příkazového řádku Azure PowerShell 1.0. Tato verze byla úspěšná ve verzi 0.9.8 vydání a převést do režimu o významné změny, zejména v vzor pojmenování spočívající v rutinách. Rutiny verze 1.0 dodržují formát pojmenování {sloveso}-AzureRm {podstatné jméno}, kdežto názvy ve verzi 0.9.8 neobsahují označení **Rm** (třeba New-AzureRmResourceGroup místo New-AzureResourceGroup). Pokud používáte prostředí Azure PowerShell 0.9.8, musíte nejdřív spuštěním příkazu **Switch-AzureMode AzureResourceManager** spustit režim Resource Manager. Tento příkaz není nutné v 1.0 nebo novější.

Pokud chcete použít skripty vytvořené pro verzi 0.9.8 prostředí v prostředí 1.0 nebo novější, měli pečlivě aktualizujete a testovat skripty v předprodukčním prostředí před jejich použitím v produkčním prostředí, aby se zabránilo neočekávaným dopad.

[Stáhněte si nejnovější verzi prostředí PowerShell](https://github.com/Azure/azure-powershell/releases) (minimální požadovaná verze je: 1.0.0)

[!INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services
Následující kroky vás provedou vytvořením trezor služby Recovery Services. Trezor služby Recovery Services se liší od trezoru služby Backup.

1. Pokud používáte Azure Backup poprvé, je nutné použít **Register-AzureRMResourceProvider** rutiny zaregistrujte zprostředkovatele služby Azure Recovery s vaším předplatným.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Trezor služby Recovery Services je prostředek ARM, proto musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořte novou. Když vytváříte novou skupinu prostředků, zadejte název a umístění pro skupinu prostředků.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "WestUS"
    ```
3. Použití **New-AzureRmRecoveryServicesVault** rutina pro vytvoření nového trezoru. Ujistěte se, k určení stejného umístění trezoru, protože byl použit pro skupinu prostředků.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```
4. Zadejte typ redundance úložiště se použije. můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantního úložiště (GRS)](../storage/common/storage-redundancy-grs.md). Následující příklad ukazuje, že možnost - BackupStorageRedundancy pro testVault nastavená na GeoRedundant.

   > [!TIP]
   > Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Recovery Services do proměnné.
   >
   >

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Zobrazit tyto trezory v rámci předplatného
Použití **Get-AzureRmRecoveryServicesVault** zobrazíte seznam všech trezorů v rámci aktuálního předplatného. Tento příkaz můžete použít ke kontrole, zda byl vytvořen nový trezor a zjistit, jaké trezory služby jsou dostupné v rámci předplatného.

Spustit příkaz, **Get-AzureRmRecoveryServicesVault**, a všech trezorů v předplatném jsou uvedeny.

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
Před instalací agenta Azure Backup, musíte mít instalační program stažený a k dispozici v systému Windows Server. Můžete získat nejnovější verzi instalačního programu z [Microsoft Download Center](https://aka.ms/azurebackup_agent) nebo ze stránky řídicího panelu trezoru služby Recovery Services. Uložte instalační program na snadno dostupném místě jako * C:\Downloads\*.

Můžete také získat downloader pomocí Powershellu:
 
 ```
 $MarsAURL = 'Http://Aka.Ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Pokud chcete nainstalovat agenta, spusťte následující příkaz v konzole Powershellu se zvýšenými oprávněními:

```
PS C:\> MARSAgentInstaller.exe /q
```

Tím se nainstaluje agent s výchozími možnostmi. Instalace trvá několik minut na pozadí. Pokud nezadáte */nu* možnost pak bude **Windows Update** otevře se okno na konci instalace žádné aktualizace. Po instalaci agenta se zobrazí v seznamu nainstalovaných programů.

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
| /q |Bezobslužnou instalaci |- |
| / p: "umístění" |Cesta ke složce instalace agenta Azure Backup. |Agent C:\Program Files\Microsoft Azure Recovery Services |
| / s: "umístění" |Cesta ke složce mezipaměti pro agenta Azure Backup. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Přihlášení ke službě Microsoft Update |- |
| /nu |Nekontrolovat aktualizace po dokončení instalace |- |
| /d |Odinstaluje Agenta Microsoft Azure Recovery Services. |- |
| /pH |Adresa hostitele proxy |- |
| /po |Číslo portu proxy serveru hostitele |- |
| /pu |Proxy Host UserName |- |
| /pw |Heslo pro proxy server |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Registrace Windows serveru nebo klientského počítače Windows, do trezoru služby Recovery Services
Po vytvoření trezoru služby Recovery Services, stáhněte si nejnovější verzi agenta a přihlašovací údaje trezoru a uložit do vhodného umístění jako C:\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
```

Ve Windows serveru nebo Windows klientského počítače, spusťte [Start OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) rutiny k registraci počítače v trezoru.
Tato a další rutiny používané pro zálohování, jsou z modulu MSONLINE, která Mars AgentInstaller se přidá jako součást procesu instalace. 

Instalační program agenta se neaktualizuje $Env: PSModulePath proměnné. To znamená, že se nezdaří automatické načtení modulu. Chcete-li tento problém vyřešit máte následující:

```
PS C:\>  $Env:psmodulepath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Alternativně můžete ručně načíst modul ve skriptu následujícím způsobem:

```
PS C:\>  Import-Module  'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'

```

Po načtení Online zálohování rutiny zaregistrujete přihlašovací údaje trezoru:


```

PS C:\> Start-OBRegistration -VaultCredentials $credsfilename.FilePath -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Nepoužívejte relativní cesty k určení souboru s přihlašovacími údaji. Jako vstup do rutiny musíte zadat absolutní cestu.
>
>

## <a name="networking-settings"></a>Nastavení sítě
Po připojení k Windows počítače k Internetu přes proxy server se nastavení proxy serveru je také možné poskytnout agenta. V tomto příkladu neexistuje žádný proxy server, takže jsme se explicitně vymazat všechny informace související s proxy serverem.

Využití šířky pásma je možné řídit také s možnostmi ```work hour bandwidth``` a ```non-work hour bandwidth``` pro danou sadu dny v týdnu.

Nastavení proxy serveru a šířky pásma podrobností se provádí pomocí [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) rutiny:

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Nastavení šifrování
Chránit jejich důvěrnost dat zašifrovaná data záloh odeslaných do služby Azure Backup. Šifrovací heslo je "password" se dešifrovat data v době obnovení.

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
PS C:\> $PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force 
PS C:\> $PassCode   = 'AzureR0ckx'
PS C:\> Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase
Server properties updated successfully
```

> [!IMPORTANT]
> Heslo informace udržují v bezpečí po nastavení. Data nejde obnovit z Azure bez tohoto hesla.
>
>

## <a name="back-up-files-and-folders"></a>Zálohování souborů a složek
Všech záloh z Windows serverů a klientů do služby Azure Backup se řídí zásadami. Zásady se skládá ze tří částí:

1. A **plán zálohování** , která určuje, kdy zálohy muset provést a synchronizovat se službou.
2. A **plán uchovávání** , která určuje, jak dlouho chcete zachovat body obnovení v Azure.
3. A **souborů zahrnutí a vyloučení** , která určuje, co by měly být zálohovány.

V tomto dokumentu protože jsme používáte automatizaci zálohování, budeme předpokládat, že se že nic není nakonfigurovaná. Začneme vytvořením nové zásady zálohování pomocí [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) rutiny.

```
PS C:\> $newpolicy = New-OBPolicy
```

V tuto chvíli se tato zásada je prázdný a další rutiny je potřeba definovat, co budou položky zahrnuté ani vyloučené, kdy zálohy se budou spouštět, a tam, kde se uloží zálohy.

### <a name="configuring-the-backup-schedule"></a>Konfigurace plánu zálohování
Plán zálohování, která je vytvořena pomocí je první ze 3 částí zásadu [New-OBSchedule](https://technet.microsoft.com/library/hh770401) rutiny. Plán zálohování definuje, kdy je třeba zálohování provést. Při vytváření plánu je potřeba zadat 2 vstupní parametry:

* **Dny v týdnu** , který se má spustit zálohování. Můžete spustit úlohu zálohování na právě jeden den nebo každý den v týdnu nebo libovolnou kombinaci mezi nimi.
* **V které denní době** kdy se má spustit zálohování. Můžete definovat až na 3 různých časech, dne, kdy se budou aktivovat zálohování.

Můžete například nakonfigurovat zásadu zálohování, která běží v 16: 00 každou sobotu a neděli.

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

Plán zálohování musí být přidružené k zásadám a toho lze dosáhnout pomocí [Set-OBSchedule](https://technet.microsoft.com/library/hh770407) rutiny.

```
PS C:> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>Konfigurace zásady uchovávání informací
Zásady uchovávání informací Určuje, jak dlouho se uchovají body obnovení vytvořené z úlohy zálohování. Při vytváření nových zásad uchovávání informací pomocí [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) rutiny, můžete zadat počet dní, které je potřeba pomocí služby Azure Backup uchovávat body obnovení záloh. Následující příklad nastaví zásady uchovávání informací 7 dní.

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

Zásady uchovávání informací musí být přidružená k hlavní zásad, pomocí rutiny [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405):

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
### <a name="including-and-excluding-files-to-be-backed-up"></a>Zahrnutí a vyloučení souborů mají být zazálohovány
```OBFileSpec``` Objekt definuje soubory, které chcete zahrnout a vyloučit zálohy. To je sada pravidel, která obor si chráněné soubory a složky na počítači. Může mít mnoho souborů zahrnutí nebo vyloučení pravidla podle potřeby, a je přidružit k zásadě. Při vytváření nového objektu OBFileSpec, můžete:

* Zadejte soubory a složky, které mají být zahrnuty
* Zadejte soubory a složky k vyloučení
* Rekurzivní záloha dat ve složce (nebo) určuje, zda by měl budou zálohovány pouze nejvyšší úrovně soubory v zadané složce zadejte nahoru.

Druhá možnost se dosahuje použitím příznaku - nerekurzivní v příkazu New-OBFileSpec.

V následujícím příkladu přidáme zálohování svazku C: a D: a vyloučit OS binárních souborů ve složce Windows a všechny dočasné složky. K tomu vytvoříme dvě pomocí specifikace souboru [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) rutiny – jeden pro zahrnutí a jeden pro vyloučení. Po vytvoření specifikace souborů jsou spojené s použitím zásad [přidat OBFileSpec](https://technet.microsoft.com/library/hh770424) rutiny.

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

### <a name="applying-the-policy"></a>Použití zásady
Objekt zásad teď hotový a má přidružený plán zálohování, zásady uchovávání informací a seznam souborů zahrnutí a vyloučení. Tuto zásadu je možné nyní potvrzeny použijte Azure Backup. Před použitím nově vytvořenou zásadu Ujistěte se, že neexistují žádné existující zásady zálohování přidružené k serveru pomocí [odebrat OBPolicy](https://technet.microsoft.com/library/hh770415) rutiny. Odebrat zásady a zobrazí výzvu k potvrzení. Chcete-li přeskočit použití potvrzení ```-Confirm:$false``` příznak pomocí rutiny.

```
PS C:> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Potvrzení objektu zásad se provádí pomocí [Set-OBPolicy](https://technet.microsoft.com/library/hh770421) rutiny. Také to požádá o potvrzení. Chcete-li přeskočit použití potvrzení ```-Confirm:$false``` příznak pomocí rutiny.

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

Můžete zobrazit podrobnosti o existující zásady zálohování pomocí [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) rutiny. Můžete procházet hierarchii dál používat [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) rutiny pro plán zálohování a [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) rutiny pro zásady uchovávání informací

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

### <a name="performing-an-ad-hoc-backup"></a>Zálohování ad hoc
Po nastavení zásady zálohování k zálohování dojde za plán. Spouští se ad hoc záloha je také možné pomocí [Start OBBackup](https://technet.microsoft.com/library/hh770426) rutiny:

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
Tato část vás provede kroky pro automatizaci obnovení dat z Azure Backup. To zahrnuje následující kroky:

1. Vyberte zdrojový svazek
2. Zvolte bod obnovení zálohy
3. Zvolte položku, kterou chcete obnovit
4. Aktivační proces obnovení

### <a name="picking-the-source-volume"></a>Výběr zdrojového svazku
Aby bylo možné obnovit položku z Azure Backup, musíte nejprve identifikovat zdroj položky. Vzhledem k tomu, že jsme při provádění příkazů v rámci systému Windows Server nebo klienta Windows, tento počítač je už identifikoval. Dalším krokem při identifikaci zdroj je identifikovat svazek, který ji obsahuje. Seznam svazků nebo zdrojů zálohovaných z tohoto počítače je možné načíst pomocí provádí [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) rutiny. Tento příkaz vrátí pole všech zdrojů zálohovaných z tohoto serveru/klienta.

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

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Výběr bodu zálohy, ze kterého se má obnovit
Načíst seznam body zálohy pomocí provádí [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) rutinu s příslušnými parametry. V našem příkladu jsme vybrali nejnovější bod zálohy pro zdrojový svazek *D:* a použít ho k obnovení k určitému souboru.

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
Objekt ```$rps``` je pole body záloh. Prvním prvkem je poslední bod a n-tý prvek je nejstarší bod. Zvolte nejnovější bod, budeme používat ```$rps[0]```.

### <a name="choosing-an-item-to-restore"></a>Výběr položky obnovení
K identifikaci souborů nebo složku, kterou chcete obnovit, použijte rekurzivní [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) rutiny. Tímto způsobem můžete procházet hierarchii složek výhradně pomocí ```Get-OBRecoverableItem```.

V tomto příkladu, pokud se mají obnovit soubor *finances.xls* jsme můžete odkázat pomocí objektu ```$filesFolders[1]```.

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

Můžete také hledat položky provést obnovení pomocí ```Get-OBRecoverableItem``` rutiny. V našem příkladu, k vyhledání *finances.xls* jsme může získat popisovač souboru spuštěním tohoto příkazu:

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>Spuštění procesu obnovení
Spustit proces obnovení, musíme nejprve zadat možnosti obnovení. To můžete udělat pomocí [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) rutiny. V tomto příkladu předpokládejme, že chceme obnovit soubory do *C:\temp*. Předpokládejme také, že chceme přeskočit soubory, které jsou už v cílové složce *C:\temp*. Pokud chcete vytvořit tyto možnosti obnovení, použijte následující příkaz:

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Nyní spustit proces obnovení pomocí [Start OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) v vybraný příkaz ```$item``` z výstupu ```Get-OBRecoverableItem``` rutiny:

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

Odinstalace agenta binární soubory z počítače má některé důsledky vzít v úvahu:

* Z počítače odebere filtr souborů a sledování změn je zastavená.
* Všechny informace o zásadách bude odebrána z počítače, ale informace o zásadách nadále uložených ve službě.
* Odeberou se všechny plány zálohování a žádné další zálohy jsou prováděny.

Ale data uložená v Azure zůstane a se závislosti na nastavení zásad uchovávání informací uchovávají vámi. Starší body se automaticky stará navýšení kapacity.

## <a name="remote-management"></a>Vzdálená správa
Veškerá Správa týkající se agenta Azure Backup, zásady a zdroje dat lze provést vzdáleně přes PowerShell. Musí správně připravily počítač, který bude možné spravovat vzdáleně.

Ve výchozím nastavení je Služba WinRM nakonfigurována pro ruční spuštění. Typ spuštění musí být nastavený na *automatické* a služby měly být spuštěny. Pokud chcete ověřit, zda je spuštěna Služba WinRM, by měla být hodnota vlastnosti stavu *systémem*.

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

Tento počítač nyní možné spravovat vzdáleně – od instalace agenta. Například následující skript zkopíruje agenta ke vzdálenému počítači a nainstaluje ho.

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
