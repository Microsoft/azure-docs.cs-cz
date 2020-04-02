---
title: Logické dekódování – databáze Azure pro PostgreSQL – jeden server
description: Popisuje logické dekódování a wal2json pro sběr dat změny v Azure Database for PostgreSQL – single server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522144"
---
# <a name="logical-decoding"></a>Logické dekódování
 
[Logické dekódování v PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) umožňuje streamovat změny dat externím spotřebitelům. Logické dekódování se populárně používá pro streamování událostí a změny dat zachytit scénáře.

Logické dekódování používá výstupní plugin pro převod postgresského zápisu do předcházející log (WAL) do čitelného formátu. Azure Database for PostgreSQL poskytuje dva výstupní pluginy: [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) a [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> Logické dekódování je ve verzi Public Preview v Azure Database for PostgreSQL – Single Server.


## <a name="set-up-your-server"></a>Nastavení serveru
Chcete-li začít používat logické dekódování, povolte serveru uložit a streamovat WAL. 

1. Nastavte azure.replication_support `logical` na používání azure CLI. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Pokud používáte čtení repliky, azure.replication_support nastavit `logical` také umožňuje repliky ke spuštění. Pokud přestanete používat logické dekódování, `replica`změňte nastavení zpět na . 


2. Chcete-li změny použít, restartujte server.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Spustit logické dekódování

Logické dekódování lze spotřebovávat pomocí protokolu streamování nebo rozhraní SQL. Obě metody používají [replikační sloty](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS). Patice představuje datový proud změn z jedné databáze.

Použití slotu replikace vyžaduje oprávnění replikace postgresu. V tuto chvíli je oprávnění replikace k dispozici pouze pro uživatele správce serveru. 

### <a name="streaming-protocol"></a>Protokol streamování
Náročné změny pomocí protokolu streamování je často vhodnější. Můžete si vytvořit svůj vlastní spotřebitel / konektor, nebo použít nástroj, jako [je Debezium](https://debezium.io/). 

Navštivte wal2json dokumentaci příklad [pomocí streaming protokolu s pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>Rozhraní SQL
V níže uvedeném příkladu používáme rozhraní SQL s pluginem wal2json.
 
1. Vytvořte slot.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Vyvydat příkazy SQL. Například:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Spotřebovávat změny.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   Výstup bude vypadat takto:
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

4. Drop slot, jakmile jste hotovi s jeho používáním.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Monitorovací sloty

Je nutné sledovat logické dekódování. Všechny nepoužívané replikační slot musí být vynechány. Sloty držet na Postgres WAL protokoly a příslušné systémové katalogy, dokud změny byly přečteny spotřebitelem. Pokud váš spotřebitel selže nebo nebyl správně nakonfigurován, nespotřebované protokoly se hromadí a zaplní úložiště. Nespotřebované protokoly také zvyšují riziko obtékání ID transakce. Obě situace mohou způsobit, že server přestane být k dispozici. Proto je důležité, aby byly chyby logické replikace spotřebovány nepřetržitě. Pokud se již nepoužívá slot logické replikace, okamžitě jej přetáhněte.

Sloupec "aktivní" v zobrazení pg_replication_slots bude označovat, zda je k patice připojen spotřebitel.
```SQL
SELECT * FROM pg_replication_slots;
```

[Nastavte výstrahy](howto-alert-on-metric.md) na *storage používá* a *maximální zpoždění mezi metriky repliky* upozornit, když hodnoty zvýšit za normální prahové hodnoty. 

> [!IMPORTANT]
> Je nutné vynechat nepoužívané sloty replikace. Pokud tak neučiníte, může to vést k nedostupnosti serveru.

## <a name="how-to-drop-a-slot"></a>Jak upustit slot
Pokud nejste aktivně spotřebovává replikační slot, měli byste ho vynechat.

Přetažení replikačního `test_slot` slotu s názvem sql:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Pokud přestanete používat logické dekódování, změňte azure.replication_support zpět na `replica` nebo `off`. Wal podrobnosti uchovávané jsou `logical` podrobnější a by měly být zakázány, pokud logické dekódování není používán. 

 
## <a name="next-steps"></a>Další kroky

* Další informace [o logickém dekódování](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)naleznete v dokumentaci k postgresu .
* Pokud máte otázky ohledně logického dekódování, spojte se s [naším týmem.](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)
* Další informace o [čtení replik](concepts-read-replicas.md).

