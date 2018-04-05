---
title: Zálohování Azure – zálohování úloh DPM pomocí Powershellu | Microsoft Docs
description: Zjistěte, jak nasadit a spravovat Azure Backup pro Data Protection Manager (DPM) pomocí prostředí PowerShell
services: backup
documentationcenter: ''
author: NKolli1
manager: shreeshd
editor: ''
ms.assetid: e9bd223c-2398-4eb1-9bf3-50e08970fea7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: adigan;anuragm;trinadhk;markgal
ms.openlocfilehash: 89dd965208cd473e47de9e0c9bdbfa3ab986c3d5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Nasazení a správa zálohování do Azure pro servery DPM (Data Protection Manager) pomocí PowerShellu
Tento článek ukazuje, jak pomocí prostředí PowerShell instalační program Azure Backup na serveru DPM a ke správě zálohování a obnovení.

## <a name="setting-up-the-powershell-environment"></a>Nastavení prostředí PowerShell
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Před použitím prostředí PowerShell pro správu z aplikace Data Protection Manager zálohování do Azure, musíte mít správné prostředí v prostředí PowerShell. Na začátku relace prostředí PowerShell Ujistěte se, že spustíte následující příkaz a naimportovat moduly vpravo umožňují správně odkazovat rutiny aplikace DPM:

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
2. Povolte rutiny pro zálohování Azure tak, že přepnutí na *AzureResourceManager* režimu pomocí **Switch-AzureMode** příkaz:

```
PS C:\> Switch-AzureMode AzureResourceManager
```

Následující instalaci a registraci úlohy je možné automatizovat pomocí prostředí PowerShell:

* Vytvoření trezoru Služeb zotavení
* Instalace agenta Azure Backup
* Registrace u služby zálohování Azure
* Nastavení sítě
* Nastavení šifrování

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services
Následující kroky vás provedou vytvoření trezoru služeb zotavení. Trezor služeb zotavení se liší od úložiště záloh.

1. Pokud používáte Azure Backup poprvé, musíte použít **Register-AzureRMResourceProvider** rutiny k registraci poskytovatele služeb zotavení Azure s vaším předplatným.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Trezor služeb zotavení je prostředek ARM, proto musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořte novou. Když vytváříte novou skupinu prostředků, zadejte název a umístění pro skupinu prostředků.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. Použití **New-AzureRmRecoveryServicesVault** vytvořte nové úložiště. Ujistěte se, že zadejte stejné umístění pro úložiště, jako byl použit pro skupinu prostředků.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. Zadejte typ redundance úložiště se použije. můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy-grs.md). Následující příklad ukazuje, že je možnost - BackupStorageRedundancy pro testVault nastavena na GeoRedundant.

   > [!TIP]
   > Mnoho rutin Azure Backup vyžadují objekt trezoru služeb zotavení jako vstup. Z tohoto důvodu je vhodné pro uložení objektu trezoru služeb zotavení zálohování v proměnné.
   >
   >

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Zobrazit trezorů v předplatném.
Použití **Get-AzureRmRecoveryServicesVault** Chcete-li zobrazit seznam všech trezorů v aktuálním předplatném. Tento příkaz můžete použít, chcete-li zkontrolovat, zda byl vytvořen nový trezor nebo jaké trezory jsou k dispozici v rámci předplatného.

