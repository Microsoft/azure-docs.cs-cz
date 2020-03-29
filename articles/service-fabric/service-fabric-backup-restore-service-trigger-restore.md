---
title: Obnovení zálohy ve službě Azure Fabric
description: Pomocí funkce pravidelného zálohování a obnovení v Service Fabric pro obnovení dat ze zálohy dat aplikace.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 1737102ee652cc2263bd0a908c1336bc93a6757b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377901"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Obnovení zálohy ve službě Azure Fabric

Ve službě Azure Fabric spolehlivé stavové služby a spolehlivé objekty actor můžete udržovat proměnlivý, autoritativní stav po dokončení transakce požadavku a odpovědi. Stavová služba může dojít k dlouhodobému pádu nebo ke ztrátě informací z důvodu havárie. Pokud k tomu dojde, služba musí být obnovena z nejnovější přijatelné zálohy, aby mohla pokračovat v práci.

Můžete například nakonfigurovat službu tak, aby zálohovala data tak, aby byla chráněna před následujícími scénáři:

- **Případ zotavení po havárii**: Trvalá ztráta celého clusteru Service Fabric.
- **Případ ztráty dat**: Trvalá ztráta většiny replik oddílu služby.
- **Případ ztráty dat**: Náhodné odstranění nebo poškození služby. Například správce chybně odstraní službu.
- **Případ poškození dat**: Chyby ve službě způsobují poškození dat. Například poškození dat může dojít při upgradu kódu služby zapíše chybná data do spolehlivé kolekce. V takovém případě bude pravděpodobně muset obnovit kód a data do dřívějšího stavu.

## <a name="prerequisites"></a>Požadavky

- Chcete-li spustit obnovení, musí být pro cluster _povolena služba ANALÝZY poruch (FAS)._
- _Záloha vytvořila službu Obnovení zálohy (BRS)._
- Obnovení lze spustit pouze v oddílu.
- Nainstalujte modul Microsoft.ServiceFabric.Powershell.Http [In Preview] pro volání konfigurace.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Před provedením jakéhokoli požadavku `Connect-SFCluster` na konfiguraci pomocí modulu Microsoft.ServiceFabric.Powershell.Http se ujistěte, že je cluster připojen pomocí příkazu.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>Spouštěné obnovení

Obnovení lze spustit v některém z následujících scénářů:

- Obnovení dat pro _zotavení po havárii_.
- Obnovení dat pro _poškození/ztrátu dat_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Obnovení dat v případě zotavení po havárii

Pokud dojde ke ztrátě celého clusteru Service Fabric, můžete obnovit data pro oddíly spolehlivé stavové služby a spolehlivé objekty actor. Požadovanou zálohu lze vybrat ze seznamu při použití [rozhraní GetBackupAPI s podrobnostmi o úložišti záloh](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). Výčet zálohy může být pro aplikaci, službu nebo oddíl.

V následujícím příkladu předpokládejme, že ztracený cluster je stejný cluster, který je uveden v [povolení pravidelného zálohování pro spolehlivé stavové služby a spolehlivé objekty actor](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). V takovém `SampleApp` případě se nasazuje s povolenou zásadou zálohování a zálohy jsou nakonfigurované do Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Prostředí Powershell pomocí modulu Microsoft.ServiceFabric.Powershell.Http

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Volání na odpočinek pomocí prostředí Powershell

Spusťte skript prostředí PowerShell a pomocí rozhraní REST API vrátíte seznam `SampleApp` záloh vytvořených pro všechny oddíly uvnitř aplikace. Rozhraní API vyžaduje informace o úložišti záloh k zobrazení seznamu dostupných záloh.

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

Chcete-li spustit obnovení, zvolte jednu ze záloh. Například aktuální záloha pro zotavení po havárii může být následující záloha:

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

Pro rozhraní API pro obnovení je třeba zadat _podrobnosti BackupId_ a _BackupLocation._

