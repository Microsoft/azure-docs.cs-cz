---
title: Návrh řídicího panelu v reálném čase s využitím Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) kurz
description: Tento kurz ukazuje, jak vytvářet, naplnit a dotazovat distribuované tabulky v databázi Azure pro PostgreSQL Hyperškálovatelného (Citus) (preview).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: a5e4b2073a29785ee851b2733c12d6331afe59d8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791309"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Kurz: Návrh řídicí panel analýzy v reálném čase s využitím Azure Database for PostgreSQL – velkokapacitní (Citus) (preview)

V tomto kurzu použijete Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) se dozvíte jak:

> [!div class="checklist"]
> * Vytvořte skupinu serverů a velkokapacitní (Citus)
> * Vytvořte schéma pomocí nástroje psql
> * Tabulky horizontálních oddílů mezi uzly
> * Generování ukázkových dat
> * Provádění kumulativních
> * Nezpracovaná a agregovaná data dotazu
> * Vypršení platnosti dat

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Vytvořte schéma pomocí nástroje psql

Po připojení k Azure Database for PostgreSQL – Hyperškálovatelného (Citus) (preview) pomocí nástroje psql, které můžete provádět některé základní úlohy. Tento kurz vás provede příjem přenosů dat z webových analýz, pak zahrnované data a poskytuje v reálném čase řídicí panely založené na těchto datech.

Pojďme vytvořit tabulku, která bude využívat všechny naše nezpracovaných dat z webu provoz. Spuštěním následujících příkazů v terminálu psql:

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

Budeme také vytvořit tabulku, která bude obsahovat naše agregace po minutách a tabulku, která udržuje pozice naší poslední kumulativní. Spuštěním následujících příkazů v také psql:

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

Zobrazí se nově vytvořené tabulky v seznamu tabulek teď pomocí tohoto příkazu psql:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Tabulky horizontálních oddílů mezi uzly

Nasazení hyperškálovatelného ukládá řádky tabulky na různých uzlech, na základě hodnoty sloupce určený uživatel. Soubor "sloupec distribuce" označuje, jak se horizontálně dělených dat napříč uzly.

Pojďme nastavit sloupec distribuce na serveru\_id, klíč horizontálního oddílu. V psql spusťte tyto funkce:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Generování ukázkových dat

Naše skupiny serverů nyní měli být připravení ingestování nějaká data. Jsme spusťte následující příkaz místně z našich `psql` připojení průběžně vložit data.

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

Dotaz vloží přibližně osm řádků za sekundu. Řádky se ukládají na jinou pracovní uzly podle sloupec distribuce `site_id`.

   > [!NOTE]
   > Nechte generování dotazu na data spuštění a otevřít druhé připojení psql pro zbývající příkazy v tomto kurzu.
   >

## <a name="query"></a>Dotaz

Velkokapacitní možnost hostování umožňuje více uzlů ke zpracování dotazů paralelně pro rychlost. Pro instanci databáze vypočítá agregace, jako jsou součet a počet na pracovní uzly a sloučí výsledky do konečné odpovědí.

Tady je dotaz na počet webových požadavků za minutu společně s pár statistiky.
Zkuste spustit v psql a sledujte výsledky.

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

## <a name="rolling-up-data"></a>Shrnutí dat

Předchozí dotaz funguje v počátečních fázích, ale jeho výkon sníží, přetrénujte jako škálování vašich dat. I přes distribuované zpracování, je rychlejší předem compute dat než přepočítat ho opakovaně.

Jsme zajistit náš řídicí panel zůstane rychlé zahrnutím pravidelně nezpracovaná data do agregační tabulky. Můžete experimentovat s dobou trvání agregace. Jsme použili tabulce agregace za minutu, ale data může rozdělit na 5, 15 nebo 60 minut místo.

Ke spuštění této souhrnné snadněji, My budeme umístíte do plpgsql funkce. Spusťte následující příkazy psql vytvořit `rollup_http_request` funkce.

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

Pomocí naší funkce v místě spusťte ji chcete-li vrátit data:

```sql
SELECT rollup_http_request();
```

A pomocí našich dat ve formě předem agregovat jsme můžete dotazovat v tabulce kumulativní získat stejné sestavy jako předtím. Spuštěním následujícího dotazu:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>U nichž vyprší platnost starých dat

Souhrny urychlit dotazů, ale musíme vyprší původní dat. vyhnete se tak náklady na úložiště bez vazby. Rozhodněte, jak dlouho chcete uchovávat data pro každý členitosti a odstraňte data s prošlou platností pomocí standardní dotazy. V následujícím příkladu jsme se rozhodli zachovat nezpracovaná data za jeden den a po minutách, agregací pro jeden měsíc:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

V produkčním prostředí můžete zalomit tyto dotazy ve funkci a volání každou minutu v úlohy cron.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině serveru. Pokud jste Neočekáváme, že v budoucnu potřeba tyto prostředky, odstraňte skupinu serveru. Stisknutím klávesy *odstranit* tlačítko *přehled* stránce pro skupinu serverů. Po zobrazení výzvy na místní stránce Potvrďte název serveru skupiny a klikněte na poslední *odstranit* tlačítko.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak zřídit skupinu serverů Hyperškálovatelného (Citus). K němu připojili pomocí nástroje psql, vytvoří schéma a distribuovat data. Jste zjistili, jak dotaz na data i v nezpracované podobě, pravidelně agregovat data dotazovat agregovaných tabulek a stará data vypršení platnosti.

V dalším kroku informace o konceptech hyperškálovatelný systém.
> [!div class="nextstepaction"]
> [Typy uzlů Hyperškálováním](https://aka.ms/hyperscale-concepts)