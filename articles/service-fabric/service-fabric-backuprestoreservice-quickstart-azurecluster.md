---
title: Pravidelné zálohování a obnovení v Azure Service Fabric
description: Použijte funkci periodického zálohování a obnovení Service Fabric k povolení pravidelného zálohování dat aplikací.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: 42097b50277e78b3f0e8f5e61a2bf70cc08dbc02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103198716"
---
# <a name="periodic-backup-and-restore-in-an-azure-service-fabric-cluster"></a>Periodické zálohování a obnovení v clusteru Azure Service Fabric
> [!div class="op_single_selector"]
> * [Clustery v Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Samostatné clustery](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric je platforma distribuovaných systémů usnadňující vývoj a správu spolehlivých distribuovaných cloudových aplikací založených na mikroslužbách. Umožňuje provozování bezstavových i stavových mikroslužeb. Stavové služby mohou udržovat proměnlivý a směrodatný stav mimo požadavek a odpověď nebo úplnou transakci. Pokud stavová služba přestane trvat dlouhou dobu nebo ztratí informace z důvodu havárie, může být nutné ji obnovit do nějakého nedávného zálohování svého stavu, aby bylo možné službu po jejím zálohování dále poskytovat.

Service Fabric replikuje stav napříč několika uzly, aby bylo zajištěno, že je služba vysoce dostupná. I v případě, že jeden uzel v clusteru dojde k chybě, služba bude nadále k dispozici. V některých případech je však stále žádoucí, aby data služby byla spolehlivá proti širším selháním.
 
Například služba může chtít zálohovat svá data, aby se chránila z následujících scénářů:
- V případě trvalé ztráty celého Service Fabric clusteru.
- Trvalá ztráta většiny replik oddílu služby
- Chyby správy, při kterých se stav omylem odstranil nebo je poškozen. Například správce s dostatečným oprávněním může službu omylem odstranit.
- Chyby ve službě, které způsobují poškození dat. K tomu může dojít například v případě, že upgrade kódu služby začne psát vadná data do spolehlivé kolekce. V takovém případě je možné, že kód i data budou muset být vráceny do předchozího stavu.
- Zpracování offline dat. Může být vhodné mít offline zpracování dat pro business intelligence, ke kterým dochází nezávisle na službě, která data generuje.

Service Fabric poskytuje předdefinované rozhraní API k tomu, aby bylo [zálohování a obnovení](service-fabric-reliable-services-backup-restore.md)k bodu v čase. Vývojáři aplikací můžou tato rozhraní API používat k pravidelnému zálohování stavu služby. Kromě toho, pokud Správci služeb chtějí aktivovat zálohování mimo službu v určitou dobu, třeba před upgradem aplikace, vývojáři potřebují vystavit zálohování (a obnovení) jako rozhraní API ze služby. Udržování záloh je nad rámec těchto nákladů vyšší. Můžete například chtít provést pět přírůstkových záloh každou půl hodiny následovaný úplným zálohováním. Po úplném zálohování můžete odstranit předchozí přírůstkové zálohy. Tento přístup vyžaduje další kód, který by měl při vývoji aplikací dopředné náklady.

Služba zálohování a obnovení v Service Fabric umožňuje snadné a automatické zálohování informací uložených ve stavových službách. Zálohování dat aplikací je v pravidelných intervalech zásadní pro ochranu před ztrátou dat a nedostupností služby. Service Fabric poskytuje volitelnou službu pro zálohování a obnovení, která umožňuje konfigurovat pravidelné zálohování stavových Reliable Services (včetně služeb actor) bez nutnosti psát další kód. Také usnadňuje obnovení dříve vytvořených záloh. 


Service Fabric poskytuje sadu rozhraní API pro zajištění následujících funkcí vztahujících se na funkci periodického zálohování a obnovení:

- Naplánování pravidelného zálohování spolehlivých stavových služeb a Reliable Actors s podporou pro nahrávání záloh do (externích) umístění úložiště. Podporovaná umístění úložiště
    - Azure Storage
    - Sdílení souborů (místně)
- Vytvoření výčtu záloh
- Aktivovat zálohu oddílu ad hoc
- Obnovení oddílu pomocí předchozí zálohy
- Dočasné pozastavení zálohování
- Správa uchovávání záloh (nadcházející)

## <a name="prerequisites"></a>Předpoklady
* Service Fabric cluster se službou Fabric verze 6,4 nebo vyšší. Postup vytvoření Service Fabricho clusteru pomocí šablony prostředků Azure najdete v tomto [článku](service-fabric-cluster-creation-via-arm.md) .
* X. 509 certifikát pro šifrování tajných kódů potřebných pro připojení k úložišti pro ukládání záloh. V [článku](service-fabric-cluster-creation-via-arm.md) najdete informace o tom, jak získat nebo vytvořit certifikát X. 509.
* Service Fabric Reliable stavová aplikace vytvořená pomocí sady Service Fabric SDK verze 3,0 nebo vyšší. Pro aplikace cílené na .NET Core 2,0 by měla být aplikace sestavená pomocí sady SDK Service Fabric verze 3,1 nebo vyšší.
* Vytvořte Azure Storage účet pro ukládání záloh aplikací.
* Nainstalujte modul Microsoft. ServiceFabric. PowerShell. http (Preview) pro provedení konfiguračních volání.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> Pokud je verze PowerShellGet menší než 1.6.0, bude nutné aktualizovat, aby se přidala podpora pro příznak *-AllowPrerelease* :
>
> `Install-Module -Name PowerShellGet -Force`

* `Connect-SFCluster`Před provedením libovolné žádosti o konfiguraci pomocí modulu Microsoft. ServiceFabric. PowerShell. http zajistěte, aby byl cluster připojen pomocí příkazu.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Povolení služby zálohování a obnovení

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal

Povolit `Include backup restore service` zaškrtávací políčko v části `+ Show optional settings` na `Cluster Configuration` kartě.

![Povolení služby obnovení zálohování pomocí portálu][1]


### <a name="using-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager
Nejdřív musíte ve svém clusteru povolit _službu zálohování a obnovení_ . Získejte šablonu pro cluster, který chcete nasadit. Můžete buď použít [ukázkové šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) , nebo vytvořit šablonu správce prostředků. Pomocí následujících kroků povolte _službu zálohování a obnovení_ :

1. Ověřte, zda `apiversion` je u prostředku nastavena na hodnotu **`2018-02-01`** `Microsoft.ServiceFabric/clusters` a v případě potřeby jej aktualizujte, jak je znázorněno v následujícím fragmentu kódu:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Nyní povolte _službu zálohování a obnovení_ přidáním následujícího oddílu do části `addonFeatures` `properties` , jak je znázorněno v následujícím fragmentu kódu: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Nakonfigurujte certifikát X. 509 pro šifrování přihlašovacích údajů. To je důležité, aby se přihlašovací údaje, které se zadaly pro připojení k úložišti, zašifroval před tím, než budou trvalé. Nakonfigurujte šifrovací certifikát přidáním následující `BackupRestoreService` části v části `fabricSettings` , jak je znázorněno v následujícím fragmentu kódu: 

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

4. Jakmile aktualizujete šablonu clusteru předchozími změnami, použijte je a nechejte nasazení nebo upgrade dokončeno. Po dokončení se _Služba zálohování a obnovení_ spustí v clusteru. Identifikátor URI této služby je `fabric:/System/BackupRestoreService` a služba se může nacházet v části systémová služba v průzkumníkovi Service Fabric. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Povolení pravidelného zálohování pro spolehlivou stavovou službu a Reliable Actors
Podívejme se na postupy, které umožňují pravidelné zálohování pro spolehlivou stavovou službu a Reliable Actors. Tyto kroky předpokládají
- Cluster se nastavuje pomocí nástroje X. 509 Security se _službou Backup and Restore_.
- V clusteru je nasazená spolehlivá stavová služba. Pro účely tohoto průvodce rychlým startem je identifikátor URI aplikace `fabric:/SampleApp` a identifikátor URI pro spolehlivou stavovou službu, která patří do této aplikace `fabric:/SampleApp/MyStatefulService` . Tato služba je nasazená s jedním oddílem a ID oddílu je `974bd92a-b395-4631-8a7f-53bd4ae9cf22` .
- Certifikát klienta s rolí správce se instaluje do _osobního_ (_osobního_) názvu úložiště _CurrentUser_ úložiště certifikátů na počítači, ze kterého se budou vyvolávat skripty. Tento příklad používá `1b7ebe2174649c45474a4819dafae956712c31d3` jako kryptografický otisk tohoto certifikátu. Další informace o klientských certifikátech najdete v tématu [řízení přístupu na základě rolí pro klienty Service Fabric](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Vytvořit zásady zálohování

Prvním krokem je vytvoření zásad zálohování popisujících plán zálohování, cílové úložiště pro zálohovaná data, název zásady, maximální počet přírůstkových záloh, které se mají povolit, než se spustí úplné zálohování a zásady uchovávání informací pro úložiště záloh. 

Pro úložiště zálohování použijte účet Azure Storage vytvořený výše. Kontejner `backup-container` je nakonfigurován pro ukládání záloh. Kontejner s tímto názvem je vytvořen, pokud ještě neexistuje, během nahrávání zálohy. Naplňte `ConnectionString` platným připojovacím řetězcem pro účet Azure Storage, nahraďte `account-name` název svého účtu úložiště a `account-key` klíč účtu úložiště.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell s použitím modulu Microsoft. ServiceFabric. PowerShell. http

Spusťte následující rutiny PowerShellu pro vytváření nových zásad zálohování. Nahraďte `account-name` názvem svého účtu úložiště a `account-key` klíčem účtu úložiště.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Volání REST pomocí PowerShellu

Pokud chcete vytvořit novou zásadu, spusťte následující skript PowerShellu pro vyvolání požadované REST API. Nahraďte `account-name` názvem svého účtu úložiště a `account-key` klíčem účtu úložiště.

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

#### <a name="using-service-fabric-explorer"></a>Použití Service Fabric Explorer

1. V Service Fabric Explorer přejděte na kartu zálohy a vyberte akce > vytvořit zásadu zálohování.

    ![Vytvořit zásady zálohování][6]

2. Vyplňte informace. U clusterů Azure by se měla vybrat AzureBlobStore.

    ![Vytvoření zásady zálohování Azure Blob Storage][7]

### <a name="enable-periodic-backup"></a>Povolit pravidelná zálohování
Po definování zásad zálohování pro splnění požadavků na ochranu dat aplikace by měly být zásady zálohování přidružené k aplikaci. V závislosti na požadavku může být zásada zálohování přidružená k aplikaci, službě nebo oddílu.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell s použitím modulu Microsoft. ServiceFabric. PowerShell. http

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>Volání REST pomocí PowerShellu

Spusťte následující skript PowerShellu pro vyvolání požadované REST API k přidružení zásady zálohování s názvem `BackupPolicy1` vytvořeným v předchozím kroku s aplikací `SampleApp` .

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>Použití Service Fabric Explorer
Ujistěte se, že je povolen [Rozšířený režim](service-fabric-visualizing-your-cluster.md#backup-and-restore) pro Service Fabric Explorer. 

1. Vyberte aplikaci a pokračujte na akci. Klikněte na povolit nebo aktualizovat zálohu aplikace.

    ![Povolit zálohování aplikací][3]

2. Nakonec vyberte požadovanou zásadu a klikněte na povolit zálohování.

    ![Vybrat zásadu][4]


### <a name="verify-that-periodic-backups-are-working"></a>Ověřte, jestli fungují pravidelné zálohy.

Po povolení zálohování na úrovni aplikace začnou všechny oddíly patřící do spolehlivých stavových služeb a Reliable Actors v rámci aplikace pravidelně zálohovat podle přidružených zásad zálohování. 

![Událost stavu BackedUp oddílu][0]

### <a name="list-backups"></a>Vypsat zálohy

Zálohy přidružené ke všem oddílům, které patří do spolehlivých stavových služeb a Reliable Actors aplikace, se dají vyčíslit pomocí rozhraní API _Getbackups_ . Pro aplikace, služby nebo oddíly lze vytvořit výčet záloh.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell s použitím modulu Microsoft. ServiceFabric. PowerShell. http

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Volání REST pomocí PowerShellu

Spuštěním následujícího skriptu PowerShellu vyvolejte rozhraní HTTP API, abyste mohli vytvořit výčet záloh vytvořených pro všechny oddíly v rámci `SampleApp` aplikace.

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

#### <a name="using-service-fabric-explorer"></a>Použití Service Fabric Explorer

Chcete-li zobrazit zálohy v Service Fabric Explorer, přejděte do oddílu a vyberte kartu zálohy.

![Vytvoření výčtu záloh][5]

## <a name="limitation-caveats"></a>Omezení a upozornění
- Rutiny Service Fabric PowerShellu jsou v režimu náhledu.
- V systému Linux není podporována podpora Service Fabricch clusterů.

## <a name="next-steps"></a>Další kroky
- [Principy konfigurace pravidelného zálohování](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Odkaz na REST API obnovení zálohy](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/creation-bp.png
