---
title: Azure Backup – zálohování úloh DPM pomocí Powershellu
description: Zjistěte, jak nasadit a spravovat Azure Backup pro Data Protection Manager (DPM) pomocí Powershellu
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 1/23/2017
ms.author: adigan
ms.openlocfilehash: 5ef9d61e880d3252eae2d8ef924ff39a5d2f6acf
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497906"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Nasazení a správa zálohování do Azure pro servery DPM (Data Protection Manager) pomocí PowerShellu
Tento článek popisuje, jak pomocí prostředí PowerShell k instalaci Azure Backup na serveru DPM a Správa zálohování a obnovení.

## <a name="setting-up-the-powershell-environment"></a>Nastavení prostředí PowerShell
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Před použitím prostředí PowerShell pro správu z aplikace Data Protection Manager zálohování do Azure, budete muset mít správné prostředí v prostředí PowerShell. Při spuštění relace Powershellu Ujistěte se, že spustíte následující příkaz k importu správné moduly a bylo možné správně odkazují na rutiny aplikace DPM:

```
PS C:> & "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"

Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Instalace a registrace
Chcete-li začít:

1. [Stáhněte si nejnovější PowerShell](https://github.com/Azure/azure-powershell/releases) (minimální požadovaná verze je: 1.0.0)
2. Povolení rutin Azure Backup přepnutím na *AzureResourceManager* režimu s použitím **Switch-AzureMode** rutinu:

```
PS C:\> Switch-AzureMode AzureResourceManager
```

Následující nastavení a registrace úlohy je možné automatizovat pomocí prostředí PowerShell:

* Vytvoření trezoru Služeb zotavení
* Instalace agenta Azure Backup
* Registrace ve službě Azure Backup
* Nastavení sítě
* Nastavení šifrování

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services
Následující kroky vás provedou vytvořením trezor služby Recovery Services. Trezor služby Recovery Services se liší od trezoru služby Backup.

1. Pokud používáte Azure Backup poprvé, je nutné použít **Register-AzureRMResourceProvider** rutiny zaregistrujte zprostředkovatele služby Azure Recovery s vaším předplatným.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Trezor služby Recovery Services je prostředek ARM, proto musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořte novou. Když vytváříte novou skupinu prostředků, zadejte název a umístění pro skupinu prostředků.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. Použití **New-AzureRmRecoveryServicesVault** rutina pro vytvoření nového trezoru. Ujistěte se, k určení stejného umístění trezoru, protože byl použit pro skupinu prostředků.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
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

Spuštěním příkazu Get-AzureRmRecoveryServicesVault, a všech trezorů v předplatném jsou uvedeny.

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


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Instalace agenta Azure Backup na serveru DPM
Před instalací agenta Azure Backup, musíte mít instalační program stažený a k dispozici v systému Windows Server. Můžete získat nejnovější verzi instalačního programu z [Microsoft Download Center](https://aka.ms/azurebackup_agent) nebo ze stránky řídicího panelu trezoru služby Recovery Services. Uložte instalační program na snadno dostupném místě jako * C:\Downloads\*.

Pokud chcete nainstalovat agenta, spusťte následující příkaz v konzole Powershellu se zvýšenými oprávněními **na serveru DPM**:

```
PS C:\> MARSAgentInstaller.exe /q
```

Tím se nainstaluje agent s výchozími možnostmi. Instalace trvá několik minut na pozadí. Pokud nezadáte */nu* možnost **Windows Update** otevře se okno na konci instalace žádné aktualizace.

Agent se zobrazí v seznamu nainstalovaných programů. Chcete-li zobrazit seznam nainstalovaných programů, přejděte na **ovládací panely** > **programy** > **programy a funkce**.

![Agent nainstalován](./media/backup-dpm-automation/installed-agent-listing.png)

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

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registrace aplikace DPM k obnovení služby úložiště
Po vytvoření trezoru služby Recovery Services, stáhněte si nejnovější verzi agenta a přihlašovací údaje trezoru a uložit do vhodného umístění jako C:\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Na serveru aplikace DPM spusťte [Start OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) rutiny k registraci počítače v trezoru.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Nastavení počáteční konfigurace
Po zaregistrování serveru aplikace DPM v trezoru služby Recovery Services začíná nastavení výchozího předplatného. Tato nastavení předplatného zahrnují sítě, šifrování a pracovní oblast. Chcete-li změnit nastavení odběru je nutné nejprve získat popisovač na existující nastavení (výchozí) pomocí [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) rutiny:

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Všechny úpravy probíhají na tento místní objekt prostředí PowerShell ```$setting``` a pak se zaměřuje na aplikace DPM a Azure Backup k jejich uložení úplný objekt pomocí [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) rutiny. Je třeba použít ```–Commit``` příznak Ujistěte se, že jsou trvalé změny. Nastavení nebude použita a použít se službou Azure Backup, pokud potvrzené.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Sítě
Je-li připojení počítače aplikace DPM do služby Azure Backup na Internetu prostřednictvím proxy serveru, nastavení proxy serveru musí být zadána pro úspěšné zálohování. To se provádí pomocí ```-ProxyServer```a ```-ProxyPort```, ```-ProxyUsername``` a ```ProxyPassword``` parametry [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) rutiny. V tomto příkladu není žádný proxy server tak můžeme jsou explicitně vymazání žádné informace související s proxy serverem.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Využití šířky pásma je možné řídit také s možností ```-WorkHourBandwidth``` a ```-NonWorkHourBandwidth``` pro danou sadu dny v týdnu. V tomto příkladu nenastavujeme žádné omezení.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Konfigurace pracovní oblasti
Agent Azure Backup spuštěný na serveru DPM potřebuje dočasné úložiště pro data obnovená z cloudu (místní pracovní oblasti). Nakonfigurujte pracovní oblast pomocí [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) rutiny a ```-StagingAreaPath``` parametru.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

V předchozím příkladu se nastaví pracovní oblast *C:\StagingArea* v objektu prostředí PowerShell ```$setting```. Ujistěte se, že zadaná složka již existuje, jinak konečného potvrzení nastavení odběru nezdaří.

### <a name="encryption-settings"></a>Nastavení šifrování
Chránit jejich důvěrnost dat zašifrovaná data záloh odeslaných do služby Azure Backup. Šifrovací heslo je "password" se dešifrovat data v době obnovení. Je důležité zachovat tyto informace bezpečným a zabezpečeným, jakmile je nastavíte.

V následujícím příkladu první příkaz převede řetězec ```passphrase123456789``` zabezpečený řetězec a přiřadí zabezpečený řetězec do proměnné s názvem ```$Passphrase```. druhý příkaz nastaví zabezpečený řetězec v ```$Passphrase``` jako heslo pro šifrování záloh.

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Heslo informace udržují v bezpečí po nastavení. Nebudete schopni obnovit data z Azure bez tohoto hesla.
>
>

V tomto okamžiku by měla provedení požadované změny pro ```$setting``` objektu. Mějte na paměti k provedení změn.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Ochrana dat do služby Azure Backup
V této části přidáte provozního serveru DPM a poté proveďte ochranu dat do místního úložiště aplikace DPM a potom do služby Azure Backup. V příkladech předvedeme, jak zálohovat soubory a složky. Logiku je možné snadno rozšířit na libovolný zdroj dat aplikace DPM nepodporuje zálohování. Všechny zálohy aplikace DPM se řídí podle ochranu skupiny (PG) s čtyři části:

1. **Seskupit členy** je seznam chránitelné objekty (označované také jako *zdroje dat* v aplikaci DPM), který chcete chránit ve stejné skupině ochrany. Chcete třeba chránit produkčních virtuálních počítačů v jedné skupiny ochrany a databáze systému SQL Server v jiné skupině ochrany mají různé požadavky na zálohování. Než budete zálohovat všechny zdroje dat na provozním serveru, které je třeba Ujistěte se, že Agent aplikace DPM je nainstalován na serveru a spravovaných aplikací DPM. Postupujte podle kroků pro [instalaci agenta aplikace DPM](https://technet.microsoft.com/library/bb870935.aspx) a odkazování na příslušném serveru aplikace DPM.
2. **Způsob ochrany dat** Určuje umístění zálohování cílové - páska, disk a cloud. V našem příkladu jsme se chránit data na místní disk a do cloudu.
3. A **plán zálohování** , která určuje, když potřebují zálohování mají být provedeny, a jak často mají data synchronizovat mezi serverem DPM a provozní server.
4. A **plán uchovávání** , která určuje, jak dlouho chcete zachovat body obnovení v Azure.

### <a name="creating-a-protection-group"></a>Vytvoření skupiny ochrany
Začněte vytvořením nové skupiny ochrany s použitím [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) rutiny.

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Výše uvedenou rutinu vytvoří skupiny ochrany s názvem *ProtectGroup01*. Do existující skupiny ochrany můžete také změnit později přidat zálohování do cloudu Azure. Žádné změny do skupiny ochrany – nové nebo existující - ještě ale zbývá k získání popisovače *upravitelná* pomocí [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) rutiny.

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Přidávají se členové skupiny do skupiny ochrany
Každý Agent aplikace DPM ví seznam zdrojů dat na serveru, na kterém je nainstalovaný. Do skupiny ochrany přidat zdroje dat, je potřeba nejdříve odeslat seznam zdrojů dat zpět na DPM server agenta aplikace DPM. Jeden nebo více zdrojů dat jsou pak vybraná a přidat do skupiny ochrany. Prostředí PowerShell kroky potřebné k dosažení tohoto cíle jsou:

1. Načte seznam všech serverů spravovaných aplikací DPM prostřednictvím agenta aplikace DPM.
2. Vyberte konkrétní server.
3. Načte seznam všech datových zdrojů na serveru.
4. Vyberte jeden nebo více zdrojů dat a přidat je do skupiny ochrany

Seznam serverů, na kterých agenta aplikace DPM je nainstalovaná a je spravována serverem aplikace DPM je získat [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) rutiny. V tomto příkladu budeme filtrovat a pouze s názvem konfigurace PS *productionserver01* pro zálohování.

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”}
```