Spusťte příkaz Get-AzureRmRecoveryServicesVault, a jsou uvedeny všechny trezorů v rámci předplatného.

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


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Instalace agenta Azure Backup na serveru aplikace DPM
Před instalací agenta Azure Backup, musíte mít instalační program stažené a existuje v systému Windows Server. Můžete získat nejnovější verzi instalačního programu z [Microsoft Download Center](http://aka.ms/azurebackup_agent) nebo ze stránky řídicího panelu trezoru služeb zotavení. Instalační program uložte na snadno dostupném místě jako * C:\Downloads\*.

Pokud chcete nainstalovat agenta, spusťte následující příkaz v konzolu prostředí PowerShell se zvýšenými oprávněními **na serveru DPM**:

```
PS C:\> MARSAgentInstaller.exe /q
```

Tím se nainstaluje agent s výchozími možnostmi. Instalace trvá několik minut na pozadí. Pokud nezadáte */nu* možnost **Windows Update** otevře se okno na konci instalace k vyhledání všech aktualizací.

Agenta se zobrazí v seznamu nainstalovaných programů. Chcete-li zobrazit seznam nainstalovaných programů, přejděte na **ovládací panely** > **programy** > **programy a funkce**.

![Agent nainstalován](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Možnosti instalace
Aby se zobrazily všechny možnosti, které jsou k dispozici prostřednictvím příkazového řádku, použijte následující příkaz:

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
| /pu |Uživatelské jméno proxy hostitele |- |
| /pw |Heslo pro proxy server |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registrace aplikace DPM k obnovení služby úložiště
Po vytvoření trezoru služeb zotavení, stáhněte si nejnovější verzi agenta a přihlašovací údaje trezoru a uložte ho do vhodného umístění jako C:\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Na serveru aplikace DPM spusťte [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) rutiny počítač zaregistrovat v trezoru.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Počáteční konfigurace nastavení
Jakmile je DPM Server registrován s trezor služeb zotavení, začíná výchozí nastavení odběru. Tato nastavení odběru zahrnují sítě, šifrování a pracovní oblasti. Chcete-li změnit nastavení odběru je nutné nejprve získat popisovač na existující nastavení (výchozí), pomocí [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) rutiny:

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Všechny změny provedené tento objekt místní prostředí PowerShell ```$setting``` a pak se zaměřuje na aplikace DPM a Azure Backup, o jejich uložení úplné objekt pomocí [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) rutiny. Budete muset použít ```–Commit``` příznak zajistit, že jsou nastavené jako trvalé změny. Nastavení nebude použita a pokud potvrzené používá Azure Backup.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Sítě
Pokud připojení počítače aplikace DPM do služby Azure Backup na internet přes proxy server, musí pro úspěšné zálohy zadat nastavení serveru proxy. To se provádí pomocí ```-ProxyServer```a ```-ProxyPort```, ```-ProxyUsername``` a ```ProxyPassword``` parametry se [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) rutiny. V tomto příkladu není žádný proxy server, jsme jsou explicitně vymazání údaje související s proxy serverem.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Využití šířky pásma se dá taky nastavit pomocí možnosti ```-WorkHourBandwidth``` a ```-NonWorkHourBandwidth``` pro danou sadu dny v týdnu. V tomto příkladu jsme nejsou nastavení žádné omezení.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Konfigurace pracovní oblasti
Agent Azure Backup spuštěný na serveru DPM potřebuje dočasné úložiště pro data obnovená z cloudu (místní pracovní oblasti). Nakonfigurovat pracovní oblasti pomocí [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) rutiny a ```-StagingAreaPath``` parametr.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

V příkladu nahoře, je možnost pracovní oblasti *C:\StagingArea* v prostředí PowerShell objektu ```$setting```. Ujistěte se, že v zadané složce již existuje, jinak se nezdaří poslední zápis nastavení přihlášení k odběru.

### <a name="encryption-settings"></a>Nastavení šifrování
Zálohování data přenášená do služby Azure Backup je zašifrovaná chránit jejich důvěrnost dat. Šifrovací přístupové heslo je 'heslo' k dešifrování dat v době obnovení. Je důležité udržovat tento informace o bezpečném po nastavení.

V následujícím příkladu převede první příkaz řetězec ```passphrase123456789``` zabezpečený řetězec a přiřadí zabezpečený řetězec proměnné s názvem ```$Passphrase```. v druhém příkazu nastaví zabezpečený řetězec v ```$Passphrase``` jako heslo pro šifrování záloh.

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Zachování informací o přístupové heslo bezpečném, po nastavení. Nebudete schopni obnovit data ze služby Azure bez tohoto hesla.
>
>

V tomto okamžiku jste měli udělali všechny požadované změny pro ```$setting``` objektu. Mějte na paměti k provedení změn.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Ochrana dat do služby Azure Backup
V této části přidáte provozním serveru DPM a poté proveďte ochranu dat do místního úložiště DPM a pak do služby Azure Backup. V příkladech jsme se ukazují, jak zálohovat soubory a složky. Chcete-li zálohovat všechny zdroje dat aplikace DPM podporováno lze snadno rozšířit logiku. Všechny zálohy aplikace DPM se řídí pomocí ochranu skupiny (PG) s čtyři části:

1. **Členy skupiny** je seznam chránitelné objekty (také označované jako *zdrojů dat* v DPM), které chcete chránit ve stejné skupině ochrany. Chcete třeba chránit produkční virtuálních počítačů na jednu skupinu ochrany a databáze systému SQL Server v jiné skupiny ochrany, protože mají různé požadavky na zálohování. Předtím, než můžete zálohovat žádný zdroj dat na provozním serveru, které potřebujete, abyste měli jistotu, agenta aplikace DPM je nainstalován na serveru a je spravovaných aplikací DPM. Postupujte podle kroků pro [instalace agenta DPM](https://technet.microsoft.com/library/bb870935.aspx) a propojení na příslušný Server aplikace DPM.
2. **Způsob ochrany dat** Určuje umístění zálohování cíl - páska, disk a cloud. V našem příkladu jsme bude chránit data na místní disk a do cloudu.
3. A **plán zálohování** určující, kdy je třeba přijmout zálohy a jak často se mají synchronizovat data mezi serverem DPM a na provozním serveru.
4. A **plán uchovávání informací** který určuje, jak dlouho chcete zachovat body obnovení v Azure.

### <a name="creating-a-protection-group"></a>Vytvoření skupiny ochrany
Začněte vytvořením nové skupiny ochrany pomocí [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) rutiny.

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Výše uvedené rutina vytvoří skupiny ochrany s názvem *ProtectGroup01*. Existující skupiny ochrany můžete také upravit později přidat zálohování do cloudu Azure. Ale žádné změny do skupiny ochrany – nové nebo existující - musíme získat popisovač *upravitelnými* pomocí [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) rutiny.

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Přidání členů skupiny do skupiny ochrany
Každý Agent aplikace DPM zná seznam zdrojů dat na serveru, který je nainstalovaný. Přidat zdroje dat do skupiny ochrany, musí nejprve poslat seznam zdrojů dat zpět na DPM server agenta aplikace DPM. Jeden nebo více zdrojů dat se pak vybrali a přidají se do skupiny ochrany. Prostředí PowerShell kroky potřebné k dosažení tohoto cíle jsou:

1. Získat seznam všech serverů spravovaných aplikací DPM pomocí agenta aplikace DPM.
2. Vyberte konkrétní server.
3. Získat seznam všech zdrojů dat na serveru.
4. Vyberte jeden nebo více zdrojů dat a přidat je do skupiny ochrany

Seznam serverů, ve kterých je nainstalován Agent aplikace DPM a je spravován serverem aplikace DPM je získán pomocí [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) rutiny. V tomto příkladu jsme bude filtrovat a konfigurovat jenom PS s názvem *productionserver01* pro zálohování.

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”}
```

Nyní se načíst seznam zdrojů dat v ```$server``` pomocí [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605) rutiny. V tomto příkladu jsme pro svazek filtrování * D:\* , jsme chcete konfigurovat pro zálohování. Tento zdroj dat se pak přidá do skupiny ochrany pomocí [přidat DPMChildDatasource](https://technet.microsoft.com/library/hh881732) rutiny. Nezapomeňte použít *upravitelnými* objektu skupiny ochrany ```$MPG``` k budou přidány.

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Opakujte tento krok tolikrát, kolikrát podle potřeby, dokud nepřidáte všechny zvolené zdroje dat do skupiny ochrany. Můžete začít s jenom jeden zdroj dat a dokončení pracovního postupu pro vytvoření skupiny ochrany a později přidat další datové zdroje do skupiny ochrany.

### <a name="selecting-the-data-protection-method"></a>Výběr způsobu ochrany dat
Jakmile zdroje dat byly přidány do skupiny ochrany, dalším krokem je zadat, ochranu pomocí metody [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) rutiny. V tomto příkladu skupina ochrany je instalační program pro místního disku a zálohování. Budete taky muset zadat zdroj dat, který chcete chránit, do cloudu pomocí [přidat DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) rutiny příznakem - Online.

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Nastavení uchování
Nastavení uchovávání bodů zálohování pomocí [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) rutiny. Když může být lichý nastavení uchovávání předtím, než byla definována plán zálohování, pomocí ```Set-DPMPolicyObjective``` rutiny automaticky nastaví výchozí plán zálohování, který je pak možné upravit. Vždy je možné sady nejdřív naplánovat zálohování a po zásady uchovávání informací.

V následujícím příkladu rutiny nastavuje parametry, uchování pro zálohy disku. To bude uchování záloh pro 10 dnů a synchronizaci dat každých 6 hodin mezi provozním serveru a serverem DPM. ```SynchronizationFrequencyMinutes``` Nedefinuje četnost zálohování je bod vytvořen, ale jak často data se zkopírují na serveru aplikace DPM.  Toto nastavení zabrání zálohy příliš velká.

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Pro zálohování přejdete do Azure (aplikace DPM na ně odkazuje jako zálohování Online) rozsahy uchovávání mohou být konfigurovány pro [dlouhodobé uchovávání používá schéma Dědečka. otec SYN (GFS)](backup-azure-backup-cloud-as-tape.md). To znamená můžete definovat zásady uchovávání informací kombinované zahrnující denní, týdenní, měsíční a roční zásady uchovávání informací. V tomto příkladu vytvoření představující schéma komplexní uchovávání, který má být pole a pak nakonfigurujte pomocí rozsah uchování [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) rutiny.

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Nastavte plán zálohování
Aplikace DPM nastaví výchozí plán zálohování automaticky, pokud zadáte cíle ochrany pomocí ```Set-DPMPolicyObjective``` rutiny. Ke změně výchozích plánů, použijte [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) následovanou [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723) rutiny.

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

V předchozím příkladu ```$onlineSch``` je pole se čtyřmi prvky, které obsahuje existující plán ochranu online pro skupinu ochrany ve schématu GFS:

1. ```$onlineSch[0]``` obsahuje denní plán
2. ```$onlineSch[1]``` obsahuje Týdenní plán
3. ```$onlineSch[2]``` obsahuje plánování měsíčně
4. ```$onlineSch[3]``` obsahuje roční plán

Takže pokud budete muset upravit týdenní plán, budete muset naleznete ```$onlineSch[1]```.

### <a name="initial-backup"></a>Prvotní zálohování
Při zálohování zdroje dat poprvé, musí aplikace DPM vytvoří počáteční repliky, vytvoří úplnou kopii zdroj dat, měly by být na svazku repliky DPM. Tato aktivita bude naplánována buď pro určitý čas nebo lze spustit ručně, pomocí [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) rutiny s parametrem ```-NOW```.

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```
### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Změna velikosti repliky aplikace DPM a svazek bodu obnovení
Můžete také změnit velikost svazku repliky DPM a stínové kopie svazku pomocí [Set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) rutiny jako v následujícím příkladu: Get-DatasourceDiskAllocation - Datasource $DS Set-DatasourceDiskAllocation - Zdroj dat $DS - ProtectionGroup $MPG – ruční - ReplicaArea (2 gb) - ShadowCopyArea (2 gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Potvrzení změny do skupiny ochrany
Nakonec změny je nutné před provedením aplikace DPM může provést zálohování na novou konfiguraci skupiny ochrany. Toho lze dosáhnout pomocí [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) rutiny.

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```
## <a name="view-the-backup-points"></a>Zobrazení body zálohy
Můžete použít [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) rutiny se získat seznam všech bodů obnovení pro zdroj dat. V tomto příkladu provedeme následující:

* načtení všech PGs na serveru DPM a uloženy do pole ```$PG```
* získání zdroje dat odpovídající ```$PG[0]```
* získání všech bodů obnovení pro zdroj dat.

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Obnovit data chráněná v Azure
Obnovení dat je kombinací ```RecoverableItem``` objektu a ```RecoveryOption``` objektu. V předchozí části My seznam body zálohy pro zdroj dat.

V následujícím příkladu jsme ukazují, jak k obnovení virtuálního počítače technologie Hyper-V z Azure Backup kombinací body zálohy s cíle pro obnovení. Tento příklad obsahuje:

* Vytváření možnost obnovení pomocí [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592) rutiny.
* Načítání pole body zálohy pomocí ```Get-DPMRecoveryPoint``` rutiny.
* Výběr bod obnovení ze zálohy.

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Příkazy můžete snadno rozšířit pro jakýkoli typ datasource.

## <a name="next-steps"></a>Další postup
* Další informace o DPM pro zálohování Azure najdete v části [Úvod k zálohování aplikace DPM](backup-azure-dpm-introduction.md)
