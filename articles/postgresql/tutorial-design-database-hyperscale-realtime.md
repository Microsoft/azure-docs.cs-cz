---
title: 'Kurz: Návrh řídicího panelu v reálném čase – Hyperscale (Citus) – Databáze Azure pro PostgreSQL'
description: Tento kurz ukazuje, jak vytvořit, naplnit a dotazovat distribuované tabulky v Azure Database for PostgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: f4eeb646de8b68c2c8d30586d0c75cece5317e40
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76716330"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Kurz: Návrh řídicího panelu analýzy v reálném čase pomocí Azure Database for PostgreSQL – Hyperscale (Citus)

V tomto kurzu pomocí Azure Database for PostgreSQL – Hyperscale (Citus) se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření skupiny serverů Hyperscale (Citus)
> * Vytvoření schématu pomocí nástroje psql
> * Tabulky šmejení mezi uzly
> * Generování ukázkových dat
> * Provádění souhrnů
> * Dotaz nezpracovaná a agregovaná data
> * Data vypršení platnosti

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Vytvoření schématu pomocí nástroje psql

Po připojení k databázi Azure pro PostgreSQL – Hyperscale (Citus) pomocí psql můžete dokončit některé základní úkoly. Tento kurz vás provede ingestováním dat o provozu z webové analýzy a následným srolováním dat tak, aby na základě těchto dat poskytovalřídicí panely v reálném čase.

Pojďme vytvořit tabulku, která bude spotřebovávat všechny naše nezpracované údaje o webovém provozu. V terminálu psql spusťte následující příkazy:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Vytvoříme také tabulku, která bude obsahovat naše agregáty za minutu, a tabulku, která udržuje pozici našeho posledního souhrnu. Spusťte také následující příkazy v psql:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Nově vytvořené tabulky můžete nyní zobrazit v seznamu tabulek pomocí tohoto příkazu psql:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Tabulky šmejení mezi uzly

Nasazení hyperškálování ukládá řádky tabulky na různých uzlech na základě hodnoty sloupce určeného uživatelem. Tento "sloupec distribuce" označuje, jak jsou data rozdělení mezi uzly.

Nastavíme sloupec distribuce tak,\_aby byl id webu, klíč střepů. V psql spusťte tyto funkce:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Generování ukázkových dat

Nyní by naše skupina serverů měla být připravena přijmout některá data. Můžeme spustit následující místně z `psql` našeho připojení k on-li neustále vkládat data.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

Dotaz vloží přibližně osm řádků za sekundu. Řádky jsou uloženy v různých pracovních uzlech podle pokynů distribučního sloupce `site_id`.

   > [!NOTE]
   > Ponechte dotaz generování dat spuštěný a otevřete druhé připojení psql pro zbývající příkazy v tomto kurzu.
   >

## <a name="query"></a>Dotaz

Možnost hyperškálování hostingu umožňuje více uzlům zpracovávat dotazy paralelně pro rychlost. Například databáze vypočítá agregace jako SUMA a COUNT na pracovníu uzly a kombinuje výsledky do konečné odpovědi.

Zde je dotaz pro počítání webových požadavků za minutu spolu s několika statistikami.
Zkuste spustit v psql a sledovat výsledky.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>Srolování dat

Předchozí dotaz funguje v prvních fázích, ale jeho výkon se snižuje s měřítkem dat. I při distribuovaném zpracování je rychlejší data předpočítat než je opakovaně přepočítávat.

Můžeme zajistit, aby náš řídicí panel zůstal rychlý tím, že pravidelně shrnuje nezpracovaná data do souhrnné tabulky. Můžete experimentovat s dobou trvání agregace. Použili jsme tabulku agregace za minutu, ale místo toho můžete rozdělit data na 5, 15 nebo 60 minut.

Chcete-li spustit tento roll-up snadněji, budeme dát do funkce plpgsql. Spusťte tyto příkazy v `rollup_http_request` psql k vytvoření funkce.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

S naší funkcí na místě, spusťte ji shrnout data:

```sql
SELECT rollup_http_request();
```

A s našimi daty v předem agregované podobě můžeme zadat dotaz na souhrnnou tabulku, abychom získali stejnou sestavu jako dříve. Spusťte tento dotaz:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Platnost starých dat s platností

Souhrny zrychlují dotazy, ale stále potřebujeme vypršet stará data, abychom se vyhnuli nevázaným nákladům na úložiště. Rozhodněte se, jak dlouho chcete uchovávat data pro každou rozlišovací schopnost, a pomocí standardních dotazů odstraňte data, jejichž platnost vypršela. V následujícím příkladu jsme se rozhodli uchovávat nezpracovaná data po dobu jednoho dne a agregací za minutu po dobu jednoho měsíce:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

V produkčním prostředí můžete tyto dotazy zabalit do funkce a volat je každou minutu v úloze cron.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině serverů. Pokud neočekáváte, že budete tyto prostředky v budoucnu potřebovat, odstraňte skupinu serverů. Stiskněte tlačítko *Odstranit* na stránce *Přehled* pro skupinu serverů. Po zobrazení výzvy na vyskakovací stránce potvrďte název skupiny serverů a klepněte na tlačítko *Konečné odstranění.*

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak zřídit skupinu serveru Hyperscale (Citus). Připojili jste k němu psql, vytvořili schéma a distribuovaná data. Naučili jste se dotazovat data v nezpracované podobě, pravidelně agregovat tato data, dotazovat se na agregované tabulky a vyprší platnost starých dat.

Dále se dozvíte o konceptech hyperškálování.
> [!div class="nextstepaction"]
> [Typy uzlů s převažovací kapacitou](https://aka.ms/hyperscale-concepts)