Teď načíst seznam zdrojů dat v ```$server``` pomocí [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605) rutiny. V tomto příkladu jsme svazku filtrování * D:\* , který chcete konfigurovat pro zálohování. Tento zdroj dat se pak přidá do skupiny ochrany pomocí [přidat DPMChildDatasource](https://technet.microsoft.com/library/hh881732) rutiny. Nezapomeňte použít *upravitelná* objekt skupiny ochrany ```$MPG``` doplnit.

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Opakujte tento krok tolikrát, kolikrát podle potřeby, dokud nepřidáte všechny zvolené zdroje dat do skupiny ochrany. Můžete začít s pouze jeden zdroj dat a dokončit pracovní postup pro vytvoření skupiny ochrany a později přidat další zdroje dat do skupiny ochrany.

### <a name="selecting-the-data-protection-method"></a>Výběr způsobu ochrany dat
Jakmile se zdroje dat byly přidány do skupiny ochrany, dalším krokem je zadat pomocí metody ochrany [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) rutiny. V tomto příkladu skupina ochrany je instalační program pro místním diskem a zálohování do cloudu. Také je třeba zadat zdroj dat, který chcete chránit, do cloudu pomocí [přidat DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) rutinu s parametrem - Online příznak.

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Nastavení uchování
Nastavení uchovávání pro zálohování bodů pomocí [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) rutiny. Když to může zdát liché předtím, než byly definovány plán zálohování, pomocí nastavení uchovávání ```Set-DPMPolicyObjective``` rutina automaticky nastaví výchozí plán zálohování, který je pak možné upravit. Vždy je možné nastavit první naplánovat zálohování a zásady uchovávání informací po.

V následujícím příkladu nastaví rutinu parametry uchování zálohování na disk. To bude uchovat zálohy po dobu 10 dnů a synchronizaci dat každých 6 hodin mezi produkční server a server aplikace DPM. ```SynchronizationFrequencyMinutes``` Nedefinuje, jak často bodu zálohy se vytvoří, ale jak často data zkopírována na DPM server.  Toto nastavení zabrání zálohy příliš velká.

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Pro zálohy přejít do Azure (aplikace DPM na ně odkazuje jako zálohování Online) rozsahy uchovávání je možné nakonfigurovat pro [dlouhodobé uchování s využitím Dědečka ze strany otce-SYN schéma (GFS)](backup-azure-backup-cloud-as-tape.md). To znamená můžete definovat zásady uchovávání informací kombinované zahrnující denní, týdenní, měsíční a roční zásady uchovávání informací. V tomto příkladu vytvoříme představující schéma komplexní uchovávání, které chceme, aby pole a potom nakonfigurujte pomocí rozsah uchování [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) rutiny.

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Nastavení plánu zálohování
Aplikace DPM nastaví výchozí plán zálohování automaticky při zadání cíle ochrany pomocí ```Set-DPMPolicyObjective``` rutiny. Chcete-li změnit výchozí plány, použijte [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) následovaný rutiny [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723) rutiny.

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

Ve výše uvedeném příkladu ```$onlineSch``` je pole, která obsahuje existující plán online ochrany pro skupinu ochrany ve schématu GFS čtyřech prvcích:

1. ```$onlineSch[0]``` obsahuje denní plán
2. ```$onlineSch[1]``` obsahuje Týdenní plán
3. ```$onlineSch[2]``` obsahuje měsíční plán
4. ```$onlineSch[3]``` obsahuje roční plán

Proto pokud potřebujete změnit týdenní plán, musíte odkazovat ```$onlineSch[1]```.

### <a name="initial-backup"></a>Prvotní zálohování
Při zálohování zdroje dat poprvé, musí aplikace DPM vytvoří počáteční repliky, který vytvoří úplná kopie zdroje dat, které se mají chránit na svazku repliky DPM. Tato aktivita můžou být plánované pro určitou dobu, nebo můžete spustit ručně, pomocí [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) rutinu s parametrem ```-NOW```.

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```
### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Změna velikosti repliky aplikace DPM a svazek bodu obnovení
Můžete také změnit velikost svazku repliky DPM a Stínová kopie svazku pomocí [Set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) rutiny jako v následujícím příkladu: Get-DatasourceDiskAllocation - Datasource $DS Set-DatasourceDiskAllocation - Datasource $DS - protectiongroup prostředí $MPG – ruční - ReplicaArea (2 gb) – ShadowCopyArea (2 gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Potvrzují se změny do skupiny ochrany
Nakonec změny musí být potvrzeny, než aplikace DPM může posloužit zálohování za novou konfiguraci skupiny ochrany. Toho lze dosáhnout pomocí [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) rutiny.

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```
## <a name="view-the-backup-points"></a>Zobrazení body zálohy
Můžete použít [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) rutiny pro získání seznamu všech bodů obnovení pro zdroj dat. V tomto příkladu provedeme následující:

* načte všechny PGs na serveru DPM a uložit v poli ```$PG```
* získání zdroje dat odpovídající ```$PG[0]```
* získáte všechny body obnovení pro zdroj dat.

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Obnovení dat chráněných v Azure
Obnovování dat jsou kombinací informací ```RecoverableItem``` objektu a ```RecoveryOption``` objektu. V předchozí části jsme teď seznam body zálohy pro zdroj dat.

V následujícím příkladu jsme ukazují, jak obnovit virtuální počítač Hyper-V z Azure Backup kombinací body zálohy cíl pro obnovení. Tento příklad zahrnuje:

* Vytváření s použitím možnosti obnovení [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592) rutiny.
* Načítají se pole body zálohy pomocí ```Get-DPMRecoveryPoint``` rutiny.
* Vyberete bod obnovení ze zálohy.

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Pro každý typ zdroje dat lze snadno rozšířit příkazy.

## <a name="next-steps"></a>Další postup
* Další informace o DPM do služby Azure Backup najdete v části [seznámení s DPM Backup](backup-azure-dpm-introduction.md)
