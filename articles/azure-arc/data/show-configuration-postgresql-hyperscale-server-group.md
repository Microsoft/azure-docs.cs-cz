---
title: Zobrazit konfiguraci skupiny serverů PostgreSQL s podporou ARC
titleSuffix: Azure Arc enabled data services
description: Zobrazit konfiguraci skupiny serverů PostgreSQL s podporou ARC
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 716759fd6542cd473c236992ac88b69bfe5d0a66
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148020"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>Zobrazit konfiguraci skupiny serverů PostgreSQL s podporou ARC

Tento článek vysvětluje, jak zobrazit konfiguraci skupin serverů. V takovém případě předpokládáme, že budete klást otázky, na které se můžete zeptat a které na ně odpoví. V některých případech může docházet k několika platným odpovědím. Tento článek se rozvíjejí z nejběžnějších nebo užitečných. Tyto otázky seskupí podle motivu:

- z Kubernetesho bodu zobrazení
- z pohledu datových služeb s podporou ARC Azure

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Z Kubernetesho bodu zobrazení

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>Kolik lusků používá PostgreSQL s podporou rozšíření Azure ARC?

Vypíše seznam prostředků Kubernetes typu Postgres. Spusťte příkaz:

```console
kubectl get postgresqls [-n <namespace name>]
```

Výstup tohoto příkazu zobrazuje seznam skupin serverů, které byly vytvořeny. U každého z nich označuje počet lusků. Například:

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

Tento příklad ukazuje, že se vytvoří 2 skupiny serverů a každý se spustí 3 lusky (1 koordinátor + 2 procesy). To znamená, že skupiny serverů vytvořené v tomto řadiči dat ARC Azure používají 6 lusků.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Jaké lusky používá PostgreSQL skupiny serverů s podporou rozšíření Azure ARC?

Spusťte tento příkaz:

```console
kubectl get pods [-n <namespace name>]
```

Vrátí seznam lusků. V závislosti na názvech, které jste pro tyto skupiny serverů zadali, se zobrazí lusky používané vašimi skupinami serverů. Například:

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

V tomto příkladu jsou šesté lusky, které jsou hostiteli dvou vytvořených skupin serverů:
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>Jaká skupina serverů se používá pro roli skupiny serveru?

Libovolný název pod příponou, `-0` který představuje uzel koordinátora. Libovolný název uzlu s příponou, `-x` kde je 1 nebo větší, je pracovní uzel. V příkladu výše:
- Koordinátoři jsou: `postgres01-0` , `postgres02-0`
- Pracovní procesy jsou: `postgres01-2` , `postgres01-2` , `postgres02-1` , `postgres02-2`

### <a name="what-is-the-status-of-the-pods"></a>Jaký je stav v luskech?

Spusťte `kubectl get pods` a podívejte se na sloupec `STATUS`

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>Které trvalé deklarace identity (PVC) se používají? 

Chcete-li zjistit, které virtuální okruhy jsou používány a které jsou používány pro data, protokoly a zálohy, spusťte příkaz: 

```console
kubectl get pvc [-n <namespace name>]
```

Ve výchozím nastavení předpona názvu okruhu PVC indikuje jeho využití:

- `backups-`...: je trvalý virtuální okruh, který se používá pro zálohy.
- `data-`...: je trvalý virtuální okruh použitý pro datové soubory.
- `logs-`...: je trvalý virtuální okruh, který se používá pro soubory transakčních protokolů/WAL.

Například:

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Z pohledu datových služeb s podporou ARC Azure:

* Kolik skupin serverů se vytváří v řadiči dat ARC?
* Jaké jsou jejich názvy?
* Kolik pracovních uzlů používá?
* Jakou verzi Postgres se spouštějí?

Použijte některý z následujících příkazů.
- **S kubectl:**

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   Například vytvoří výstup uvedený níže, kde je každý řádek `servergroup` . Struktura názvu v následujícím zobrazení je tvořena následujícím způsobem:

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   Výše uvedený výstup zobrazuje 2 skupiny serverů, které jsou Postgres verze 12. Pokud byla verze Postgres 11, název CRD by byl místo toho postgresql-11.arcdata.microsoft.com.

   Každý z nich běží na 3 uzlech a luskech: 1 koordinátor a 2 pracovní procesy.

- **S azdata:**

Spusťte následující příkaz. Výstup zobrazuje podobné informace, které kubectl ukazuje:

   ```console
   azdata arc postgres server list

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>Kolik paměti a virtuální jádra se používá a jaká rozšíření se vytvořila pro skupinu?

Spusťte některý z následujících příkazů

**S Kubectl:**

Použijte kubectl k popisu prostředků Postgres. K tomu budete potřebovat svůj druh (název prostředku Kubernetes (CRD) pro odpovídající verzi Postgres, jak je vidět výše), a název skupiny serverů.
Obecný formát tohoto příkazu je:

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

Například:

```console
kubectl describe postgresql-12/postgres02
```

V tomto příkazu se zobrazuje konfigurace skupiny serverů:

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Shards:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

Řekněme, že se v popisu výše uvedeného postupu vyvolají konkrétní body zájmu `servergroup` . Co nám o této skupině serverů říkáme?

- Je ve verzi 12 Postgres: 
   > Plnění         `postgresql-12`
- Byl vytvořen během měsíce ze srpna 2020:
   > Časové razítko vytvoření:  `2020-08-31T21:01:07Z`
- V této skupině serverů se vytvořila dvě rozšíření Postgres: `citus` a. `pg_stat_statements`
   > Modul: rozšíření: název:  `citus` Název:  `pg_stat_statements`
- Používá dva pracovní uzly
   > Škálování: horizontálních oddílů:  `2`
- Je zaručeno, že pro každý uzel bude použit 1 procesor/vCore a 512 MB paměti RAM. Bude používat více než 4 procesor/virtuální jádra a 1024MB paměti:
   > Plánování: výchozí: prostředky: omezení: procesor: 4 paměť: požadavky 1024Mi: procesor: 1 paměť: 512Mi
 - Je k dispozici pro dotazy a nemá žádný problém. Všechny uzly jsou v provozu a jsou spuštěny:
   > Stav:... Připravené lusky: 3/3 stav: připraveno

**S azdata:**

Obecný formát příkazu je:

```console
azdata arc postgres server show -n <server group name>
```

Například:

```console
azdata arc postgres server show -n postgres02
```

Vrátí níže uvedený výstup ve formátu a obsahu, který je velmi podobný řetězci vrácenému funkcí kubectl.

```console
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "shards": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>Další kroky
- [Přečtěte si o konceptech PostgreSQL s povoleným rozšířením Azure ARC](concepts-distributed-postgres-hyperscale.md)
- [Přečtěte si o možnostech horizontálního navýšení kapacity (Přidání pracovních uzlů) skupiny serverů.](scale-out-postgresql-hyperscale-server-group.md)
- [Přečtěte si o tom, jak horizontální navýšení kapacity (zvýšení nebo snížení velikosti paměti nebo virtuální jádra) skupiny serverů](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [Přečtěte si o konfiguraci úložiště](storage-configuration.md)
- [Přečtěte si, jak monitorovat instanci databáze.](monitor-grafana-kibana.md)
- [Použijte rozšíření PostgreSQL ve skupině serverů PostgreSQL s podporou škálování na úrovni Azure ARC](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Konfigurace zabezpečení pro skupinu serverů PostgreSQL Hyperscale s podporou služby Azure Arc](configure-security-postgres-hyperscale.md)
