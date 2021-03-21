---
title: 'Kurz: návrh databáze s více instancemi – Citus (multi-tenant) – Azure Database for PostgreSQL'
description: V tomto kurzu se dozvíte, jak vytvořit škálovatelnou aplikaci pro více tenantů s Azure Database for PostgreSQL Citus (škálování).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: d8dee086f55c5596af50245b5271d98536eb7e57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026195"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Kurz: návrh databáze s více klienty pomocí Azure Database for PostgreSQL – Citus (škálování)

V tomto kurzu se naučíte, jak používat Azure Database for PostgreSQL-Citus (škálování na více procesorů):

> [!div class="checklist"]
> * Vytvoření skupiny serverů Hyperscale (Citus)
> * Vytvoření schématu pomocí nástroje psql
> * Tabulky horizontálních oddílů napříč uzly
> * Ingestace ukázkových dat
> * Dotaz na data tenanta
> * Sdílení dat mezi klienty
> * Přizpůsobení schématu pro jednotlivé klienty

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Vytvoření schématu pomocí nástroje psql

Po připojení k Azure Database for PostgreSQL – Citus () pomocí psql můžete dokončit některé základní úlohy. Tento kurz vás provede vytvořením webové aplikace, která umožňuje inzerentům sledovat své kampaně.

Aplikace může používat více společností, takže vytvoříme tabulku, která bude uchovávat společnosti a jinou pro své kampaně. V konzole psql spusťte tyto příkazy:

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

Každá kampaň bude platit pro spuštění reklam. Přidejte tabulku pro reklamu, a to spuštěním následujícího kódu v psql po výše uvedeném kódu:

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

Nakonec budeme sledovat statistiku kliknutí a potlačení pro každou reklamu:

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

Nově vytvořené tabulky můžete zobrazit v seznamu tabulek nyní v psql spuštěním:

```postgres
\dt
```

Víceklientské aplikace můžou vymáhat jedinečnost jenom pro každého tenanta, což znamená, proč všechny primární a cizí klíče zahrnují ID společnosti.

## <a name="shard-tables-across-nodes"></a>Tabulky horizontálních oddílů napříč uzly

Nasazení v rámci škálování ukládá řádky tabulky na různých uzlech na základě hodnoty uživatelem označeného sloupce. Tento "distribuční sloupec" označuje, ve kterém tenantovi vlastní řádky.

Pojďme nastavit distribuční sloupec jako \_ ID společnosti, identifikátor tenanta. V psql spusťte tyto funkce:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="ingest-sample-data"></a>Ingestace ukázkových dat

Mimo psql nyní na běžném příkazovém řádku Stáhněte ukázkové sady dat:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Zpět v rámci psql data hromadného načtení. Nezapomeňte spustit psql ve stejném adresáři, kam jste stáhli datové soubory.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Tato data se teď rozšíří mezi pracovními uzly.

## <a name="query-tenant-data"></a>Dotaz na data tenanta

Když aplikace požaduje data pro jednoho tenanta, databáze může spustit dotaz na jednom pracovním uzlu. Dotazy jednoho tenanta se filtrují podle jednoho ID tenanta. Například následující filtry dotazu vyfiltrují `company_id = 5` reklamy a natisky. Zkuste ho spustit v psql, aby se zobrazily výsledky.

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

Až do chvíle, kdy byly všechny tabulky distribuovány `company_id` , ale některá data nejsou přirozeně "patřila" do jakéhokoli klienta, konkrétně a lze je sdílet. Například všechny společnosti v ukázkové platformě služby AD mohou chtít získat geografické informace pro svou cílovou skupinu na základě IP adres.

Vytvořte tabulku pro ukládání sdílených geografických informací. V psql spusťte následující příkazy:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Dále vytvořte `geo_ips` odkazovou tabulku pro uložení kopie tabulky v každém pracovním uzlu.

```sql
SELECT create_reference_table('geo_ips');
```

Načtěte ho s ukázkovými daty. Nezapomeňte spustit tento příkaz v psql zevnitř v adresáři, do kterého jste stáhli datovou sadu.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Spojování tabulky kliknutí s geografickými \_ IP adresami je efektivní na všech uzlech.
Tady je spojení, kde najdete umístění všech uživatelů, kteří na reklamu klikli.
290. Zkuste spustit dotaz v psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Přizpůsobení schématu pro jednotlivé klienty

Každý tenant může potřebovat ukládat speciální informace, které jiné nevyžadují. Všichni klienti ale sdílejí společnou infrastrukturu se stejným schématem databáze. Kde může další data přejít?

Jedním z zdvihů je použití typu otevřeného a koncového sloupce, jako je PostgreSQL JSONB.  Naše schéma má volané pole JSONB `clicks` `user_data` .
Společnost (vyslovit pět firem) může pomocí sloupce sledovat, jestli je uživatel v mobilním zařízení.

Tady je dotaz, který zjistí, kdo klikne na další: mobilní nebo tradiční Návštěvníci.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Tento dotaz pro jednu společnost můžeme optimalizovat vytvořením [částečného indexu](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Obecněji můžeme vytvořit [gin indexy](https://www.postgresql.org/docs/current/static/gin-intro.html) pro každý klíč a hodnotu ve sloupci.

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

V předchozích krocích jste vytvořili prostředky Azure ve skupině serverů. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu serverů. Stiskněte tlačítko *Odstranit* na stránce *Přehled* pro skupinu serverů. Po zobrazení výzvy na místní stránce potvrďte název skupiny serverů a klikněte na tlačítko poslední *Odstranit* .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak zřídit skupinu serverů (Citus). K němu jste se připojili pomocí psql, vytvořili schéma a distribuovaná data. Seznámili jste se s dotazem na data uvnitř i mezi klienty a k přizpůsobení schématu pro každého tenanta.

- Informace o [typech uzlů](./concepts-hyperscale-nodes.md) skupin serverů
- Určení nejlepší [počáteční velikosti](howto-hyperscale-scale-initial.md) pro skupinu serverů
