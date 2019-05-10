---
title: Návrh řídicího panelu v reálném čase s využitím Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) kurz
description: Tento kurz ukazuje, jak vytvářet, naplnit a dotazovat distribuované tabulky v databázi Azure pro PostgreSQL Hyperškálovatelného (Citus) (preview).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 9f3473d83678ffea888dad736a9620006b2961f7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406397"
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

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Vytvoření Azure Database for PostgreSQL

Server Azure Database for PostgreSQL vytvoříte pomocí tohoto postupu:
1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Na stránce **Nový** vyberte **Databáze** a na stránce **Databáze** vyberte **Azure Database for PostgreSQL**.
3. Možnost nasazení, klikněte na tlačítko **vytvořit** tlačítko **skupinu serverů Hyperškálovatelného (Citus) – ve verzi PREVIEW.**
4. Do formuláře podrobností o novém serveru zadejte následující informace:
   - Skupina prostředků: klikněte na tlačítko **vytvořit nový** odkaz pod textové pole pro toto pole. Zadejte název, například **myresourcegroup**.
   - Název skupiny serverů: **mydemoserver** (název serveru, který se mapuje na název DNS a je nutné být globálně jedinečný).
   - Uživatelské jméno správce: **myadmin** (to se později použijí pro připojení k databázi).
   - Heslo: musí být alespoň osm znaků a musí obsahovat znaky ze tří z těchto kategorií – velká písmena anglické, anglické abecedy, malá písmena, číslice (0 – 9) a jiné než alfanumerické znaky (!, $, #, % atd.)
   - Umístění: použijte umístění co nejblíže vašim uživatelům jim zajistili nejrychlejší přístup k datům.

   > [!IMPORTANT]
   > Zde zadané přihlašovací jméno a heslo správce serveru se vyžadují pro přihlášení k serveru a jeho databázím dále v tomto kurzu. Tyto informace si zapamatujte nebo poznamenejte pro pozdější použití.

5. Klikněte na tlačítko **konfigurovat skupinu serverů**. Ponechejte nastavení v této části beze změny a klikněte na tlačítko **Uložit**.
6. Klikněte na tlačítko **revize + vytvořit** a potom **vytvořit** ke zřízení serveru. Zřizování trvá několik minut.
7. Monitorování nasazení přesměruje na stránku. Změny za provozu stavu z **nasazení probíhá** k **vaše nasazení je kompletní**, klikněte na tlačítko **výstupy** položky nabídky na levé straně stránky.
8. Stránka výstupy bude obsahovat název hostitele koordinátor s tlačítkem vedle něj zkopírovat hodnotu do schránky. Poznamenejte si tyto údaje pro pozdější použití.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurace pravidla brány firewall na úrovni serveru

Služba Azure Database for PostgreSQL využívá bránu firewall na úrovni serveru. Ve výchozím nastavení brána firewall brání všem externím aplikacím a nástrojům v připojení k serveru a kterékoli databázi na serveru. Přidáme pravidlo k otevření brány firewall pro konkrétní rozsah IP adres.

1. Z **výstupy** části, kde jste dříve zkopírovali název hostitele koordinační uzel, klikněte na tlačítko zpět do **přehled** položky nabídky.

2. Najít skupinu škálování pro nasazení v seznamu prostředků a klikněte na něj. (Její název bude obsahovat předponu "sg-".)

3. Klikněte na tlačítko **brány Firewall** pod **zabezpečení** v levé nabídce.

4. Klikněte na odkaz **+ přidat pravidlo brány firewall pro aktuální IP adresa klienta**. Nakonec klikněte na tlačítko **Uložit** tlačítko.

5. Klikněte na **Uložit**.

   > [!NOTE]
   > Server Azure PostgreSQL komunikuje přes port 5432. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 5432 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru služby Azure SQL Database, dokud vaše IT oddělení neotevře port 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Připojení k databázi pomocí nástroje psql ve službě Cloud Shell

Teď se pomocí nástroje příkazového řádku [psql](https://www.postgresql.org/docs/current/app-psql.html) připojíme k serveru Azure Database for PostgreSQL.
1. Pomocí ikony terminálu v horním navigačním podokně spusťte službu Azure Cloud Shell.

   ![Azure Database for PostgreSQL – ikona terminálu Azure Cloud Shell](./media/tutorial-design-database-hyperscale-realtime/psql-cloud-shell.png)

2. Služba Azure Cloud Shell se otevře v prohlížeči a umožní vám zadat příkazy Bash.

   ![Azure Database for PostgreSQL – příkazový řádek Bash služby Azure Shell](./media/tutorial-design-database-hyperscale-realtime/psql-bash.png)

3. V příkazovém řádku služby Cloud Shell se pomocí příkazů psql připojte k serveru Azure Database for PostgreSQL. Následující formát se používá pro připojení k serveru Azure Database for PostgreSQL s nástrojem [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Například následující příkaz se připojí k výchozí databázi s názvem **citus** na váš server PostgreSQL **mydemoserver.postgres.database.azure.com** pomocí přihlašovacích údajů pro přístup. Po zobrazení výzvy zadejte heslo správce serveru.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

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

Budeme také vytvořit tabulku, která bude obsahovat naše agregace po minutách a tabulku, která udržuje pozice naší poslední kumulativní. Spuštěním následujícího příkazu v také psql:

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
      concat('https://example.com/', md5(random()::text)),
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
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

Dotaz přidá řádek druhé přibližně každé čtvrtletí. Řádky se ukládají na jinou pracovní uzly podle sloupec distribuce `site_id`.

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

## <a name="performing-rollups"></a>Provádění kumulativních

Výše uvedeném dotazu funguje v počátečních fázích, ale jako vaše škálování dat se sníží výkon. I přes distribuované zpracování, je rychlejší než přepočítat opakovaně předem compute tato data.

Jsme zajistit náš řídicí panel zůstane rychlé zahrnutím pravidelně nezpracovaná data do agregační tabulky. V tomto případě jsme budou zahrnuty do naší tabulce agregace jednu minutu, ale může také obsahovat agregace 5 minut, 15 minut, hodinu a tak dále.

Protože jsme neustále se spustí toto shrnutí chceme vytvořit funkci, kterou chcete provést. Spusťte následující příkazy psql vytvořit `rollup_http_request` funkce.

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

A pomocí našich dat ve formě předem agregovat jsme můžete dotazovat v tabulce kumulativní získat stejné sestavy jako předtím. Spusťte následující příkazy:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>U nichž vyprší platnost starých dat

Souhrny urychlit dotazů, ale musíme vyprší původní dat. vyhnete se tak náklady na úložiště bez vazby. Jednoduše rozhodněte, jak dlouho chcete uchovávat data pro každý členitosti a odstraňte data s prošlou platností pomocí standardní dotazy. V následujícím příkladu jsme se rozhodli zachovat nezpracovaná data za jeden den a po minutách, agregací pro jeden měsíc:

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