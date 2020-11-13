---
title: Logické dekódování – Azure Database for PostgreSQL – jeden server
description: Popisuje logické dekódování a wal2json pro Change Data Capture v Azure Database for PostgreSQLm jednom serveru.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: cda305ac705b728e0d2e129d7d42d53ea0251d86
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591525"
---
# <a name="logical-decoding"></a>Logické dekódování
 
> [!NOTE]
> Logické dekódování je ve verzi Public Preview na serveru Azure Database for PostgreSQL-Single.

[Logické dekódování v PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) umožňuje streamovat změny dat na externí příjemce. Logické dekódování se používá v oblíbených případech pro streamování událostí a Change Data Capture.

Logické dekódování používá výstupní modul plug-in pro převod protokolu Postgres (WAL) na zápis do čitelného formátu. Azure Database for PostgreSQL poskytuje výstupní moduly plug-in [wal2json](https://github.com/eulerto/wal2json), [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) a pgoutput. pgoutput je k dispozici prostřednictvím PostgreSQL z PostgreSQL verze 10 a.

Přehled toho, jak funguje logické dekódování Postgres, [najdete na našem blogu](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/change-data-capture-in-postgres-how-to-use-logical-decoding-and/ba-p/1396421). 

> [!NOTE]
> Logická replikace s použitím publikace nebo předplatného PostgreSQL není u serveru Azure Database for PostgreSQL-Single podporována.


## <a name="set-up-your-server"></a>Nastavení serveru 
Logické dekódování a [repliky čtení](concepts-read-replicas.md) jsou závislé na Postgres zápisu předem (WAL) pro informace. Tyto dvě funkce vyžadují různé úrovně protokolování z Postgres. Logické dekódování potřebuje vyšší úroveň protokolování než repliky čtení.

Ke konfiguraci správné úrovně protokolování použijte parametr podpory replikace Azure. Podpora replikace Azure má tři možnosti nastavení:

* **Off** – vloží do Wal minimální informace. Toto nastavení není k dispozici na většině Azure Database for PostgreSQL serverů.  
* **Replika** – přesnější podrobnější informace než **vypnuto**. Toto je minimální úroveň protokolování potřebného pro fungování [replik pro čtení](concepts-read-replicas.md) . Toto nastavení je na většině serverů výchozí.
* **Logický** – další podrobnější informace než **replika** Toto je minimální úroveň protokolování pro logické dekódování, které funguje. V tomto nastavení fungují i repliky pro čtení.


### <a name="using-azure-cli"></a>Použití Azure CLI

1. Nastavte azure.replication_support na `logical` .
   ```azurecli-interactive
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ``` 

2. Restartujte server, aby se změna projevila.
   ```azurecli-interactive
   az postgres server restart --resource-group mygroup --name myserver
   ```
3. Pokud používáte Postgres 9,5 nebo 9,6 a používáte přístup k veřejné síti, přidejte pravidlo brány firewall, abyste zahrnuli veřejnou IP adresu klienta, ze které budete spouštět logickou replikaci. Název pravidla brány firewall musí zahrnovat **_replrule**. Například *test_replrule*. Chcete-li vytvořit nové pravidlo brány firewall na serveru, spusťte příkaz [AZ Postgres server firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) . 

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal

1. Nastavte podporu replikace Azure na **logickou**. Vyberte **Uložit**.

   :::image type="content" source="./media/concepts-logical/replication-support.png" alt-text="Azure Database for PostgreSQL – replikace – podpora replikace Azure":::

2. Restartujte server, aby se změna projevila, a to tak, že vyberete **Ano**.

   :::image type="content" source="./media/concepts-logical/confirm-restart.png" alt-text="Azure Database for PostgreSQL-replikace – potvrzení restartování":::

3. Pokud používáte Postgres 9,5 nebo 9,6 a používáte přístup k veřejné síti, přidejte pravidlo brány firewall, abyste zahrnuli veřejnou IP adresu klienta, ze které budete spouštět logickou replikaci. Název pravidla brány firewall musí zahrnovat **_replrule**. Například *test_replrule*. Potom klikněte na **Uložit**.

   :::image type="content" source="./media/concepts-logical/client-replrule-firewall.png" alt-text="Azure Database for PostgreSQL – replikace – přidání pravidla brány firewall":::

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
 
2. Vydejte příkazy SQL. Například:
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

Postup vyřazení slotu replikace s názvem `test_slot` pomocí SQL:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Pokud přerušíte použití logického dekódování, změňte azure.replication_support zpět na `replica` nebo `off` . Podrobnosti WAL uchované `logical` jsou podrobněji podrobnější a měly by být zakázány, pokud se logické dekódování nepoužívá. 

 
## <a name="next-steps"></a>Další kroky

* [Další informace o logickém dekódování](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)najdete v dokumentaci k Postgres.
* Pokud máte dotazy týkající se logického dekódování, obraťte se na [náš tým](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) .
* Přečtěte si další informace o [replikách pro čtení](concepts-read-replicas.md).

