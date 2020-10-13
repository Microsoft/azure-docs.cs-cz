---
title: Použití rozšíření PostgreSQL
description: Použití rozšíření PostgreSQL
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 58386786266c48c6e721094f9f2837709bb684e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631762"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Použijte rozšíření PostgreSQL ve skupině serverů PostgreSQL s podporou škálování na úrovni Azure ARC

PostgreSQL je nejlépe při použití s rozšířeními. Klíčovým prvkem naší vlastní funkce škálování je ve skutečnosti rozšíření poskytované společností Microsoft, `citus` které je ve výchozím nastavení nainstalované, což umožňuje Postgres transparentně horizontálních oddílů data napříč více uzly.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="list-of-extensions"></a>Seznam rozšíření
Kromě rozšíření v systému [`contrib`](https://www.postgresql.org/docs/12/contrib.html) je seznam rozšíření, která jsou k dispozici v kontejnerech skupiny serverů s podporou Azure ARC PostgreSQL, následující:
- `citus`, v: 9,4
- `pg_cron`, v: 1,2
- `plpgsql`, v: 1,0
- `postgis`, v: 3.0.2
- `plv8`, v: 2.3.14

Tento seznam se rozvíjejí přesčasy a aktualizace se budou publikovat v tomto dokumentu. Ještě není možné přidat rozšíření nad rámec uvedených výše.

Tato příručka povede ve scénáři použití dvou z těchto rozšíření:
- [PostGIS](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)


## <a name="manage-extensions"></a>Správa rozšíření

### <a name="enable-extensions"></a>Povolit rozšíření
Tento krok není nutný pro rozšíření, která jsou součástí `contrib` .
Obecný formát příkazu pro povolení rozšíření je:

#### <a name="enable-an-extension-at-the-creation-time-of-a-server-group"></a>Povolit rozšíření v době vytváření skupiny serverů:
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
#### <a name="enable-an-extension-on-an-instance-that-already-exists"></a>Povolit rozšíření na instanci, která již existuje:
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```

#### <a name="get-the-list-of-extensions-enabled"></a>Získat seznam povolených rozšíření:
Spusťte některý z následujících příkazů.

##### <a name="with-azdata"></a>S azdata
```console
azdata arc postgres server show -n <server group name>
```
Posuňte se ve výstupu a Všimněte si částí engine\extensions ve specifikacích vaší skupiny serverů. Například:
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
##### <a name="with-kubectl"></a>S kubectl
```console
kubectl describe postgresql-12s/postgres02
```
Posuňte se ve výstupu a Všimněte si částí engine\extensions ve specifikacích vaší skupiny serverů. Například:
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


### <a name="create-extensions"></a>Vytvořit rozšíření:
Připojte se ke skupině serverů pomocí nástroje klienta podle vašeho výběru a spusťte standardní dotaz PostgreSQL:
```console
CREATE EXTENSION <extension name>;
```

### <a name="get-the-list-of-extension-created-in-your-server-group"></a>Získat seznam rozšíření vytvořených ve skupině serverů:
Připojte se ke skupině serverů pomocí nástroje klienta podle vašeho výběru a spusťte standardní dotaz PostgreSQL:
```console
select * from pg_extension;
```

### <a name="drop-an-extension-from-your-server-group"></a>Přetáhněte rozšíření ze skupiny serverů:
Připojte se ke skupině serverů pomocí nástroje klienta podle vašeho výběru a spusťte standardní dotaz PostgreSQL:
```console
drop extension <extension name>;
```

## <a name="use-the-postgis-and-the-pg_cron-extensions"></a>Použití PostGIS a rozšíření Pg_cron

### <a name="the-postgis-extension"></a>Rozšíření PostGIS

Rozšíření PostGIS můžeme buď povolit ve stávající skupině serverů, nebo vytvořit nové s už povoleným rozšířením:

**Povolení rozšíření v době vytváření skupiny serverů:**
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server create -n pg2 -w 2 --extensions postgis
```

**Povolení rozšíření na instanci, která již existuje:**
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server edit --extensions postgis -n pg2
```

Chcete-li ověřit, jaká rozšíření jsou nainstalována, použijte níže uvedený standardní příkaz PostgreSQL po připojení k instanci s oblíbeným klientským nástrojem PostgreSQL, jako je Azure Data Studio:
```console
select * from pg_extension;
```

Pro PostGIS příklad nejprve získejte [ukázková data](http://duspviz.mit.edu/tutorials/intro-postgis/) z ministerstva městské studie MIT & plánování. Možná budete muset spustit `apt-get install unzip` , aby se při testování používal virtuální počítač k instalaci.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Pojďme se připojit k naší databázi a vytvořit rozšíření PostGIS:

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Pokud chcete použít jedno z rozšíření v `postgis` balíčku (například, `postgis_raster` `postgis_topology` `postgis_sfcgal` , `fuzzystrmatch` ...), musíte nejprve vytvořit rozšíření PostGIS a pak vytvořit druhé rozšíření. Instance: `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

A vytvořte schéma:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

Nyní můžeme kombinovat PostGIS s funkcí horizontálního navýšení kapacity tím, že coffee_shops tabulku rozšíříte:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Pojďme načíst nějaká data:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

A vyplňte `geom` pole pomocí správně kódované zeměpisné šířky a délky v `geometry` datovém typu PostGIS:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Teď můžeme seznamovat kavárny, které jsou nejblíže MIT (77 Massachusetts Ave na 42,359055,-71,093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


### <a name="the-pg_cron-extension"></a>Rozšíření pg_cron

Pojďme `pg_cron` na naši skupinu serverů PostgreSQL, a to i na PostGIS:

```console
azdata postgres server update -n pg2 -ns arc --extensions postgis,pg_cron
```

Všimněte si, že tato akce restartuje uzly a nainstaluje další rozšíření, což může trvat 2-3 minut.

Teď se můžeme znovu připojit a vytvořit `pg_cron` rozšíření:

```sql
CREATE EXTENSION pg_cron;
```

Pro účely testování umožňuje vytvořit tabulku `the_best_coffee_shop` , která bude mít náhodný název z naší předchozí `coffee_shops` tabulky, a nastaví obsah tabulky:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

`cron.schedule`K získání náhodného názvu tabulky můžeme použít plus několik příkazů SQL (Všimněte si, že se použije dočasná tabulka k uložení výsledku distribuovaného dotazu) a uloží se do `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

A teď získáte jiný název:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

Úplné podrobnosti o syntaxi najdete v [souboru readme pg_cron](https://github.com/citusdata/pg_cron) .

>[!NOTE]
>Rozšíření není podporované `citus` . `citus`Rozšíření se vyžaduje pro zajištění prostředí s škálovatelným škálováním.

## <a name="next-steps"></a>Další kroky:
- Přečíst dokumentaci k [plv8](https://plv8.github.io/)
- Přečíst dokumentaci k [PostGIS](https://postgis.net/)
- Přečíst dokumentaci [`pg_cron`](https://github.com/citusdata/pg_cron)
