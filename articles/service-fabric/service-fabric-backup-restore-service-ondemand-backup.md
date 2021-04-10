---
title: Zálohování na vyžádání v Azure Service Fabric
description: Pomocí funkce zálohování a obnovení v Service Fabric můžete zálohovat data aplikací v případě potřeby.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d7986c8cd8d0714215c7b4dc57170be346e627ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928033"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Zálohování na vyžádání v Azure Service Fabric

Můžete zálohovat data spolehlivých stavových služeb a Reliable Actors řešit scénáře týkající se havárie nebo ztráty dat.

Azure Service Fabric obsahuje funkce pro [pravidelné zálohování dat](service-fabric-backuprestoreservice-quickstart-azurecluster.md) a zálohování dat podle potřeby. Zálohování na vyžádání je užitečné, protože chrání před  / _poškozením dat_ ztráty dat z důvodu plánovaných změn v základní službě nebo jejím prostředí.

Funkce zálohování na vyžádání jsou užitečné pro zaznamenání stavu služeb před ruční aktivací operace služby nebo služby Service Environment. Například pokud provedete změnu v binárních souborech služby při upgradu nebo downgrade služby. V takovém případě může zálohování na vyžádání pomáhat chránit data před poškozením chyb kódu aplikace.
## <a name="prerequisites"></a>Požadavky

- Nainstalujte modul Microsoft. ServiceFabric. PowerShell. http (Preview) pro provedení konfiguračních volání.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> Pokud je verze PowerShellGet menší než 1.6.0, bude nutné aktualizovat, aby se přidala podpora pro příznak *-AllowPrerelease* :
>
> `Install-Module -Name PowerShellGet -Force`

- `Connect-SFCluster`Před provedením libovolné žádosti o konfiguraci pomocí modulu Microsoft. ServiceFabric. PowerShell. http zajistěte, aby byl cluster připojen pomocí příkazu.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Aktivuje se zálohování na vyžádání.

Zálohování na vyžádání vyžaduje podrobnosti úložiště pro nahrávání záložních souborů. Umístění zálohy na vyžádání zadáte buď v zásadách pravidelného zálohování, nebo v žádosti o zálohování na vyžádání.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Zálohování na vyžádání do úložiště určeného pravidelnými zásadami zálohování

Zásady pravidelného zálohování můžete nakonfigurovat tak, aby používaly oddíl spolehlivé stavové služby nebo spolehlivého objektu actor pro další zálohování na vyžádání do úložiště.

V tomto případě se jedná o pokračování scénáře při [povolování pravidelného zálohování pro spolehlivou stavovou službu a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). V takovém případě zapnete zásadu zálohování, aby používala oddíl, a záloha se zavede na nastavené frekvenci v Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell s použitím modulu Microsoft. ServiceFabric. PowerShell. http

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Volání REST pomocí PowerShellu

K nastavení aktivace pro zálohování na vyžádání pro ID oddílu použijte rozhraní [BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) API `974bd92a-b395-4631-8a7f-53bd4ae9cf22` .

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Pomocí rozhraní [GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API Povolte sledování [průběhu zálohování na vyžádání](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Zálohování na vyžádání do určeného úložiště

Můžete požádat o zálohování na vyžádání pro oddíl spolehlivé stavové služby nebo spolehlivého objektu actor. Poskytněte informace o úložišti jako součást žádosti o zálohování na vyžádání.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell s použitím modulu Microsoft. ServiceFabric. PowerShell. http

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Volání REST pomocí PowerShellu

K nastavení aktivace pro zálohování na vyžádání pro ID oddílu použijte rozhraní [BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) API `974bd92a-b395-4631-8a7f-53bd4ae9cf22` . Zahrnout následující informace o Azure Storage:

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

K nastavení sledování [průběhu zálohování na vyžádání](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)můžete použít rozhraní [GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API.

### <a name="using-service-fabric-explorer"></a>Použití Service Fabric Explorer
Ujistěte se, že je v nastavení Service Fabric Explorer povolený rozšířený režim.
1. Vyberte požadované oddíly a klikněte na akce. 
2. Vyberte možnost zálohování oddílu aktivačního oddílu a vyplňte informace pro Azure:

    ![Spustit zálohu oddílu][0]

    nebo sdílená složka:

    ![Aktivovat sdílenou složku zálohovaného oddílu][1]

## <a name="tracking-on-demand-backup-progress"></a>Sledování průběhu zálohování na vyžádání

Oddíl spolehlivé stavové služby nebo spolehlivého objektu actor přijímá v jednom okamžiku jenom jednu žádost o zálohování na vyžádání. Další žádost lze přijmout až po dokončení aktuální žádosti o zálohování na vyžádání.

Různé oddíly mohou najednou spustit požadavky na zálohování na vyžádání.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell s použitím modulu Microsoft. ServiceFabric. PowerShell. http

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Volání REST pomocí PowerShellu

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Požadavky na zálohování na vyžádání můžou být v následujících stavech:

- **Přijato**: záloha začala v oddílu a probíhá.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Úspěch**, **selhání** nebo **časový limit**: Požadovaná záloha na vyžádání může být dokončena v některém z následujících stavů:
  - **Úspěch**: stav zálohování _po úspěchu_ indikuje, že se stav oddílu úspěšně zálohoval. Odpověď poskytuje _BackupEpoch_ a _BackupLSN_ pro oddíl spolu s časem ve standardu UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Selhání**: stav zálohování _selhání_ indikuje, že došlo k selhání během zálohování stavu oddílu. Příčina selhání je uvedena v odpovědi.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Timeout**: _časový limit_ stavu zálohování indikuje, že se v daném časovém intervalu nepovedlo vytvořit zálohu stavu oddílu. Výchozí hodnota časového limitu je 10 minut. V tomto scénáři Iniciujte novou žádost o zálohování na vyžádání s větším [BackupTimeoutm](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) .
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Další kroky

- [Vysvětlení Konfigurace pravidelného zálohování](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Odkaz na REST API BackupRestore](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png
