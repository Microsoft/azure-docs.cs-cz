---
title: Migrace dat z databáze PostgreSQL do skupiny serverů s povoleným PostgreSQLem s rozšířením.
titleSuffix: Azure Arc enabled database services
description: Migrace dat z databáze PostgreSQL do skupiny serverů s povoleným PostgreSQLem s rozšířením.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d9cbfc30b10373ad2a4f4304987dac426b5dcabe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101643571"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Migrace databáze PostgreSQL do skupiny serverů s povoleným PostgreSQL a škálováním na úrovni Azure

Tento dokument popisuje kroky pro získání existující databáze PostgreSQL (ta, která není hostovaná ve službě Azure ARC s povoleným Data Services), do skupiny serverů PostgreSQL s podporou škálování na úrovni Azure ARC.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>Požadavky

PostgreSQL skupina serverů s podporou Azure ARC je verze komunity PostgreSQL a je spuštěná s povoleným rozšířením CitusData. Takže jakýkoli nástroj, který funguje na PostgreSQL mimo ARC Azure, by měl spolupracovat se skupinou serverů PostgreSQL s podporou škálování na úrovni Azure ARC.


Podobně jako u sady nástrojů, které dnes používáte pro Postgres, byste měli mít tyto možnosti:
1. Zálohování databáze Postgres z vaší instance hostované mimo ARC Azure
2. Obnovení ve skupině serverů s povoleným PostgreSQLm rozšířením Azure ARC

K čemu zbývá:
- resetování parametrů serveru
- resetovat kontexty zabezpečení: znovu vytvořit uživatele, role a obnovit oprávnění...

Chcete-li provést tuto operaci zálohování nebo obnovení, můžete použít libovolný nástroj, který může provádět zálohování a obnovení pro Postgres. Například:
- Azure Data Studio a jeho rozšíření Postgres
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>Příklad
Pojďme tyto kroky ilustrovat pomocí `pgAdmin` nástroje.
Vezměte v úvahu následující nastavení:
- **Zdrojová**  
    Server Postgres běžící místně na holém serveru a s názvem JEANYDSRV. Má verzi 12 a hostuje databázi s názvem MyOnPremPostgresDB, která má jednu tabulku T1, která má 1 řádek :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="migrace – zdroj":::

- **Tabulka**  
    Postgres Server běžící v prostředí Azure ARC a s názvem postgres01. Verze je 12. Nemá žádnou databázi s výjimkou standardní databáze Postgres.  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="Migrace – cíl":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>Pořídit zálohu zdrojové databáze místně

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="Migrace – zdroj-záloha":::

Nakonfigurovat:
1. Zadat název souboru: **MySourceBackup**
2. Nastavení formátu na **vlastní** 
 :::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="migrace – zdroj-zálohování – konfigurace":::

Zálohování bylo úspěšně dokončeno:  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="Migrace – zdroj-zálohování – dokončeno":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Vytvoření prázdné databáze v cílovém systému v PostgreSQL skupině serverů s povoleným rozšířením Azure ARC

> [!NOTE]
> Pokud chcete v nástroji zaregistrovat instanci Postgres `pgAdmin` , musíte použít veřejnou IP adresu vaší instance v clusteru Kubernetes a odpovídajícím způsobem nastavit port a kontext zabezpečení. Tyto podrobnosti najdete na `psql` řádku koncového bodu po spuštění následujícího příkazu:

```console
azdata arc postgres endpoint list -n postgres01
```
Vrátí výstup podobný tomuto:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

Pojďme pojmenovat cílovou databázi **RESTORED_MyOnPremPostgresDB**.

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="Migrace – cíl-DB-Create" lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>Obnovení databáze v nastavení ARC

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="Migratre-DB – obnovení":::

Nakonfigurujte obnovení:
1. Najeďte na soubor obsahující zálohu, která se má obnovit: **MySourceBackup**
2. Zachovat formát nastavený na **vlastní nebo tar** 
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="migrace – DB-Restore-Configure":::

