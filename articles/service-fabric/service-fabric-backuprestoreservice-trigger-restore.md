---
title: Obnovení zálohy v Azure Service Fabric | Dokumentace Microsoftu
description: Použít Service Fabric pravidelné zálohování a obnovení funkce pro obnovení dat ze zálohy dat ve vašich aplikacích.
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
ms.openlocfilehash: d0b385e5856e85ad0d7d9baeaddbac0fb4230e3b
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730125"
---
#  <a name="restoring-backup-in-azure-service-fabric"></a>Obnovení zálohy v Azure Service Fabric


Spolehlivé stavové služby Reliable Actors v Service Fabric můžete udržují měnitelný a autoritativní stav i mimo požadavek a odpověď nebo celou transakci. Pokud do stavové služby ocitne mimo provoz delší dobu nebo nedochází ke ztrátě informací z důvodu selhání, bude pravděpodobně nutné obnovit do nejnovější přijatelné zálohování stavu, aby bylo možné pokračovat v poskytování služeb, jakmile se vrátí zpět.

Služba může být například vhodné zálohovat svá data z důvodu ochrany z následujících scénářů:

- V případě trvalé ztrátě celý cluster Service Fabric. **(Případ zotavení po havárii – zotavení po Havárii)**
- Trvalé ztrátě většiny repliky oddílu služby. **(V případě ztráty dat)**
- Chyb správy kterým stav omylem získá odstraněn nebo poškozen. Například správce s dostatečná oprávnění omylem odstraní službu. **(V případě ztráty dat)**
- Chyby ve službě, které dojít k poškození dat. Poškození dat může například dojít v případě upgrade kódu služby zahájí zápis vadným dat k Reliable Collection. V takovém případě se kód a data možná muset vrátit do předchozího stavu. **(V případě poškození dat)**


## <a name="prerequisites"></a>Požadavky
* Pokud chcete aktivovat, obnovení _služba FAS (Fault Analysis)_ by měla být povolená pro cluster
* Zálohování bude obnovení by měl mít zabrané _zálohy obnovit služby (BRS)_
* Obnovení je třeba spustit pouze v oddílu.

## <a name="triggering-restore"></a>Aktivace operace obnovení

Obnovení může být pro všechny z následujících scénářů 
* Data obnovení v případě _zotavení po havárii_ (DR)
* Data obnovení v případě _poškození dat nebo ztrátě dat._



### <a name="data-restore-in-the-event-of-disaster-recovery-dr"></a>Data obnovení v případě _zotavení po havárii_ (DR)
V případě celý cluster Service Fabric ke ztrátě je možné data pro oddíly spolehlivé stavové služby a Reliable Actors obnovit do alternativního clusteru. Požadovaný zálohování můžete vybrat z výčet [GetBackupAPI údaji ze záložního úložiště](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). Výčet zálohování může být pro aplikace, služby nebo oddíl.

Předpokládejme, že ke ztrátě clusteru se cluster podle [povolení pravidelné zálohování služby Reliable Stateful a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), který měl `SampleApp` nasazení, kde oddílu byla s povolenou zásadu zálohování a zálohování se děje ve službě Azure Storage. 


Spusťte následující skript Powershellu pro volání rozhraní REST API k vytvoření výčtu zálohy vytvořené pro všechny oddíly uvnitř `SampleApp` v ztráty clusteru Service Fabric. Výčet rozhraní API vyžaduje informace o úložiště, ve kterém jsou uložené zálohy aplikace, pro vytvoření výčtu dostupné zálohy. 

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



Pro spuštění obnovení, musíme zvolte požadovanou zálohování. Umožní požadované zálohy pro aktuální zotavení po havárii (DR) se následující zálohování

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

