---
title: Zálohování úloh Aplikace DPM pomocí Prostředí PowerShell
description: Zjistěte, jak nasadit a spravovat Azure Backup for Data Protection Manager (DPM) pomocí PowerShellu
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 06c138a4015a0b730369e091fc57a34d2190051d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616735"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Nasazení a správa zálohování do Azure pro servery DPM (Data Protection Manager) pomocí PowerShellu

Tento článek ukazuje, jak pomocí PowerShellu nastavit Azure Backup na serveru DPM a spravovat zálohování a obnovení.

## <a name="setting-up-the-powershell-environment"></a>Nastavení prostředí Prostředí PowerShell

Než budete moct používat PowerShell ke správě záloh ze Správce ochrany dat do Azure, musíte mít v PowerShellu správné prostředí. Na začátku relace prostředí PowerShell se ujistěte, že jste spustili následující příkaz pro import správných modulů a umožnili vám správně odkazovat na rutiny Aplikace DPM:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Nastavení a registrace

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li začít, [stáhněte si nejnovější Azure PowerShell](/powershell/azure/install-az-ps).

Pomocí prostředí PowerShell lze automatizovat následující úlohy nastavení a registrace:

* Vytvoření trezoru Služeb zotavení
* Instalace agenta Azure Backup
* Registrace ve službě Azure Backup
* Nastavení sítě
* Nastavení šifrování

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services

Následující kroky vás provedou vytvořením trezoru služby Recovery Services. Trezor služby Recovery Services se liší od trezoru zálohování.

1. Pokud používáte Azure Backup poprvé, musíte použít rutinu **Register-AzResourceProvider** k registraci poskytovatele služby Azure Recovery Service s vaším předplatným.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Trezor služby Recovery Services je prostředek ARM, takže jej musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořit novou. Při vytváření nové skupiny prostředků zadejte název a umístění skupiny prostředků.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Pomocí rutiny **New-AzRecoveryServicesVault** vytvořte novou úschovnu. Nezapomeňte zadat stejné umístění pro úschovnu, jaké bylo použito pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Zadejte typ redundance úložiště, která se má použít. můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantní úložiště (GRS).](../storage/common/storage-redundancy-grs.md) Následující příklad ukazuje -BackupStorageRedundancy možnost testVault je nastavena na GeoRedundant.

   > [!TIP]
   > Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Recovery Services do proměnné.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Zobrazení trezorů v předplatném

Pomocí **příkazu Get-AzRecoveryServicesVault** zobrazíte seznam všech trezorů v aktuálním předplatném. Pomocí tohoto příkazu můžete zkontrolovat, zda byl vytvořen nový trezor, nebo zjistit, jaké trezory jsou k dispozici v předplatném.

Spusťte příkaz Get-AzRecoveryServicesVault a všechny trezory v předplatném jsou uvedeny.

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

## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Instalace agenta Azure Backup na server DPM

