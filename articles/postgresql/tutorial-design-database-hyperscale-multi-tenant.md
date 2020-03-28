---
title: 'Kurz: Návrh víceklientské databáze – Hyperscale (Citus) – databáze Azure pro PostgreSQL'
description: Tento kurz ukazuje, jak vytvořit, naplnit a dotazovat distribuované tabulky v Azure Database for PostgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 17ac29de243f4abfff1cfc83fc6424799978bf0e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74978147"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Kurz: návrh víceklientské databáze pomocí Azure Database for PostgreSQL – Hyperscale (Citus)

V tomto kurzu pomocí Azure Database for PostgreSQL – Hyperscale (Citus) se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření skupiny serverů Hyperscale (Citus)
> * Vytvoření schématu pomocí nástroje psql
> * Tabulky šmejení mezi uzly
> * Ingestace ukázkových dat
> * Dotaz na data klienta
> * Sdílení dat mezi klienty
> * Přizpůsobení schématu na tenanta

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Vytvoření schématu pomocí nástroje psql

Po připojení k databázi Azure pro PostgreSQL – Hyperscale (Citus) pomocí psql můžete dokončit některé základní úkoly. Tento kurz vás provede vytvořením webové aplikace, která inzerentům umožní sledovat jejich kampaně.

Aplikaci může používat více společností, takže vytvoříme tabulku, která bude držet společnosti a další pro jejich kampaně. V konzoli psql spusťte tyto příkazy:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Každá kampaň se vyplatí za provozování reklam. Přidejte tabulku pro reklamy příliš spuštěním následující kód v psql po výše uvedený kód:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Nakonec budeme sledovat statistiky o kliknutích a zobrazeních pro každou reklamu:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

Nově vytvořené tabulky můžete zobrazit v seznamu tabulek, které jsou nyní v psql, spuštěním:

```postgres
\dt
```

Víceklientské aplikace můžete vynutit jedinečnost pouze na klienta, což je důvod, proč všechny primární a cizí klíče patří ID společnosti.

## <a name="shard-tables-across-nodes"></a>Tabulky šmejení mezi uzly

Nasazení hyperškálování ukládá řádky tabulky na různých uzlech na základě hodnoty sloupce určeného uživatelem. Tento "distribuční sloupec" označuje, který tenant vlastní které řádky.

Nastavíme sloupec distribuce jako\_ID společnosti, identifikátor klienta. V psql spusťte tyto funkce:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Ingestace ukázkových dat

Mimo psql nyní, v normálním příkazovém řádku, stáhněte ukázkové datové sady:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Zpět uvnitř psql, hromadné načtení dat. Ujistěte se, že spustit psql ve stejném adresáři, kde jste stáhli datové soubory.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Tato data budou nyní rozložena mezi pracovní uzly.

## <a name="query-tenant-data"></a>Dotaz na data klienta

Když aplikace požaduje data pro jednoho klienta, databáze může spustit dotaz na jednom pracovním uzlu. Dotazy s jedním tenantem filtrují podle jednoho ID klienta. Následující filtry `company_id = 5` dotazů například pro reklamy a zobrazení. Zkuste spustit v psql vidět výsledky.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Sdílení dat mezi klienty

Až dosud byly všechny `company_id`tabulky distribuovány aplikacemi , ale některá data přirozeně "nepatří" k žádnému konkrétnímu klientovi a lze je sdílet. Například všechny společnosti v příkladu platformy pro reklamy mohou chtít získat geografické informace pro své publikum na základě IP adres.

Vytvořte tabulku pro uložení sdílených geografických informací. Spusťte v psql následující příkazy:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Dále `geo_ips` vytvořte "referenční tabulku" pro uložení kopie tabulky na každém pracovním uzlu.

```sql
SELECT create_reference_table('geo_ips');
```

Načtěte jej s ukázkovými daty. Nezapomeňte spustit tento příkaz v psql z adresáře, kde jste stáhli datovou sadu.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Připojení k tabulce kliknutí\_pomocí geografických ips je efektivní na všech uzlech.
Zde je připojit se k nalezení místa každého, kdo klikl na reklamu
290. Zkuste spustit dotaz v psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Přizpůsobení schématu na tenanta

Každý klient může potřebovat ukládat speciální informace, které ostatní nepotřebují. Všichni klienti však sdílejí společnou infrastrukturu se schématem identických databází. Kam mohou být další data?

Jedním z triků je použít otevřený typ sloupce, jako je JSONB PostgreSQL.  Naše schéma má pole JSONB `clicks` s `user_data`názvem .
Společnost (řekněme společnost pět) může sloupec použít ke sledování, zda je uživatel na mobilním zařízení.

Tady je dotaz, který zjistí, kdo klikne na další: mobilní nebo tradiční návštěvníky.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Můžeme optimalizovat tento dotaz pro jednu společnost vytvořením [částečného indexu](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Obecněji můžeme vytvořit [indexy GIN](https://www.postgresql.org/docs/current/static/gin-intro.html) na každý klíč a hodnotu ve sloupci.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině serverů. Pokud neočekáváte, že budete tyto prostředky v budoucnu potřebovat, odstraňte skupinu serverů. Stiskněte tlačítko *Odstranit* na stránce *Přehled* pro skupinu serverů. Po zobrazení výzvy na vyskakovací stránce potvrďte název skupiny serverů a klepněte na tlačítko *Konečné odstranění.*

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak zřídit skupinu serveru Hyperscale (Citus). Připojili jste k němu psql, vytvořili schéma a distribuovaná data. Naučili jste se dotazovat data v rámci klienta i mezi nimi a přizpůsobit schéma na klienta.

Dále se dozvíte o konceptech hyperškálování.
> [!div class="nextstepaction"]
> [Typy uzlů s převažovací kapacitou](https://aka.ms/hyperscale-concepts)
