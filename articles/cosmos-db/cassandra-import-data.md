---
title: Migrace dat na účet rozhraní API Cassandra v Azure Cosmos DB kurzu
description: V tomto kurzu se naučíte používat příkaz CQL Copy & Spark ke kopírování dat z Apache Cassandra na účet rozhraní API Cassandra v Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: b450d8463c09647b8f87547afe9d714c4b8659c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82858557"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>Kurz: migrace dat na účet rozhraní API Cassandra v Azure Cosmos DB

Jako vývojář můžete mít stávající Cassandra úlohy, které běží místně nebo v cloudu, a můžete je chtít migrovat do Azure. Tyto úlohy můžete migrovat na účet rozhraní API Cassandra v Azure Cosmos DB. V tomto kurzu najdete informace o různých možnostech, které jsou k dispozici pro migraci dat Apache Cassandra do účtu rozhraní API Cassandra v Azure Cosmos DB.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Plánování migrace
> * Požadavky pro migraci
> * Migrace dat pomocí příkazu cqlsh COPY
> * Migrace dat pomocí Sparku

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites-for-migration"></a>Požadavky pro migraci

* **Odhad vašich potřeb propustnosti:** Před migrací dat na účet rozhraní API Cassandra v Azure Cosmos DB byste měli odhadnout nároky na propustnost vašich úloh. Obecně se doporučuje začít s průměrnou propustností vyžadovanou pro operace CRUD a potom přidat další propustnost požadovanou pro extrakci, transformaci a načítání (ETL) a nárazové operace. K naplánování migrace budete potřebovat následující podrobnosti: 

  * **Velikost stávajících dat nebo odhadovanou velikost dat:** Definuje minimální požadavky na velikost a propustnost databáze. Při odhadování velikosti dat pro novou aplikaci můžete předpokládat, že data jsou rovnoměrně distribuována mezi řádky, a odhadnout hodnotu vynásobením velikostí dat. 

  * **Požadovaná propustnost:** Přibližná míra propustnosti čtení (dotazů/získání) a zápis (aktualizace/odstranění/vložení). Tato hodnota je nutná k výpočtu požadovaných jednotek žádostí (RU) spolu s velikostí dat při stabilním stavu.  

  * **Schéma:** Připojte se ke stávajícímu clusteru Cassandra prostřednictvím cqlsh a exportujte schéma z Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Po zjištění požadavků na stávající zatížení byste měli vytvořit účet, databázi a kontejnery Azure Cosmos podle shromážděných požadavků na propustnost.  

  * **Určení poplatků za ru za operaci:** Ru můžete určit pomocí kterékoli sady SDK podporované rozhraní API Cassandra. Tento příklad ukazuje získání poplatků za RU pro verzi .NET.

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

* **Vytvořte v účtu rozhraní API Cassandra tabulky:** Před zahájením migrace dat Představte všechny tabulky z Azure Portal nebo z cqlsh. Pokud migrujete na účet Azure Cosmos, který má propustnost na úrovni databáze, nezapomeňte při vytváření kontejnerů Azure Cosmos zadat klíč oddílu.

* **Zvýšená propustnost:** Doba trvání migrace dat závisí na propustnosti, kterou pro tabulky v Azure Cosmos DB zřídíte. Po dobu trvání migrace propustnost zvyšte. Vyšší propustnost vám umožní zabránit omezování rychlosti a zkrátit dobu migrace. Po dokončení migrace propustnost snižte, abyste dosáhli nižších nákladů. Doporučuje se také účet Azure Cosmos ve stejné oblasti jako zdrojová databáze. 

* **Povolit protokol TLS:** Azure Cosmos DB má přísné požadavky na zabezpečení a standardy. Při interakci s vaším účtem Nezapomeňte povolit protokol TLS. Při použití CQL s protokolem SSH máte možnost zadat informace o protokolu TLS.

## <a name="options-to-migrate-data"></a>Možnosti migrace dat

Data můžete přesunout data z existujících úloh Cassandra do Azure Cosmos DB pomocí následujících možností:

* [Pomocí příkazu cqlsh COPY](#migrate-data-using-cqlsh-copy-command)  
* [Pomocí Sparku](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrace dat pomocí příkazu cqlsh COPY

[Příkaz pro kopírování CQL](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) slouží ke zkopírování místních dat do účtu rozhraní API Cassandra v Azure Cosmos DB. Zkopírujte data provedením následujících kroků:

1. Získejte informace o připojovacím řetězci vašeho účtu rozhraní API Cassandra:

   * Přihlaste se k [Azure Portal](https://portal.azure.com)a přejděte k účtu Azure Cosmos.

   * Otevřete podokno **Připojovací řetězec**, které obsahuje všechny informace potřebné pro připojení k vašemu účtu rozhraní API Cassandra z cqlsh.

2. Přihlaste se ke cqhsh pomocí informací o připojení z portálu.

3. Pomocí příkazu CQL COPY zkopírujte místní data do účtu rozhraní API Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrace dat pomocí Sparku 

Pomocí následujících kroků migrujte data na účet rozhraní API Cassandra pomocí Sparku:

- Zřízení [clusteru Azure Databricks](cassandra-spark-databricks.md) nebo [clusteru HDInsight](cassandra-spark-hdinsight.md) 

- Přesunutí dat do cílového koncového bodu rozhraní API Cassandra pomocí [operace kopírování tabulky](cassandra-spark-table-copy-ops.md) 

Migrace dat pomocí úloh Spark je doporučená možnost, pokud máte data umístěná v existujícím clusteru na virtuálních počítačích Azure nebo v jakémkoli jiném cloudu. Tato možnost vyžaduje, aby Spark byl nastavený jako prostředník pro jednorázovou a běžnou příjemovou dobu. Tuto migraci můžete zrychlit pomocí připojení Azure ExpressRoute mezi místními počítači a Azure. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už je nepotřebujete, můžete odstranit skupinu prostředků, účet Azure Cosmos a všechny související prostředky. Pokud to chcete udělat, vyberte skupinu prostředků pro virtuální počítač, vyberte **Odstranit**a pak potvrďte název skupiny prostředků, která se má odstranit.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak migrovat data na účet rozhraní API Cassandra v Azure Cosmos DB. Nyní můžete přejít k následujícímu článku a získat informace o dalších Azure Cosmos DB konceptech:

> [!div class="nextstepaction"]
> [Nastavitelné úrovně konzistence dat v Azure Cosmos DB](../cosmos-db/consistency-levels.md)


