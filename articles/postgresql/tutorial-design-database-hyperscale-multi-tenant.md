---
title: Návrh víceklientskou databázi s využitím Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) kurz
description: Tento kurz ukazuje, jak vytvářet, naplnit a dotazovat distribuované tabulky v databázi Azure pro PostgreSQL Hyperškálovatelného (Citus) (preview).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 73d7aebf3dbff59320e0ef92cbd54811503c71b4
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792276"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Kurz: návrh víceklientskou databázi s využitím Azure Database for PostgreSQL – velkokapacitní (Citus) (preview)

V tomto kurzu použijete Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) se dozvíte jak:

> [!div class="checklist"]
> * Vytvořte skupinu serverů a velkokapacitní (Citus)
> * Vytvořte schéma pomocí nástroje psql
> * Tabulky horizontálních oddílů mezi uzly
> * Ingestace ukázkových dat
> * Dotazování na data tenanta
> * Sdílení dat mezi tenanty
> * Přizpůsobit schéma na tenanta

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Vytvořte schéma pomocí nástroje psql

Po připojení k Azure Database for PostgreSQL – Hyperškálovatelného (Citus) (preview) pomocí nástroje psql, které můžete provádět některé základní úlohy. Tento kurz vás provede vytvořením webové aplikace, která umožňuje advertisers. ke sledování svých kampaní.

Více společností můžete použít aplikaci, Pojďme vytvořit tabulku pro uložení společnosti a druhý pro jejich kampaně. V konzole nástroje psql spusťte tyto příkazy:

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

Každý kampaně bude platit pro spuštění služby Active Directory. Přidání tabulky pro služby Active Directory, spuštěním následujícího kódu v psql po výše uvedeném kódu:

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

Nakonec jsme budete sledovat statistické údaje o kliknutí a dojmy u každé:

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

Nově vytvořené tabulky v seznamu tabulek v psql teď můžete zobrazit spuštěním:

```postgres
\dt
```

Aplikace s více tenanty můžete vynutit jedinečnost pouze každého klienta, což je důvod, proč všechny primární a cizí klíče zahrnují ID společnosti.

## <a name="shard-tables-across-nodes"></a>Tabulky horizontálních oddílů mezi uzly

Nasazení hyperškálovatelného ukládá řádky tabulky na různých uzlech, na základě hodnoty sloupce určený uživatel. Tato "distribučního sloupce" značky, kterého tenanta vlastní řádků.

Pojďme nastavit sloupec distribuce bude společnosti\_id, identifikátor tenanta. V psql spusťte tyto funkce:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Ingestace ukázkových dat

Mimo nástroj psql nyní, v příkazovém řádku normální stažení ukázkových datových sad:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Vrátí zpět do nástroje psql provést hromadné načtení dat. Nezapomeňte spustit nástroj psql ve stejném adresáři, kam jste stáhli datových souborů.

```sql
\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Tato data budou nyní rozloženy mezi uzly pracovního procesu.

## <a name="query-tenant-data"></a>Dotazování na data tenanta

Pokud aplikace požaduje data pro jednoho tenanta, můžete databáze spusťte dotaz na jednoho pracovního uzlu. Dotazy s jedním tenantem filtrovat podle ID jednoho tenanta. Například následující dotaz filtry `company_id = 5` pro reklamy a dojmy. Zkuste spustit v psql pro zobrazení výsledků.

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

## <a name="share-data-between-tenants"></a>Sdílení dat mezi tenanty

Až doteď všechny tabulky distributor `company_id`, ale některá data "nepatří přirozeně" do žádného tenanta zejména a je možné sdílet. Všechny společností ve službě ad platformy příklad například může být vhodné získat geografickými informacemi pro jejich cílové skupiny na základě IP adres.

Vytvoření tabulky k uložení sdíleného zeměpisné údaje. Spuštěním následujících příkazů v psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Dále zkontrolujte `geo_ips` "referenční tabulku" pro uložení kopie v tabulce v každém uzlu pracovního procesu.

```sql
SELECT create_reference_table('geo_ips');
```

Načtete do ní ukázková data. Mějte na paměti ke spuštění tohoto příkazu v psql z do adresáře, kam jste stáhli datové sady.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Připojení k tabulce klikne na tlačítko s geograficky\_IP adresy jsou efektivní na všech uzlech.
Tady je spojení pro vyhledání umístění všech uživatelů, kteří kliknuli ad
290. Zkuste dotaz spustit v psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Přizpůsobit schéma na tenanta

Každý klient mohl potřebovat ukládat speciální informace není potřeba jinými uživateli. Za všechny tenanty však sdílet společnou infrastrukturu s identické databázového schématu. Kam se obrátit doplňující data?

Jeden možností je použití typu neuzavřenou sloupců jako je PostgreSQL JSONB.  Naše schéma obsahuje pole JSONB `clicks` volá `user_data`.
Společnosti (třeba společnosti pěti), můžete použít sloupec pro sledování, zda uživatel je na mobilním zařízení.

Tady je dotaz pro vyhledání, kdo klikne na tlačítko Další: mobilní nebo tradiční návštěvníků.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Jsme tento dotaz optimalizovat pro jeden společnosti tak, že vytvoříte [částečné index](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Obecně platí, můžeme vytvořit [GIN indexy](https://www.postgresql.org/docs/current/static/gin-intro.html) na každý klíč a hodnotu ve sloupci.

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

V předchozích krocích jste vytvořili prostředky Azure ve skupině serveru. Pokud jste Neočekáváme, že v budoucnu potřeba tyto prostředky, odstraňte skupinu serveru. Stisknutím klávesy *odstranit* tlačítko *přehled* stránce pro skupinu serverů. Po zobrazení výzvy na místní stránce Potvrďte název serveru skupiny a klikněte na poslední *odstranit* tlačítko.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak zřídit skupinu serverů Hyperškálovatelného (Citus). K němu připojili pomocí nástroje psql, vytvoří schéma a distribuovat data. Jste zjistili, jak dotazy na data v rámci a mezi klienty a přizpůsobit schéma každého tenanta.

V dalším kroku informace o konceptech hyperškálovatelný systém.
> [!div class="nextstepaction"]
> [Typy uzlů Hyperškálováním](https://aka.ms/hyperscale-concepts)
