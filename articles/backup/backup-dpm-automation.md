---
title: Použití PowerShellu k zálohování úloh DPM
description: Naučte se nasazovat a spravovat Azure Backup pro Data Protection Manager (DPM) pomocí PowerShellu.
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 176cbffe5152462055c4ffdb2367cf9c0ab97c1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90968303"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Nasazení a správa zálohování do Azure pro servery DPM (Data Protection Manager) pomocí PowerShellu

V tomto článku se dozvíte, jak pomocí PowerShellu nastavit Azure Backup na serveru DPM a spravovat zálohování a obnovení.

## <a name="setting-up-the-powershell-environment"></a>Nastavení prostředí PowerShell

Než budete moct pomocí PowerShellu spravovat zálohy z Data Protection Manager do Azure, musíte mít v prostředí PowerShell správné prostředí. Na začátku relace PowerShellu se ujistěte, že spustíte následující příkaz, který naimportuje správné moduly a umožní vám správně odkazovat na rutiny DPM:

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

## <a name="setup-and-registration"></a>Instalace a registrace

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Začněte [stažením nejnovější Azure PowerShell](/powershell/azure/install-az-ps).

Prostředí PowerShell může automatizovat následující úlohy nastavení a registrace:

* Vytvoření trezoru Služeb zotavení
* Instalace agenta Azure Backup
* Registrace ve službě Azure Backup
* Nastavení sítě
* Nastavení šifrování

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Následující kroky vás provedou vytvořením trezoru Recovery Services. Recovery Services trezor se liší od trezoru záloh.

