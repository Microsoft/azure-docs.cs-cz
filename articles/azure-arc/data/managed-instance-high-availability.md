---
title: Vysoká dostupnost spravované instance s podporou ARC Azure
titleSuffix: Deploy Azure Arc enabled Managed Instance with high availability
description: Přečtěte si, jak nasadit spravovanou instanci s povoleným ARC Azure s vysokou dostupností.
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: conceptual
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: e04b9b98db26b3b9a024a60c6f82820fe20fcbf1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693032"
---
# <a name="azure-arc-enabled-managed-instance-high-availability"></a>Vysoká dostupnost spravované instance s podporou ARC Azure

Spravovaná instance s povoleným ARC Azure je nasazená na Kubernetes jako kontejnerová aplikace a používá konstrukce Kubernetes, jako jsou stavové sady a trvalé úložiště, aby poskytovaly integrované monitorování stavu, detekci selhání a mechanismy převzetí služeb při selhání pro udržení stavu služby. Pro zvýšení spolehlivosti můžete také nakonfigurovat spravovanou instanci s povoleným ARC Azure, aby se nasadila s dalšími replikami v konfiguraci s vysokou dostupností. Monitorování, detekce selhání a automatické převzetí služeb při selhání spravuje řadič dat ARC (Data Services). Tato služba se poskytuje bez zásahu uživatele – to vše z nastavení skupiny dostupnosti, konfigurace koncových bodů zrcadlení databáze, přidání databází do skupiny dostupnosti nebo převzetí služeb při selhání a koordinace upgradu. Tento dokument zkoumá oba typy vysoké dostupnosti.

## <a name="built-in-high-availability"></a>Integrovaná vysoká dostupnost 

Vestavěnou vysokou dostupnost poskytuje Kubernetes při konfiguraci vzdáleného trvalého úložiště a jeho sdílení s uzly, které používá nasazení datové služby ARC. V této konfiguraci Kubernetes hraje roli nástroje Orchestrator clusteru. Pokud se spravovaná instance v kontejneru nebo v podkladovém uzlu nezdařila, nástroj Orchestrator spustí jinou instanci kontejneru a připojí se ke stejnému trvalému úložišti. Tento typ je ve výchozím nastavení povolený, když nasadíte spravovanou instanci s povoleným ARC Azure.

### <a name="verify-built-in-high-availability"></a>Ověření integrované vysoké dostupnosti

V této části ověříte vestavěnou vysokou dostupnost poskytovanou službou Kubernetes. Pokud budete postupovat podle kroků pro otestování této funkce, odstraníte z existující spravované instance a ověříte, že se Kubernetes obnoví z této akce. 

### <a name="prerequisites"></a>Požadavky

