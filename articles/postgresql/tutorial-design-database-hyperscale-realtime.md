---
title: 'Kurz: Návrh řídicího panelu v reálném čase – Citus () – Azure Database for PostgreSQL'
description: V tomto kurzu se dozvíte, jak paralelizovat dotazy na řídicí panel v reálném čase s využitím Azure Database for PostgreSQLho škálování (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 480af87519f8e11c14f009058b0f518bdfc34a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026263"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Kurz: Návrh řídicího panelu analýzy v reálném čase pomocí Azure Database for PostgreSQL – škálovatelné (Citus)

V tomto kurzu se naučíte, jak používat Azure Database for PostgreSQL-Citus (škálování na více procesorů):

> [!div class="checklist"]
> * Vytvoření skupiny serverů Hyperscale (Citus)
> * Vytvoření schématu pomocí nástroje psql
> * Tabulky horizontálních oddílů napříč uzly
> * Generování ukázkových dat
> * Provést souhrny
> * Dotazování na hrubá a agregovaná data
> * Vypršení platnosti dat

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Vytvoření schématu pomocí nástroje psql

Po připojení k Azure Database for PostgreSQL – Citus () pomocí psql můžete dokončit některé základní úlohy. Tento kurz vás provede příjmem dat přenosů z webové analýzy a následným shrnutím dat a poskytnutím řídicích panelů v reálném čase na základě těchto dat.

Pojďme vytvořit tabulku, která bude využívat všechna nezpracované data webového provozu. V terminálu psql spusťte následující příkazy:

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

Také se vytvoří tabulka, která bude obsahovat naši agregaci za minutu, a tabulku, která bude uchovávat pozici poslední souhrn. V psql také spusťte následující příkazy:

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

Nově vytvořené tabulky můžete zobrazit v seznamu tabulek nyní pomocí tohoto příkazu psql:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Tabulky horizontálních oddílů napříč uzly

Nasazení v rámci škálování ukládá řádky tabulky na různých uzlech na základě hodnoty uživatelem označeného sloupce. Tento "distribuční sloupec" označuje způsob, jakým se data horizontálně dělené napříč uzly.

Nastavíme distribuční sloupec jako \_ ID webu, klíč horizontálních oddílů. V psql spusťte tyto funkce:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="generate-sample-data"></a>Generování ukázkových dat

Naše skupina serverů by teď měla být připravená na ingestování některých dat. `psql`K průběžnému vkládání dat můžeme z našeho připojení spustit následující místně.

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

Dotaz vkládá přibližně osm řádků každou sekundu. Řádky jsou uloženy na různých pracovních uzlech, které jsou směrovány pomocí distribučního sloupce `site_id` .

   > [!NOTE]
   > Ponechte dotaz na generování dat spuštěný a otevřete druhé připojení psql pro zbývající příkazy v tomto kurzu.
   >

## <a name="query"></a>Dotaz

Možnost hostování v rámci škálování umožňuje více uzlům zpracovávat paralelně dotazy pro rychlost. Databáze například vypočítá agregované hodnoty, jako je součet a počet na pracovních uzlech, a zkombinuje výsledky do konečné odpovědi.

Tady je dotaz, jak počítat webové žádosti za minutu spolu s několika statistikami.
Zkuste ho spustit v psql a sledujte výsledky.

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

Předchozí dotaz funguje v počátečních fázích dobře, ale jeho výkon se sníží, protože se škálují data. I u distribuovaného zpracování je rychlejší vypočítávat data, než je znovu přepočítat.

V rámci pravidelného Shrnutí nezpracovaných dat do agregované tabulky můžeme zajistit, aby byl náš řídicí panel stále rychlý. Můžete experimentovat s trváním agregace. Použili jsme tabulku agregace po minutách, ale místo ní můžete data přerušit na 5, 15 nebo 60 minut.

Aby bylo možné tento souhrn snadněji spustit, přidáme ho do plpgsql funkce. Spusťte tyto příkazy v psql a vytvořte `rollup_http_request` funkci.

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

Když je naše funkce na místě, spusťte ji a zaveďte data:

```sql
SELECT rollup_http_request();
```

A s našimi daty v předem agregované podobě se můžeme dotazovat na souhrnnou tabulku a získat tak stejnou sestavu jako dříve. Spusťte tento dotaz:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Vypršení starých dat

Souhrny urychlují dotazy, ale pořád potřebujeme vypršet stará data, aby nedocházelo k nevázaným nákladům na úložiště. Určete, jak dlouho chcete uchovat data pro každou členitost, a pomocí standardních dotazů odstraňte data s vypršenou platností. V následujícím příkladu jsme se rozhodli uchovávat nezpracovaná data za jeden den a agregace po minutách po dobu jednoho měsíce:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

V produkčním prostředí byste tyto dotazy mohli zabalit do funkce a volat je každou minutu v rámci úlohy cron.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině serverů. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu serverů. Stiskněte tlačítko *Odstranit* na stránce *Přehled* pro skupinu serverů. Po zobrazení výzvy na místní stránce potvrďte název skupiny serverů a klikněte na tlačítko poslední *Odstranit* .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak zřídit skupinu serverů (Citus). K němu jste se připojili pomocí psql, vytvořili schéma a distribuovaná data. Seznámili jste se s dotazem na data v nezpracovaném formuláři, pravidelně agreguje tato data, dotazování na agregované tabulky a vyprší jejich stará data.

- Informace o [typech uzlů](./concepts-hyperscale-nodes.md) skupin serverů
- Určení nejlepší [počáteční velikosti](howto-hyperscale-scale-initial.md) pro skupinu serverů
