---
title: Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) rychlý start
description: Rychlý start k vytvoření a dotazování distribuovaných tabulkách v databázi Azure pro PostgreSQL Hyperškálovatelného (Citus) (preview).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: efc3801ab03f739761a41bec754f975fe43dcd8e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65757520"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Rychlý start: Vytvoření Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) na webu Azure Portal

Azure Database for PostgreSQL je spravovaná služba, pomocí které spouštíte, spravujete a škálujete vysoce dostupné databáze PostgreSQL v cloudu. V tomto rychlém startu se dozvíte, jak vytvořit službu Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) skupiny serverů pomocí webu Azure portal. Prozkoumáte distribuovaných dat: horizontálního dělení tabulky napříč uzly ingestovat ukázkových dat a spuštění dotazů, které jsou spuštěny na několika uzlech.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Vytvoření a distribuce tabulky

Po připojení na koordinační uzel hyperškálovatelného pomocí nástroje psql se začít provádět některé základní úlohy.

V rámci Hyperškálovatelného existuje servery jsou tři typy tabulek:

- Distribuované nebo horizontálně dělené tabulky (rozprostřete umožňující škálování pro výkon a paralelizace)
- Referenční tabulky (udržuje několik kopií)
- Místní tabulky (často používají pro správu vnitřní tabulky)

V tomto rychlém startu primárně zaměříme na distribuované tabulky a získání obeznámeni s nimi.

Datový model, My budeme pracovat s je jednoduchý: data uživatele a událostí z Githubu. Události patří vytvoření forku, potvrzením git související s organizace a další.

Po připojení pomocí nástroje psql, vytvoříme našimi tabulkami. V konzole nástroje psql spustit:

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

`payload` Pole `github_events` má nastavení JSONB datatype. JSONB je datového typu JSON v binárním formátu v Postgres. Datový typ umožňuje snadno ukládat ve flexibilním schématu v jednom sloupci.

Můžete vytvořit Postgres `GIN` index u tohoto typu, které se budou indexovat každý klíč a hodnotu v něm. S indexem, stane se rychle a snadno se dotaz na datovou část s různých podmínek. Nyní pokračujme a vytvořte několik indexů předtím, než načteme data. V psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Další používají tyto tabulky Postgres na koordinační uzel a řekněte Hyperškálovatelného do horizontálního oddílu je napříč zaměstnanců. Uděláte to tak, dotaz pro každou tabulku zadání klíče horizontálního oddílu spustíme ji. V uvedeném příkladě necháme události a uživatelé tabulky na horizontální oddíl `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Jsme připraveni načíst data. V psql stále, prostředí Stáhněte soubory:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

V dalším kroku načtěte data ze souborů do distribuované tabulky:

```sql
\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Spouštění dotazů

Teď je čas zábavné části, ve skutečnosti spuštěné některé dotazy. Začněme jednoduchou `count (*)` chcete zobrazit množství dat, můžeme načíst:

```sql
SELECT count(*) from github_events;
```

Který pracoval hezky. Jsme budete vraťte se na tento druh agregace za chvilku, ale prozatím Podívejme se na několik dalších dotazů. V rámci JSONB `payload` sloupec je dobré bit dat, ale to se liší podle typu události. `PushEvent` události obsahují velikostí, která obsahuje počet jedinečných potvrzení pro vložení. Jsme slouží k vyhledání celkový počet potvrzení za hodinu:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Zatím jste dotazy podílejí githubu\_události výhradně, ale jsme je v kombinaci s githubem\_uživatelů. Protože jsme horizontálně dělené uživatelů a události na stejný identifikátor (`user_id`), řádky obou tabulek s odpovídajícím identifikátorem ID uživatele bude [umístěny společně](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) na stejném databázových uzlů a můžete snadno připojit.

Pokud se nám spojit `user_id`, hyperškálovatelný systém může doručit bez vyžádání provádění spojení do horizontálních oddílů pro spuštění paralelně na pracovních uzlech. Můžete například zjistit třeba, uživatelé, kteří si vytvořili největší počet úložišť:

```sql
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
      payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině serveru. Pokud jste Neočekáváme, že v budoucnu potřeba tyto prostředky, odstraňte skupinu serveru. Stisknutím klávesy **odstranit** tlačítko **přehled** stránce pro skupinu serverů. Po zobrazení výzvy na místní stránce Potvrďte název serveru skupiny a klikněte na poslední **odstranit** tlačítko.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak zřídit skupinu serverů Hyperškálovatelného (Citus). K němu připojili pomocí nástroje psql, vytvoří schéma a distribuovat data.

Potom postupujte podle kurzu vytvářet škálovatelné aplikace s více tenanty.
> [!div class="nextstepaction"]
> [Návrh databáze pro více Tenantů](https://aka.ms/hyperscale-tutorial-multi-tenant)
