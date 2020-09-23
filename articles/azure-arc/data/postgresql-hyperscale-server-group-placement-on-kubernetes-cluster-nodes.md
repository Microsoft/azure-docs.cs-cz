---
title: Umístění PostgreSQL skupiny serverů s škálovatelným škálováním na uzlech clusteru Kubernetes
description: Vysvětluje, jak PostgreSQL instance vytvářející PostgreSQL skupinu serverů s škálovatelným škálováním v Kubernetes uzlech clusteru.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 5da00916a3f7a6a3685b1de1c56dd032355e28fa
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935823"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Umístění skupiny serverů PostgreSQL s podporou rozšíření Azure ARC

V tomto článku si ukážeme, jak na fyzických uzlech clusteru Kubernetes, který je hostuje, na fyzických uzlech clusteru, který je hostitelem PostgreSQL instancí Azure ARC s povoleným PostgreSQL na úrovni serveru. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>Konfigurace

V tomto příkladu používáme cluster Azure Kubernetes Service (AKS), který má čtyři fyzické uzly. 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="cluster AKS se čtyřmi uzly v Azure Portal":::

Seznam fyzických uzlů clusteru Kubernetes spuštěním příkazu:

```console
kubectl get nodes
```

Zobrazuje čtyři fyzické uzly v rámci clusteru Kubernetes:

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

Architekturu lze znázornit jako:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="Logická reprezentace 4 uzlů seskupených v clusteru Kubernetes":::

Cluster Kubernetes hostuje jeden řadič dat ARC Azure a jednu skupinu serverů s podporou rozšíření Azure ARC PostgreSQL. Tato skupina serverů se skládá ze tří instancí PostgreSQL: jednoho koordinátora a dva pracovní procesy.

Uveďte lusky pomocí příkazu:

```console
kubectl get pods -n arc3
```
Což generuje následující výstup:

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          9h
postgres01-1         3/3     Running   0          9h
postgres01-2         3/3     Running   0          9h
```
Každé z těchto lusků hostuje instanci PostgreSQL. Společně tvoří skupinu serverů s povoleným PostgreSQLm rozšířením Azure ARC:

```output
Pod name    Role in the server group
postgres01-0            Coordinator
postgres01-1    Worker
postgres01-2    Worker
```

## <a name="placement"></a>Umístění
Pojďme se podívat, jak Kubernetes umístí lusky skupiny serverů. Popište každý pod a určete, na kterém fyzickém uzlu clusteru Kubernetes se umístí. Pro koordinátora například spusťte následující příkaz:

```console
kubectl describe pod postgres01-0 -n arc3
```

Což generuje následující výstup:

```output
Name:         postgres01-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

Po spuštění tohoto příkazu pro každé z obou lusků shrnujeme aktuální umístění jako:

| Role skupiny serverů|Skupina serverů pod|Kubernetes fyzický uzel hostující pod |
|--|--|--|
| Zaměstnanec|postgres01-1|AKS-neznámá-42715708-vmss000002 |
| Zaměstnanec|postgres01 – 2|AKS-neznámá-42715708-vmss000003 |

A Všimněte si také v popisu lusků, názvů kontejnerů, které každý pod hostuje. Pro druhý pracovní proces například spusťte následující příkaz:

```console
kubectl describe pod postgres01-2 -n arc3
```

Což generuje následující výstup:

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

Každý z nich, který je součástí PostgreSQL skupiny serverů s podporou ARC Azure, je hostitelem následujících tří kontejnerů:

|Kontejnery|Description
|----|----|
|`Fluentbit` |Data * kolektor protokolů: https://fluentbit.io/
|`Postgres`|Část instance PostgreSQL skupiny serverů s povoleným PosgreSQLm rozšířením Azure ARC
|`Telegraf` |Kolektor metrik: https://www.influxdata.com/time-series-platform/telegraf/

Architektura vypadá takto:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="3 lusky umístěné na samostatných uzlech":::

