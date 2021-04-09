---
title: Pravidelné zálohování a obnovování v samostatných Service Fabricch Azure
description: Použijte funkci periodického zálohování a obnovení samostatného Service Fabric k povolení pravidelného zálohování dat aplikací.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: d78a627c0c50a3e2ec57138e40cb5bc97486d6f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103198695"
---
# <a name="periodic-backup-and-restore-in-a-standalone-service-fabric"></a>Pravidelné zálohování a obnovování v samostatné Service Fabric
> [!div class="op_single_selector"]
> * [Clustery v Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Samostatné clustery](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric je platforma distribuovaných systémů usnadňující vývoj a správu spolehlivých distribuovaných cloudových aplikací založených na mikroslužbách. Umožňuje provozování bezstavových i stavových mikroslužeb. Stavové služby mohou udržovat proměnlivý a směrodatný stav mimo požadavek a odpověď nebo úplnou transakci. Pokud stavová služba přestane trvat dlouhou dobu nebo ztratí informace z důvodu havárie, může být nutné ji obnovit do nějakého nedávného zálohování svého stavu, aby bylo možné službu po jejím zálohování dále poskytovat.

Service Fabric replikuje stav napříč několika uzly, aby bylo zajištěno, že je služba vysoce dostupná. I v případě, že jeden uzel v clusteru dojde k chybě, služba bude nadále k dispozici. V některých případech je však stále žádoucí, aby data služby byla spolehlivá proti širším selháním.
 
Služba může například potřebovat zálohovat svá data, aby se chránila z následujících scénářů:
- Trvalá ztráta celého clusteru Service Fabric.
- Trvalá ztráta většiny replik oddílu služby
- Chyby správy, při kterých se stav omylem odstranil nebo je poškozen. Například správce s dostatečným oprávněním může službu omylem odstranit.
- Chyby ve službě, které způsobují poškození dat. K tomu může dojít například v případě, že upgrade kódu služby začne psát vadná data do spolehlivé kolekce. V takovém případě je možné, že kód i data budou muset být vráceny do předchozího stavu.
- Zpracování offline dat. Může být vhodné mít offline zpracování dat pro business intelligence, ke kterým dochází nezávisle na službě, která data generuje.

Service Fabric poskytuje integrované rozhraní API k tomu, aby bylo [zálohování a obnovení](service-fabric-reliable-services-backup-restore.md)k určitému bodu v čase. Vývojáři aplikací můžou tato rozhraní API používat k pravidelnému zálohování stavu služby. Kromě toho, pokud Správci služeb chtějí aktivovat zálohování mimo službu v určitou dobu (například před upgradem aplikace), musí vývojáři vystavit zálohování (a obnovení) jako rozhraní API ze služby. Udržování záloh je nad rámec těchto nákladů vyšší. Můžete například chtít provést pět přírůstkových záloh každou půl hodiny následovaný úplným zálohováním. Po úplném zálohování můžete odstranit předchozí přírůstkové zálohy. Tento přístup vyžaduje další kód, který by měl při vývoji aplikací dopředné náklady.

Zálohování dat aplikací je v pravidelných intervalech základní nutnost spravovat distribuovanou aplikaci a chránit před ztrátou dat nebo dlouhodobou ztrátou dostupnosti služeb. Service Fabric poskytuje volitelnou službu pro zálohování a obnovení, která umožňuje konfigurovat pravidelné zálohování stavových Reliable Services (včetně služeb actor) bez nutnosti psát další kód. Také usnadňuje obnovení dříve vytvořených záloh. 

Service Fabric poskytuje sadu rozhraní API pro zajištění následujících funkcí vztahujících se na funkci periodického zálohování a obnovení:

- Naplánování pravidelného zálohování spolehlivých stavových služeb a Reliable Actors s podporou pro nahrávání záloh do (externích) umístění úložiště. Podporovaná umístění úložiště
    - Azure Storage
    - Sdílení souborů (místně)
- Vytvoření výčtu záloh
- Aktivace neplánované zálohy oddílu
- Obnovení oddílu pomocí předchozí zálohy
- Dočasné pozastavení zálohování
- Správa uchovávání záloh (nadcházející)

## <a name="prerequisites"></a>Požadavky
* Service Fabric cluster se službou Fabric verze 6,4 nebo novější. Postup stažení požadovaného balíčku najdete v tomto [článku](service-fabric-cluster-creation-for-windows-server.md) .
* X. 509 certifikát pro šifrování tajných kódů potřebných pro připojení k úložišti pro ukládání záloh. V [článku](service-fabric-windows-cluster-x509-security.md) najdete informace o tom, jak získat nebo vytvořit certifikát X. 509 podepsaný svým držitelem.

* Service Fabric Reliable stavová aplikace vytvořená pomocí sady Service Fabric SDK verze 3,0 nebo vyšší. Pro aplikace cílené na .NET Core 2,0 by měla být aplikace sestavená pomocí sady Service Fabric SDK verze 3,1 nebo novější.
* Nainstalujte modul Microsoft. ServiceFabric. PowerShell. http (Preview) pro provedení konfiguračních volání.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.PowerShell.Http -AllowPrerelease
```

> [!NOTE]
> Pokud je verze PowerShellGet menší než 1.6.0, bude nutné aktualizovat, aby se přidala podpora pro příznak *-AllowPrerelease* :
>
> `Install-Module -Name PowerShellGet -Force`

* `Connect-SFCluster`Před provedením libovolné žádosti o konfiguraci pomocí modulu Microsoft. ServiceFabric. PowerShell. http zajistěte, aby byl cluster připojen pomocí příkazu.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Povolení služby zálohování a obnovení
Nejdřív musíte ve svém clusteru povolit _službu zálohování a obnovení_ . Získejte šablonu pro cluster, který chcete nasadit. Můžete použít [ukázkové šablony](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Pomocí následujících kroků povolte _službu zálohování a obnovení_ :

1. Ověřte, že `apiversion` je `10-2017` v konfiguračním souboru clusteru nastavená na hodnotu, a pokud ne, aktualizujte ho tak, jak je znázorněno v následujícím fragmentu kódu:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Nyní povolte _službu zálohování a obnovení_ přidáním následujícího oddílu do části `addonFeatures` `properties` , jak je znázorněno v následujícím fragmentu kódu: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Nakonfigurujte certifikát X. 509 pro šifrování přihlašovacích údajů. To je důležité pro zajištění, aby se přihlašovací údaje, které se mají připojit k úložišti, zašifroval před tím, než se zachovají. Nakonfigurujte šifrovací certifikát přidáním následující `BackupRestoreService` části v části `fabricSettings` , jak je znázorněno v následujícím fragmentu kódu: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            },
            {
                "name": "SecretEncryptionCertX509StoreName",
                "value": "My"
            }]
        }
        ...
    }
    ```

4. Po aktualizaci konfiguračního souboru clusteru předchozími změnami je použijte a nechejte nasazení nebo upgrade dokončeno. Po dokončení se _Služba zálohování a obnovení_ spustí v clusteru. Identifikátor URI této služby je `fabric:/System/BackupRestoreService` a služba se může nacházet v části systémová služba v průzkumníkovi Service Fabric. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Povolení pravidelného zálohování pro spolehlivou stavovou službu a Reliable Actors
Podívejme se na postupy, které umožňují pravidelné zálohování pro spolehlivou stavovou službu a Reliable Actors. Tyto kroky předpokládají
- Cluster je nakonfigurovaný s service_ zálohování a obnovení.
- V clusteru je nasazená spolehlivá stavová služba. Pro účely tohoto průvodce rychlým startem je identifikátor URI aplikace `fabric:/SampleApp` a identifikátor URI pro spolehlivou stavovou službu, která patří do této aplikace `fabric:/SampleApp/MyStatefulService` . Tato služba je nasazená s jedním oddílem a ID oddílu je `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7` .  

### <a name="create-backup-policy"></a>Vytvořit zásady zálohování

Prvním krokem je vytvoření zásad zálohování popisujících plán zálohování, cílové úložiště pro zálohovaná data, název zásady, maximální počet přírůstkových záloh, které se mají povolit, než se spustí úplné zálohování a zásady uchovávání informací pro úložiště záloh. 

Pro úložiště zálohování vytvořte sdílenou složku a udělte přístup k této sdílené složce pro všechny počítače s Service Fabricm uzlem. Tento příklad předpokládá, že sdílená složka s názvem `BackupStore` je přítomna `StorageServer` .


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell s použitím modulu Microsoft. ServiceFabric. PowerShell. http

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Volání REST pomocí PowerShellu

Pokud chcete vytvořit novou zásadu, spusťte následující skript PowerShellu pro vyvolání požadované REST API.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

#### <a name="using-service-fabric-explorer"></a>Použití Service Fabric Explorer

1. V Service Fabric Explorer přejděte na kartu zálohy a vyberte akce > vytvořit zásadu zálohování.

    ![Vytvořit zásady zálohování][6]

2. Vyplňte informace. Pro samostatné clustery by měly být vybrány sdílené složky.

    ![Vytvořit sdílenou složku zásad zálohování][7]

### <a name="enable-periodic-backup"></a>Povolit pravidelná zálohování
Po definování zásad pro splnění požadavků na ochranu dat aplikace by měly být zásady zálohování přidružené k aplikaci. V závislosti na požadavku může být zásada zálohování přidružená k aplikaci, službě nebo oddílu.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell s použitím modulu Microsoft. ServiceFabric. PowerShell. http

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>Volání REST pomocí PowerShellu
Spusťte následující skript PowerShellu pro vyvolání požadované REST API k přidružení zásady zálohování s názvem `BackupPolicy1` vytvořeným v předchozím kroku s aplikací `SampleApp` .

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>Použití Service Fabric Explorer

1. Vyberte aplikaci a pokračujte na akci. Klikněte na povolit nebo aktualizovat zálohu aplikace.

    ![Povolit zálohování aplikací][3] 

2. Nakonec vyberte požadovanou zásadu a vyberte *Povolit zálohování*.

    ![Vybrat zásadu][4]

### <a name="verify-that-periodic-backups-are-working"></a>Ověřte, jestli fungují pravidelné zálohy.

Po povolení zálohování aplikace budou všechny oddíly patřící do spolehlivých stavových služeb a Reliable Actors v rámci aplikace začínat pravidelné zálohování podle přidružených zásad zálohování.

![Událost stavu BackedUp oddílu][0]

### <a name="list-backups"></a>Vypsat zálohy

Zálohy přidružené ke všem oddílům, které patří do spolehlivých stavových služeb a Reliable Actors aplikace, se dají vyčíslit pomocí rozhraní API _Getbackups_ . V závislosti na požadavku lze vytvořit výčet záloh pro aplikace, služby nebo oddíl.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell s použitím modulu Microsoft. ServiceFabric. PowerShell. http

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Volání REST pomocí PowerShellu

Spuštěním následujícího skriptu PowerShellu vyvolejte rozhraní HTTP API, abyste mohli vytvořit výčet záloh vytvořených pro všechny oddíly v rámci `SampleApp` aplikace.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Ukázkový výstup pro výše uvedený běh:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
FailureError            : 
```

#### <a name="using-service-fabric-explorer"></a>Použití Service Fabric Explorer

Chcete-li zobrazit zálohy v Service Fabric Explorer, přejděte do oddílu a vyberte kartu zálohy.

![Vytvoření výčtu záloh][5]

## <a name="limitation-caveats"></a>Omezení a upozornění
- Rutiny Service Fabric PowerShellu jsou v režimu náhledu.
- V systému Linux není podporována podpora Service Fabricch clusterů.

## <a name="next-steps"></a>Další kroky
- [Principy konfigurace pravidelného zálohování](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Odkaz na REST API obnovení zálohy](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/create-bp-fileshare.png
