---
title: Rychlý start – pravidelné zálohování a obnovení v Azure Service Fabric (verze Preview) | Dokumentace Microsoftu
description: Použít Service Fabric pravidelné zálohování a obnovení funkce umožňující použití periodických dat. zálohování dat ve vašich aplikacích.
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
ms.openlocfilehash: 5666ed4226104104b91b6f964abeb0490ef80866
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990740"
---
# <a name="quickstart-periodic-backup-and-restore-in-azure-service-fabric-preview"></a>Rychlý start: Pravidelné zálohování a obnovení v Azure Service Fabric (verze Preview)
> [!div class="op_single_selector"]
> * [Clustery v Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Samostatné clustery](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric je platforma distribuovaných systémů, který usnadňuje vývoj a správu mikroslužeb spolehlivé, distribuované, na základě cloudových aplikací. To umožňuje spuštění bezstavové a stavové mikroslužby. Stavové služby můžete udržovat měnitelný a autoritativní stav i mimo požadavek a odpověď nebo celou transakci. Pokud do stavové služby ocitne mimo provoz delší dobu nebo nedochází ke ztrátě informací z důvodu selhání, bude pravděpodobně nutné možné obnovit do některých poslední zálohy stavu, aby bylo možné pokračovat v poskytování služeb, až se znovu zobrazí.

Service Fabric replikuje stav napříč několika uzly k zajištění, zda je služba s vysokou dostupností. I když selže jeden uzel v clusteru, bude služba nadále být k dispozici. V některých případech je však stále vhodná pro data služby spolehlivé před širším chybami.
 
Služba může být například vhodné zálohovat svá data z důvodu ochrany z následujících scénářů:
- V případě trvalé ztrátě celý cluster Service Fabric.
- Trvalé ztrátě většinou repliky oddílu služby
- Chyb správy kterým stav omylem získá odstraněn nebo poškozen. Například správce s dostatečná oprávnění omylem odstraní službu.
- Chyby ve službě, které dojít k poškození dat. Například tomu může dojít v případě upgrade kódu služby zahájí zápis vadným dat k Reliable Collection. V takovém případě se kód a data možná muset vrátit do předchozího stavu.
- Zpracování dat v režimu offline. Může být užitečné mít offline zpracování dat pro business intelligence, která se stane odděleně od služby, který generuje data.

Service Fabric nabízí integrované rozhraní API do bodu v čase [zálohování a obnovení](service-fabric-reliable-services-backup-restore.md). Vývojáři aplikací mohou pomocí těchto rozhraní API pravidelně zálohovat stav služby. Kromě toho Pokud správci služeb, aby se spustit zálohování z mimo službu v určitý čas, stejně jako před upgradem aplikace, vývojáři potřebují vystavit zálohování (a obnovení) jako rozhraní API ze služby. Další poplatky nad touto je udržování zálohy. Můžete například trvat 5 přírůstkové zálohy každou půlhodinu, za nímž následuje úplné zálohy. Po dokončení úplné zálohování můžete odstranit předchozí přírůstkové zálohování. Tento přístup vyžaduje další kód, což vede k další náklady během vývoje aplikace.

Zálohování dat aplikace v pravidelných intervalech je základní potřebu správy distribuované aplikace a zabezpečení proti ztrátě dat nebo dlouhotrvající ztrátou dostupnosti služby. Service Fabric poskytuje volitelný zálohování a obnovení služby, která umožňuje konfigurovat pravidelné zálohování stavovém modelu Reliable Services (včetně služby objektu Actor) bez nutnosti psát žádný další kód. Zajišťuje také obnovení už provedli zálohy. 

> [!NOTE]
> Pravidelné zálohování a obnovení funkce je v současné době v **ve verzi Preview** a nepodporuje se pro produkční úlohy. 
>

Service Fabric nabízí sadu rozhraní API k dosažení následující funkce vztahující se k pravidelné zálohování a obnovení funkce:

- Naplánovat pravidelné zálohování Reliable Stateful services a Reliable Actors s podporou pro nahrání umístění úložiště pro zálohování pro (externí). Umístění úložiště podporuje
    - Azure Storage
    - Sdílení souborů (v místním prostředí)
- Vytvoření výčtu zálohování
- Aktivační událost ad-hoc zálohování oddílu
- Obnovit oddíl pomocí předchozí zálohy
- Dočasně pozastavit zálohy
- Správa uchovávání záloh (nadcházející)

## <a name="prerequisites"></a>Požadavky
* Cluster Service Fabric s Fabric verze 6.2 a novějším. Cluster by měl být instalační program na Windows serveru. Přečtěte si [článku](service-fabric-cluster-creation-for-windows-server.md) pokyny ke stažení požadovaný balíček.
* Certifikát X.509 pro šifrování tajných kódů, které jsou potřebné pro připojení do služby storage k ukládání záloh. Přečtěte si [článku](service-fabric-windows-cluster-x509-security.md) vědět, jak získat nebo vytvořit certifikát X.509 podepsaný svým držitelem.
* Service Fabric Reliable stavové aplikace sestavené pomocí Service Fabric SDK verze 3.0 nebo vyšší. Aplikace cílené na.Net Core 2.0, aplikace by měly být sestaveny pomocí Service Fabric SDK verze 3.1 nebo novější.

## <a name="enabling-backup-and-restore-service"></a>Povolení zálohování a obnovení služby
Nejdřív je potřeba povolit _zálohování a obnovení služby_ ve vašem clusteru. Získáte šablonu pro cluster, do které chcete nasadit. Můžete použít [ukázkových šablon](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Povolit _zálohování a obnovení služby_ pomocí následujících kroků:

1. Zkontrolujte, že `apiversion` je nastavena na `10-2017` v konfiguraci clusteru souboru a pokud ne, aktualizovat, ji jak je znázorněno v následujícím fragmentu kódu:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Teď povolte _zálohování a obnovení služby_ přidáním následujícího kódu `addonFeatures` části `properties` jak je znázorněno v následujícím fragmentu kódu: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Konfigurace certifikátů X.509 pro šifrování přihlašovacích údajů. To je důležité zajistit, aby přihlašovací údaje k dispozici, pokud se chcete připojit k úložišti zašifrují před uložením. Konfigurace certifikátu šifrování přidáním následujícího kódu `BackupRestoreService` části `fabricSettings` jak je znázorněno v následujícím fragmentu kódu: 

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

4. Jakmile mají se aktualizoval váš soubor konfigurace clusteru s předchozí změny, je použít a umožní nasazení/upgrade dokončí. Po dokončení _zálohování a obnovení služby_ spuštění ve vašem clusteru. Identifikátor Uri této služby je `fabric:/System/BackupRestoreService` a služba se může nacházet v části systému služby v Service Fabric explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Povolení pravidelné zálohování služby Reliable Stateful a Reliable Actors
Projděme si postup povolení pravidelné zálohování služby Reliable Stateful a Reliable Actors. Tento postup předpokládá
- Že cluster je instalační program s _zálohování a obnovení služby_.
- Spolehlivé stavové služby je nasazen v clusteru. Pro účely této úvodní příručky, je identifikátor Uri aplikace `fabric:/SampleApp` a identifikátor Uri pro spolehlivé stavové služby patřící do této aplikace je `fabric:/SampleApp/MyStatefulService`. Tato služba je nasazená s jedním oddílem a ID oddílu je `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Vytvoření zásady zálohování

Prvním krokem je vytvoření zásady zálohování popisující plán zálohování, cílové úložiště pro zálohovaná data, název zásady a maximální přírůstkové zálohování povolené před aktivací úplné zálohování. 

Pro úložiště záloh vytvoření sdílené složky a umožněte přístup ReadWrite do této sdílené složky pro všechny počítače uzel Service Fabric. Tento příklad předpokládá sdílenou složku s názvem `BackupStore` je k dispozici na `StorageServer`.

Spusťte následující skript prostředí PowerShell pro vyvolání požadované rozhraní REST API k vytvoření nové zásady.

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

### <a name="enable-periodic-backup"></a>Povolit pravidelné zálohování
Po definování zásad pro splnění požadavků na ochranu dat aplikace, zásady zálohování by měly být přidružené aplikace. V závislosti na požadavku může být zásady zálohování přidružené aplikace, služby nebo oddíl.

Spusťte následující skript prostředí PowerShell pro vyvolání požadované rozhraní REST API k přidružení zásady zálohování s názvem `BackupPolicy1` vytvořené v nad krok s aplikací `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Ověřte, zda jsou funkční pravidelného zálohování

Po povolení zálohování pro aplikace, všechny oddíly, které patří k Reliable Stateful services a Reliable Actors v rámci aplikace spustí načítání zálohovanou pravidelně podle přidružené zásady zálohování. 

![Událost stavu zálohovaný oddílu][0]

### <a name="list-backups"></a>Seznam záloh

Zálohy přidružené všechny oddíly, které patří k Reliable Stateful services a Reliable Actors aplikace mohou být uvedené pomocí _GetBackups_ rozhraní API. V závislosti na požadavku jsou uvedené v zálohování pro aplikace, služby nebo oddíl.

Spusťte následující skript Powershellu pro vyvolání rozhraní HTTP API k vytvoření výčtu zálohy vytvořené pro všechny oddíly uvnitř `SampleApp` aplikace.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Ukázkový výstup pro výše uvedené spusťte:

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

## <a name="preview-limitation-caveats"></a>Omezení verze Preview / upozornění
- Rutiny prostředí PowerShell předdefinované bez Service Fabric.
- Žádná podpora pro Service Fabric CLI.
-  Žádná podpora pro automatizované vyprazdňování zálohování. [Zálohování uchování skript](https://github.com/Microsoft/service-fabric-scripts-and-templates/tree/master/scripts/BackupRetentionScript) může být uvedené nastavení externí automatizace skript na základě odstraňovat zařízení nepřipojená k zálohování.
- Žádná podpora pro Service Fabric clustery v Linuxu.

## <a name="next-steps"></a>Další postup
- [Principy pravidelné zálohování konfigurace](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Reference k rozhraní API REST obnovení zálohy](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent.png

