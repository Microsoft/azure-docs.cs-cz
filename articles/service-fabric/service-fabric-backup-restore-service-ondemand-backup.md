---
title: Zálohování na vyžádání v Azure Service Fabric | Dokumentace Microsoftu
description: Použít Service Fabric zálohování a obnovení funkce pro zálohování dat aplikací na základě potřeba.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 35d97f1da6b5d1c75073264c70e1cd1607d5fe0d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730124"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Zálohování na vyžádání v Azure Service Fabric

K řešení po havárii nebo data ztráty scénářů lze zálohovat data Reliable Stateful services a Reliable Actors.

Service Fabric je vybaven funkcí pro [pravidelné zálohování dat](service-fabric-backuprestoreservice-quickstart-azurecluster.md) a zálohování dat na základě potřeba. Zálohování na vyžádání je užitečné, protože ho budete chráněni před _ztrátě dat_/_poškození dat_ situace způsobena vlivem plánovaných změn v základní služby nebo jeho prostředí.

Funkce zálohování na vyžádání je užitečné při zaznamenávání stavu služby před jakoukoli ručně aktivované operaci související se service nebo prostředí služby. Stejně jako, který je změna binární soubory služby, upgrade nebo při downgradu služby; jak bude mít data strážený proti poškození dat chyby v kódu aplikace.

## <a name="triggering-on-demand-backup"></a>Spouští se záloha na vyžádání

Zálohování na vyžádání vyžaduje, aby podrobnosti o úložišti pro nahrání záložních souborů. Zálohování na vyžádání se bude provádět ve službě storage zadán v pravidelných zásady zálohování nebo v zadané úložiště v požadavek na zálohování na vyžádání.

### <a name="on-demand-backup-to-storage-specified-by-periodic-backup-policy"></a>Zálohování na vyžádání do úložiště určené zásady pravidelné zálohování

Oddíl spolehlivé stavové služby nebo objekty Reliable Actor mohou být požadována pro speciální potřebu základ zálohování do úložiště zadaný v pravidelných zásady zálohování. 

Následující případ je pokračování ukázka, jak je uvedeno v [povolení pravidelné zálohování služby Reliable Stateful a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), kde oddíl má povolené zásady zálohování a trvá na požadovanou četnost zálohování ve službě Azure Storage.

Zálohování na vyžádání pro ID oddílu `974bd92a-b395-4631-8a7f-53bd4ae9cf22` může aktivovat [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) rozhraní API. 

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[Průběh zálohování na vyžádání](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress) lze sledovat pomocí [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) rozhraní API.

### <a name="on-demand-backup-to-specified-storage"></a>Zálohování na vyžádání do zadaného úložiště

Zálohování na vyžádání mohou být požadována pro oddíl spolehlivé stavové služby nebo Reliable Actors společně s informacemi úložiště. Informace o úložiště by měl být ve formě součástí požadavek na zálohování na vyžádání.

Zálohování na vyžádání pro ID oddílu `974bd92a-b395-4631-8a7f-53bd4ae9cf22` může aktivovat [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API ve službě Azure storage informace, jak je znázorněno níže.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[Průběh zálohování na vyžádání](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress) lze sledovat pomocí [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) rozhraní API.


## <a name="tracking-on-demand-backup-progress"></a>Sledování průběhu zálohování na vyžádání

Oddíl spolehlivé stavové služby nebo Reliable Actors přijímá pouze jeden požadavek na zálohování na vyžádání v čase. Jiná žádost může přijmout pouze po dokončení aktuální žádost o zálohu na vyžádání. 

Více požadavků pro zálohování na vyžádání se dá spouštět na různých oddílů ve stejnou dobu.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Probíhá zálohování požadavek na vyžádání může být jedna z následujících stavů

* **Přijata** – zálohy se inicializoval na oddíl a probíhá.
    ```
    BackupState             : Accepted
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          : 
    EpochOfLastBackupRecord : 
    LsnOfLastBackupRecord   : 0
    FailureError            : 
    ```
    
* **Úspěch nebo selhání nebo vypršení časového limitu** -požadovaného zálohování na vyžádání můžete dokončit v některém z následujících stavů. Každý stav má následující údaje význam a odpovědi.

    * **Úspěch** – zálohování stavu jako _úspěch_ označuje, že se úspěšně zálohuje stav oddílu. Odpověď bude poskytovat __BackupEpoch__ a __BackupLSN__ pro oddíl spolu s čas ve standardu UTC.
        ```
        BackupState             : Success
        TimeStampUtc            : 2018-11-21T20:00:01Z
        BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
        BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
        EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
        LsnOfLastBackupRecord   : 36
        FailureError            : 
        ```
        
    * **Selhání** – zálohování stavu jako _selhání_ označuje během zálohování oddílu stavu došlo k chybě. Příčina selhání se bude zobrazovat v odpovědi.
        ```
        BackupState             : Failure
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
        ```
       
    * **Časový limit** – zálohování stavu jako _vypršení časového limitu_ označuje, že zálohování stavu oddílu nelze vytvořit v zadaný časový rámec; výchozí hodnota časového limitu je 10 minut. Zahajuje novou žádost o zálohování s větší [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) v požadavek na zálohování na vyžádání se doporučuje v tomto scénáři.

        ```
        BackupState             : Timeout
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
        ```

## <a name="next-steps"></a>Další postup
- [Principy pravidelné zálohování konfigurace](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Reference k rozhraní API REST obnovení zálohy](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

