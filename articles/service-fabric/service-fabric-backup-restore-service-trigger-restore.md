---
title: Obnovování zálohy v Azure Service Fabric
description: Použijte funkci periodického zálohování a obnovení v Service Fabric pro obnovení dat ze zálohy dat aplikace.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: f98bf4f4518abd5f1b1a826e355c851acc055852
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86246686"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Obnovování zálohy v Azure Service Fabric

V Azure Service Fabric můžou spolehlivé stavové služby a Reliable Actors udržovat proměnlivý a autoritativní stav po dokončení transakce žádosti a odpovědi. Stavová služba může trvat dlouhou dobu nebo ztratit informace z důvodu havárie. Pokud k tomu dojde, je nutné obnovit službu z nejnovější přijatelné zálohy, aby mohla nadále fungovat.

Můžete například nakonfigurovat službu, která bude zálohovat svá data a chránit tak v následujících situacích:

- **Případ zotavení po havárii**: trvalá ztráta celého clusteru Service Fabric.
- **Případ ztráty dat**: trvalá ztráta většiny replik oddílu služby.
- **Případ ztráty dat**: náhodné odstranění nebo poškození služby. Správce například omylem odstraní službu.
- **Případ poškození dat**: chyby ve službě způsobují poškození dat. Například poškození dat může nastat, pokud upgrade kódu služby zapisuje vadná data do spolehlivé kolekce. V takovém případě může být nutné obnovit kód i data do předchozího stavu.

## <a name="prerequisites"></a>Požadavky

- Aby bylo možné spustit obnovení, musí být pro cluster povolená _služba analýzy chyb (v)_ .
- _Služba obnovení záloh (BRS)_ vytvořila zálohu.
- Obnovení se dá aktivovat jenom v oddílu.
- Nainstalujte modul Microsoft. ServiceFabric. PowerShell. http [v Preview] pro provedení konfiguračních volání.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- `Connect-SFCluster`Před provedením libovolné žádosti o konfiguraci pomocí modulu Microsoft. ServiceFabric. PowerShell. http zajistěte, aby byl cluster připojen pomocí příkazu.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>Aktivované obnovení

Obnovení můžete spustit pro kterýkoli z následujících scénářů:

- Obnovení dat pro _zotavení po havárii_.
- Obnovení dat pro _poškození dat/ztrátu dat_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Obnovení dat v případě zotavení po havárii

Pokud dojde ke ztrátě celého Service Fabric clusteru, můžete obnovit data pro oddíly spolehlivé stavové služby a Reliable Actors. Požadovaná záloha se dá vybrat ze seznamu při použití [GetBackupAPI s podrobnostmi úložiště zálohování](/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). Výčet zálohování může být pro aplikaci, službu nebo oddíl.

V následujícím příkladu Předpokládejme, že ztracený cluster je stejný jako cluster, na který se odkazuje v [Povolení pravidelného zálohování pro spolehlivou stavovou službu a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). V tomto případě `SampleApp` je nasazen se zapnutou zásadou zálohování a zálohy jsou nakonfigurovány na Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell s použitím modulu Microsoft. ServiceFabric. PowerShell. http

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Volání REST pomocí PowerShellu

Spusťte skript prostředí PowerShell, který použije REST API k vrácení seznamu záloh vytvořených pro všechny oddíly v rámci `SampleApp` aplikace. Rozhraní API vyžaduje, aby informace o úložišti zálohování vypisovat dostupné zálohy.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Ukázkový výstup pro výše uvedený běh:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

Chcete-li spustit obnovení, vyberte jeden ze záloh. Například aktuální záloha pro zotavení po havárii může být následující zálohování:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Pro rozhraní API pro obnovení musíte zadat podrobnosti _BackupId_ a _BackupLocation_ .

