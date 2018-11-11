---
title: Pravidelné zálohování a obnovení v Azure Service Fabric (verze Preview) | Dokumentace Microsoftu
description: Použít Service Fabric pravidelné zálohování a obnovení funkce umožňující použití periodických dat. zálohování dat ve vašich aplikacích.
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
ms.openlocfilehash: ef92212b84496802dc2464498a0b6789f79a729b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246697"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric-preview"></a>Pravidelné zálohování a obnovení v Azure Service Fabric (verze Preview)
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
* Cluster Service Fabric s Fabric verze 6.2 a novějším. Cluster by měl být instalační program na Windows serveru. Přečtěte si [článku](service-fabric-cluster-creation-via-arm.md) postup vytvoření Service Fabric cluster pomocí šablony Azure resource.
* Certifikát X.509 pro šifrování tajných kódů, které jsou potřebné pro připojení do služby storage k ukládání záloh. Přečtěte si [článku](service-fabric-cluster-creation-via-arm.md) vědět, jak získat nebo vytvořit certifikát X.509.
* Service Fabric Reliable stavové aplikace sestavené pomocí Service Fabric SDK verze 3.0 nebo vyšší. Aplikace cílené na.Net Core 2.0, aplikace by měly být sestaveny pomocí Service Fabric SDK verze 3.1 nebo novější.
* Vytvoření účtu Azure Storage pro ukládání záloh aplikace.

## <a name="enabling-backup-and-restore-service"></a>Povolení zálohování a obnovení služby
Nejdřív je potřeba povolit _zálohování a obnovení služby_ ve vašem clusteru. Získáte šablonu pro cluster, do které chcete nasadit. Můžete použít [ukázkových šablon](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) nebo vytvoření šablony Resource Manageru. Povolit _zálohování a obnovení služby_ pomocí následujících kroků:

1. Zkontrolujte, že `apiversion` je nastavena na **`2018-02-01`** pro `Microsoft.ServiceFabric/clusters` prostředků a pokud ne, aktualizovat, ji jak je znázorněno v následujícím fragmentu kódu:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Teď povolte _zálohování a obnovení služby_ přidáním následujícího kódu `addonFeatures` části `properties` jak je znázorněno v následujícím fragmentu kódu: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Konfigurace certifikátů X.509 pro šifrování přihlašovacích údajů. To je důležité zajistit, že jsou před uložením zašifrované přihlašovací údaje zadané pro připojení k úložišti. Konfigurace certifikátu šifrování přidáním následujícího kódu `BackupRestoreService` části `fabricSettings` jak je znázorněno v následujícím fragmentu kódu: 

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

4. Jakmile máte aktualizované šablony clusteru s předchozí změny, aplikovat a umožní nasazení/upgrade dokončí. Po dokončení _zálohování a obnovení služby_ spuštění ve vašem clusteru. Identifikátor Uri této služby je `fabric:/System/BackupRestoreService` a služba se může nacházet v části systému služby v Service Fabric explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Povolení pravidelné zálohování služby Reliable Stateful a Reliable Actors
Projděme si postup povolení pravidelné zálohování služby Reliable Stateful a Reliable Actors. Tento postup předpokládá
- Že cluster je instalační program pomocí zabezpečení na X.509 s _zálohování a obnovení služby_.
- Spolehlivé stavové služby je nasazen v clusteru. Pro účely této úvodní příručky, je identifikátor Uri aplikace `fabric:/SampleApp` a identifikátor Uri pro spolehlivé stavové služby patřící do této aplikace je `fabric:/SampleApp/MyStatefulService`. Tato služba je nasazená s jedním oddílem a ID oddílu je `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- Nainstaluje se certifikát klienta s rolí správce _Moje_ (_osobní_) název úložiště _CurrentUser_ umístění úložiště na počítač, ze kterého pod certifikátu skripty se vyvolá. Tento příklad používá `1b7ebe2174649c45474a4819dafae956712c31d3` jako kryptografický otisk tohoto certifikátu. Další informace o klientské certifikáty, najdete v části [řízení přístupu na základě rolí pro Service Fabric klienty](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Vytvoření zásady zálohování

Prvním krokem je vytvoření zásady zálohování popisující plán zálohování, cílové úložiště pro zálohovaná data, název zásady a maximální přírůstkové zálohování povolené před aktivací úplné zálohování. 

Pro úložiště záloh pomocí služby Azure Storage, který je účet vytvořený výše. Kontejner `backup-container` je nakonfigurovaná pro ukládání záloh. Vytvoření kontejneru s tímto názvem již neexistuje, při zálohování nahrávání. Naplnění `ConnectionString` platným připojovacím řetězcem pro účet služby Azure Storage, nahrazení `account-name` s názvem vašeho účtu úložiště a `account-key` vaším klíčem účtu úložiště.

Spusťte následující skript prostředí PowerShell pro vyvolání požadované rozhraní REST API k vytvoření nové zásady. Nahraďte `account-name` s názvem vašeho účtu úložiště a `account-key` vaším klíčem účtu úložiště.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
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

### <a name="enable-periodic-backup"></a>Povolit pravidelné zálohování
Po definování zásad zálohování pro splnění požadavků na ochranu dat aplikace, zásady zálohování by měly být přidružené aplikace. V závislosti na požadavku může být zásady zálohování přidružené aplikace, služby nebo oddíl.

Spusťte následující skript prostředí PowerShell pro vyvolání požadované rozhraní REST API k přidružení zásady zálohování s názvem `BackupPolicy1` vytvořené v nad krok s aplikací `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Ověřte, zda jsou funkční pravidelného zálohování

Po povolení zálohování na úrovni aplikace, všechny oddíly, které patří k Reliable Stateful services a Reliable Actors v rámci aplikace spustí načítání zálohovanou pravidelně podle přidružené zásady zálohování. 

![Událost stavu zálohovaný oddílu][0]

### <a name="list-backups"></a>Seznam záloh

Zálohy přidružené všechny oddíly, které patří k Reliable Stateful services a Reliable Actors aplikace mohou být uvedené pomocí _GetBackups_ rozhraní API. Zálohy jsou uvedené pro aplikace, služby nebo oddíl.

Spusťte následující skript Powershellu pro vyvolání rozhraní HTTP API k vytvoření výčtu zálohy vytvořené pro všechny oddíly uvnitř `SampleApp` aplikace.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

## <a name="preview-limitation-caveats"></a>Omezení verze Preview / upozornění
- Rutiny prostředí PowerShell předdefinované bez Service Fabric.
- Žádná podpora pro Service Fabric CLI.
- Žádná podpora pro automatizované vyprazdňování zálohování. [Zálohování uchování skript](https://github.com/Microsoft/service-fabric-scripts-and-templates/tree/master/scripts/BackupRetentionScript) může být uvedené nastavení externí automatizace skript na základě odstraňovat zařízení nepřipojená k zálohování.
- Žádná podpora pro Service Fabric clustery v Linuxu.

## <a name="next-steps"></a>Další postup
- [Principy pravidelné zálohování konfigurace](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Reference k rozhraní API REST obnovení zálohy](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png