- Cluster Kubernetes musí mít [sdílené, vzdálené úložiště](https://docs.microsoft.com/en-us/azure/azure-arc/data/storage-configuration#factors-to-consider-when-choosing-your-storage-configuration) . 
- Spravovaná instance s povoleným ARC Azure nasazená s jednou replikou (výchozí)

1. Zobrazte lusky. 

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

2. Odstraňte spravovanou instanci pod.

   ```console
   kubectl delete pod <name of managed instance>-0 -n <namespace of data controller>
   ```

   Například

   ```output
   user@pc:/# kubectl delete pod sql1-0 -n arc
   pod "sql1-0" deleted
   ```

3. Prohlédněte si lusky a ověřte, že se spravovaná instance obnovuje.

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

   Například

   ```output
   user@pc:/# kubectl get pods -n arc
   NAME                 READY   STATUS    RESTARTS   AGE
   sql1-0               2/3     Running   0          22s
   ```

Po obnovení všech kontejnerů v části pod ní se můžete připojit ke spravované instanci.

## <a name="deploy-with-always-on-availability-groups"></a>Nasazení s využitím skupin dostupnosti Always On

Pro zvýšení spolehlivosti můžete nakonfigurovat spravovanou instanci s povoleným ARC Azure, aby se nasadila s dalšími replikami v konfiguraci s vysokou dostupností. 

Možnosti, které skupiny dostupnosti umožňují:

- Při nasazení s více replikami je vytvořena jedna skupina dostupnosti s názvem `containedag` . Ve výchozím nastavení `containedag` má tři repliky, včetně primární. Všechny operace CRUD pro skupinu dostupnosti jsou spravované interně, včetně vytvoření skupiny dostupnosti nebo připojení replik do skupiny dostupnosti, která se vytvořila. Ve spravované instanci s povoleným obloukem Azure nelze vytvořit další skupiny dostupnosti.

- Všechny databáze jsou automaticky přidány do skupiny dostupnosti, včetně všech uživatelských a systémových databází jako `master` a `msdb` . Tato funkce poskytuje jedno systémové zobrazení v rámci replik skupin dostupnosti. `containedag_master` `containedag_msdb` Pokud se připojujete přímo k instanci, Všimněte si obou i databází. `containedag_*`Databáze reprezentují `master` a `msdb` uvnitř skupiny dostupnosti.

- Externí koncový bod se automaticky zřídí pro připojení k databázím v rámci skupiny dostupnosti. Tento koncový bod `<managed_instance_name>-svc-external` hraje roli naslouchacího procesu skupiny dostupnosti.

### <a name="deploy"></a>Nasadit

Pokud chcete nasadit spravovanou instanci se skupinami dostupnosti, spusťte následující příkaz.

```console
azdata arc sql mi create -n <name of instance> --replicas 3
```

### <a name="check-status"></a>Zkontrolování stavu
Po nasazení instance spusťte následující příkazy, abyste zkontrolovali stav vaší instance:

```console
azdata arc sql mi list
azdata arc sql mi show -n <name of instance>
```

Příklad výstupu:

```output
user@pc:/# azdata arc sql mi list
ExternalEndpoint    Name    Replicas    State
------------------  ------  ----------  -------
20.131.31.58,1433   sql2    3/3         Ready

user@pc:/#  azdata arc sql mi show -n sql2
{
...
  "status": {
    "AGStatus": "Healthy",
    "externalEndpoint": "20.131.31.58,1433",
    "logSearchDashboard": "link to logs dashboard",
    "metricsDashboard": "link to metrics dashboard",
    "readyReplicas": "3/3",
    "state": "Ready"
  }
}
```

Všimněte si dalšího čísla `Replicas` a pole, které `AGstatus` indikuje stav skupiny dostupnosti. Pokud jsou všechny repliky nahoru a synchronizovány, je tato hodnota `healthy` . 

### <a name="restore-a-database"></a>Obnovení databáze 
K obnovení databáze do skupiny dostupnosti jsou nutné další kroky. Následující kroky ukazují, jak obnovit databázi do spravované instance a přidat ji do skupiny dostupnosti. 

1. Vytvořením nové služby Kubernetes vystavte externímu koncovému bodu primární instance.

    Určete hostitele, který je hostitelem primární repliky, připojením ke spravované instanci a spuštěním:

    ```sql
    SELECT @@SERVERNAME
    ```
    Vytvořte službu Kubernetes s primární instancí spuštěním příkazu níže, Pokud cluster Kubernetes používá služby nodePort. Nahraďte `podName` názvem serveru vráceným v předchozím kroku `serviceName` s preferovaným názvem pro vytvořenou službu Kubernetes.

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=NodePort
    ```

    U služby Vyrovnávání zatížení spusťte stejný příkaz s výjimkou toho, že je vytvořen typ služby `LoadBalancer` . Například: 

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=LoadBalancer
    ```

    Tady je příklad tohoto příkazu spuštěného v rámci služby Azure Kubernetes, kde hostitel pod ním je `sql2-0` :

    ```bash
    kubectl -n arc-cluster expose pod sql2-0 --port=1533  --name=sql2-0-p --type=LoadBalancer
    ```

    Získejte IP adresu služby Kubernetes, kterou jste vytvořili:

    ```bash
    kubectl get services -n <namespaceName>
    ```
2. Obnovte databázi do koncového bodu primární instance.

    Přidejte záložní soubor databáze do kontejneru primární instance.

    ```console
    kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>
    ```

    Příklad

    ```console
    kubectl cp /home/WideWorldImporters-Full.bak sql2-1:var/opt/mssql/data/WideWorldImporters-Full.bak -c arc-sqlmi -n arc
    ```

    Obnovte záložní soubor databáze spuštěním příkazu níže.

    ```sql 
    RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
    WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
    ,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
    ,RECOVERY, REPLACE, STATS = 5;  
    GO
    ```
    
    Příklad

    ```sql
    RESTORE Database WideWorldImporters
    FROM DISK = '/var/opt/mssql/data/WideWorldImporters-Full.BAK'
    WITH
    MOVE 'WWI_Primary' TO '/var/opt/mssql/data/WideWorldImporters.mdf',
    MOVE 'WWI_UserData' TO '/var/opt/mssql/data/WideWorldImporters_UserData.ndf',
    MOVE 'WWI_Log' TO '/var/opt/mssql/data/WideWorldImporters.ldf',
    MOVE 'WWI_InMemory_Data_1' TO '/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1',
    RECOVERY, REPLACE, STATS = 5;  
    GO
    ```

3. Přidejte databázi do skupiny dostupnosti.

    Aby bylo možné databázi přidat do nástroje AG, musí být spuštěna v režimu úplného obnovení a musí být provedena záloha protokolu. Spuštěním následujících příkazů TSQL přidejte obnovenou databázi do skupiny dostupnosti.

    ```sql
    ALTER DATABASE <databaseName> SET RECOVERY FULL;
    BACKUP DATABASE <databaseName> TO DISK='<filePath>'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE <databaseName>
    ```

    Následující příklad přidá databázi s názvem `WideWorldImporters` , která byla obnovena v instanci:

    ```sql
    ALTER DATABASE WideWorldImporters SET RECOVERY FULL;
    BACKUP DATABASE WideWorldImporters TO DISK='/var/opt/mssql/data/WideWorldImporters.bak'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE WideWorldImporters
    ```

> [!IMPORTANT]
> Jako osvědčený postup byste měli vyčistit odstraněním služby Kubernetes vytvořené výše spuštěním tohoto příkazu:
>
>```bash
>kubectl delete svc sql2-0-p -n arc
>```

### <a name="limitations"></a>Omezení

Skupiny dostupnosti spravované instance s podporou ARC Azure mají stejná [omezení jako skupiny dostupnosti clusteru velkých objemů dat. Pokud se chcete dozvědět víc, klikněte sem.](/sql/big-data-cluster/deployment-high-availability#known-limitations)

## <a name="next-steps"></a>Další kroky

Další informace o [funkcích a možnostech spravované instance SQL ARC s podporou Azure](managed-instance-features.md)
