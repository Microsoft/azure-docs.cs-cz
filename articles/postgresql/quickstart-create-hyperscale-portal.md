---
title: Azure Database for PostgreSQL – Citus (Preview) – rychlý Start
description: Rychlý Start pro vytváření a dotazování distribuovaných tabulek v Azure Database for PostgreSQL Citus (Preview).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 6e9e7d884b7580d7655921134a7ab63b0b1b0dd6
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899989"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Rychlý start: Vytvoření Azure Database for PostgreSQL – Citus (Preview) v Azure Portal

Azure Database for PostgreSQL je spravovaná služba, pomocí které spouštíte, spravujete a škálujete vysoce dostupné databáze PostgreSQL v cloudu. V tomto rychlém startu se dozvíte, jak vytvořit skupinu serverů Azure Database for PostgreSQL (Citus) (ve verzi Preview) pomocí Azure Portal. Budete zkoumat distribuovaná data: horizontálního dělení tabulky napříč uzly, ingestování ukázkových dat a spouštění dotazů, které se spouštějí na více uzlech.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Vytváření a distribuce tabulek

Po připojení k uzlu koordinátora s jednoduchým škálováním pomocí psql můžete provést některé základní úlohy.

Na serverech s škálovatelným škálováním existují tři typy tabulek:

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

`payload` Pole`github_events` má datový typ JSONB. JSONB je datový typ JSON v binárním formátu v Postgres. Datový typ usnadňuje ukládání flexibilního schématu do jednoho sloupce.

Postgres může pro tento `GIN` typ vytvořit index, který bude indexovat každý klíč a hodnotu v rámci něj. S indexem se dá rychle a snadno dotazovat datovou část s různými podmínkami. Pojďme dopředu a vytvořit několik indexů, než načteme naše data. V psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

V dalším kroku převezmeme tyto tabulky Postgres v uzlu koordinátora a oznámíme tak, aby se horizontálních oddílů napříč zaměstnanci. Uděláte to tak, že spustíte dotaz pro každou tabulku, která určuje klíč, na který se má horizontálních oddílů. V aktuálním příkladu horizontálních oddílů události a uživatele v `user_id`tabulce:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Teď jsme připraveni načíst data. V psql pořád pro stažení souborů prostředí.

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Pak načtěte data ze souborů do distribuovaných tabulek:

```sql
\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Spouštění dotazů

Teď je čas na zábavu, ve kterém jsou ve skutečnosti spuštěné nějaké dotazy. Pojďme začít jednoduchým `count (*)` způsobem a zjistit, kolik dat jsme načetli:

```sql
SELECT count(*) from github_events;
```

Ty, které byly k dispracovanému. Vrátíme se k tomuto uspořádání agregace, ale teď se podíváme na několik dalších dotazů. Ve sloupci JSONB `payload` je dobrým bitem dat, ale liší se v závislosti na typu události. `PushEvent`události obsahují velikost, která zahrnuje počet jedinečných potvrzení pro vložení. Dá se použít k vyhledání celkového počtu potvrzení za hodinu:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Doposud dotazy zahrnovaly jenom události GitHubu\_, ale tyto informace můžeme kombinovat s uživateli GitHubu.\_ Vzhledem k tomu, že jsme horizontálně dělené uživatele i události na stejný`user_id`identifikátor (), řádky obou tabulek s ID odpovídajícího uživatele budou společně [umístěny](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) na stejných uzlech databáze a lze je snadno připojit.

V případě, že `user_id`se připojíme k systému, může být do horizontálních oddílů spuštěno souběžné spouštění. Pojďme například najít uživatele, kteří vytvořili největší počet úložišť:

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

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se dozvěděli, jak zřídit skupinu serverů (Citus). K němu jste se připojili pomocí psql, vytvořili schéma a distribuovaná data.

Dále postupujte podle kurzu a sestavte škálovatelné víceklientské aplikace.
> [!div class="nextstepaction"]
> [Návrh databáze s více klienty](https://aka.ms/hyperscale-tutorial-multi-tenant)