Je také třeba zvolit cílový oddíl v alternativním clusteru, jak je podrobně popsáno v [schématu oddílů](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Alternativní záloha clusteru je obnovena do oddílu určeného v schématu oddílů z původního ztraceného clusteru.

Pokud je `1c42c47f-439e-4e09-98b9-88b8f60800c6`ID oddílu v alternativním clusteru , můžete `974bd92a-b395-4631-8a7f-53bd4ae9cf22` jej namapovat na původní ID oddílu clusteru porovnáním vysokého a nízkého klíče pro _dělení na dálku (UniformInt64Partition)._

V _případě pojmenovaného dělení_je hodnota názvu porovnána s identifikací cílového oddílu v alternativním clusteru.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Prostředí Powershell pomocí modulu Microsoft.ServiceFabric.Powershell.Http

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Volání na odpočinek pomocí prostředí Powershell

Chcete-li obnovení proti oddílu záložního clusteru, pomocí následujícího [rozhraní API pro obnovení](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition):

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

Můžete sledovat průběh obnovení s TrackRestoreProgress.

### <a name="using-service-fabric-explorer"></a>Použití Průzkumníka prostředků infrastruktury služby
Obnovení můžete spustit z aplikace Service Fabric Explorer. Zkontrolujte, zda byl v nastavení aplikace Service Fabric Explorer povolen rozšířený režim.
1. Vyberte požadované oddíly a klikněte na akce. 
2. Vyberte Obnovení spouštěcího oddílu a vyplňte informace pro Azure:

    ![Obnovení spouštěcího oddílu][2]

    nebo FileShare:

    ![Sdílení souborů pro obnovení spouštěcího oddílu][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Obnovení dat pro_ztrátu poškození_ _dat_/

Pro _ztrátu dat_ nebo _poškození dat_, zálohované oddíly pro spolehlivé stavové služby a spolehlivé actors oddíly lze obnovit do libovolné ho zvolené zálohy.

Následující příklad je pokračování [povolení pravidelné zálohování pro spolehlivé stavové služby a spolehlivé objekty actor](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). V tomto příkladu je pro oddíl povolena zásada zálohování a služba provádí zálohy na požadované frekvenci ve službě Azure Storage.

Vyberte zálohu z výstupu [getbackupapi](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). V tomto scénáři je záloha generována ze stejného clusteru jako dříve.

Chcete-li spustit obnovení, zvolte zálohu ze seznamu. Pro aktuální_poškození dat_ _ztráty_/dat vyberte následující zálohu:

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

Pro rozhraní API pro obnovení zadejte _podrobnosti BackupId_ a _BackupLocation._ Cluster má povolenou zálohu, takže _služba BRS (Service_ Fabric Backup Restore Service Fabric) identifikuje správné umístění úložiště z přidružené zásady zálohování.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Prostředí Powershell pomocí modulu Microsoft.ServiceFabric.Powershell.Http

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>Volání na odpočinek pomocí prostředí Powershell

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Průběh obnovení můžete sledovat pomocí nástroje TrackRestoreProgress.

## <a name="track-restore-progress"></a>Sledování průběhu obnovení

Oddíl spolehlivé stavové služby nebo spolehlivý objekt actor přijímá pouze jeden požadavek na obnovení najednou. Oddíl přijímá pouze jiný požadavek po dokončení aktuálního požadavku na obnovení. Více požadavků na obnovení lze aktivovat na různých oddílech současně.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Prostředí Powershell pomocí modulu Microsoft.ServiceFabric.Powershell.Http

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>Volání na odpočinek pomocí prostředí Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

Požadavek na obnovení postupuje v následujícím pořadí:

1. **Přijato**: _Přijatý_ stav obnovení označuje, že požadovaný oddíl byl spuštěn se správnými parametry požadavku.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress**: _InProgress_ stav obnovení označuje, že obnovení dochází v oddílu se zálohou uvedenou v požadavku. Oddíl hlásí stav _ztráty dat._
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Úspěch**, **Neúspěch**nebo **Časový plán**: Požadované obnovení lze dokončit v některém z následujících stavů. Každý stav má následující podrobnosti o významu a odpovědi:
    - **Úspěch**: Stav obnovení _úspěchu_ označuje obnovený stav oddílu. Oddíl sestavy _RestoredEpoch_ a _RestoredLSN_ státy spolu s časem v UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Selhání**: Stav obnovení _selhání_ označuje selhání požadavku na obnovení. Příčina selhání je hlášena.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Časový čas**: Stav obnovení _časového času_ označuje, že požadavek má časový čas. Vytvořte nový požadavek na obnovení s větší [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). Výchozí časový limit je 10 minut. Ujistěte se, že oddíl není ve stavu ztráty dat před žádostí o obnovení znovu.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatické obnovení

Můžete nakonfigurovat spolehlivé stavové služby a spolehlivé actors oddíly v clusteru Service Fabric pro _automatické obnovení_. V zásadách `AutoRestore` zálohování nastavených na _hodnotu true_. Povolení _automatického obnovení_ automaticky obnoví data z nejnovější zálohy oddílu, když je hlášena ztráta dat. Další informace naleznete v tématu:

- [Automatické obnovení povolení v zásadách zálohování](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Odkaz na rozhraní API pro obnovení oddílu](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [Odkaz rozhraní API GetPartitionRestoreProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Další kroky
- [Principy konfigurace pravidelného zálohování](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Odkaz na rozhraní REST API pro obnovení zálohy](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png