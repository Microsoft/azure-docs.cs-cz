---
title: Obnovení zálohy v Azure Service Fabric | Dokumentace Microsoftu
description: Použít pravidelné zálohování a obnovení funkce v Service Fabric pro obnovení dat ze zálohy dat ve vašich aplikacích.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 1482497f3767e7533d1d56e6eb63e55cdb5c9ebb
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104639"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Obnovení zálohy v Azure Service Fabric

V Azure Service Fabric Reliable Stateful services a Reliable Actors udržují měnitelný a autoritativní stav po dokončení transakce žádostí a odpovědí. Stavové služby může ujmout po dlouhou dobu nebo z důvodu selhání dojít ke ztrátě informací. Pokud k tomu dojde, služba je potřeba obnovit z nejnovější přijatelné zálohování tak, aby ho můžete pokračovat v práci.

Například můžete nakonfigurovat službu zálohovat svá data chránit před následující scénáře:

- **Případ zotavení po havárii**: Trvalé ztrátě celý cluster Service Fabric.
- **Případ ztráty dat**: Trvalé ztrátě většiny repliky oddílu služby.
- **Případ ztráty dat**: Náhodnému odstranění nebo poškození služby. Například správce omylem odstraní službu.
- **Případ poškození dat**: Chyby ve službě dojít k poškození dat. Poškození dat může například dojít v případě upgrade kódu služby zapisuje vadným data do spolehlivé kolekce. V takovém případě budete muset obnovit kód a data do předchozího stavu.

## <a name="prerequisites"></a>Požadavky

- K aktivaci obnovení _služba FAS (Fault Analysis)_ musí být povolené pro cluster.
- _Zálohy obnovit služby (BRS)_ vytvoření zálohy.
- Obnovení je třeba spustit pouze v oddílu.

## <a name="triggered-restore"></a>Aktivované obnovení

Obnovení se dá spouštět pro některý z následujících scénářů:

- Data obnovení pro _zotavení po havárii_.
- Data obnovení pro _dat poškození nebo ztrátě_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Obnovení dat v případě zotavení po havárii

Pokud dojde ke ztrátě celý cluster Service Fabric, můžete obnovit data pro oddíly spolehlivé stavové služby a Reliable Actors. Požadovaný zálohování lze vybrat ze seznamu, při použití [GetBackupAPI údaji ze záložního úložiště](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). Zálohování výčet může být pro aplikace, služby nebo oddíl.

Následující příklad předpokládá, že ke ztrátě clusteru je stejný cluster, který je uvedený v [povolení pravidelné zálohování služby Reliable Stateful a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). V takovém případě `SampleApp` se nasazuje se povolí, zásady zálohování a zálohování jsou nakonfigurovány pro Azure Storage.

Spusťte skript prostředí PowerShell pomocí rozhraní REST API vrátí seznam zálohy vytvořené pro všechny oddíly uvnitř `SampleApp` aplikace. Rozhraní API vyžaduje informace o zálohování úložiště do seznamu dostupných záloh.

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

Ukázkový výstup pro výše uvedené spusťte:

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

Pro aktivaci obnovení, zvolte jednu ze zálohy. Aktuální záloha pro zotavení po havárii může být například následující zálohování:

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

Pro rozhraní API pro obnovení, je potřeba zadat _BackupId_ a _BackupLocation_ podrobnosti.

Musíte také vybrat cílový oddíl v alternativní clusteru podle popisu v [schéma oddílu](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Obnovení zálohy alternativní clusteru oddíl určený ve schématu oddílu z původního clusteru ztraceny.

Pokud je ID oddílu na alternativní clusteru `1c42c47f-439e-4e09-98b9-88b8f60800c6`, můžete ho namapovat na původní ID oddílu clusteru `974bd92a-b395-4631-8a7f-53bd4ae9cf22` porovnáním vysoká hodnota klíče a nízká hodnota klíče pro _Rozsahové dělení (UniformInt64Partition)_.

Pro _s názvem dělení_, hodnota názvu se porovnává se identifikovat cílový oddíl v alternativní clusteru.

Žádost o obnovení před oddílu zálohování clusteru pomocí následujících [obnovení API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition):

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Můžete sledovat průběh obnovení s [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress).

### <a name="data-restore-for-data-corruptiondata-loss"></a>Data obnovení pro _poškození dat_/_ztráty dat_

Pro _ztrátě dat_ nebo _poškození dat_, zálohovanou oddíly pro spolehlivé stavové služby a Reliable Actors oddíly lze obnovit k jakémukoli z vybrané zálohy.

V následujícím příkladu je pokračováním [povolení pravidelné zálohování služby Reliable Stateful a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). V tomto příkladu je povolené zásady zálohování pro oddíl a služba je provádění zálohování s požadovanou frekvencí ve službě Azure Storage.

Výběr zálohy z výstupu [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). V tomto scénáři zálohování je vygenerován ze stejného clusteru jako před.

Pro aktivaci obnovení, vyberte ze seznamu zálohy. Pro aktuální _ztrátě dat_/_poškození dat_, vybrat zálohu, která následující:

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

Rozhraní API pro obnovení, zadejte _BackupId_ a _BackupLocation_ podrobnosti. Cluster má povoleným zálohováním proto Service Fabric _zálohy obnovit služby (BRS)_ polohu správné úložiště z přidružené zásady zálohování.

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Obnovení můžete sledovat pomocí [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress).

## <a name="track-restore-progress"></a>Sledovat průběh obnovení

Oddíl spolehlivé stavové služby nebo Reliable Actors přijímá pouze jeden požadavek na obnovení v čase. Po dokončení aktuální žádost o obnovení, oddíl přijímá pouze jiná žádost. Obnovení vícenásobných se dá spouštět na různých oddílů ve stejnou dobu.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

Průběh žádosti o obnovení v následujícím pořadí:

1. **Přijata**: _Přijato_ obnovit stav označuje, že požadovaný oddíl spustila s parametry správnou žádost.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **Probíhá zpracování**: _InProgress_ obnovit stav označuje, že je službou backup uvedený v žádosti o obnovení dochází v oddílu. Oddíl sestavy _dataloss_ stavu.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Úspěch**, **selhání**, nebo **vypršení časového limitu**: V některém z následujících stavů je možné dokončit požadovanou obnovení. Každý stav má následující údaje význam a odpovědi:
    - **Úspěch**: A _úspěch_ obnovit stav určuje stav znovu získali oddílu. Oddíl sestavy _RestoredEpoch_ a _RestoredLSN_ stavy spolu s čas ve standardu UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Selhání**: A _selhání_ obnovit stav indikuje selhání žádost o obnovení. Příčina selhání se použije v hlášení.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Časový limit**: A _vypršení časového limitu_ obnovit stav znamená, že žádost má časový limit. Vytvořit novou žádost o obnovení s větší [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). Výchozí časový limit je 10 minut. Ujistěte se, že oddíl není ve stavu ke ztrátě dat. před požadováním obnovení znovu.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatické obnovení

Můžete nakonfigurovat spolehlivé stavové služby a cluster Service Fabric Reliable Actors oddílů pro _automatické obnovení_. V zásadě zálohování nastavte `AutoRestore` k _true_. Povolení _automatické obnovení_ automaticky obnoví data z poslední zálohy oddílu při ztrátě dat se použije v hlášení. Další informace naleznete v tématu:

- [Povolení automatického obnovení v zásady zálohování](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Reference k rozhraní API RestorePartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [Reference k rozhraní API GetPartitionRestoreProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Další postup
- [Principy pravidelné zálohování konfigurace](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Reference k rozhraní API REST obnovení zálohy](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
