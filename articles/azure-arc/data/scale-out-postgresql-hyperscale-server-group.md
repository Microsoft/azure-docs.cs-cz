---
title: Horizontální navýšení kapacity Azure Database for PostgreSQL skupině serverů s možností škálování
description: Horizontální navýšení kapacity Azure Database for PostgreSQL skupině serverů s možností škálování
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: df0620308fab2e813fe3802dc7effb9dc1ce226c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91285379"
---
# <a name="scale-out-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>Horizontální navýšení kapacity PostgreSQL skupiny serverů s rozšířením Azure ARC přidáním dalších pracovních uzlů
Tento dokument vysvětluje, jak škálovat PostgreSQL skupinu serverů s povoleným rozšířením Azure ARC. Provede vás prostřednictvím scénáře. **Pokud nechcete spouštět přes scénář a chcete jen si přečíst informace o tom, jak horizontální navýšení kapacity, přejděte na [horizontální](#scale-out)** navýšení kapacity odstavce.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>Začínáme
Pokud už jste obeznámeni s modelem škálování s povoleným PostgreSQLm škálováním na úrovni služby Azure ARC nebo Azure Database for PostgreSQL škálování na více verzí (Citus), můžete tento odstavec přeskočit. Pokud nejste, doporučujeme začít tím, že si přečtete tento model škálování na stránce dokumentace Azure Database for PostgreSQL Citus (škálování). Azure Database for PostgreSQL Citus () je stejná technologie, která je hostovaná jako služba v Azure (platforma jako služba označovaná také jako PAAS) namísto nabízená v rámci Data Services s povoleným rozšířením Azure ARC:
- [Uzly a tabulky](../../postgresql/concepts-hyperscale-nodes.md)
- [Určení typu aplikace](../../postgresql/concepts-hyperscale-app-type.md)
- [Volba distribučního sloupce](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [Společné umístění tabulek](../../postgresql/concepts-hyperscale-colocation.md)
- [Distribuce a úprava tabulek](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [Návrh databáze s více klienty](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [Návrh řídicího panelu pro analýzu v reálném čase](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* V dokumentech výše přeskočte oddíly, **které se přihlásí k Azure Portal**, & **Vytvoření Azure Database for PostgreSQL-Citus (škálování)**. Implementujte zbývající kroky v nasazení ARC Azure. Tyto části jsou specifické pro Azure Database for PostgreSQL Citus (PaaS), které nabízíme jako službu v cloudu Azure, ale ostatní části dokumentů jsou přímo použitelné pro PostgreSQL škálování na úrovni Azure ARC.

## <a name="scenario"></a>Scénář
Tento scénář odkazuje na PostgreSQL skupinu serverů s škálovatelným škálováním, která se vytvořila jako příklad v dokumentaci k [Vytvoření skupiny serverů PostgreSQL s podporou škálování na úrovni Azure ARC](create-postgresql-hyperscale-server-group.md) .

### <a name="load-test-data"></a>Načtení testovacích dat
Scénář používá ukázku veřejně dostupných dat GitHubu, která jsou k dispozici na [webu Citus data](https://www.citusdata.com/) (Citus data jsou součástí Microsoftu).

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Připojení ke skupině serverů s povoleným PostgreSQLým rozšířením Azure ARC

##### <a name="list-the-connection-information"></a>Výpis informací o připojení
Připojte se ke skupině serverů s povoleným PostgreSQLem s rozšířením Azure ARC tím, že nejprve získáte informace o připojení: obecný formát tohoto příkazu je
```console
azdata arc postgres endpoint list -n <server name>
```
Příklad:
```console
azdata arc postgres endpoint list -n postgres01
```

Příklad výstupu:

```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

##### <a name="connect-with-the-client-tool-of-your-choice"></a>Připojte se pomocí nástroje klienta podle vašeho výběru.

Spusťte následující dotaz, abyste ověřili, že aktuálně máte dva (nebo více pracovních uzlů s škálováním na více pracovníků), z nichž každý odpovídá Kubernetes pod:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>Vytvoření ukázkového schématu
Vytvořte dvě tabulky spuštěním následujícího dotazu:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

JSONB je datový typ JSON v binárním formátu v PostgreSQL. Ukládá flexibilní schéma v jednom sloupci a v PostgreSQL. Schéma bude mít GIN index, aby bylo možné indexovat každý klíč a hodnotu v něm. GIN index je rychlejší a snadno se dotazuje s různými podmínkami přímo na této datové části. A proto před nahráním našich dat vytvoříme několik indexů:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Pokud chcete horizontálních oddílů standardní tabulky, spusťte dotaz pro každou tabulku. Zadejte tabulku, kterou chceme horizontálních oddílů, a klíč, na který ho chcete horizontálních oddílů. Horizontálních oddílů se v tabulce události a uživatelé na user_id:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>Načtení ukázkových dat
Načíst data pomocí kopie... Z PROGRAMU:

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>Vytváření dotazů na data
A nyní měříte, jak dlouho trvá jednoduchý dotaz se dvěma uzly:

```sql
SELECT COUNT(*) FROM github_events;
```
Poznamenejte si dobu provádění dotazu.


## <a name="scale-out"></a>Horizontální navýšení kapacity
Obecný formát příkazu pro horizontální navýšení kapacity je:
```console
azdata arc postgres server edit -n <server group name> -w <target number of worker nodes>
```

> [!CAUTION]
> Verze Preview nepodporuje opětovné navýšení kapacity. Například ještě není možné snížit počet pracovních uzlů. V takovém případě je třeba data extrahovat nebo zálohovat, vyřadit skupinu serverů, vytvořit novou skupinu serverů s méně pracovními uzly a následně data importovat.

V tomto příkladu zvýšíme počet pracovních uzlů od 2 na 4 spuštěním následujícího příkazu:

```console
azdata arc postgres server edit -n postgres01 -w 4
```

Po přidání uzlů se zobrazí stav čekání na skupinu serverů. Příklad:
```console
azdata arc postgres server list
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

Až budou uzly k dispozici, horizontálních oddílů se automaticky spustí nástroj pro vyrovnávání zatížení a znovu distribuuje data na nové uzly. Operace škálování na více instancí je operace online. I když jsou uzly přidány a data jsou znovu distribuována napříč uzly, data zůstávají k dispozici pro dotazy.

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>Ověření nového tvaru skupiny serverů (volitelné)
Pomocí kterékoli z následujících metod ověřte, že skupina serverů teď používá další pracovní uzly, které jste přidali.

#### <a name="with-azdata"></a>S azdata:
Spusťte příkaz:
```console
azdata arc postgres server list
```

Vrátí seznam skupin serverů vytvořených ve vašem oboru názvů a označuje jejich počet pracovních uzlů. Příklad:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>S kubectl:
Spusťte příkaz:
```console
kubectl get postgresql-12
```

Vrátí seznam skupin serverů vytvořených ve vašem oboru názvů a označuje jejich počet pracovních uzlů. Příklad:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```
> [!NOTE]
> Pokud jste vytvořili skupinu serverů verze 11 PostgreSQL a ne 12, spusťte následující příkaz: _kubectl Get PostgreSQL-11_

#### <a name="with-a-sql-query"></a>S dotazem SQL:
Připojte se ke skupině serverů pomocí nástroje klienta podle vašeho výběru a spusťte následující dotaz:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>Návrat do scénáře

Chcete-li porovnat dobu provádění dotazu Select Count s ukázkovou datovou sadou, použijte stejný dotaz na počet. Dá se použít v rámci čtyř pracovních uzlů bez jakýchkoli změn v příkazu SQL.

```sql
SELECT COUNT(*) FROM github_events;
```
Všimněte si doby spuštění.


> [!NOTE]
> V závislosti na vašem prostředí – například pokud jste nasadili skupinu testovacích serverů s nástrojem `kubeadm` na jednom virtuálním počítači s jedním uzlem, může se v době provádění zobrazit mírné zlepšení. Chcete-li získat lepší představu o typu zlepšení výkonu, které byste mohli dosáhnout s PostgreSQLm škálováním na základě služby Azure ARC, podívejte se na následující krátká videa:
>* [Vysoce výkonné HTAP se škálováním Azure PostgreSQL (Citus)](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [Sestavování aplikací HTAP pomocí Pythonu & Azure PostgreSQL s měřítkem (Citus)](https://www.youtube.com/watch?v=YDT8_riLLs0)


## <a name="next-steps"></a>Další kroky

- Přečtěte si o tom [, jak horizontální navýšení a snížení kapacity (paměť, virtuální jádra) pro skupinu serverů s podporou škálování Azure na PostgreSQL](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) .
- Přečtěte si o nastavení parametrů serveru ve skupině serverů s povoleným PostgreSQLm rozšířením Azure ARC.
- Přečtěte si téma koncepty a návody Azure Database for PostgreSQLho škálování pro distribuci vašich dat napříč několika PostgreSQL uzly a využijte výhod všech možností škálování Azure Database for Postgres. :
    * [Uzly a tabulky](../../postgresql/concepts-hyperscale-nodes.md)
    * [Určení typu aplikace](../../postgresql/concepts-hyperscale-app-type.md)
    * [Volba distribučního sloupce](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Společné umístění tabulek](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuce a úprava tabulek](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Návrh databáze s více klienty](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Návrh řídicího panelu pro analýzu v reálném čase](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \* V dokumentech výše přeskočte oddíly, **které se přihlásí k Azure Portal**, & **Vytvoření Azure Database for PostgreSQL-Citus (škálování)**. Implementujte zbývající kroky v nasazení ARC Azure. Tyto části jsou specifické pro Azure Database for PostgreSQL Citus (PaaS), které nabízíme jako službu v cloudu Azure, ale ostatní části dokumentů jsou přímo použitelné pro PostgreSQL škálování na úrovni Azure ARC.

- [Koncepty konfigurace úložiště a Kubernetes úložiště](storage-configuration.md)
- [Rozšiřování deklarací trvalých svazků](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Model prostředků Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