Před instalací agenta Azure Backup je třeba nechat instalační program stáhnout a prezentovat na Windows Serveru. Nejnovější verzi instalačního programu můžete získat ze [služby Stažení softwaru společnosti Microsoft](https://aka.ms/azurebackup_agent) nebo ze stránky řídicího panelu úložiště služby Recovery Services. Uložte instalační program do snadno přístupného umístění,\*jako je *C:\Downloads .

Chcete-li agenta nainstalovat, spusťte následující příkaz v konzole PowerShell se zvýšenými oprávněními **na serveru DPM**:

```powershell
MARSAgentInstaller.exe /q
```

Tím nainstalujete agenta se všemi výchozími možnostmi. Instalace trvá několik minut na pozadí. Pokud nezadáte možnost */nu,* otevře se na konci instalace okno **Windows Update,** abyste zkontrolovali všechny aktualizace.

Agent se zobrazí v seznamu nainstalovaných programů. Seznam nainstalovaných programů naleznete v **Ovládacích panelech** > **Programy** > **programy a funkce**.

![Nainstalovaný agent](./media/backup-dpm-automation/installed-agent-listing.png)

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

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registrace aplikace DPM do trezoru služby Recovery Services

Po vytvoření trezoru služby Recovery Services stáhněte nejnovějšího agenta a přihlašovací údaje trezoru a uložte je na vhodném místě, jako je C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Na serveru DPM spusťte rutinu [Start-OBRegistration](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) a zaregistrujte počítač s trezorem.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Počáteční nastavení konfigurace

Jakmile je server DPM zaregistrován v trezoru služby Recovery Services, začne s výchozím nastavením předplatného. Tato nastavení předplatného zahrnují sítě, šifrování a pracovní oblast. Chcete-li změnit nastavení předplatného, musíte nejprve získat popisovač existujícího (výchozího) nastavení pomocí rutiny [Get-DPMCloudSubscriptionSetting:](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019)

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Všechny změny jsou provedeny na tento ```$setting``` místní objekt Prostředí PowerShell a pak celý objekt je potvrzena DPM a Azure Backup uložit pomocí [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) rutina. Je třeba použít ```–Commit``` příznak k zajištění, že změny jsou trvalé. Nastavení nebude použita a používá azure backup, pokud potvrzena.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Síťové služby

Pokud připojení počítače DPM ke službě Azure Backup na internetu je prostřednictvím proxy serveru, pak nastavení proxy serveru by měla být k dispozici pro úspěšné zálohování. To se provádí ```-ProxyServer```pomocí ```-ProxyPort``` ```-ProxyUsername``` a ```ProxyPassword``` , a parametry s [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) rutina. V tomto příkladu neexistuje žádný proxy server, takže explicitně vyklízení všech informací souvisejících s proxy serverem.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Využití šířky pásma lze ```-WorkHourBandwidth``` ```-NonWorkHourBandwidth``` také ovládat pomocí možností a pro danou sadu dnů v týdnu. V tomto příkladu nenastavujeme žádné omezení.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Konfigurace pracovní oblasti

Agent Azure Backup spuštěný na serveru DPM potřebuje dočasné úložiště pro data obnovená z cloudu (místní pracovní oblast). Nakonfigurujte pracovní oblast pomocí rutiny [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) a parametru. ```-StagingAreaPath```

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

Ve výše uvedeném příkladu bude pracovní oblast nastavena na *C:\StagingArea* v objektu ```$setting```Prostředí PowerShell . Ujistěte se, že zadaná složka již existuje, jinak se konečné potvrzení nastavení předplatného nezdaří.

### <a name="encryption-settings"></a>Nastavení šifrování

Záložní data odeslaná do služby Azure Backup jsou šifrovaná, aby byla chráněna důvěrnost dat. Heslo pro šifrování je "heslo" pro dešifrování dat v době obnovení. Je důležité, aby tyto informace bezpečné a bezpečné, jakmile je nastavena.

V níže uvedeném příkladu první ```passphrase123456789``` příkaz převede řetězec na zabezpečený řetězec ```$Passphrase```a přiřadí zabezpečený řetězec proměnné s názvem . druhý příkaz nastaví zabezpečený řetězec ```$Passphrase``` jako heslo pro šifrování záloh.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Po nastavení uchovávejte informace o přístupové mši v bezpečí. Bez tohoto přístupového hesla nebude možné obnovit data z Azure.
>
>

V tomto okamžiku byste měli provést všechny ```$setting``` požadované změny objektu. Nezapomeňte potvrdit změny.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Ochrana dat do Azure Backup

V této části přidáte produkční server do aplikace DPM a potom je budete chránit do místního úložiště DPM a potom do služby Azure Backup. V příkladech ukážeme, jak zálohovat soubory a složky. Logiku lze snadno rozšířit tak, aby zálohovala libovolný zdroj dat podporovaný dpm. Všechny zálohy aplikace DPM se řídí skupinou ochrany (PG) se čtyřmi částmi:

1. **Členové skupiny** je seznam všech chránitelných objektů (označovaných také jako *Datové zdroje* v systému DPM), které chcete chránit ve stejné skupině ochrany. Například můžete chtít chránit produkční virtuální počítače v jedné skupině ochrany a SQL Server databáze v jiné skupině ochrany, protože mohou mít různé požadavky na zálohování. Před zálohováním libovolného zdroje dat na produkčním serveru je třeba zajistit, aby byl agent DPM nainstalován na serveru a spravován aplikací DPM. Postupujte podle pokynů pro [instalaci agenta DPM a](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) jeho propojení s příslušným serverem DPM.
2. **Metoda ochrany dat** určuje umístění cílového zálohování – pásku, disk a cloud. V našem příkladu budeme chránit data na místní disk a do cloudu.
3. **Plán zálohování,** který určuje, kdy je třeba provést zálohování a jak často mají být data synchronizována mezi serverem DPM a produkčním serverem.
4. **Plán uchovávání informací,** který určuje, jak dlouho chcete zachovat body obnovení v Azure.

### <a name="creating-a-protection-group"></a>Vytvoření skupiny ochrany

Začněte vytvořením nové skupiny ochrany pomocí rutiny [New-DPMProtectionGroup.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmprotectiongroup?view=systemcenter-ps-2019)

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Výše uvedená rutina vytvoří skupinu ochrany s názvem *ProtectGroup01*. Existující skupinu ochrany lze také upravit později přidat zálohu do cloudu Azure. Chcete-li však provést jakékoli změny skupiny ochrany – nové nebo existující – musíme získat popisovač *upravitelného* objektu pomocí rutiny [Get-DPMModifiableProtectionGroup.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup?view=systemcenter-ps-2019)

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Přidání členů skupiny do skupiny ochrany

Každý agent dpm zná seznam zdrojů dat na serveru, na který je nainstalován. Chcete-li přidat zdroj dat do skupiny ochrany, musí agent DPM nejprve odeslat seznam zdrojů dat zpět na server DPM. Poté je vybrán jeden nebo více zdrojů dat a přidán do skupiny ochrany. Kroky prostředí PowerShell, které jsou k dosažení tohoto cíle nutné, jsou:

1. Načtení seznamu všech serverů spravovaných službou DPM prostřednictvím agenta DPM.
2. Zvolte konkrétní server.
3. Načtení seznamu všech zdrojů dat na serveru.
4. Zvolte jeden nebo více zdrojů dat a přidejte je do skupiny ochrany.

Seznam serverů, na kterých je nainstalován agent DPM a je spravován serverem DPM, je získán pomocí rutiny [Get-DPMProductionServer.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmproductionserver?view=systemcenter-ps-2019) V tomto příkladu budeme filtrovat a konfigurovat pouze PS s názvem *productionserver01* pro zálohování.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Nyní načíst seznam zdrojů ```$server``` dat na použití [rutiny Get-DPMDatasource.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmdatasource?view=systemcenter-ps-2019) V tomto příkladu filtrujeme svazek *D:\\ * který chceme nakonfigurovat pro zálohování. Tento zdroj dat je pak přidán do skupiny ochrany pomocí rutiny [Add-DPMChildDatasource.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) Nezapomeňte použít *modifikovatelný* ```$MPG``` objekt skupiny ochrany, aby se dodatky.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Tento krok opakujte tolikrát, kolikrát je to nutné, dokud do skupiny ochrany nepřidáte všechny vybrané zdroje dat. Můžete také začít pouze s jedním zdrojem dat a dokončit pracovní postup pro vytvoření skupiny ochrany a později přidat další zdroje dat do skupiny ochrany.

### <a name="selecting-the-data-protection-method"></a>Výběr metody ochrany dat

Po přidání zdrojů dat do skupiny ochrany je dalším krokem určení metody ochrany pomocí rutiny [Set-DPMProtectionType.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiontype?view=systemcenter-ps-2019) V tomto příkladu je skupina ochrany nastavena pro místní disk a cloudzálohování. Je také nutné zadat zdroj dat, který chcete chránit do cloudu pomocí rutiny [Add-DPMChildDatasource](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) s příznakem -Online.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Nastavení rozsahu uchování

Nastavte uchování pro záložní body pomocí rutiny [Set-DPMPolicyObjective.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019) I když se může zdát divné nastavit uchovávání informací před ```Set-DPMPolicyObjective``` plán zálohování byla definována, pomocí rutiny automaticky nastaví výchozí plán zálohování, které pak lze upravit. Je vždy možné nejprve nastavit plán zálohování a zásady uchovávání informací po.

V níže uvedeném příkladu rutina nastaví parametry uchování pro zálohování disků. Tím se zachová zálohy po dobu 10 dnů a synchronizuje te data každých 6 hodin mezi produkčním serverem a serverem DPM. Nedefinuje, ```SynchronizationFrequencyMinutes``` jak často je záložní bod vytvořen, ale jak často jsou data kopírována na server DPM.  Toto nastavení zabrání příliš velkému zálohování.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Pro zálohy jít do Azure (DPM odkazuje na ně jako online zálohy) rozsahy uchovávání lze nakonfigurovat pro [dlouhodobé uchovávání pomocí dědeček-otec-syn schéma (GFS)](backup-azure-backup-cloud-as-tape.md). To znamená, že můžete definovat kombinované zásady uchovávání informací zahrnující denní, týdenní, měsíční a roční zásady uchovávání informací. V tomto příkladu vytvoříme pole představující komplexní schéma uchování, které chceme, a pak nakonfigurujeme rozsah uchování pomocí rutiny [Set-DPMPolicyObjective.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019)

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Nastavení plánu zálohování

Aplikace DPM nastaví výchozí plán zálohování automaticky, ```Set-DPMPolicyObjective``` pokud pomocí rutiny zadáte účel ochrany. Chcete-li změnit výchozí plány, použijte rutinu [Get-DPMPolicySchedule](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmpolicyschedule?view=systemcenter-ps-2019) následovanou rutinou [Set-DPMPolicySchedule.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyschedule?view=systemcenter-ps-2019)

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

Ve výše uvedeném příkladu je pole se čtyřmi prvky, ```$onlineSch``` které obsahuje existující plán ochrany online pro skupinu ochrany v režimu GFS:

1. ```$onlineSch[0]```obsahuje denní rozvrh
2. ```$onlineSch[1]```obsahuje týdenní plán
3. ```$onlineSch[2]```obsahuje měsíční plán
4. ```$onlineSch[3]```obsahuje roční harmonogram

Pokud tedy potřebujete upravit týdenní plán, musíte se ```$onlineSch[1]```obrátit na .

### <a name="initial-backup"></a>Prvotní zálohování

Při prvním zálohování zdroje dat aplikace DPM vytvoří počáteční repliku, která vytvoří úplnou kopii zdroje dat, který má být chráněn na svazku repliky aplikace DPM. Tato aktivita může být naplánována na určitý čas nebo může být spuštěna ručně pomocí rutiny ```-NOW``` [Set-DPMReplicaCreationMethod](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod?view=systemcenter-ps-2019) s parametrem .

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Změna velikosti repliky aplikace DPM & svazku bodů obnovení

Můžete také změnit velikost svazku repliky DPM a svazku stínové kopie pomocí rutiny [Set-DPMDatasourceDiskAllocation](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation?view=systemcenter-ps-2019) jako v následujícím příkladu: Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Potvrzení změn ve skupině ochrany

Nakonec změny musí být potvrzeny před DPM může provést zálohu na novou konfiguraci skupiny ochrany. Toho lze dosáhnout pomocí rutiny [Set-DPMProtectionGroup.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiongroup?view=systemcenter-ps-2019)

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Zobrazení záložních bodů

Rutina [Get-DPMRecoveryPoint](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmrecoverypoint?view=systemcenter-ps-2019) můžete použít k získání seznamu všech bodů obnovení pro zdroj dat. V tomto příkladu:

* načtení všech pgů na serveru DPM a uložených v poli```$PG```
* získat zdroje údajů odpovídající```$PG[0]```
* získat všechny body obnovení pro zdroj dat.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Obnovení dat chráněných v Azure

Obnovení dat je kombinací ```RecoverableItem``` objektu ```RecoveryOption``` a objektu. V předchozí části jsme získali seznam záložních bodů pro zdroj dat.

V níže uvedeném příkladu ukazujeme, jak obnovit virtuální počítač Hyper-V z Azure Backup kombinací bodů zálohování s cílem pro obnovení. Tento příklad zahrnuje:

* Vytvoření možnosti obnovení pomocí rutiny [New-DPMRecoveryOption.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmrecoveryoption?view=systemcenter-ps-2019)
* Načítání pole záložních bodů ```Get-DPMRecoveryPoint``` pomocí rutiny.
* Výběr záložního bodu, ze který chcete obnovit.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Příkazy lze snadno rozšířit pro libovolný typ zdroje dat.

## <a name="next-steps"></a>Další kroky

* Další informace o dpm do azure backup viz [Úvod do zálohování DPM](backup-azure-dpm-introduction.md)