1. Pokud používáte Azure Backup poprvé, musíte pomocí rutiny **Register-AzResourceProvider** zaregistrovat poskytovatele služby Azure Recovery Services s vaším předplatným.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Recovery Services trezor je prostředek ARM, takže ho budete muset umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořit novou. Při vytváření nové skupiny prostředků zadejte název a umístění pro skupinu prostředků.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. K vytvoření nového trezoru použijte rutinu **New-AzRecoveryServicesVault** . Nezapomeňte zadat stejné umístění úložiště, jaké bylo použito pro skupinu prostředků.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Zadejte typ redundance úložiště, který se má použít. Můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage), [geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)nebo [úložiště redundantní v zóně (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage). Následující příklad ukazuje možnost **BackupStorageRedundancy** pro *testVault*  nastavenou na hodnotu **inredundantní**.

   > [!TIP]
   > Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Backup Recovery Services do proměnné.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Zobrazení trezorů v předplatném

K zobrazení seznamu všech trezorů v rámci aktuálního předplatného použijte **příkaz Get-AzRecoveryServicesVault** . Tento příkaz můžete použít ke kontrole vytvoření nového trezoru nebo k zobrazení, které trezory jsou k dispozici v rámci předplatného.

V seznamu je spuštěný příkaz, Get-AzRecoveryServicesVault a všechny trezory v rámci předplatného.

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

Před instalací agenta Azure Backup musíte mít Instalační program stažený a přítomen na Windows serveru. Nejnovější verzi instalačního programu můžete získat z webu [Microsoft Download Center](https://aka.ms/azurebackup_agent) nebo ze stránky řídicího panelu Recovery Servicesového trezoru. Uložte instalační program do snadno přístupného umístění, jako je `C:\Downloads\*` .

Chcete-li nainstalovat agenta, spusťte následující příkaz v konzole PowerShell se zvýšenými oprávněními **na serveru DPM**:

```powershell
MARSAgentInstaller.exe /q
```

Tím se nainstaluje Agent se všemi výchozími možnostmi. Instalace na pozadí trvá několik minut. Pokud nezadáte možnost */Nu* , na konci instalace se otevře okno **web Windows Update** , kde můžete vyhledat aktualizace.

Agent se zobrazí v seznamu nainstalovaných programů. Seznam nainstalovaných programů zobrazíte tak, že přejdete do části **Ovládací panely** programy programy  >    >  **a funkce**.

![Agent nainstalován](./media/backup-dpm-automation/installed-agent-listing.png)

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
| řetězec |Výslovný souhlas s Microsoft Update |- |
| /nu |Po dokončení instalace Nekontrolovat aktualizace |- |
| parametr |Odinstaluje agenta Microsoft Azure Recovery Services. |- |
| /pH |Adresa hostitele proxy serveru |- |
| /po |Číslo portu hostitele proxy serveru |- |
| /pu |Uživatelské jméno hostitele proxy serveru |- |
| /pw |Heslo proxy |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registrace aplikace DPM do trezoru Recovery Services

Po vytvoření trezoru Recovery Services Stáhněte nejnovějšího agenta a přihlašovací údaje trezoru a uložte je do vhodného umístění, jako je C:\Downloads..

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Na serveru DPM spusťte rutinu [Start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) , která zaregistruje počítač do trezoru.

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

### <a name="initial-configuration-settings"></a>Nastavení počáteční konfigurace

Po registraci serveru DPM v trezoru Recovery Services se spustí výchozí nastavení předplatného. Mezi tato nastavení předplatného patří sítě, šifrování a pracovní oblast. Pokud chcete změnit nastavení předplatného, musíte nejdřív získat popisovač pro existující (výchozí) nastavení pomocí rutiny [Get-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting) :

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

V tomto místním objektu prostředí PowerShell jsou provedeny všechny úpravy ```$setting```  a potom je celý objekt potvrzen do aplikace DPM a Azure Backup jej uložit pomocí rutiny [set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) . K ```–Commit``` zajištění trvalého uložení změn je nutné použít příznak. Nastavení se nepoužijí a Azure Backup použít, pokud se nepotvrdí.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Sítě

Pokud je připojení počítače DPM k Azure Backup službě na internetu prostřednictvím proxy server, mělo by být k dispozici nastavení proxy server pro úspěšné zálohování. To se provádí pomocí ```-ProxyServer``` ```-ProxyPort``` parametrů a ```-ProxyUsername``` a ```ProxyPassword``` pomocí rutiny [set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) . V tomto příkladu není proxy server, takže explicitně vymažeme všechny informace týkající se proxy serveru.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Využití šířky pásma je také možné ovládat pomocí možností ```-WorkHourBandwidth``` a ```-NonWorkHourBandwidth``` pro danou sadu dnů v týdnu. V tomto příkladu nenastavujeme žádné omezení.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Konfigurace pracovní oblasti

Agent Azure Backup spuštěný na serveru DPM potřebuje dočasné úložiště pro data obnovená z cloudu (místní pracovní oblast). Nastavte pracovní oblast pomocí rutiny [set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) a ```-StagingAreaPath``` parametru.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

V předchozím příkladu se pracovní oblast nastaví na *C:\StagingArea* v objektu PowerShellu ```$setting``` . Zajistěte, aby Zadaná složka již existovala, nebo jinak se konečné potvrzení nastavení předplatného nezdaří.

### <a name="encryption-settings"></a>Nastavení šifrování

Zálohovaná data odesílaná do Azure Backup jsou šifrovaná za účelem ochrany důvěrnosti dat. Šifrovací heslo je "heslo", které dešifruje data v době obnovení. Je důležité, aby tyto informace byly po nastavení bezpečné a zabezpečené.

V následujícím příkladu převede první příkaz řetězec ```passphrase123456789``` na zabezpečený řetězec a přiřadí zabezpečený řetězec k proměnné s názvem ```$Passphrase``` . Druhý příkaz nastaví zabezpečený řetězec ```$Passphrase``` jako heslo pro šifrování záloh.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Ponechte informace o heslu v bezpečí a zabezpečení po jeho nastavení. Nebudete moct obnovit data z Azure bez tohoto přístupového hesla.
>
>

V tomto okamžiku byste měli provést všechny požadované změny ```$setting``` objektu. Nezapomeňte změny potvrdit.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Ochrana dat pro Azure Backup

V této části přidáte do DPM provozní server a pak data ochráníte do místního úložiště DPM a pak na Azure Backup. V příkladech ukážeme, jak zálohovat soubory a složky. Logika se dá snadno rozšířit na zálohování libovolného zdroje dat podporovaného aplikací DPM. Všechny zálohy aplikace DPM se řídí skupinou ochrany (PG) se čtyřmi částmi:

1. **Členové skupiny** jsou seznam všech chráněných objektů (označovaných také jako *zdroje dat* v aplikaci DPM), které chcete chránit ve stejné skupině ochrany. Například můžete chtít chránit produkční virtuální počítače v jedné skupině ochrany a SQL Server databáze v jiné skupině ochrany, protože mohou mít různé požadavky na zálohování. Než budete moct zálohovat jakýkoli zdroj dat na provozním serveru, musíte se ujistit, že je na serveru nainstalovaný agent DPM a spravuje ho DPM. Postupujte podle kroků pro [instalaci agenta DPM](/system-center/dpm/deploy-dpm-protection-agent) a jeho propojení s příslušným serverem DPM.
2. **Metoda ochrany dat** Určuje cílové umístění zálohy – páska, disk a Cloud. V našem příkladu budeme chránit data na místní disk a do cloudu.
3. **Plán zálohování** , který určuje, kdy je potřeba provést zálohování a jak často se mají synchronizovat data mezi serverem DPM a provozním serverem.
4. **Plán uchovávání informací** , který určuje, jak dlouho mají být body obnovení v Azure uchovávány.

### <a name="creating-a-protection-group"></a>Vytvoření skupiny ochrany

Začněte vytvořením nové skupiny ochrany pomocí rutiny [New-DPMProtectionGroup](/powershell/module/dataprotectionmanager/new-dpmprotectiongroup) .

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Výše uvedená rutina vytvoří skupinu ochrany s názvem *ProtectGroup01*. Existující skupinu ochrany je také možné upravit později a přidat tak zálohu do cloudu Azure. Pokud ale chcete udělat změny ve skupině ochrany – nové nebo existující, potřebujeme získat popisovač pro *upravitelný* objekt pomocí rutiny [Get-DPMModifiableProtectionGroup](/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup) .

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Přidání členů skupiny do skupiny ochrany

Každý agent DPM ví seznam zdrojů dat na serveru, na kterém je nainstalován. Chcete-li přidat zdroj dat do skupiny ochrany, musí nejdřív agent DPM odeslat seznam datových zdrojů zpět na server DPM. Jeden nebo více zdrojů dat se pak vybere a přidají se do skupiny ochrany. K dosažení těchto kroků je potřeba tento postup:

1. Načte seznam všech serverů spravovaných aplikací DPM prostřednictvím agenta DPM.
2. Vyberte konkrétní server.
3. Načte seznam všech zdrojů dat na serveru.
4. Vyberte jeden nebo více zdrojů dat a přidejte je do skupiny ochrany.

Seznam serverů, na kterých je nainstalován agent aplikace DPM a který je spravován serverem DPM, je získán pomocí rutiny [Get-DPMProductionServer](/powershell/module/dataprotectionmanager/get-dpmproductionserver) . V tomto příkladu vyfiltrujeme a nakonfigurujeme PowerShell s názvem *productionserver01* pro zálohování.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Nyní načtěte seznam zdrojů dat na základě ```$server``` rutiny [Get-DPMDatasource](/powershell/module/dataprotectionmanager/get-dpmdatasource) . V tomto příkladu filtrujeme svazek `D:\` , který chceme nakonfigurovat pro zálohování. Tento zdroj dat se pak přidá do skupiny ochrany pomocí rutiny [Add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource) . Nezapomeňte použít objekt skupiny ochrany s *upravitelnou* ochranou, aby ```$MPG``` bylo možné dodatky.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Tento krok opakujte tolikrát, kolikrát je potřeba, dokud nepřidáte všechny vybrané zdroje dat do skupiny ochrany. Můžete také začít s pouze jedním zdrojem dat a dokončit pracovní postup pro vytvoření skupiny ochrany a později přidat další zdroje dat do skupiny ochrany.

### <a name="selecting-the-data-protection-method"></a>Výběr způsobu ochrany dat

Po přidání zdrojů dat do skupiny ochrany je dalším krokem zadání metody ochrany pomocí rutiny [set-DPMProtectionType](/powershell/module/dataprotectionmanager/set-dpmprotectiontype) . V tomto příkladu je skupina ochrany nastavená na místní disk a cloudové zálohování. Je také potřeba zadat zdroj dat, který chcete chránit ke cloudu pomocí příznaku [Add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource) s příznakem-online.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Nastavení rozsahu uchování

Nastavte uchovávání bodů zálohy pomocí rutiny [set-DPMPolicyObjective](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective) . I když se může stát, že nastavení uchovávání před definováním plánu zálohování je liché, pomocí ```Set-DPMPolicyObjective``` rutiny se automaticky nastaví výchozí plán zálohování, který se dá upravit. Je vždycky možné nastavit plán zálohování jako první a zásady uchovávání informací po.

V následujícím příkladu rutina nastavuje parametry uchovávání pro zálohy disku. Tím se uchovávají zálohy po dobu 10 dnů a data se synchronizují každých 6 hodin mezi provozním serverem a serverem DPM. ```SynchronizationFrequencyMinutes```Nedefinuje, jak často se vytvoří bod zálohování, ale jak často se data zkopírují na server DPM.  Toto nastavení zabrání nadměrnému navracení záloh.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

V případě záloh do Azure (aplikace DPM na ně odkazuje jako na online zálohování) rozsahy uchovávání dat je možné nakonfigurovat pro [dlouhodobé uchovávání dat pomocí schématu dědečka-otce-syn (GFS)](backup-azure-backup-cloud-as-tape.md). To znamená, že můžete definovat kombinované zásady uchovávání dat zahrnující denní, týdenní, měsíční a roční zásady uchovávání informací. V tomto příkladu vytvoříme pole reprezentující komplexní schéma uchovávání, které chceme, a pak nakonfigurujete rozsah uchování pomocí rutiny [set-DPMPolicyObjective](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective) .

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Nastavení plánu zálohování

DPM nastaví výchozí plán zálohování automaticky, pokud určíte cíl ochrany pomocí ```Set-DPMPolicyObjective``` rutiny. Pokud chcete změnit výchozí plány, použijte rutinu [Get-DPMPolicySchedule](/powershell/module/dataprotectionmanager/get-dpmpolicyschedule) následovanou rutinou [set-DPMPolicySchedule](/powershell/module/dataprotectionmanager/set-dpmpolicyschedule) .

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

V předchozím příkladu ```$onlineSch``` je pole se čtyřmi prvky, které obsahují existující plán online ochrany pro skupinu ochrany v GFS schématu:

1. ```$onlineSch[0]``` obsahuje denní plán.
2. ```$onlineSch[1]``` obsahuje týdenní plán.
3. ```$onlineSch[2]``` obsahuje měsíční plán.
4. ```$onlineSch[3]``` obsahuje roční plán.

Takže pokud potřebujete upravit týdenní plán, je třeba použít odkaz na ```$onlineSch[1]``` .

### <a name="initial-backup"></a>Prvotní zálohování

Při prvním zálohování zdroje dat potřebuje DPM vytvořit počáteční repliku, která vytvoří úplnou kopii zdroje dat, který se má chránit na svazku repliky DPM. Tuto aktivitu je možné naplánovat buď na určitou dobu, nebo ji lze aktivovat ručně pomocí rutiny [set-DPMReplicaCreationMethod](/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod) s parametrem ```-NOW``` .

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Změna velikosti repliky aplikace DPM & svazku bodu obnovení

Velikost svazku repliky DPM a svazku stínové kopie můžete také změnit pomocí rutiny [set-DPMDatasourceDiskAllocation](/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation) , jako v následujícím příkladu: Get-DatasourceDiskAllocation-Datasource $DS Set-DatasourceDiskAllocation-DataSource $DS-Protection $MPG-Manual-ReplicaArea (2 GB)-ShadowCopyArea (2 GB)

### <a name="committing-the-changes-to-the-protection-group"></a>Změny se potvrdily do skupiny ochrany.

Nakonec je třeba provést změny, aby aplikace DPM mohla získat zálohu na novou konfiguraci skupiny ochrany. Toho lze dosáhnout pomocí rutiny [set-DPMProtectionGroup](/powershell/module/dataprotectionmanager/set-dpmprotectiongroup) .

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Zobrazit body zálohy

Pomocí rutiny [Get-DPMRecoveryPoint](/powershell/module/dataprotectionmanager/get-dpmrecoverypoint) můžete získat seznam všech bodů obnovení pro zdroj dat. V tomto příkladu budeme:

* Načte všechny PGs na serveru DPM a uloží je do pole. ```$PG```
* získat zdroje dat odpovídající ```$PG[0]```
* Získá všechny body obnovení pro zdroj dat.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Obnovení dat chráněných v Azure

Obnovování dat je kombinací ```RecoverableItem``` objektu a ```RecoveryOption``` objektu. V předchozí části jsme získali seznam bodů zálohování pro zdroj dat.

V následujícím příkladu ukážeme, jak obnovit virtuální počítač Hyper-V z Azure Backup kombinováním bodů zálohování s cílem obnovení. Tento příklad obsahuje:

* Vytvoření možnosti obnovení pomocí rutiny  [New-DPMRecoveryOption](/powershell/module/dataprotectionmanager/new-dpmrecoveryoption) .
* Načítá se pole záložních bodů pomocí ```Get-DPMRecoveryPoint``` rutiny.
* Výběr bodu zálohování, ze kterého se má obnovit.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Příkazy lze snadno rozšířit pro libovolný typ zdroje dat.

## <a name="next-steps"></a>Další kroky

* Další informace o aplikaci DPM pro Azure Backup najdete v tématu [Úvod do zálohování aplikace DPM](backup-azure-dpm-introduction.md) .