3. Klikněte na **obnovit**.  

   Obnovení bylo úspěšné.  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="Migrace – DB-Restore-Completed":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Ověřte, že se databáze úspěšně obnovila ve skupině serverů PostgreSQL s povoleným rozšířením Azure ARC.

Použijte jednu z následujících metod:

**Od `pgAdmin` :**  

Rozbalte instanci Postgres hostovanou v nastavení ARC Azure. Zobrazí se tabulka v databázi, kterou jste obnovili, a když vyberete data, která zobrazuje stejný řádek jako v místní instanci:

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="Migrace – DB-Restore-ověření":::

**V `psql` rámci vašeho nastavení ARC Azure:**  

V rámci nastavení ARC můžete použít `psql` pro připojení k instanci Postgres, nastavení kontextu databáze `RESTORED_MyOnPremPostgresDB` a dotazování na data:

1. Seznam koncových bodů, které vám pomůžou s `psql` připojovacím řetězcem:

   ```console
   azdata arc postgres endpoint list -n postgres01
   [
     {
       "Description": "PostgreSQL Instance",
       "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
     },
     {
       "Description": "Log Search Dashboard",
       "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
     },
     {
       "Description": "Metrics Dashboard",
       "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
     }
   ]
   ```

1. Z `psql` připojovacího řetězce použijte `-d` parametr k označení názvu databáze. Pomocí níže uvedeného příkazu se zobrazí výzva k zadání hesla:

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` pojovat.

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. Vyberte tabulku a zobrazí se data, která jste obnovili z místní instance Postgres:

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - Neuvidíte, že se vám neprojeví výhody výkonu provozu na PostgreSQL s povoleným rozšířením Azure ARC, dokud nebudete horizontální navýšení kapacity a Vy horizontálních oddílů/distribuujete data mezi pracovními uzly vaší skupiny serverů PostgreSQL s vlastním škálováním. Další informace najdete v části [Další kroky](#next-steps).
>
> - Stávající instanci Postgres, která by běžela místně nebo v jakémkoli jiném cloudu, není možné v dnešní době připojit do Azure ARC. Jinými slovy, není možné do existující instance Postgres nainstalovat nějaký druh "agent Azure ARC", aby bylo nastavení Postgres povoleno pomocí ARC Azure. Místo toho je třeba vytvořit novou instanci Postgres a přenést do ní data. K tomu můžete použít techniku uvedenou výše, nebo můžete použít libovolný nástroj ETL podle vašeho výběru.

## <a name="next-steps"></a>Další kroky

- Přečtěte si téma koncepty a návody Azure Database for PostgreSQLho škálování pro distribuci dat napříč několika PostgreSQL uzly a využijte výhod Azure Database for PostgreSQLho škálování na úrovni:
    * [Uzly a tabulky](../../postgresql/concepts-hyperscale-nodes.md)
    * [Určení typu aplikace](../../postgresql/concepts-hyperscale-app-type.md)
    * [Volba distribučního sloupce](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Společné umístění tabulek](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuce a úprava tabulek](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Návrh databáze s více klienty](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Návrh řídicího panelu pro analýzu v reálném čase](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> * V těchto dokumentech přeskočte oddíly **Přihlaste se k Azure Portal** a **Vytvořte Azure Database for Postgres – Citus (škálování)**. Implementujte zbývající kroky v nasazení ARC Azure. Tyto části jsou specifické pro Azure Database for PostgreSQL Citus (PaaS), které nabízíme jako službu v cloudu Azure, ale ostatní části dokumentů jsou přímo použitelné pro PostgreSQL škálování na úrovni Azure ARC.

- [Škálování skupiny serverů Azure Database for PostgreSQL Hyperscale na více instancí](scale-out-postgresql-hyperscale-server-group.md)
