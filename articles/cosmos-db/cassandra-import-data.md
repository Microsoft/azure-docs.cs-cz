---
title: 'Kurz: Migrace dat do účtu rozhraní Cassandra API ve službě Azure Cosmos DB'
description: V tomto kurzu zjistěte, jak pomocí příkazu Kopírovat CQL a Spark ke zkopírování dat z Cassandry do účtu rozhraní Cassandra API ve službě Azure Cosmos DB.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
Customer intent: As a developer, I want to migrate my existing Cassandra workloads to Azure Cosmos DB so that the overhead to manage resources, clusters, and garbage collection is automatically handled by Azure Cosmos DB.
ms.openlocfilehash: b12e7aad5fbdf65a8936b943f5053eda76dbd883
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037476"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>Kurz: Migrace dat do účtu rozhraní Cassandra API ve službě Azure Cosmos DB

Jako vývojář, můžete mít existující úlohy Cassandra, které jsou spuštěné místně nebo v cloudu, a může být vhodné k migraci do Azure. Tyto úlohy můžete migrovat na účet Cassandra API ve službě Azure Cosmos DB. Tento kurz obsahuje pokyny k migraci dat Apache Cassandra v úvahu rozhraní Cassandra API ve službě Azure Cosmos DB k dispozici různé možnosti.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Plánování migrace
> * Požadavky na migraci
> * Migrace dat pomocí příkazu cqlsh COPY
> * Migrace dat pomocí Sparku

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites-for-migration"></a>Požadavky na migraci

* **Odhadněte propustnost musí:** Před migrací dat k rozhraní Cassandra API účtu ve službě Azure Cosmos DB, by měl odhadnou propustnost potřebám vaší zátěže. Obecně se doporučuje začít s průměrnou propustností vyžadovanou pro operace CRUD a potom přidat další propustnost požadovanou pro extrakci, transformaci a načítání (ETL) a nárazové operace. K naplánování migrace budete potřebovat následující podrobnosti: 

   * **Odhadovaná velikost dat nebo stávající velikost dat:** Definuje minimální velikosti a propustnosti požadavek na databázi. Při odhadování velikosti dat pro novou aplikaci můžete předpokládat, že data jsou rovnoměrně distribuována mezi řádky, a odhadnout hodnotu vynásobením velikostí dat. 

   * **Požadované propustnosti:** Přibližná čtení (dotaz/get) a propustnost zápisu (update, delete nebo insert). Tato hodnota je nutná k výpočtu požadovaných jednotek žádostí (RU) spolu s velikostí dat při stabilním stavu.  

   * **Schéma:** Připojte se do existujícího clusteru Cassandra prostřednictvím cqlsh a export schématu z Cassandra: 

     ```bash
     cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
     ```

   Po identifikaci požadavků vašeho existujícího pracovního zatížení byste měli vytvořit účet Azure Cosmos, databáze a kontejnerů podle požadavků získaná propustnost.  

   * **Určení RU poplatky za operace:** Pomocí některé z podporovaných rozhraní Apache Cassandra API sady SDK můžete určit jednotky RU. Tento příklad ukazuje získání poplatků za RU pro verzi .NET.

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

* **Přidělte požadované propustnosti:** Azure Cosmos DB může automaticky škálovat úložiště a propustnost podle rostoucích požadavků. S odhadem vašich potřeb z hlediska propustnosti vám pomůže [kalkulačka jednotek žádostí služby Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

* **Vytvoření tabulek v účtu rozhraní Cassandra API:** Než začnete s migrací dat, předem vytvořte všechny tabulky z webu Azure portal nebo z cqlsh. Pokud provádíte migraci na účet Azure Cosmos, který má databáze úroveň propustnosti, ujistěte se, že jste při vytváření kontejnerů Azure Cosmos zadat klíč oddílu.

* **Zvýšení propustnosti:** Doba trvání migrace dat závisí na množství propustnost, kterou jste zřídili tabulek ve službě Azure Cosmos DB. Po dobu trvání migrace propustnost zvyšte. Vyšší propustnost vám umožní zabránit omezování rychlosti a zkrátit dobu migrace. Po dokončení migrace propustnost snižte, abyste dosáhli nižších nákladů. Také se doporučuje mít účet Azure Cosmos ve stejné oblasti jako zdrojové databáze. 

* **Povolte protokol SSL:** Azure Cosmos DB má striktní bezpečnostní požadavky a standardy. Při práci se svým účtem nezapomeňte povolit SSL. Když použijete CQL s protokolem SSH, máte možnost zadat informace SSL.

## <a name="options-to-migrate-data"></a>Možnosti migrace dat

Data můžete přesunout data z existujících úloh Cassandra do Azure Cosmos DB pomocí následujících možností:

* [Pomocí příkazu cqlsh COPY](#migrate-data-using-cqlsh-copy-command)  
* [Pomocí Sparku](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrace dat pomocí příkazu cqlsh COPY

[CQL KOPÍROVACÍ příkazy](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) slouží ke kopírování místních dat do účtu rozhraní Cassandra API ve službě Azure Cosmos DB. Zkopírujte data provedením následujících kroků:

1. Získejte informace o připojovacím řetězci vašeho účtu rozhraní API Cassandra:

   * Přihlaste se k [webu Azure portal](https://portal.azure.com)a přejděte do svého účtu Azure Cosmos.

   * Otevřete podokno **Připojovací řetězec**, které obsahuje všechny informace potřebné pro připojení k vašemu účtu rozhraní API Cassandra z cqlsh.

2. Přihlaste se ke cqhsh pomocí informací o připojení z portálu.

3. Pomocí příkazu CQL COPY zkopírujte místní data do účtu rozhraní API Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrace dat pomocí Sparku 

K migraci dat do účtu rozhraní Cassandra API s Spark použijte následující kroky:

- Zřízení [Azure Databricks pro cluster](cassandra-spark-databricks.md) nebo [clusteru HDInsight](cassandra-spark-hdinsight.md) 

- Přesun dat na cílový koncový bod Cassandra API s použitím [tabulky operace kopírování](cassandra-spark-table-copy-ops.md) 

Migrace dat pomocí Sparkových úloh je doporučená možnost, pokud máte data uložená v existujícím clusteru v Azure virtual machines nebo jiný cloud. Tato možnost vyžaduje Spark pro jeden čas nebo pravidelné přijímání nastavit jako prostředník. Tato migrace může urychlit pomocí připojení Azure ExpressRoute mezi místní a Azure. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste už nepotřebujete, můžete odstranit skupinu prostředků, účet Azure Cosmos a všechny související prostředky. Uděláte to tak, vyberte skupinu prostředků pro virtuální počítač, vyberte **odstranit**a potom ověřte název skupiny prostředků pro odstranění.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak migrovat data do účtu rozhraní Cassandra API ve službě Azure Cosmos DB. Teď můžete přejít k další informace o dalších konceptech služby Azure Cosmos DB v následujícím článku:

> [!div class="nextstepaction"]
> [Nastavitelné úrovně konzistence dat v Azure Cosmos DB](../cosmos-db/consistency-levels.md)