To znamená, že v tomto okamžiku je každá instance PostgreSQL tvořící PostgreSQLou skupinu serverů s podporou Azure ARC hostovaná na konkrétním fyzickém hostiteli v kontejneru Kubernetes. Toto je nejlepší konfigurace, která vám umožní dosáhnout co nejvyššího výkonu ze skupiny serverů PostgreSQL s povoleným rozšířením Azure ARC jako každá role (koordinátor a pracovní procesy) pomocí prostředků každého fyzického uzlu. Tyto prostředky nejsou sdíleny mezi několika PostgreSQL rolemi.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>Horizontální navýšení kapacity PostgreSQL s povoleným rozšířením Azure ARC

Teď nahorizontální navýšení kapacity pro přidání třetího pracovního uzlu do skupiny serverů a sledování, co se stane. Vytvoří čtvrtou instanci PostgreSQL, která bude hostována ve čtvrtém pod.
K horizontálnímu navýšení kapacity spusťte příkaz:

```console
azdata arc postgres server edit --name postgres01 --workers 3
```

Který generuje následující výstup:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Seznam skupin serverů nasazených v řadiči dat ARC Azure a ověření, že skupina serverů teď používá tři pracovní procesy. Spusťte příkaz:

```console
azdata arc postgres server list
```

A pozor, aby bylo možné škálovat ze dvou pracovních procesů na tři pracovní procesy:

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Stejně jako dříve jsme zjistili, že skupina serverů teď používá celkem čtyři lusky:

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          11h
postgres01-1         3/3     Running   0          11h
postgres01-2         3/3     Running   0          11h
postgres01-3         3/3     Running   0          5m2s
```

A popište nový pod a určete, na které fyzické uzly clusteru Kubernetes je hostovaný.
Spusťte příkaz:

```console
kubectl describe pod postgres01-3 -n arc3
```

Chcete-li identifikovat název hostitelského uzlu:

```output
Name:         postgres01-3
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

Umístění instancí PostgreSQL ve fyzických uzlech clusteru je teď:

|Role skupiny serverů|Skupina serverů pod|Kubernetes fyzický uzel hostující pod
|-----|-----|-----
|Coordinator|postgres01-0|AKS-neznámá-42715708-vmss000000
|Zaměstnanec|postgres01-1|AKS-neznámá-42715708-vmss000002
|Zaměstnanec|postgres01 – 2|AKS-neznámá-42715708-vmss000003
|Zaměstnanec|postgres01-3|AKS-neznámá-42715708-vmss000000

A Všimněte si, že pod novým pracovníkem (postgres01-3) byl umístěn na stejném uzlu jako koordinátor. 

Architektura vypadá takto:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="Čtvrtý pod na stejném uzlu jako koordinátor":::

Proč není nový pracovní proces/uzel umístěn na zbývajícím fyzickém uzlu clusteru Kubernetes AKS-neznámá-42715708-vmss000003?

Důvodem je, že poslední fyzický uzel clusteru Kubernetes je ve skutečnosti hostitelem několika lusků, které hostují další součásti, které jsou nutné ke spuštění datových služeb s podporou ARC Azure. Kubernetes vyhodnotila, že nejlepší kandidát – v době plánování – pro hostování dalšího pracovního procesu je fyzický uzel AKS-neznámá-42715708-vmss000000. 

Pomocí stejných příkazů jako v předchozích krocích. Vidíte, co každý fyzický uzel hostuje:

|Jiné názvy lusků\* |Využití|Kubernetes fyzický uzel hostující lusky
|----|----|----
|zaváděcí nástroj – jh48b||AKS-neznámá-42715708-vmss000003
|Control – gwmbs||AKS-neznámá-42715708-vmss000002
|controldb-0||AKS-neznámá-42715708-vmss000001
|controlwd-zzjp7||AKS-neznámá-42715708-vmss000000
|logsdb-0|Elasticsearch, přijímá data z `Fluentbit` kontejneru každého pod|AKS-neznámá-42715708-vmss000003
|logsui-5fzv5||AKS-neznámá-42715708-vmss000003
|metricsdb-0|InfluxDB přijímá data z `Telegraf` kontejneru každého pod.|AKS-neznámá-42715708-vmss000000
|metricsdc-47d47||AKS-neznámá-42715708-vmss000002
|metricsdc-864kj||AKS-neznámá-42715708-vmss000001
|metricsdc-l8jkf||AKS-neznámá-42715708-vmss000003
|metricsdc-nxm4l||AKS-neznámá-42715708-vmss000000
|metricsui-4fb7l||AKS-neznámá-42715708-vmss000003
|mgmtproxy-4qppp||AKS-neznámá-42715708-vmss000002

