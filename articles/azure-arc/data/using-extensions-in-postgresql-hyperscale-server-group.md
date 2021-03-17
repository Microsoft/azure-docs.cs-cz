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
ms.openlocfilehash: 6586375d7db71274f40eb62aeb24f9daad0d7c2e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688293"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Použijte rozšíření PostgreSQL ve skupině serverů PostgreSQL s podporou škálování na úrovni Azure ARC

PostgreSQL je nejlépe při použití s rozšířeními. Klíčovým prvkem naší vlastní funkce škálování je ve skutečnosti rozšíření poskytované společností Microsoft, `citus` které je ve výchozím nastavení nainstalované, což umožňuje Postgres transparentně horizontálních oddílů data napříč více uzly.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>Podporovaná rozšíření
Standardní [`contrib`](https://www.postgresql.org/docs/12/contrib.html) rozšíření a následující rozšíření jsou již nasazena v kontejnerech skupiny serverů s povoleným PostgreSQLm rozšířením Azure ARC:
- [`citus`](https://github.com/citusdata/citus), v: 9,4. Rozšíření Citus podle [dat Citus](https://www.citusdata.com/) se ve výchozím nastavení načítají, protože přináší schopnost škálování modulu PostgreSQL. Vyřazení rozšíření Citus ze skupiny serverů PostgreSQL s rozšířením s vlastním škálováním ve službě Azure Arc se nepodporuje.
- [`pg_cron`](https://github.com/citusdata/pg_cron), v: 1,2
- [`pgaudit`](https://www.pgaudit.org/), v: 1,4
- plpgsql, v: 1,0
- [`postgis`](https://postgis.net), v: 3.0.2
- [`plv8`](https://plv8.github.io/), v: 2.3.14

Aktualizace tohoto seznamu se budou publikovat, když se v průběhu času vyvíjí.

> [!IMPORTANT]
> I když se dá skupině serverů přenést rozšíření jiné než uvedené výše, v této verzi Preview se neuloží do systému. To znamená, že nebude k dispozici po restartování systému a bude nutné ho znovu přenést.

Tato příručka povede ve scénáři použití dvou z těchto rozšíření:
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>Jaká rozšíření je potřeba přidat do shared_preload_libraries a vytvořit?

|Rozšíření   |Vyžaduje přidání do shared_preload_libraries  |Vyžaduje vytvoření |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |Ne       |Ano        |
|`pg_audit`     |Ano       |Ano        |
|`plpgsql`      |Ano       |Ano        |
|`postgis`      |Ne       |Ano        |
|`plv8`      |Ne       |Ano        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>Přidat rozšíření do shared_preload_libraries
Podrobnosti o tom, co se shared_preload_libraries, najdete v dokumentaci k PostgreSQL v [tomto](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES)článku:
- Tento krok není potřebný pro rozšíření, která jsou součástí `contrib`
- Tento krok není nutný pro rozšíření, která nejsou nutná k předběžnému načtení shared_preload_libraries. U těchto rozšíření můžete přejít k dalšímu dalšímu odstavci [vytvořit rozšíření](https://docs.microsoft.com/azure/azure-arc/data/using-extensions-in-postgresql-hyperscale-server-group#create-extensions).

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>Přidání rozšíření v okamžiku vytvoření skupiny serverů
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>Přidat rozšíření k instanci, která již existuje
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>Zobrazit seznam rozšíření přidaných do shared_preload_libraries
Spusťte některý z následujících příkazů.

### <a name="with-an-azdata-cli-command"></a>Pomocí příkazu CLI azdata
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
### <a name="with-kubectl"></a>S kubectl
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


## <a name="create-extensions"></a>Vytvořit rozšíření
Připojte se ke skupině serverů pomocí nástroje klienta podle vašeho výběru a spusťte standardní dotaz PostgreSQL:
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>Zobrazí seznam vytvořených rozšíření.
Připojte se ke skupině serverů pomocí nástroje klienta podle vašeho výběru a spusťte standardní dotaz PostgreSQL:
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>Odstranit rozšíření
Připojte se ke skupině serverů pomocí nástroje klienta podle vašeho výběru a spusťte standardní dotaz PostgreSQL:
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>`PostGIS`Rozšíření
Nemusíte přidávat `PostGIS` rozšíření do `shared_preload_libraries` .
Získejte [ukázková data](http://duspviz.mit.edu/tutorials/intro-postgis/) z ministerstva městské studie MIT & plánování. Spusťte `apt-get install unzip` pro instalaci rozbalení podle potřeby.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Pojďme se připojit k naší databázi a vytvořit `PostGIS` rozšíření:

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

Nyní můžeme kombinovat `PostGIS` s funkcí škálování na více instancí tím, že coffee_shops tabulku rozšíříte:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Pojďme načíst nějaká data:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

A vyplňte `geom` pole správnou zakódovanou šířkou zeměpisné šířky a délky v `PostGIS` `geometry` datovém typu:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Teď můžeme seznamovat kavárny, které jsou nejblíže MIT (77 Massachusetts Ave na 42,359055,-71,093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>`pg_cron`Rozšíření

Teď to můžeme povolit `pg_cron` na naší skupině PostgreSQL serverů tak, že ji přidáte do shared_preload_libraries:

```console
azdata postgres server update -n pg2 -ns arc --extensions pg_cron
```

Vaše skupina serverů se restartuje a dokončí instalaci rozšíření. Může to trvat 2 až 3 minuty.

Teď se můžeme znovu připojit a vytvořit `pg_cron` rozšíření:

```sql
CREATE EXTENSION pg_cron;
```

Pro účely testování umožňuje vytvořit tabulku `the_best_coffee_shop` , která bude mít náhodný název z naší předchozí `coffee_shops` tabulky, a vloží obsah tabulky:

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


## <a name="next-steps"></a>Další kroky
- Přečíst dokumentaci [`plv8`](https://plv8.github.io/)
- Přečíst dokumentaci [`PostGIS`](https://postgis.net/)
- Přečíst dokumentaci [`pg_cron`](https://github.com/citusdata/pg_cron)
