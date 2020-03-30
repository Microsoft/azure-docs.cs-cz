---
title: Pravidelné zálohování a obnovení v Azure Service Fabric
description: Použijte funkci pravidelného zálohování a obnovení service fabric pro povolení pravidelného zálohování dat dat aplikace.
author: hrushib
ms.topic: conceptual
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: f56fcb7d1dde700d954c3b55bcf8cd7759893521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259003"
---
# <a name="periodic-backup-and-restore-in-an-azure-service-fabric-cluster"></a>Pravidelné zálohování a obnovení v clusteru Azure Service Fabric
> [!div class="op_single_selector"]
> * [Clustery v Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Samostatné clustery](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric je platforma distribuovaných systémů, která usnadňuje vývoj a správu spolehlivých, distribuovaných cloudových aplikací založených na mikroslužbách. Umožňuje provoz bezstavových i stavových mikroslužeb. Stavové služby mohou udržovat proměnlivý, autoritativní stav mimo požadavek a odpověď nebo úplnou transakci. Pokud stavová služba po dlouhou dobu klesá nebo ztratí informace v důsledku havárie, může být nutné obnovit některé nedávné zálohy svého stavu, aby bylo možné pokračovat v poskytování služby poté, co se vrátí zpět nahoru.

Service Fabric replikuje stav napříč více uzly k zajištění, že služba je vysoce dostupná. I v případě, že jeden uzel v clusteru selže, služba je i nadále k dispozici. V některých případech je však stále žádoucí, aby údaje o službě byly spolehlivé proti širším selháním.
 
Služba může například chtít zálohovat svá data, aby byla chráněna před následujícími scénáři:
- V případě trvalé ztráty celého clusteru Service Fabric.
- Trvalá ztráta většiny replik oddílu služby
- Chyby správy, při kterých se stav omylem odstraní nebo poškodí. Například správce s dostatečným oprávněním chybně odstraní službu.
- Chyby ve službě, které způsobují poškození dat. Například k tomu může dojít, když upgrade kódu služby začne psát chybná data do spolehlivé kolekce. V takovém případě kód a data může být muset vrátit do dřívějšího stavu.
- Offline zpracování dat. Může být vhodné mít offline zpracování dat pro business intelligence, které se děje odděleně od služby, která generuje data.

Service Fabric poskytuje vestavěné rozhraní API dělat bod v čase [zálohování a obnovení](service-fabric-reliable-services-backup-restore.md). Vývojáři aplikací mohou tato rozhraní API pravidelně zálohovat stav služby. Navíc pokud správci služeb chcete spustit zálohu mimo službu v určitém čase, jako před upgradem aplikace, vývojáři musí vystavit zálohování (a obnovení) jako rozhraní API ze služby. Údržba záloh je další náklady nad to. Můžete například chtít provést pět přírůstkové zálohy každou půlhodinu, následuje úplné zálohování. Po úplném zálohování můžete odstranit předchozí přírůstkové zálohy. Tento přístup vyžaduje další kód vedoucí k dodatečným nákladům během vývoje aplikace.

Služba Zálohování a obnovení ve službě Service Fabric umožňuje snadné a automatické zálohování informací uložených ve stavových službách. Pravidelné zálohování dat aplikací je zásadní pro ochranu před ztrátou dat a nedostupností služby. Service Fabric poskytuje volitelnou službu zálohování a obnovení, která umožňuje konfigurovat pravidelné zálohování stavových spolehlivých služeb (včetně služeb actor), aniž byste museli psát další kód. Usnadňuje také obnovení dříve přijatých záloh. 


Service Fabric poskytuje sadu rozhraní API k dosažení následujících funkcí souvisejících s funkcí pravidelného zálohování a obnovení:

- Naplánujte pravidelné zálohování spolehlivých stavových služeb a spolehlivých aktérů s podporou odesílání záloh do (externích) úložišť. Podporovaná umístění úložišť
    - Azure Storage
    - Sdílení souborů (místní)
- Výčet záloh
- Spuštění zálohy oddílu ad hoc
- Obnovení oddílu pomocí předchozí zálohy
- Dočasné pozastavení záloh
- Retenční správa záloh (nadcházející)

## <a name="prerequisites"></a>Požadavky
* Cluster Service Fabric s prostředků fabric verze 6.4 nebo vyšší. Postup vytvoření clusteru Service Fabric pomocí šablony prostředků Azure naleznete v tomto [článku.](service-fabric-cluster-creation-via-arm.md)
* Certifikát X.509 pro šifrování tajných kódů potřebných k připojení k úložišti pro ukládání záloh. Informace o tom, jak získat nebo vytvořit certifikát X.509, naleznete v [článku.](service-fabric-cluster-creation-via-arm.md)
* Service Fabric Spolehlivé stavové aplikace vytvořené pomocí Service Fabric SDK verze 3.0 nebo vyšší. Pro aplikace zaměřené na rozhraní .NET Core 2.0 by aplikace měla být vytvořena pomocí sady Service Fabric SDK verze 3.1 nebo vyšší.
* Vytvořte účet Azure Storage pro ukládání záloh aplikací.
* Nainstalujte modul Microsoft.ServiceFabric.Powershell.Http [In Preview] pro volání konfigurace.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Před provedením jakéhokoli požadavku `Connect-SFCluster` na konfiguraci pomocí modulu Microsoft.ServiceFabric.Powershell.Http se ujistěte, že je cluster připojen pomocí příkazu.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Povolení zálohování a obnovení služby

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal

Povolte `Include backup restore service` zaškrtávací políčko v `+ Show optional settings` `Cluster Configuration` části na kartě.

![Povolit službu obnovení zálohování pomocí portálu][1]


### <a name="using-azure-resource-manager-template"></a>Použití šablony Správce prostředků Azure
Nejprve je třeba povolit _službu zálohování a obnovení_ v clusteru. Získejte šablonu pro cluster, který chcete nasadit. Můžete použít [ukázkové šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) nebo vytvořit šablonu Správce prostředků. Povolte _službu zálohování a obnovení_ pomocí následujících kroků:

1. Zkontrolujte, `apiversion` zda **`2018-02-01`** je `Microsoft.ServiceFabric/clusters` prostředek nastaven na prostředek, a pokud ne, aktualizujte jej tak, jak je znázorněno v následujícím fragmentu:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Nyní povolte _službu zálohování_ a `addonFeatures` obnovení `properties` přidáním následující části v části, jak je znázorněno v následujícím fragmentu: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Nakonfigurujte certifikát X.509 pro šifrování přihlašovacích údajů. To je důležité zajistit, že pověření pro připojení k úložišti jsou šifrována před uchováním. Nakonfigurujte šifrovací certifikát přidáním následující `BackupRestoreService` části do `fabricSettings` části, jak je znázorněno v následujícím fragmentu: 

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

4. Po aktualizaci šablony clusteru s předchozími změnami je použijte a nechte nasazení/upgrade dokončit. Po dokončení se _služba zálohování a obnovení_ spustí v clusteru. Uri této služby `fabric:/System/BackupRestoreService` je a služba může být umístěn a v části systémové služby v průzkumníku Service Fabric. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Povolení pravidelného zálohování pro spolehlivé stavové služby a spolehlivé aktéry
Pojďme projít kroky k povolení pravidelné zálohování pro spolehlivé stavové služby a spolehlivé aktéry. Tyto kroky předpokládají, že
- Že je cluster nastaven pomocí zabezpečení X.509 se _službou zálohování a obnovení_.
- V clusteru je nasazena služba Reliable Stateful. Pro účely tohoto průvodce rychlým startem `fabric:/SampleApp` je aplikace Uri a Uri pro `fabric:/SampleApp/MyStatefulService`spolehlivé stavové služby patřící do této aplikace je . Tato služba je nasazena s jedním oddílem `974bd92a-b395-4631-8a7f-53bd4ae9cf22`a ID oddílu je .
- Klientský certifikát s rolí správce je nainstalován v části _Můj_ (_Osobní_) název úložiště úložiště certifikátů _CurrentUser_ v počítači, ze kterého budou vyvolány níže uvedené skripty. Tento příklad `1b7ebe2174649c45474a4819dafae956712c31d3` používá jako kryptografický otisk tohoto certifikátu. Další informace o klientských certifikátech naleznete v [tématu Řízení přístupu na základě rolí pro klienty Service Fabric](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Vytvořit zásady zálohování

Prvním krokem je vytvoření zásady zálohování popisující plán zálohování, cílové úložiště pro zálohovaná data, název zásady, maximální přírůstkové zálohy, které mají být povoleny před aktivací úplné ho zálohování a uchovávání zásad pro ukládání záloh. 

Pro úložiště záloh použijte účet Azure Storage vytvořený výše. Kontejner `backup-container` je nakonfigurován pro ukládání záloh. Kontejner s tímto názvem je vytvořen, pokud ještě neexistuje, během odesílání zálohy. Naplňte `ConnectionString` platným připojovacím řetězcem `account-name` pro účet Azure Storage, který nahradí název účtu úložiště a `account-key` klíč účtu úložiště.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Prostředí PowerShell pomocí modulu Microsoft.ServiceFabric.Powershell.Http

Spusťte následující rutiny prostředí PowerShell pro vytvoření nové zásady zálohování. Nahraďte `account-name` název účtu `account-key` úložiště a klíčem účtu úložiště.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Volání na odpočinek pomocí PowerShellu

Spusťte následující skript prostředí PowerShell pro vyvolání požadované rozhraní REST API k vytvoření nové zásady. Nahraďte `account-name` název účtu `account-key` úložiště a klíčem účtu úložiště.

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

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

```

#### <a name="using-service-fabric-explorer"></a>Použití Průzkumníka prostředků infrastruktury služby

1. V Průzkumníku infrastruktury služby přejděte na kartu Zálohy a vyberte akce > vytvořit zásady zálohování.

    ![Vytvořit zásady zálohování][6]

2. Vyplňte informace. Pro clustery Azure azure by měl být vybrán AzureBlobStore.

    ![Vytvoření úložiště objektů blob Azure pro zásady zálohování][7]

### <a name="enable-periodic-backup"></a>Povolit pravidelné zálohování
Po definování zásad zálohování ke splnění požadavků na ochranu dat aplikace by měly být zásady zálohování přidruženy k aplikaci. V závislosti na požadavku mohou být zásady zálohování přidruženy k aplikaci, službě nebo oddílu.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Prostředí PowerShell pomocí modulu Microsoft.ServiceFabric.Powershell.Http

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>Volání na odpočinek pomocí PowerShellu

Spusťte následující skript prostředí PowerShell pro vyvolání požadovaného rozhraní REST API pro přidružení zásady zálohování k názvu `BackupPolicy1` vytvořenému ve výše uvedeném kroku k aplikaci `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>Použití Průzkumníka prostředků infrastruktury služby

1. Vyberte aplikaci a přejděte k akci. Klikněte na Povolit nebo aktualizovat zálohování aplikací.

    ![Povolit zálohování aplikací][3]

2. Nakonec vyberte požadovanou zásadu a klepněte na tlačítko Povolit zálohování.

    ![Vybrat zásadu][4]


### <a name="verify-that-periodic-backups-are-working"></a>Ověřte, zda pravidelné zálohování funguje

Po povolení zálohování na úrovni aplikace, všechny oddíly, které patří spolehlivé stavové služby a spolehlivé objekty actor v rámci aplikace začne získávání zálohovány pravidelně podle přidružené zásady zálohování. 

![Událost stavu zálohovaného oddílu][0]

### <a name="list-backups"></a>Seznam záloh

Zálohy přidružené ke všem oddílům, které patří do spolehlivé stavové služby a spolehlivé objekty actor aplikace lze výčet pomocí _rozhraní API GetBackups._ Zálohy lze výčet pro aplikaci, službu nebo oddíl.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Prostředí PowerShell pomocí modulu Microsoft.ServiceFabric.Powershell.Http

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Volání na odpočinek pomocí PowerShellu

Spusťte následující skript Prostředí PowerShell k vyvolání rozhraní HTTP API pro `SampleApp` výčet záloh vytvořených pro všechny oddíly uvnitř aplikace.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Ukázkový výstup pro výše uvedený běh:

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

#### <a name="using-service-fabric-explorer"></a>Použití Průzkumníka prostředků infrastruktury služby

Chcete-li zobrazit zálohy v aplikaci Service Fabric Explorer, přejděte na oddíl a vyberte kartu Zálohy.

![Výčet záloh][5]

## <a name="limitation-caveats"></a>Omezení/ upozornění
- Rutiny Service Fabric PowerShell jsou v režimu náhledu.
- Žádná podpora clusterů Service Fabric v Systému Linux.

## <a name="next-steps"></a>Další kroky
- [Principy konfigurace pravidelného zálohování](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Odkaz na rozhraní REST API pro obnovení zálohy](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/creation-bp.png