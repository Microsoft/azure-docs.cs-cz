---
title: 'Rychlý Start: Vytvoření skupiny serverů – Citus (škálování na serveru) – Azure Database for PostgreSQL'
description: Rychlý Start pro vytváření a dotazování distribuovaných tabulek v Azure Database for PostgreSQL Citus ().
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/17/2020
ms.openlocfilehash: e43e20ceb5e84d652fee9ca4db6d5dc871ed1e4f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91268448"
---
# <a name="quickstart-create-a-hyperscale-citus-server-group-in-the-azure-portal"></a>Rychlý Start: Vytvoření skupiny serverů Citus () v Azure Portal

Azure Database for PostgreSQL je spravovaná služba, pomocí které spouštíte, spravujete a škálujete vysoce dostupné databáze PostgreSQL v cloudu. V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit skupinu serverů Azure Database for PostgreSQL-Citus (škálovatelný škálování). Budete zkoumat distribuovaná data: horizontálního dělení tabulky napříč uzly, ingestování ukázkových dat a spouštění dotazů, které se spouštějí na více uzlech.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Vytváření a distribuce tabulek

Po připojení k uzlu koordinátora s jednoduchým škálováním pomocí psql můžete provést některé základní úlohy.

V rámci serverů Citus () Existují tři typy tabulek:

- Distribuované nebo horizontálně dělené tabulky (rozprostření za účelem zvýšení kapacity pro výkon a paralelní zpracování)
- Referenční tabulky (zachované více kopií)
- Místní tabulky (často používané pro interní tabulky správců)

V tomto rychlém startu se primárně zaměřujeme na distribuované tabulky a seznámíte se s nimi.

Datový model, se kterým budeme pracovat, je jednoduchý: data o uživatelích a událostech z GitHubu. Události zahrnují vytváření větví, potvrzení Git souvisejících s organizací a další.

Jakmile se připojíte přes psql, pojďme vytvořit tabulky. V konzole psql spusťte:

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

`payload`Pole `github_events` má datový typ JSONB. JSONB je datový typ JSON v binárním formátu v Postgres. Datový typ usnadňuje ukládání flexibilního schématu do jednoho sloupce.

Postgres může `GIN` pro tento typ vytvořit index, který bude indexovat každý klíč a hodnotu v rámci něj. S indexem se dá rychle a snadno dotazovat datovou část s různými podmínkami. Pojďme dopředu a vytvořit několik indexů, než načteme naše data. V psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

V dalším kroku převezmeme tyto tabulky Postgres v uzlu koordinátora a oznámíme tak škálování (Citus), aby se na ně horizontálních oddílůi napříč zaměstnanci. Uděláte to tak, že spustíte dotaz pro každou tabulku, která určuje klíč, na který se má horizontálních oddílů. V aktuálním příkladu horizontálních oddílů události a uživatele v tabulce `user_id` :

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

Teď jsme připraveni načíst data. V psql pořád pro stažení souborů prostředí.

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Pak načtěte data ze souborů do distribuovaných tabulek:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Spouštění dotazů

Teď je čas na zábavu, ve kterém jsou ve skutečnosti spuštěné nějaké dotazy. Pojďme začít jednoduchým `count (*)` způsobem a zjistit, kolik dat jsme načetli:

```sql
SELECT count(*) from github_events;
```

Ty, které byly k dispracovanému. Vrátíme se k tomuto uspořádání agregace, ale teď se podíváme na několik dalších dotazů. Ve sloupci JSONB `payload` je dobrým bitem dat, ale liší se v závislosti na typu události. `PushEvent` události obsahují velikost, která zahrnuje počet jedinečných potvrzení pro vložení. Dá se použít k vyhledání celkového počtu potvrzení za hodinu:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Doposud dotazy zahrnovaly jenom události GitHubu \_ , ale tyto informace můžeme kombinovat s uživateli GitHubu \_ . Vzhledem k tomu, že jsme horizontálně dělené uživatele i události na stejný identifikátor ( `user_id` ), řádky obou tabulek s ID odpovídajícího uživatele budou společně [umístěny](concepts-hyperscale-colocation.md) na stejných uzlech databáze a lze je snadno připojit.

Pokud se připojíme k `user_id` Citus, může se spuštění spojení do horizontálních oddílů spustit souběžně na pracovních uzlech. Pojďme například najít uživatele, kteří vytvořili největší počet úložišť:

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině serverů. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu serverů. Stiskněte tlačítko **Odstranit** na stránce **Přehled** pro skupinu serverů. Po zobrazení výzvy na místní stránce potvrďte název skupiny serverů a klikněte na tlačítko poslední **Odstranit** .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak zřídit skupinu serverů (Citus). K němu jste se připojili pomocí psql, vytvořili schéma a distribuovaná data.

- Postupujte podle kurzu pro [vytváření škálovatelných víceklientské aplikací](https://aka.ms/hyperscale-tutorial-multi-tenant) .
- Určení nejlepší [počáteční velikosti](howto-hyperscale-scaling.md#picking-initial-size) pro skupinu serverů