> \* Přípona názvu pod se bude lišit v ostatních nasazeních. Tady uvádíme jenom lusky hostované v oboru názvů Kubernetes v řadiči dat ARC Azure.

Architektura vypadá takto:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="Všechny lusky v oboru názvů na různých uzlech":::

To znamená, že uzly koordinátora (pod 1) skupiny serverů s povoleným Postgresým rozšířením Azure budou sdílet stejné fyzické prostředky jako třetí pracovní uzel (pod 4) skupiny serverů. To je přijatelné, protože uzel koordinátora obvykle používá velmi málo prostředků v porovnání s tím, co může pracovní uzel používat. Od toho můžete odvodit, že byste měli pečlivě vybrat:
- velikost clusteru Kubernetes a vlastností každého z jeho fyzických uzlů (Memory, vCore)
- počet fyzických uzlů v rámci clusteru Kubernetes
- aplikace nebo úlohy, které hostuje v clusteru Kubernetes.

Kvůli neočekávanému zatížení clusteru Kubernetes se může stát, že služba Azure ARC PostgreSQL pro skupinu serverů s technologií webscale. Pokud k tomu dojde, nebudete výhodně škálovat ze své schopnosti na horizontální škálování. Výkon, který jste získali ze systému, se neshoduje s umístěním nebo fyzickými charakteristikami fyzických uzlů nebo systému úložiště. Výkon, který získáte, je také o tom, jak nakonfigurujete každý z prostředků spuštěných v clusteru Kubernetes (včetně škálování PostgreSQL s povoleným rozšířením Azure ARC), třeba u požadavků a omezení, které nastavíte pro paměť a vCore. Velikost úlohy, kterou můžete hostovat na daném clusteru Kubernetes, je relativní vzhledem k vlastnostem clusteru Kubernetes, povaze zatížení, počtu uživatelů a způsobu, jakým se provádí operace clusteru Kubernetes...

## <a name="scale-out-aks"></a>Horizontální navýšení kapacity AKS

Ukážeme, že horizontální škálování v clusteru AKS i serveru PostgreSQL s podporou ARC Azure je způsob, jak těžit z vysokého výkonu s povoleným PostgreSQL škálováním na úrovni Azure ARC.
Pojďme do clusteru AKS přidat pátý uzel:

:::row:::
    :::column:::
        Před
    :::column-end:::
    :::column:::
        Po
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="Azure Portal rozložení před":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="Azure Portal rozložení po":::
    :::column-end:::
:::row-end:::

Architektura vypadá takto:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="Logické rozložení v clusteru Kubernetes po aktualizaci":::

Pojďme se podívat na to, které části oboru názvů řadiče dat ARC jsou hostované na novém fyzickém uzlu AKS spuštěním příkazu:

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

A pojďme aktualizovat reprezentace architektury našeho systému:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="Všechny lusky v logickém diagramu clusteru":::

Můžeme si všimnout, že nový fyzický uzel clusteru Kubernetes je hostitelem jenom metrik pod, který je nezbytný pro datové služby Azure ARC. Všimněte si, že v tomto příkladu se zaměřujeme jenom na obor názvů řadiče dat ARC, Nepředstavujeme jiné lusky.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>Navýšení kapacity s povoleným PostgreSQL rozšířením Azure ARC

Pátý fyzický uzel ještě nehostuje žádné úlohy. Vzhledem k tomu, že se škáluje škálovatelné PostgreSQL škálování Azure, Kubernetes optimalizuje umístění nového PostgreSQL pod a neměl by společné umístění na fyzických uzlech, které už hostují víc úloh. Spuštěním následujícího příkazu Škálujte PostgreSQL s povoleným rozšířením Azure ARC z 3 na 4 pracovní procesy. Na konci operace bude skupina serverů vytvořená a distribuovaná napříč pěti PostgreSQL instancemi, jedním koordinátorem a čtyřmi pracovníky.

