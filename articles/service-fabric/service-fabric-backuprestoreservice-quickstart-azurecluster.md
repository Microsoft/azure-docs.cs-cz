---
title: Pravidelné zálohování a obnovení v Azure Service Fabric (Preview) | Microsoft Docs
description: Použít Service Fabric pravidelné zálohování a obnovení funkce pro ochranu před ztrátou dat aplikace.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAA58600-897E-4CEE-9D1C-93FACF98AD1C
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: b2e2e7dcc26bece79ae0423d55b08416065d599e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
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
* Cluster Service Fabric s prostředky infrastruktury verze 6.2 a vyšší. Cluster musí být instalační program v systému Windows Server. Odkazovat [článku](service-fabric-cluster-creation-via-arm.md) kroky k vytvoření Service Fabric pomocí šablony Azure prostředků clusteru.
* Certifikát X.509 pro šifrování tajné klíče potřebné pro připojení k úložiště pro ukládání záloh. Odkazovat [článku](service-fabric-cluster-creation-via-arm.md) vědět, jak získat nebo vytvořit certifikát X.509.
* Service Fabric spolehlivé Stateful aplikace vyvíjené v Service Fabric SDK verze 3.0 nebo vyšší. Pro aplikace cílený na rozhraní .net základní 2.0, aplikace by měly být vytvořeny pomocí Service Fabric SDK verze 3.1 nebo novější.
* Vytvořte účet úložiště Azure pro ukládání záloh aplikace.

## <a name="enabling-backup-and-restore-service"></a>Povolení zálohování a obnovení služby
Nejprve musíte povolit _zálohování a obnovení služby_ v clusteru. Získáte šablonu pro cluster, který chcete nasadit. Můžete použít [ukázkových šablon](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) nebo vytvořit šablonu Resource Manager. Povolit _zálohování a obnovení služby_ pomocí následujících kroků:

1. Zkontrolujte, zda `apiversion` je nastaven na **`2018-02-01`** pro `Microsoft.ServiceFabric/clusters` prostředků a pokud ne, aktualizovat, je znázorněné v následujícím fragmentu kódu:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Teď povolit _zálohování a obnovení služby_ přidáním následující `addonFeatures` oddílu pod `properties` části, jak je znázorněno v následujícím fragmentu kódu: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Nakonfigurujte certifikát X.509 pro šifrování přihlašovacích údajů. To je důležité zajistit, aby byly přihlašovací údaje se připojit k úložišti šifrované před uložením. Nakonfigurovat šifrovací certifikát přidáním následující `BackupRestoreService` oddílu pod `fabricSettings` části, jak je znázorněno v následujícím fragmentu kódu: 

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

4. Jakmile vaše šablona clusteru byly aktualizovány předchozí změny, je použít a umožní nasazení/upgrade dokončit. Po dokončení _zálohování a obnovení služby_ spuštění v clusteru. Identifikátor Uri této služby je `fabric:/System/BackupRestoreService` a služba může být umístěn v oddílu systému služby v Service Fabric Exploreru. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Povolení pravidelné zálohování služby spolehlivé Stateful a Reliable Actors
Pojďme provede kroky k povolení pravidelné zálohování služby spolehlivé Stateful a Reliable Actors. Tento postup předpokládá
- Že cluster je instalaci pomocí zabezpečení X.509 s _zálohování a obnovení služby_.
- Služba spolehlivé Stateful je nasazen na clusteru. Pro účely tohoto průvodce rychlým zahájením je identifikátor Uri aplikace `fabric:/SampleApp` a identifikátor Uri služby spolehlivé Stateful patřící do této aplikace je `fabric:/SampleApp/MyStatefulService`. Tato služba je nasazený s jedním oddílem a ID oddílu se `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- Certifikát klienta s rolí správce je nainstalován v _Moje_ (_osobní_) uložit název _CurrentUser_ certifikátu umístění úložiště na počítač, ze kterého níže skripty, který bude vyvolán. Tento příklad používá `1b7ebe2174649c45474a4819dafae956712c31d3` jako kryptografický otisk tohoto certifikátu. Další informace o klientských certifikátů najdete v tématu [řízení přístupu na základě rolí pro Service Fabric klienty](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Vytvořit zásady zálohování

Prvním krokem je vytvoření zásady zálohování popisující plán zálohování, cílového úložiště pro zálohovaná data, název zásady a maximální přírůstkové zálohování smí před spuštěním úplného zálohování. 

Pro úložiště záloh použijte účet vytvořili výše úložiště Azure. Tento příklad předpokládá účtu úložiště Azure s názvem `sfbackupstore`. Kontejner `backup-container` je nakonfigurován pro ukládání záloh, je vytvořit kontejner s tímto názvem, pokud ještě není přítomný, během nahrávání zálohování. Naplnění `ConnectionString` s platný připojovací řetězec pro účet úložiště Azure.

Spusťte následující skript prostředí PowerShell pro vyvolání požadované rozhraní REST API pro vytvoření nové zásady.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=sfbackupstore;AccountKey=64S+3ykBgOuKhd2DK1qHJJtDml3NtRzgaZUa+8iwwBAH4EzuGt95JmOm7mp/HOe8V3l645iv5l8oBfnhhc7dJA==;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

### <a name="enable-periodic-backup"></a>Povolení pravidelné zálohování
Po definování zásad zálohování pro splnění požadavků na ochranu dat aplikace, musí být zásada zálohování přidružená k aplikaci. V závislosti na požadavek může být přidružené aplikace, služby nebo oddíl zásady zálohování.

Spustit následující skript prostředí PowerShell pro vyvolání požadované rozhraní API REST k přidružení zásady zálohování s názvem `BackupPolicy1` vytvořili výše krok s aplikací v `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Ověřte, zda jsou funkční pravidelné zálohy

Po povolení zálohování na úrovni aplikace, se všechny oddíly patřící do spolehlivé Stateful services a Reliable Actors v rámci aplikace spustí získávání zálohovaná pravidelně podle přidružené zásady zálohování. 

![Událost stavu zálohovaná oddílu][0]

### <a name="list-backups"></a>Zálohování seznamu

Zálohování přidružené všechny oddíly, které patří do spolehlivé Stateful services a Reliable Actors aplikace mohou být uvedené pomocí _GetBackups_ rozhraní API. Zálohy jsou uvedené pro aplikace, služby nebo oddíl.

Spustit následující skript prostředí PowerShell na volání rozhraní API HTTP výčet záloh vytvořených pro všechny oddíly uvnitř `SampleApp` aplikace.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Ukázkový výstup pro výše spusťte:

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

## <a name="preview-limitation-caveats"></a>Náhled omezení / upozornění
- Žádné Service Fabric součástí rutiny prostředí PowerShell.
- Žádná podpora pro Service Fabric rozhraní příkazového řádku.
- Žádná podpora pro automatizované zálohování vyprazdňování. Vyžaduje ruční čištění záloh.
- Žádná podpora pro Service Fabric clusterů v systému Linux.

## <a name="next-steps"></a>Další kroky
- [Odkazu k REST API obnovení zálohy](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png

