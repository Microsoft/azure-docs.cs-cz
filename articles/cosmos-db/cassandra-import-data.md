---
title: Migrace dat do účtu rozhraní API Cassandra služby Azure Cosmos DB
description: Získejte další informace o kopírování dat z Apache Cassandra do rozhraní API Cassandra služby Azure Cosmos DB pomocí příkazu CQL Copy a Sparku.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.author: govindk
ms.topic: tutorial
ms.date: 09/24/2018
ms.reviewer: sngun
ms.openlocfilehash: 56fc07c6d775ee8015ce244acb7782607bda802a
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739783"
---
# <a name="tutorial-migrate-your-data-to-azure-cosmos-db-cassandra-api-account"></a>Kurz: Migrace dat do účtu rozhraní API Cassandra služby Azure Cosmos DB

Tento kurz obsahuje pokyny k migraci dat Apache Cassandra do rozhraní API Cassandra služby Azure Cosmos DB. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Plánování migrace
> * Požadavky na migraci
> * Migrace dat pomocí příkazu cqlsh COPY
> * Migrace dat pomocí Sparku 

## <a name="plan-for-migration"></a>Plánování migrace

Před migrací dat do rozhraní API Cassandra pro Azure Cosmos DB byste měli odhadnout svoje potřeby pro úlohy z hlediska propustnosti. Obecně se doporučuje začít s průměrnou propustností vyžadovanou pro operace CRUD a potom přidat další propustnost požadovanou pro extrakci, transformaci a načítání (ETL) a nárazové operace. K naplánování migrace budete potřebovat následující podrobnosti: 

* **Velikost stávajících dat nebo odhadovanou velikost dat:** Definuje minimální požadavky na velikost a propustnost databáze. Při odhadování velikosti dat pro novou aplikaci můžete předpokládat, že data jsou rovnoměrně distribuována mezi řádky, a odhadnout hodnotu vynásobením velikostí dat. 

* **Požadovaná propustnost:** Přibližná propustnost pro čtení (operace query/get) a zápis (operace update/delete/insert). Tato hodnota je nutná k výpočtu požadovaných jednotek žádostí (RU) spolu s velikostí dat při stabilním stavu.  

* **Získání schématu:** Připojte se ke svému existujícímu clusteru Cassandra prostřednictvím cqlsh a vyexportujte si z něj schéma: 

  ```bash
  cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
  ```

Po identifikování požadavků vaší existující úlohy byste měli podle zjištěných požadavků na propustnost vytvořit účet služby Azure Cosmos DB a také databázi a kontejnery.  

* **Zjištění poplatku za RU pro operaci:** Počet jednotek RU můžete zjistit pomocí sady Azure Cosmos DB Cassandra API SDK podle vašeho výběru. Tento příklad ukazuje získání poplatků za RU pro verzi .NET.

  ```csharp
  var tableInsertStatement = table.Insert(sampleEntity);
  var insertResult = await tableInsertStatement.ExecuteAsync();

  foreach (string key in insertResult.Info.IncomingPayload)
    {
       byte[] valueInBytes = customPayload[key];
       string value = Encoding.UTF8.GetString(valueInBytes);
       Console.WriteLine($"CustomPayload:  {key}: {value}");
    }
  ```

* **Přidělení požadované propustnosti:** Azure Cosmos DB dokáže automaticky škálovat úložiště a propustnost podle vašich rostoucích požadavků. S odhadem vašich potřeb z hlediska propustnosti vám pomůže [kalkulačka jednotek žádostí služby Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

## <a name="prerequisites-for-migration"></a>Požadavky na migraci

* **Vytvořené tabulky v rámci účtu rozhraní API Cassandra služby Azure Cosmos DB:** Před zahájením migrace dat si předem vytvořte všechny tabulky z portálu Azure Portal nebo z cqlsh. Pokud provádíte migraci do účtu služby Azure Cosmos DB, který má propustnost na úrovni databáze, nezapomeňte při vytváření kontejnerů Azure Cosmos DB zadat klíč oddílu.

* **Zvýšená propustnost:** Doba trvání migrace dat závisí na propustnosti, kterou pro tabulky v Azure Cosmos DB zřídíte. Po dobu trvání migrace propustnost zvyšte. Vyšší propustnost vám umožní zabránit omezování rychlosti a zkrátit dobu migrace. Po dokončení migrace propustnost snižte, abyste dosáhli nižších nákladů. Další informace o zvýšení propustnosti najdete v tématu věnovaném [nastavení propustnosti](set-throughput.md) pro kontejnery Azure Cosmos DB. Také se doporučuje mít účet služby Azure Cosmos DB ve stejné oblasti, v jaké je zdrojová databáze. 

* **Povolený protokol SSL:** Azure Cosmos DB má striktní bezpečnostní požadavky a standardy. Při práci se svým účtem nezapomeňte povolit SSL. Když použijete CQL s protokolem SSH, máte možnost zadat informace SSL.

## <a name="options-to-migrate-data"></a>Možnosti migrace dat

Data můžete přesunout data z existujících úloh Cassandra do Azure Cosmos DB pomocí následujících možností:

* [Pomocí příkazu cqlsh COPY](#using-cqlsh-copy-command)  
* [Pomocí Sparku](#using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrace dat pomocí příkazu cqlsh COPY

[Příkaz CQL COPY](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) se používá ke zkopírování místních dat do účtu rozhraní API Cassandra služby Azure Cosmos DB. Zkopírujte data provedením následujících kroků:

1. Získejte informace o připojovacím řetězci vašeho účtu rozhraní API Cassandra:

   * Přihlaste se na portál [Azure Portal](https://portal.azure.com) a přejděte na svůj účet služby Azure Cosmos DB.

   * Otevřete podokno **Připojovací řetězec**, které obsahuje všechny informace potřebné pro připojení k vašemu účtu rozhraní API Cassandra z cqlsh.

2. Přihlaste se ke cqhsh pomocí informací o připojení z portálu.

3. Pomocí příkazu CQL COPY zkopírujte místní data do účtu rozhraní API Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrace dat pomocí Sparku 

Provedením následujících kroků migrujte data do rozhraní API Cassandra pro Azure Cosmos DB pomocí Sparku:

- Zřiďte [cluster HDInsight](cassandra-spark-hdinsight.md) nebo [Azure Databricks](cassandra-spark-databricks.md). 

- Přesuňte data do cílového koncového bodu rozhraní API Cassandra pomocí [operace kopírování tabulky](cassandra-spark-table-copy-ops.md). 

Migrace dat pomocí sparkových úloh je doporučenou možností, pokud máte data uložená v existujícím clusteru ve virtuálních počítačích Azure nebo jakémkoli jiném cloudu. Tato možnost vyžaduje, aby byl Spark nastavený jako prostředník pro jednorázové nebo pravidelné ingestování dat. Tuto migraci můžete urychlit pomocí připojení Express Route mezi místním prostředím a Azure. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak si migrovat data do účtu rozhraní API Cassandra služby Azure Cosmos DB. Teď můžete přejít k části Koncepty, která obsahuje další informace o službě Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Nastavitelné úrovně konzistence dat v Azure Cosmos DB](../cosmos-db/consistency-levels.md)


