---
title: Zálohování na vyžádání v Azure Service Fabric | Dokumentace Microsoftu
description: Použít funkci zálohování a obnovení funkce v Service Fabric k zálohování vašich aplikačních dat podle potřeby.
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
ms.openlocfilehash: 8a276f26367e66f55b8fc10dbcba2429dc2e5450
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062687"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Zálohování na vyžádání v Azure Service Fabric

Můžete zálohovat data Reliable Stateful services a Reliable Actors k řešení po havárii nebo data ztráty scénářů.

Azure Service Fabric obsahuje funkce [pravidelné zálohování dat](service-fabric-backuprestoreservice-quickstart-azurecluster.md) a zálohování dat na základě potřeba. Zálohování na vyžádání je užitečné, protože ho budete chráněni před _ztrátě dat_/_poškození dat_ kvůli plánované změny v základní služby nebo jeho prostředí.

Funkce zálohování na vyžádání jsou užitečné pro zaznamenání stavu služby předtím, než ručně aktivujete službu nebo operace prostředí služby. Například pokud provedete změny v binární soubory služby při upgradu nebo downgradu služby. V takovém případě může pomoct zálohování na vyžádání, ochranu proti poškození dat podle kódu chyby aplikace.

## <a name="triggering-on-demand-backup"></a>Spouští se záloha na vyžádání

Zálohování na vyžádání vyžaduje, aby podrobnosti o úložišti pro nahrání záložních souborů. Umístění zálohy na vyžádání, zadáte v zásadách pravidelné zálohování nebo v požadavku zálohování na vyžádání.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Zálohování na vyžádání do úložiště určené pravidelné zásady zálohování

Můžete nakonfigurovat pravidelně zásady zálohování pro použití oddílu spolehlivé stavové služby nebo objekty Reliable Actor pro velmi zálohování na vyžádání do úložiště.

Následující případ je pokračování scénář v [povolení pravidelné zálohování služby Reliable Stateful a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). V takovém případě povolit zásady zálohování a použít oddíl a dojde k zálohování frekvencí sady ve službě Azure Storage.

Použití [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) rozhraní API k nastavení aktivace pro zálohování na vyžádání pro ID oddílu `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Použití [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) rozhraní API, sledování [průběh zálohování na vyžádání](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Zálohování na vyžádání do zadaného úložiště

Můžete požádat o zálohování na vyžádání pro oddíl spolehlivé stavové služby nebo objekty Reliable Actor. Zadejte informace o úložiště jako součást požadavek na zálohování na vyžádání.

Použití [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) rozhraní API k nastavení aktivace pro zálohování na vyžádání pro ID oddílu `974bd92a-b395-4631-8a7f-53bd4ae9cf22`. Uveďte následující informace služby Azure Storage:

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

Můžete použít [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API nastavit sledování [průběh zálohování na vyžádání](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

## <a name="tracking-on-demand-backup-progress"></a>Sledování průběhu zálohování na vyžádání

Oddíl spolehlivé stavové služby nebo Reliable Actors přijímá pouze jeden požadavek na zálohování na vyžádání v čase. Jiná žádost může přijmout, až po dokončení aktuální žádost o zálohu na vyžádání.

Různé oddíly můžete aktivovat zálohování požadavků na vyžádání ve stejnou dobu.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Požadavky pro zálohování na vyžádání, může být v následujících stavů:

- **Přijata**: Zálohování bylo zahájeno v oddílu a probíhá.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Úspěch**, **selhání**, nebo **vypršení časového limitu**: Požadovaný zálohování na vyžádání můžete dokončit v některém z následujících stavů:
  - **Úspěch**: A _úspěch_ zálohování stav znamená, že se úspěšně zálohovány stav oddílu. Poskytuje odpověď _BackupEpoch_ a _BackupLSN_ pro oddíl spolu s čas ve standardu UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Selhání**: A _selhání_ zálohování stav znamená, že došlo k chybě během zálohování stav oddílu. Příčina selhání je uvedeno v odpovědi.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Časový limit**: A _vypršení časového limitu_ zálohování stav znamená, že zálohování stavu oddílu nelze vytvořit v daném čase. Výchozí hodnota časového limitu je 10 minut. Zahajte novou žádost zálohování na vyžádání s větší [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) v tomto scénáři.
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

- [Porozumět konfiguraci pravidelné zálohování](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Reference k rozhraní API REST BackupRestore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
