---
title: Logické dekódování – Azure Database for PostgreSQL – jeden server
description: Popisuje logické dekódování a wal2json pro Change Data Capture v Azure Database for PostgreSQLm jednom serveru.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80522144"
---
# <a name="logical-decoding"></a>Logické dekódování
 
[Logické dekódování v PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) umožňuje streamovat změny dat na externí příjemce. Logické dekódování se používá v oblíbených případech pro streamování událostí a Change Data Capture.

Logické dekódování používá výstupní modul plug-in pro převod protokolu Postgres (WAL) na zápis do čitelného formátu. Azure Database for PostgreSQL poskytuje dva výstupní moduly plug-in: [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) a [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> Logické dekódování je ve verzi Public Preview na serveru Azure Database for PostgreSQL-Single.


## <a name="set-up-your-server"></a>Nastavení serveru
Pokud chcete začít používat logické dekódování, umožněte serveru ukládat a streamovat WAL. 

1. Nastavte Azure. replication_support na `logical` používání rozhraní příkazového řádku Azure CLI. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Pokud používáte repliky pro čtení, Azure. replication_support nastavíte `logical` k povolení spouštění replik. Pokud přerušíte použití logického dekódování, změňte nastavení zpět `replica`na. 


2. Restartujte server, aby se změny projevily.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Spustit logické dekódování

Logické dekódování lze spotřebovat prostřednictvím protokolu streamování nebo rozhraní SQL. Obě metody používají [sloty replikace](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS). Slot představuje datový proud změn z jedné databáze.

Použití slotu replikace vyžaduje oprávnění pro replikaci Postgres. V současné době je oprávnění replikace k dispozici pouze pro uživatele s oprávněními správce serveru. 

### <a name="streaming-protocol"></a>Protokol streamování
Využívání změn pomocí protokolu streamování je často vhodnější. Můžete vytvořit vlastní příjemce nebo konektor nebo použít nástroj, jako je [Debezium](https://debezium.io/). 

[Příklad použití protokolu streamování s pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical)najdete v dokumentaci k wal2json.


### <a name="sql-interface"></a>Rozhraní SQL
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


## <a name="monitoring-slots"></a>Sloty monitorování

Je nutné monitorovat logické dekódování. Je nutné vyřadit všechny nepoužívané replikační přihrádky. Sloty se uchovávají pro Postgres protokolů WAL a relevantních systémových katalogů, dokud je uživatel nepřečte. Pokud váš příjemce selže nebo není správně nakonfigurovaný, nevyužité protokoly se budou pohromadit a vyplnit úložiště. Nevyužité protokoly také zvyšují riziko ID transakce wraparound. V obou situacích může dojít k nedostupnosti serveru. Proto je důležité, aby logické sloty replikace byly nepřetržitě spotřebovány. Pokud se už logický replikační slot nepoužívá, okamžitě ho umístěte.

Sloupec "aktivní" v zobrazení pg_replication_slots bude označovat, zda je příjemce připojen ke slotu.
```SQL
SELECT * FROM pg_replication_slots;
```

[Nastavte výstrahy](howto-alert-on-metric.md) na *využité úložiště* a *maximální prodlevu mezi replikami* a upozorněte na to, že hodnoty zvyšují minulé normální prahové hodnoty. 

> [!IMPORTANT]
> Je nutné vyřadit nepoužívané replikační sloty. Navrácení služeb po obnovení může způsobit nedostupnost serveru.

## <a name="how-to-drop-a-slot"></a>Postup vyřazení slotu
Pokud slot pro replikaci aktivně nespotřebováváte, měli byste ho vyřadit.

Postup vyřazení slotu replikace `test_slot` s názvem pomocí SQL:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Pokud přestanete používat logické dekódování, změňte Azure. replication_support zpět `replica` na `off`nebo. Podrobnosti WAL uchované `logical` jsou podrobněji podrobnější a měly by být zakázány, pokud se logické dekódování nepoužívá. 

 
## <a name="next-steps"></a>Další kroky

* [Další informace o logickém dekódování](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)najdete v dokumentaci k Postgres.
* Pokud máte dotazy týkající se logického dekódování, obraťte se na [náš tým](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) .
* Přečtěte si další informace o [replikách pro čtení](concepts-read-replicas.md).