Také je nutné zvolit cílový oddíl v alternativním clusteru, jak je popsáno ve [schématu oddílu](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Alternativní záloha clusteru se obnoví do oddílu zadaného ve schématu oddílu z původního ztraceného clusteru.

Pokud je ID oddílu na alternativním clusteru `1c42c47f-439e-4e09-98b9-88b8f60800c6` , můžete ho namapovat na původní ID oddílu clusteru `974bd92a-b395-4631-8a7f-53bd4ae9cf22` porovnáním horního klíče a nízkého klíče pro _škálované dělení (UniformInt64Partition)_.

U _pojmenovaných oddílů_se hodnota název porovnává s určením cílového oddílu v alternativním clusteru.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell s použitím modulu Microsoft. ServiceFabric. PowerShell. http

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Volání REST pomocí PowerShellu

Pomocí následujícího [rozhraní API pro obnovení](/rest/api/servicefabric/sfclient-api-restorepartition)vyžádáte obnovení na oddíl záložního clusteru:

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Průběh obnovení můžete sledovat pomocí TrackRestoreProgress.

### <a name="using-service-fabric-explorer"></a>Použití Service Fabric Explorer
Můžete aktivovat obnovení z Service Fabric Explorer. Ujistěte se, že je v nastavení Service Fabric Explorer povolený rozšířený režim.
1. Vyberte požadované oddíly a klikněte na akce. 
2. Vyberte aktivovat oddíl obnovit a vyplňte informace pro Azure:

    ![Obnovení oddílu triggeru][2]

    nebo sdílená složka:

    ![Spouštěcí sdílená složka pro obnovení oddílu][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Obnovení dat pro _data corruption_ / _ztrátu dat_ poškození dat

V případě _ztráty dat_ nebo _poškození dat_můžete zálohovat oddíly pro spolehlivou stavovou službu a Reliable Actors oddíly obnovit do některého z vybraných záloh.

Následující příklad je pokračování v [Povolení pravidelného zálohování pro spolehlivou stavovou službu a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). V tomto příkladu jsou pro oddíl povoleny zásady zálohování a služba provádí zálohování s požadovanou frekvencí v Azure Storage.

Vyberte zálohu z výstupu  [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). V tomto scénáři je záloha vygenerována ze stejného clusteru jako dříve.

Chcete-li spustit obnovení, vyberte ze seznamu zálohu. U aktuálního _data loss_ / _poškození dat_ztráty dat vyberte následující zálohu:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Pro rozhraní API pro obnovení zadejte podrobnosti _BackupId_ a _BackupLocation_ . Cluster má povolenou službu Backup, takže _služba Service Fabric Backup Restore (BRS)_ identifikuje správné umístění úložiště z přidružených zásad zálohování.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell s použitím modulu Microsoft. ServiceFabric. PowerShell. http

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>Volání REST pomocí PowerShellu

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Průběh obnovy můžete sledovat pomocí TrackRestoreProgress.

## <a name="track-restore-progress"></a>Sledovat průběh obnovení

Oddíl spolehlivé stavové služby nebo spolehlivého objektu actor přijímá najednou jenom jednu žádost o obnovení. Po dokončení aktuální žádosti o obnovení přijme oddíl pouze jiný požadavek. V různých oddílech současně může být spuštěno více žádostí o obnovení.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell s použitím modulu Microsoft. ServiceFabric. PowerShell. http

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>Volání REST pomocí PowerShellu

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

Požadavek na obnovení bude postupovat v následujícím pořadí:

1. **Přijato**: stav _přijetí_ obnovení indikuje, že požadovaný oddíl byl spuštěn se správnými parametry požadavku.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress**: stav obnovení _probíhá_ , což znamená, že v oddílu se zálohováním uvedeným v žádosti dojde k obnovení. Oddíl oznamuje stav _ztráty dataztrát_ .
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Úspěch**, **selhání**nebo **časový limit**: požadovaná obnova může být dokončena v některém z následujících stavů. Každý stav má následující údaje o významu a odezvě:
    - **Úspěch**: stav obnovení _po úspěšném dokončení_ indikuje stav opětovného oddílu. Oddíl hlásí stav _RestoredEpoch_ a _RestoredLSN_ spolu s časem ve standardu UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Chyba**: stav obnovení _při selhání_ označuje selhání žádosti o obnovení. Příčina selhání je hlášena.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Časový limit**: stav obnovy _časového limitu_ označuje, že žádost má časový limit. Vytvořte novou žádost o obnovení s větším [RestoreTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). Výchozí časový limit je 10 minut. Před opětovným požadováním obnovení se ujistěte, že oddíl není ve stavu ztráty dat.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatické obnovení

Pro _Automatické obnovení_můžete nakonfigurovat Reliable stavovou službu a Reliable Actors oddíly v clusteru Service Fabric. V části zásada zálohování je nastavena `AutoRestore` na _hodnotu true_. Povolení _automatického obnovení_ automaticky obnoví data z poslední zálohy oddílu při hlášení ztráty dat. Další informace naleznete v tématech:

- [Povolení automatického obnovení v zásadách zálohování](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Reference k rozhraní API RestorePartition](/rest/api/servicefabric/sfclient-api-restorepartition)
- [Reference k rozhraní API GetPartitionRestoreProgress](/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Další kroky
- [Principy konfigurace pravidelného zálohování](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Odkaz na REST API obnovení zálohy](/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png
