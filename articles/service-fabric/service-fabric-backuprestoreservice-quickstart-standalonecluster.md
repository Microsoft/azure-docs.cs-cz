---
title: Pravidelné zálohování a obnovení v Azure Service Fabric (Preview) | Microsoft Docs
description: Použít Service Fabric pravidelné zálohování a obnovení funkce pro ochranu před ztrátou dat aplikace.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAADBCAB-F0CF-4CBC-B663-4A6DCCB4DEE1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: cfbc0e6ca255bd005bb6e4cc381a9121347fe227
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206041"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric-preview"></a>Pravidelné zálohování a obnovení v Azure Service Fabric (Preview)
> [!div class="op_single_selector"]
> * [Clustery v Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Samostatné clustery](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric je platforma distribuovaných systémů, která usnadňuje vývoj a správa mikroslužeb spolehlivé, distribuovaný, na základě cloudové aplikace. Umožňuje spuštění bezzstavovými i stavovými malých služeb. Stavové služby můžete udržovat měnitelný, autoritativní stavu nad rámec požadavku a odpovědi nebo dokončení transakce. Pokud se stavové služby ocitne mimo provoz delší dobu nebo ztratí informace z důvodu havárie, potřebuje na některé poslední zálohy stavu obnovit, aby bylo možné pokračovat v poskytování služby po uvede do provozu.

Service Fabric replikuje stav napříč několika uzly a zajistit tak vysokou dostupnost služby. I v případě, že jeden uzel v clusteru selže, služba bude stále k dispozici. V některých případech je však stále vhodná pro data služby za spolehlivé proti širší selhání.
 
Služba může například chcete zálohovat jeho data za účelem ochrany z následujících scénářů:
- V případě trvalé ztrátě celého clusteru Service Fabric.
- Trvalé ztrátě většiny repliky oddílu služby
- Správce chyby, které stav omylem získá odstranit nebo poškozený. Například správce s dostatečná oprávnění omylem odstraní službu.
- Chyby v rámci služby, které dojít k poškození dat. Například tomu může dojít při aktualizace služby kód spustí zápis vadný dat na kolekci spolehlivé. V takovém případě budou muset kód a data mohou být vrácen do předchozího stavu.
- Zpracování dat je offline. Může být vhodné používat offline zpracování dat pro business intelligence, který se stane odděleně od služby, který generuje data.

Service Fabric nabízí rozhraní API integrované do bodu v čase [zálohování a obnovení](service-fabric-reliable-services-backup-restore.md). Vývojáři aplikací může pravidelně zálohovat stav služby použijte tato rozhraní API. Kromě toho Pokud správci služeb, aby se spustit zálohování z mimo službu v konkrétním čase, jako je před upgradem aplikace, vývojáři nutné vystavit zálohování (a obnovit) jako rozhraní API ze služby. Dalších poplatků nad touto je udržování zálohy. Můžete například provést 5 přírůstkové zálohování každou půlhodinu následuje úplné zálohování. Po provedení úplného zálohování můžete odstranit předchozí přírůstkové zálohování. Tento postup vyžaduje další kód vedoucí k další náklady během vývoje aplikace.

Zálohování dat aplikace v pravidelných intervalech je základní třeba pro správu distribuované aplikace a zabezpečení proti ztrátě dat nebo provozu v případě delších ztrátě dostupnost služeb. Service Fabric nabízí volitelné zálohování a obnovení služby, která můžete konfigurovat pravidelné zálohování stavová spolehlivé služby (včetně služeb objektu Actor) bez nutnosti psaní jakéhokoli kódu Další. Zajišťuje také obnovení dříve prováděné zálohy. 

> [!NOTE]
> Pravidelné zálohování a obnovení funkce je v současné době v **Preview** a nejsou podporovány pro úlohy v produkčním prostředí. 
>

Service Fabric nabízí sadu rozhraní API pro dosažení následující funkce související s pravidelné zálohování a obnovení funkce:

- Naplánovat pravidelné zálohování spolehlivé Stateful services a Reliable Actors s podporou nahrát umístění úložiště zálohy do (external). Umístění úložiště podporované
    - Azure Storage
    - Sdílené složky (místní)
- Zobrazení výčtu zálohování
- Spustit zálohování ad-hoc v oddílu
- Obnovení oddílu pomocí předchozí zálohy
- Dočasně pozastavit zálohy
- Správa uchovávání záloh (nadcházející)

## <a name="prerequisites"></a>Požadavky
* Cluster Service Fabric s prostředky infrastruktury verze 6.2 a vyšší. Cluster musí být instalační program v systému Windows Server. Odkazovat [článku](service-fabric-cluster-creation-for-windows-server.md) kroky, jak stáhnout požadovaný balíček.
* Certifikát X.509 pro šifrování tajné klíče potřebné pro připojení k úložiště pro ukládání záloh. Odkazovat [článku](service-fabric-windows-cluster-x509-security.md) vědět, jak získat nebo vytvořit certifikát podepsaný svým držitelem X.509.
* Service Fabric spolehlivé Stateful aplikace vyvíjené v Service Fabric SDK verze 3.0 nebo vyšší. Pro aplikace cílený na rozhraní .net základní 2.0, aplikace by měly být vytvořeny pomocí Service Fabric SDK verze 3.1 nebo novější.

## <a name="enabling-backup-and-restore-service"></a>Povolení zálohování a obnovení služby
Nejprve musíte povolit _zálohování a obnovení služby_ v clusteru. Získáte šablonu pro cluster, který chcete nasadit. Můžete použít [ukázkových šablon](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Povolit _zálohování a obnovení služby_ pomocí následujících kroků:

1. Zkontrolujte, zda `apiversion` je nastaven na `10-2017` v konfiguraci clusteru souboru a pokud ne, aktualizovat, je znázorněné v následujícím fragmentu kódu:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Teď povolit _zálohování a obnovení služby_ přidáním následující `addonFeatures` oddílu pod `properties` části, jak je znázorněno v následujícím fragmentu kódu: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Nakonfigurujte certifikát X.509 pro šifrování přihlašovacích údajů. To je důležité zajistit, aby přihlašovací údaje, pokud existuje, se připojit k úložišti se šifrují před uložením. Nakonfigurovat šifrovací certifikát přidáním následující `BackupRestoreService` oddílu pod `fabricSettings` části, jak je znázorněno v následujícím fragmentu kódu: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Jakmile konfiguračního souboru clusteru byly aktualizovány předchozí změny, je použít a umožnit dokončení nasazení nebo upgradovat. Po dokončení _zálohování a obnovení služby_ spuštění v clusteru. Identifikátor Uri této služby je `fabric:/System/BackupRestoreService` a služba může být umístěn v oddílu systému služby v Service Fabric Exploreru. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Povolení pravidelné zálohování služby spolehlivé Stateful a Reliable Actors
Pojďme provede kroky k povolení pravidelné zálohování služby spolehlivé Stateful a Reliable Actors. Tento postup předpokládá
- Že cluster je instalační program s _zálohování a obnovení služby_.
- Služba spolehlivé Stateful je nasazen na clusteru. Pro účely tohoto průvodce rychlým zahájením je identifikátor Uri aplikace `fabric:/SampleApp` a identifikátor Uri služby spolehlivé Stateful patřící do této aplikace je `fabric:/SampleApp/MyStatefulService`. Tato služba je nasazený s jedním oddílem a ID oddílu se `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Vytvořit zásady zálohování

Prvním krokem je vytvoření zásady zálohování popisující plán zálohování, cílového úložiště pro zálohovaná data, název zásady a maximální přírůstkové zálohování smí před spuštěním úplného zálohování. 

Pro úložiště záloh vytvoření sdílené složky a poskytněte ReadWrite přístup k této sdílené složce pro všechny počítače, uzel topologie Fabric Service. Tento příklad předpokládá sdílenou složku s názvem `BackupStore` je k dispozici na `StorageServer`.

Spusťte následující skript prostředí PowerShell pro vyvolání požadované rozhraní REST API pro vytvoření nové zásady.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

### <a name="enable-periodic-backup"></a>Povolení pravidelné zálohování
Po definování zásad pro splnění požadavků na ochranu dat aplikace, musí být zásada zálohování přidružená k aplikaci. V závislosti na požadavek může být přidružené aplikace, služby nebo oddíl zásady zálohování.

Spustit následující skript prostředí PowerShell pro vyvolání požadované rozhraní API REST k přidružení zásady zálohování s názvem `BackupPolicy1` vytvořili výše krok s aplikací v `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Ověřte, zda jsou funkční pravidelné zálohy

Po povolení zálohování pro aplikaci, všechny oddíly patřící do spolehlivé Stateful services a Reliable Actors v rámci aplikace spustí získávání zálohovaná pravidelně podle přidružené zásady zálohování. 

![Událost stavu zálohovaná oddílu][0]

### <a name="list-backups"></a>Zálohování seznamu

Zálohování přidružené všechny oddíly, které patří do spolehlivé Stateful services a Reliable Actors aplikace mohou být uvedené pomocí _GetBackups_ rozhraní API. V závislosti na požadavek můžete vytvořit výčet zálohy pro aplikace, služby nebo oddíl.

Spustit následující skript prostředí PowerShell na volání rozhraní API HTTP výčet záloh vytvořených pro všechny oddíly uvnitř `SampleApp` aplikace.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Ukázkový výstup pro výše spusťte:

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

## <a name="preview-limitation-caveats"></a>Náhled omezení / upozornění
- Žádné Service Fabric součástí rutiny prostředí PowerShell.
- Žádná podpora pro Service Fabric rozhraní příkazového řádku.
- Žádná podpora pro automatizované zálohování vyprazdňování. Vyžaduje ruční čištění záloh.
- Žádná podpora pro Service Fabric clusterů v systému Linux.

## <a name="next-steps"></a>Další kroky
- [Odkazu k REST API obnovení zálohy](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent.png

