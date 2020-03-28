---
title: Migrace dat do účtu cassandry API v kurzu Azure Cosmos DB-
description: V tomto kurzu se dozvíte, jak pomocí příkazu CQL Copy & Spark zkopírovat data z Apache Cassandra do účtu rozhraní API Cassandra v Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
Customer intent: As a developer, I want to migrate my existing Cassandra workloads to Azure Cosmos DB so that the overhead to manage resources, clusters, and garbage collection is automatically handled by Azure Cosmos DB.
ms.openlocfilehash: c754740369da6d0a8084b9b60ef178fb28e32f1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75445672"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>Kurz: Migrace dat do účtu Cassandro API v Azure Cosmos DB

Jako vývojář můžete mít existující úlohy Cassandra, které běží místně nebo v cloudu, a možná je budete chtít migrovat do Azure. Tyto úlohy můžete migrovat do účtu rozhraní CASSAndRo API v Azure Cosmos DB. Tento kurz obsahuje pokyny k různým možnostem, které jsou k dispozici pro migraci dat Apache Cassandra do účtu rozhraní API Cassandra v Azure Cosmos DB.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Plánování migrace
> * Požadavky na migraci
> * Migrace dat pomocí příkazu cqlsh COPY
> * Migrace dat pomocí Sparku

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites-for-migration"></a>Požadavky na migraci

* **Odhadněte své propustnost:** Před migrací dat do účtu cascassandra api v Azure Cosmos DB byste měli odhadnout propustnost vašeho pracovního vytížení. Obecně se doporučuje začít s průměrnou propustností vyžadovanou pro operace CRUD a potom přidat další propustnost požadovanou pro extrakci, transformaci a načítání (ETL) a nárazové operace. K naplánování migrace budete potřebovat následující podrobnosti: 

  * **Velikost stávajících dat nebo odhadovanou velikost dat:** Definuje minimální požadavky na velikost a propustnost databáze. Při odhadování velikosti dat pro novou aplikaci můžete předpokládat, že data jsou rovnoměrně distribuována mezi řádky, a odhadnout hodnotu vynásobením velikostí dat. 

  * **Požadovaná propustnost:** Přibližná rychlost propustnosti pro čtení (dotaz/get) a zápis (aktualizace/odstranění/vložení). Tato hodnota je nutná k výpočtu požadovaných jednotek žádostí (RU) spolu s velikostí dat při stabilním stavu.  

  * **Schéma:** Připojte se k existujícímu clusteru Cassandra prostřednictvím cqlsh a exportujte schéma z Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Po identifikaci požadavků na vaše existující úlohy, měli byste vytvořit účet Azure Cosmos, databáze a kontejnery podle požadavků na shromážděné propustnost.  

  * **Určete poplatek ŽP pro operaci:** Můžete určit ru pomocí některého z sad SDK podporované rozhraní mandralina kódem . Tento příklad ukazuje získání poplatků za RU pro verzi .NET.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **Přidělení požadované propustnosti:** Azure Cosmos DB dokáže automaticky škálovat úložiště a propustnost podle vašich rostoucích požadavků. S odhadem vašich potřeb z hlediska propustnosti vám pomůže [kalkulačka jednotek žádostí služby Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

* **Vytvořte tabulky v účtu Cassandra API:** Než začnete migrovat data, předem vytvořte všechny tabulky z webu Azure Portal nebo z cqlsh. Pokud migrujete na účet Azure Cosmos, který má propustnost na úrovni databáze, ujistěte se, že při vytváření kontejnerů Azure Cosmos poskytnete klíč oddílu.

* **Zvýšená propustnost:** Doba trvání migrace dat závisí na propustnosti, kterou pro tabulky v Azure Cosmos DB zřídíte. Po dobu trvání migrace propustnost zvyšte. Vyšší propustnost vám umožní zabránit omezování rychlosti a zkrátit dobu migrace. Po dokončení migrace propustnost snižte, abyste dosáhli nižších nákladů. Doporučujese také mít účet Azure Cosmos ve stejné oblasti jako zdrojová databáze. 

* **Povolený protokol SSL:** Azure Cosmos DB má striktní bezpečnostní požadavky a standardy. Při práci se svým účtem nezapomeňte povolit SSL. Když použijete CQL s protokolem SSH, máte možnost zadat informace SSL.

## <a name="options-to-migrate-data"></a>Možnosti migrace dat

Data můžete přesunout data z existujících úloh Cassandra do Azure Cosmos DB pomocí následujících možností:

* [Pomocí příkazu cqlsh COPY](#migrate-data-using-cqlsh-copy-command)  
* [Pomocí Sparku](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrace dat pomocí příkazu cqlsh COPY

[Příkaz CQL COPY](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) se používá ke kopírování místních dat do účtu rozhraní API Cassandra v Azure Cosmos DB. Zkopírujte data provedením následujících kroků:

1. Získejte informace o připojovacím řetězci vašeho účtu rozhraní API Cassandra:

   * Přihlaste se k [portálu Azure](https://portal.azure.com)a přejděte na svůj účet Azure Cosmos.

   * Otevřete podokno **Připojovací řetězec**, které obsahuje všechny informace potřebné pro připojení k vašemu účtu rozhraní API Cassandra z cqlsh.

2. Přihlaste se ke cqhsh pomocí informací o připojení z portálu.

3. Pomocí příkazu CQL COPY zkopírujte místní data do účtu rozhraní API Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrace dat pomocí Sparku 

Pomocí následujících kroků migrujte data do účtu Cassandra API se Sparkem:

- Zřízení [clusteru Azure Databricks](cassandra-spark-databricks.md) nebo [clusteru HDInsight](cassandra-spark-hdinsight.md) 

- Přesunutí dat do cílového koncového bodu rozhraní CASSANDRA API pomocí [operace kopírování tabulky](cassandra-spark-table-copy-ops.md) 

Migrace dat pomocí úloh Spark je doporučená možnost, pokud máte data v existujícím clusteru ve virtuálních počítačích Azure nebo v jakémkoli jiném cloudu. Tato možnost vyžaduje, aby byla Spark nastavena jako zprostředkovatel pro jeden čas nebo pravidelné požití. Tuto migraci můžete urychlit pomocí připojení Azure ExpressRoute mezi místním a Azure. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nejsou potřeba, můžete odstranit skupinu prostředků, účet Azure Cosmos a všechny související prostředky. Chcete-li tak učinit, vyberte skupinu prostředků pro virtuální počítač, vyberte **Odstranit**a potvrďte název skupiny prostředků, kterou chcete odstranit.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak migrovat data do účtu Casandři a rozhraní API v Azure Cosmos DB. Teď můžete přejít k následujícímu článku, kde se dozvíte o dalších konceptech Azure Cosmos DB:

> [!div class="nextstepaction"]
> [Nastavitelné úrovně konzistence dat v Azure Cosmos DB](../cosmos-db/consistency-levels.md)


