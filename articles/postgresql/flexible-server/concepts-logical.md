---
title: Logická replikace a logické dekódování – Azure Database for PostgreSQL – flexibilní Server
description: Další informace o použití logické replikace a logického dekódování v Azure Database for PostgreSQL-flexibilním serveru
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: fd0826ad11a153d72ee47f35930d25f0df498418
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936775"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Logická replikace a logické dekódování v Azure Database for PostgreSQL-flexibilním serveru

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Logická replikace PostgreSQL a funkce logického dekódování jsou podporovány v Azure Database for PostgreSQL-flexibilním serveru.

## <a name="comparing-logical-replication-and-logical-decoding"></a>Porovnání logické replikace a logického dekódování
Logická replikace a logické dekódování mají několik podobností. Oba směry
* umožňuje replikovat data z Postgres.
* jako zdroj změn použít [Protokol WAL (zápis dopředu)](https://www.postgresql.org/docs/current/wal.html)
* k odesílání dat použijte [sloty logické replikace](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) . Slot představuje datový proud změn.
* k určení, které změny je možné odeslat, použijte [vlastnost identity repliky](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY) tabulky.
* Nereplikovat změny DDL


Tyto dvě technologie mají rozdíly: logické replikace 
* umožňuje zadat tabulku nebo sadu tabulek, které se mají replikovat.
* replikuje data mezi instancemi PostgreSQL.

Logické dekódování 
* extrahuje změny ve všech tabulkách v databázi. 
* data mezi PostgreSQL instancemi nelze přímo odeslat.


## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>Předpoklady pro logickou replikaci a logické dekódování

1. Nastavte parametr serveru `wal_level` na `logical` .
2. Restartujte server, aby se změna projevila `wal_level` .
3. Potvrďte, že vaše instance PostgreSQL umožňuje síťový provoz z vašeho připojujícího se prostředku.
4. Při provádění příkazů replikace použijte uživatele s oprávněními správce.

## <a name="using-logical-replication-and-logical-decoding"></a>Použití logické replikace a logického dekódování

### <a name="logical-replication"></a>Logická replikace
Logická replikace používá výrazy Publisher a předplatitel. 
* Vydavatelem je databáze PostgreSQL, **ze**které odesíláte data. 
* Předplatitelem je databáze PostgreSQL, **do**které odesíláte data.

Tady je ukázkový kód, který můžete použít k vyzkoušení logické replikace.

1. Připojte se k vydavateli. Vytvořte tabulku a přidejte nějaká data.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. Vytvoří publikaci pro tabulku.
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. Připojte se k předplatiteli. Vytvořte tabulku se stejným schématem, jako má Vydavatel.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. Vytvořte předplatné, které se připojí k publikaci, kterou jste vytvořili dříve.
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname>' PUBLICATION pub;
   ```

5. Nyní můžete zadat dotaz na tabulku v odběrateli. Uvidíte, že od vydavatele obdrželi data.
   ```SQL
   SELECT * FROM basic;
   ```

Do tabulky vydavatele můžete přidat další řádky a zobrazit změny v odběrateli.

Další informace o [logické replikaci](https://www.postgresql.org/docs/current/logical-replication.html)najdete v dokumentaci k PostgreSQL.

### <a name="logical-decoding"></a>Logické dekódování
Logické dekódování lze spotřebovat prostřednictvím protokolu streamování nebo rozhraní SQL. 

#### <a name="streaming-protocol"></a>Protokol streamování
Využívání změn pomocí protokolu streamování je často vhodnější. Můžete vytvořit vlastní příjemce nebo konektor nebo použít službu třetí strany, jako je [Debezium](https://debezium.io/). 

[Příklad použití protokolu streamování s pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical)najdete v dokumentaci k wal2json.

#### <a name="sql-interface"></a>Rozhraní SQL 
V následujícím příkladu používáme rozhraní SQL s modulem plug-in wal2json.
 
1. Vytvořte slot.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Vydejte příkazy SQL. Příklad:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Využití změn.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   Výstup bude vypadat nějak takto:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Po dokončení používání slotu.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

Další informace o [logickém dekódování](https://www.postgresql.org/docs/current/logicaldecoding.html)najdete v dokumentaci k PostgreSQL.


## <a name="monitoring"></a>Monitorování
Je nutné monitorovat logické dekódování. Je nutné vyřadit všechny nepoužívané replikační přihrádky. Sloty mají na Postgres protokoly WAL a příslušné Systémové katalogy, dokud se nepřečtou změny. Pokud váš předplatitel nebo příjemce selže nebo není správně nakonfigurovaný, budou nevyužité protokoly zaplněny a vyplňovat vaše úložiště. Nevyužité protokoly také zvyšují riziko ID transakce wraparound. V obou situacích může dojít k nedostupnosti serveru. Proto je důležité, aby logické sloty replikace byly nepřetržitě spotřebovány. Pokud se už logický replikační slot nepoužívá, okamžitě ho umístěte.

Sloupec "aktivní" v zobrazení pg_replication_slots bude označovat, zda je příjemce připojen ke slotu.
```SQL
SELECT * FROM pg_replication_slots;
```

[Nastavte výstrahy](howto-alert-on-metrics.md) na základě **maximálního počtu použitých ID transakcí** a úložiště, které **používají** flexibilní metriky serveru, aby vás upozornily na to, že hodnoty zvyšují minulé normální prahové hodnoty 

## <a name="read-replicas"></a>Čtení replik
Azure Database for PostgreSQL replik pro čtení se v současnosti nepodporují pro flexibilní servery.

## <a name="next-steps"></a>Další kroky
* Další informace o [možnostech sítě](concepts-networking.md)
* Informace o [rozšířeních](concepts-extensions.md) dostupných v flexibilním serveru
* Další informace o [vysoké dostupnosti](concepts-high-availability.md)

