---
title: Zálohování na vyžádání ve službě Azure Fabric
description: Pomocí funkce zálohování a obnovení v Service Fabric zálohovat data aplikace na základě potřeby.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d5eada62bec49fe771373671e9438d2786d6b165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458414"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Zálohování na vyžádání ve službě Azure Fabric

Můžete zálohovat data spolehlivé stavové služby a spolehlivé objekty actor k řešení scénáře ztráty havárií nebo ztráty dat.

Azure Service Fabric obsahuje funkce pro [pravidelné zálohování dat](service-fabric-backuprestoreservice-quickstart-azurecluster.md) a zálohování dat na základě potřeby. Zálohování na vyžádání je užitečné, protože chrání před_poškozením dat_ _ztrátou_/dat z důvodu plánovaných změn v podkladové službě nebo jejím prostředí.

Funkce zálohování na vyžádání jsou užitečné pro zachycení stavu služeb před ručníaktivací operace služby nebo prostředí služby. Pokud například provedete změnu binárních souborů služby při upgradu nebo downgradu služby. V takovém případě může zálohování na vyžádání pomoci chránit data před poškozením pomocí chyb kódu aplikace.
## <a name="prerequisites"></a>Požadavky

- Nainstalujte modul Microsoft.ServiceFabric.Powershell.Http [In Preview] pro volání konfigurace.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Před provedením jakéhokoli požadavku `Connect-SFCluster` na konfiguraci pomocí modulu Microsoft.ServiceFabric.Powershell.Http se ujistěte, že je cluster připojen pomocí příkazu.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Aktivace zálohování na vyžádání

Zálohování na vyžádání vyžaduje podrobnosti úložiště pro nahrávání záložních souborů. Umístění zálohování na vyžádání zadáte buď v zásadách pravidelného zálohování, nebo v požadavku na zálohování na vyžádání.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Zálohování na vyžádání do úložiště určeného zásadami pravidelného zálohování

Můžete nakonfigurovat zásady pravidelného zálohování pro použití oddílu spolehlivé stavové služby nebo spolehlivého objektu actor pro další zálohování na vyžádání do úložiště.

Následující případ je pokračování scénáře v [povolení pravidelné zálohování pro spolehlivé stavové služby a spolehlivé objekty actor](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). V takovém případě povolíte zásady zálohování pro použití oddílu a zálohování probíhá na nastavené frekvenci v Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Prostředí Powershell pomocí modulu Microsoft.ServiceFabric.Powershell.Http

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Volání na odpočinek pomocí prostředí Powershell

Pomocí rozhraní [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API nastavte aktivaci pro zálohování na `974bd92a-b395-4631-8a7f-53bd4ae9cf22`vyžádání pro ID oddílu .

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Pomocí rozhraní [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API povolte sledování [průběhu zálohování na vyžádání](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Zálohování na vyžádání do zadaného úložiště

Můžete požádat o zálohování na vyžádání pro oddíl spolehlivé stavové služby nebo spolehlivý objekt actor. Zadejte informace o úložišti jako součást požadavku na zálohování na vyžádání.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Prostředí Powershell pomocí modulu Microsoft.ServiceFabric.Powershell.Http

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Volání na odpočinek pomocí prostředí Powershell

Pomocí rozhraní [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API nastavte aktivaci pro zálohování na `974bd92a-b395-4631-8a7f-53bd4ae9cf22`vyžádání pro ID oddílu . Uveďte následující informace o úložišti Azure:

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

Rozhraní [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API můžete použít k nastavení sledování [průběhu zálohování na vyžádání](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="using-service-fabric-explorer"></a>Použití Průzkumníka prostředků infrastruktury služby
Zkontrolujte, zda byl v nastavení aplikace Service Fabric Explorer povolen rozšířený režim.
1. Vyberte požadované oddíly a klikněte na akce. 
2. Vyberte Aktivovat zálohování oddílů a vyplňte informace pro Azure:

    ![Zálohování spouštěcího oddílu][0]

    nebo FileShare:

    ![Spouštěcí oddíl Záložní fileshare][1]

## <a name="tracking-on-demand-backup-progress"></a>Sledování průběhu zálohování na vyžádání

Oddíl spolehlivé stavové služby nebo spolehlivý objekt actor přijímá pouze jeden požadavek na zálohování na vyžádání najednou. Další požadavek lze přijmout až po dokončení aktuálního požadavku na zálohování na vyžádání.

Různé oddíly můžete aktivovat požadavky na zálohování na vyžádání ve stejnou dobu.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Prostředí Powershell pomocí modulu Microsoft.ServiceFabric.Powershell.Http

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Volání na odpočinek pomocí prostředí Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Požadavky na zálohování na vyžádání mohou být v následujících stavech:

- **Přijato**: Zálohování bylo spuštěno na oddílu a probíhá.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Úspěch**, **Neúspěch**nebo **Časový výta :** Požadovanou zálohu na vyžádání lze dokončit v některém z následujících stavů:
  - **Úspěch**: Stav zálohování _úspěch_ udává, že stav oddílu byl úspěšně zálohován. Odpověď poskytuje _BackupEpoch_ a _BackupLSN_ pro oddíl spolu s časem v UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Selhání**: Stav zálohování _selhání_ označuje, že došlo k selhání během zálohování stavu oddílu. Příčina poruchy je uvedena v reakci.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Časový čas**: Stav zálohování _časového času_ označuje, že zálohu stavu oddílu nelze vytvořit v daném čase. Výchozí hodnota časového limitu je 10 minut. Iniciujte nový požadavek na zálohování na vyžádání s větší [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) v tomto scénáři.
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

- [Principy konfigurace pravidelného zálohování](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Reference rozhraní REST BackupRestore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png