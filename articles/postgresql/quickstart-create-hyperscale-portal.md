---
title: Vytváření distribuovaných tabulek – Hyperscale (Citus) – databáze Azure pro PostgreSQL
description: Rychlý start k vytvoření a dotazování distribuovaných tabulek v Azure Database for PostgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 02e009e6fff2e717693d1579d409199ab179d941
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241511"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-in-the-azure-portal"></a>Úvodní příručka: Vytvoření databáze Azure pro PostgreSQL – hyperškálování (Citus) na webu Azure Portal

Azure Database for PostgreSQL je spravovaná služba, pomocí které spouštíte, spravujete a škálujete vysoce dostupné databáze PostgreSQL v cloudu. Tento úvodní příručka ukazuje, jak vytvořit skupinu serverů Azure Database for PostgreSQL – Hyperscale (Citus) pomocí portálu Azure. Budete zkoumat distribuovaná data: rozdělení tabulek mezi uzly, ingestování ukázkových dat a spouštění dotazů, které se spouštějí na více uzlech.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Vytváření a distribuce tabulek

Po připojení k uzlu hyperškálování koordinátora pomocí psql, můžete dokončit některé základní úkoly.

V rámci serverů Hyperscale existují tři typy tabulek:

- Distribuované nebo horizontální rozdělení tabulek (rozprostřené pro změnu měřítka pro výkon a paralelizaci)
- Referenční tabulky (více uchovávaných kopií)
- Místní tabulky (často používané pro interní tabulky správců)

V tomto rychlém startu se primárně zaměříme na distribuované tabulky a seznámení se s nimi.

Datový model, se kterým budeme pracovat, je jednoduchý: uživatelská data a data událostí z GitHubu. Události zahrnují vytvoření rozvinec, git commits vztahující se k organizaci a další.

Jakmile se připojíte přes psql, pojďme vytvořit naše tabulky. V psql konzole spustit:

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

Pole `payload` `github_events` má datový typ JSONB. JSONB je datový typ JSON v binární podobě v Postgres. Datový typ usnadňuje ukládání flexibilníschéma v jednom sloupci.

Postgres můžete `GIN` vytvořit index na tento typ, který bude indexovat každý klíč a hodnotu v něm. S indexem se stává rychlé a snadné dotaz datové části s různými podmínkami. Pojďme do toho a vytvořit několik indexů, než načteme naše data. V psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Dále vezmeme ty tabulky Postgres na uzlu koordinátora a řekneme Hyperscale, aby je proměňovali mezi pracovníky. Chcete-li tak učinit, spustíme dotaz pro každou tabulku určující klíč pro jeho horizontál. V aktuálním příkladu budeme střep události `user_id`a uživatelé tabulka na :

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Jsme připraveni načíst data. V psql stále, shell se stáhnout soubory:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Dále načtěte data ze souborů do distribuovaných tabulek:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Spouštění dotazů

Nyní je čas na zábavnou část, která skutečně běží některé dotazy. Začněme s jednoduchým `count (*)` vidět, kolik dat jsme načetli:

```sql
SELECT count(*) from github_events;
```

To fungovalo dobře. Vrátíme se k tomuto druhu agregace v trochu, ale teď se podívejme na několik dalších dotazů. Ve sloupci JSONB `payload` je dobrý kousek dat, ale liší se v závislosti na typu události. `PushEvent`události obsahují velikost, která obsahuje počet odlišných potvrzení pro nabízenou položku. Můžeme ji použít k nalezení celkového počtu potvrzení za hodinu:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Zatím dotazy zahrnovaly výhradně události\_githubu, ale můžeme tyto informace\_kombinovat s uživateli githubu. Vzhledem k tomu, že jsme rozděleni`user_id`mezi uživatele a události na stejný identifikátor ( ), řádky obou tabulek s odpovídajícími ID uživatele budou [společně umístěny](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) na stejných uzlech databáze a lze snadno připojit.

Pokud se `user_id`připojíme na , Hyperscale můžete tlačit spuštění spojení dolů do horizontálních oddílů pro provádění paralelně na pracovní uzly. Například pojďme najít uživatele, kteří vytvořili největší počet úložišť:

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

V předchozích krocích jste vytvořili prostředky Azure ve skupině serverů. Pokud neočekáváte, že budete tyto prostředky v budoucnu potřebovat, odstraňte skupinu serverů. Stiskněte tlačítko **Odstranit** na stránce **Přehled** pro skupinu serverů. Po zobrazení výzvy na vyskakovací stránce potvrďte název skupiny serverů a klepněte na tlačítko **Konečné odstranění.**

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak zřídit skupinu serverů Hyperscale (Citus). Připojili jste k němu psql, vytvořili schéma a distribuovaná data.

Dále postupujte podle kurzu k vytvoření škálovatelných víceklientských aplikací.
> [!div class="nextstepaction"]
> [Návrh víceklientské databáze](https://aka.ms/hyperscale-tutorial-multi-tenant)