```console
azdata arc postgres server edit --name postgres01 --workers 4
```

Který generuje následující výstup:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Seznam skupin serverů nasazených v řadiči dat a ověření, že se skupina serverů teď spouští se čtyřmi pracovními procesy:

```console
azdata arc postgres server list
```

A pozor, aby bylo horizontální navýšení kapacity ze tří na čtyři pracovní procesy. 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

Stejně jako dříve jsme zjistili, že skupina serverů teď používá čtyři lusky:

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          13h
postgres01-1         3/3     Running   0          13h
postgres01-2         3/3     Running   0          13h
postgres01-3         3/3     Running   0          179m
postgres01-4         3/3     Running   0          3m13s
```

Tvar skupiny serverů je teď:

|Role skupiny serverů|Skupina serverů pod
|----|-----
|Coordinator|postgres01-0
|Zaměstnanec|postgres01-1
|Zaměstnanec|postgres01 – 2
|Zaměstnanec|postgres01-3
|Zaměstnanec|postgres01-4

Popište postgres01-4 a Identifikujte, v jakém fyzickém uzlu hostuje:

```console
kubectl describe pod postgres01-4 -n arc3
```

A sledujte, na čem se běhy spouští:

|Role skupiny serverů|Skupina serverů pod| Nulu
|----|-----|------
|Coordinator|postgres01-0|AKS-neznámá-42715708-vmss000000
|Zaměstnanec|postgres01-1|AKS-neznámá-42715708-vmss000002
|Zaměstnanec|postgres01 – 2|AKS-neznámá-42715708-vmss000003
|Zaměstnanec|postgres01-3|AKS-neznámá-42715708-vmss000000
|Zaměstnanec|postgres01-4|AKS-neznámá-42715708-vmss000004

A architektura vypadá takto:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="Kubernetes plány na nejnovější pod v uzlu s nejnižším využitím":::

Kubernetes naplánovala nový PostgreSQL pod v nejméně načteném fyzickém uzlu clusteru Kubernetes.

## <a name="summary"></a>Shrnutí

Chcete-li využít výhod škálovatelnosti a výkonu škálované skupiny serverů s povoleným obloukem Azure, měli byste se vyhnout kolize prostředků v clusteru Kubernetes:
- mezi PostgreSQL a dalšími úlohami, které jsou hostovány ve stejném clusteru Kubernetes, mezi serverem povoleným rozšířením Azure ARC a dalšími úlohami.
- mezi všemi instancemi PostgreSQL, které tvoří skupinu serverů PostgreSQL s podporou rozšíření Azure s podporou ARC

Můžete to dosáhnout několika způsoby:
- Horizontální navýšení kapacity Kubernetes i Kubernetes ARC Postgres: Zvažte horizontální horizontální navýšení kapacity clusteru stejným způsobem jako u skupiny serverů s povoleným rozšířením Azure ARC PostgreSQL. Přidejte fyzický uzel do clusteru pro každého pracovního procesu, který přidáte do skupiny serverů.
- Horizontální navýšení kapacity Postgres rozšíření na úrovni služby Azure ARC bez horizontálního navýšení kapacity Kubernetes: nastavením správného omezení prostředků (požadavků a omezení paměti a vCore) na zátěžích hostovaných v Kubernetes (zahrnuté do sady Azure ARC s podporou škálování na více instancí) umožníte společné umístění úloh na PostgreSQL a snížit riziko kolizí prostředků. Je nutné zajistit, aby fyzické vlastnosti fyzických uzlů clusteru Kubernetes mohly dodržovat omezení prostředků, které definujete. Zajistěte také, aby rovnováha zůstala v době, kdy se úlohy vyvíjejí v průběhu času nebo aby se do clusteru Kubernetes přidaly víc úloh.
- Pro ovlivnění umístění lusků použijte mechanismy Kubernetes (selektor pod, spřažení, anti-spřažení).

## <a name="next-steps"></a>Další kroky

[Horizontální navýšení kapacity PostgreSQL skupiny serverů s rozšířením Azure ARC přidáním dalších pracovních uzlů](scale-out-postgresql-hyperscale-server-group.md)