Pro rozhraní API pro obnovení, potřebujeme pro poskytování __BackupId__ a __BackupLocation__ podrobnosti. Oddíl v alternativní clusteru je potřeba zvolené jak je uvedeno [schéma oddílu](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Je ale zodpovědnost za uživatele zvolte cílový oddíl k obnovení zálohy z alternativních clusteru podle schématu oddílu v původním clusteru ztraceny.

Předpokládejme, že je ID oddílu na alternativní clusteru `1c42c47f-439e-4e09-98b9-88b8f60800c6`, která se mapuje na původní ID oddílu clusteru `974bd92a-b395-4631-8a7f-53bd4ae9cf22` porovnáním vysoká hodnota klíče a nízká hodnota klíče pro _Rozsahové dělení (UniformInt64Partition)_.

Pro _s názvem dělení_, hodnota názvu se porovnává se identifikovat cílový oddíl v alternativní clusteru.

Obnovení je požadován pro oddíl zálohování clusteru následující [obnovení rozhraní API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)

```powershell 
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 
Probíhá obnovení může být [TrackRestoreProgress](service-fabric-backuprestoreservice-trigger-restore.md#tracking-restore-progress)

### <a name="data-restore-in-the-event-of-data-corruption--data-loss"></a>Data obnovení v případě _poškození dat nebo ztrátě dat._

Pro případ _ztrátě dat_ nebo _poškození dat_ lze obnovit data pro oddíly spolehlivé stavové služby a Reliable Actors k některému z vybrané zálohy. Následující případ je pokračování ukázka, jak je uvedeno v [povolení pravidelné zálohování služby Reliable Stateful a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), kde oddíl má povolené zásady zálohování a trvá na požadovanou četnost zálohování ve službě Azure Storage. 

Požadovaný zálohování je vybrána z výstupu [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). V tomto scénáři zálohování vygenerovat ze stejného clusteru v minulosti.
Pro spuštění obnovení, musíme požadovaného zálohování zvolte ze seznamu. Nechat naše požadované zálohy pro aktuální _ztrátě dat_ / _poškození dat_ být následující zálohování

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

Pro rozhraní API pro obnovení, potřebujeme pro poskytování __BackupId__ a __BackupLocation__ podrobnosti. Od clusteru nemá zálohování povolené Service Fabric _zálohy obnovit služby (BRS)_ polohu správné úložiště z přidružené zásady zálohování.

```powershell
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4', 
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Probíhá obnovení může být [TrackRestoreProgress](service-fabric-backuprestoreservice-trigger-restore.md#tracking-restore-progress)


## <a name="tracking-restore-progress"></a>Sledování průběhu obnovení

Oddíl spolehlivé stavové služby nebo Reliable Actors přijímá pouze jeden požadavek na obnovení v čase. Jiná žádost může přijmout pouze po dokončení aktuální žádost o obnovení. Obnovení vícenásobných se dá spouštět na různých oddílů ve stejnou dobu.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
 
$restoreResponse = (ConvertFrom-Json $response.Content) 
$restoreResponse | Format-List
```

průběh žádosti o obnovení v uvedeném pořadí

1. __Přijaté__ -obnovit stav jako _přijato_ označuje, že požadovaná byla spuštěna s parametry správnou žádost.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
2. __Probíhá zpracování__ -obnovit stav jako _InProgress_ označuje, že oddíl projdou zálohou uvedený v žádosti o obnovení. Oddíl oznámí _dataloss_ stavu.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. __Úspěch__/ __selhání__/ __vypršení časového limitu__ -požadovaný obnovení může být provedena v některém z následujících stavů. Každý stav má následující údaje význam a odpovědi.
       
    * __Úspěch__ -obnovit stav jako _úspěch_ označuje stav oddílu je obnoveno. Odpověď bude poskytovat RestoreEpoch a RestordLSN pro oddíl spolu s čas ve standardu UTC. 
    
        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```
        
    *. __Selhání__ -obnovit stav jako _selhání_ indikuje selhání žádost o obnovení. Příčina selhání se bude zobrazovat v požadavku.
        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    *. __Časový limit__ -obnovit stav jako _vypršení časového limitu_ znamená žádost má časový limit. Nová žádost o obnovení s větší [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) se doporučuje; výchozí hodnota časového limitu je 10 minut. Doporučujeme, abyste měli jistotu, že oddíl je mimo stav ztrátu dat, než požádáte o obnovení znovu.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="auto-restore"></a>Automatické obnovení

Oddíly pro spolehlivé stavové služby a Reliable Actors v clusteru Service Fabric je možné nakonfigurovat pro _automatické obnovení_. Při vytváření zásady zálohování, může mít zásadu `AutoRestore` nastavena na _true_.  Povolení _automatické obnovení_ pro oddíl, obnoví data z poslední zálohy a pokud ztrátě dat se použije v hlášení.
 
 [Povolení automatického obnovení v zásady zálohování](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)


[Reference k rozhraní API RestorePartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
[GetPartitionRestoreProgress API – referenční informace](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Další postup
- [Principy pravidelné zálohování konfigurace](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Reference k rozhraní API REST obnovení zálohy](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
