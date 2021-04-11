---
title: Migrace dat na účet rozhraní API Cassandra v Azure Cosmos DB kurzu
description: V tomto kurzu se dozvíte, jak kopírovat data z Apache Cassandra do účtu rozhraní API Cassandra v Azure Cosmos DB.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 7c55cbf4b8739a885c499c820a7e68825522ea4e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449286"
---
# <a name="tutorial-migrate-your-data-to-a-cassandra-api-account"></a>Kurz: migrace dat na účet rozhraní API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Jako vývojář můžete mít stávající Cassandra úlohy, které běží místně nebo v cloudu, a můžete je chtít migrovat do Azure. Tyto úlohy můžete migrovat na účet rozhraní API Cassandra v Azure Cosmos DB. V tomto kurzu najdete informace o různých možnostech, které jsou k dispozici pro migraci dat Apache Cassandra do účtu rozhraní API Cassandra v Azure Cosmos DB.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Plánování migrace
> * Požadavky na migraci
> * Migrace dat pomocí `cqlsh` `COPY` příkazu
> * Migrace dat pomocí Sparku

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites-for-migration"></a>Požadavky na migraci

* **Odhad vašich potřeb propustnosti:** Před migrací dat na účet rozhraní API Cassandra v Azure Cosmos DB byste měli odhadnout nároky na propustnost vašich úloh. Obecně je třeba začít s průměrnou propustností požadovanou operacemi CRUD a pak zahrnout další propustnost vyžadovanou pro extrakci transformačního načtení nebo nárazové operace. K naplánování migrace budete potřebovat následující podrobnosti: 

  * **Velikost stávajících dat nebo odhadovanou velikost dat:** Definuje minimální požadavky na velikost a propustnost databáze. Pokud odhaduje velikost dat pro novou aplikaci, můžete předpokládat, že data jsou rovnoměrně rozložena napříč řádky, a odhadnout hodnotu vynásobením velikosti dat. 

  * **Požadovaná propustnost:** Přibližná míra propustnosti čtení (dotazů/získání) a zápis (aktualizace/odstranění/vložení) operací. Tato hodnota je nutná k výpočtu požadovaných jednotek žádosti spolu s velikostí dat ustáleného stavu.  

  * **Schéma:** Připojte se ke stávajícímu clusteru Cassandra prostřednictvím `cqlsh` a exportujte schéma z Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Až zjistíte požadavky na stávající zatížení, vytvořte účet Azure Cosmos DB, databázi a kontejnery podle shromážděných požadavků na propustnost.  

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

* **Vytvořte v účtu rozhraní API Cassandra tabulky:** Před zahájením migrace dat Představte všechny tabulky z Azure Portal nebo z `cqlsh` . Pokud provádíte migraci na účet Azure Cosmos DB, který má propustnost na úrovni databáze, nezapomeňte při vytváření kontejnerů zadat klíč oddílu.

* **Zvýšená propustnost:** Doba trvání migrace dat závisí na propustnosti, kterou pro tabulky v Azure Cosmos DB zřídíte. Po dobu trvání migrace propustnost zvyšte. Vyšší propustnost vám umožní zabránit omezování rychlosti a zkrátit dobu migrace. Po dokončení migrace propustnost snižte, abyste dosáhli nižších nákladů. Doporučujeme také, abyste měli účet Azure Cosmos DB ve stejné oblasti jako zdrojová databáze. 

* **Povolit protokol TLS:** Azure Cosmos DB má přísné požadavky na zabezpečení a standardy. Při interakci s vaším účtem Nezapomeňte povolit protokol TLS. Při použití CQL s protokolem SSH máte možnost zadat informace o protokolu TLS.

## <a name="options-to-migrate-data"></a>Možnosti migrace dat

Data z existujících úloh Cassandra můžete přesunout do Azure Cosmos DB pomocí `cqlsh` `COPY` příkazu nebo pomocí Sparku. 

### <a name="migrate-data-by-using-the-cqlsh-copy-command"></a>Migrace dat pomocí příkazu cqlsh COPY

Pomocí [příkazu CQL Copy](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) zkopírujte místní data do účtu rozhraní API Cassandra v Azure Cosmos DB.

1. Získejte informace o připojovacím řetězci vašeho účtu rozhraní API Cassandra:

   * Přihlaste se k [Azure Portal](https://portal.azure.com)a pokračujte na účet Azure Cosmos DB.

   * Otevřete podokno **připojovací řetězec** . Tady vidíte všechny informace, které potřebujete ke svému účtu rozhraní API Cassandra připojit z `cqlsh` .

1. Přihlaste se k `cqlsh` pomocí informací o připojení z portálu.

1. Pomocí `CQL` `COPY` příkazu zkopírujte místní data do účtu rozhraní API Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

### <a name="migrate-data-by-using-spark"></a>Migrace dat pomocí Sparku 

Pomocí následujících kroků migrujte data na účet rozhraní API Cassandra pomocí Sparku:

1. Zřídí [cluster Azure Databricks](cassandra-spark-databricks.md) nebo [cluster Azure HDInsight](cassandra-spark-hdinsight.md). 

1. Přesuňte data do cílového koncového bodu rozhraní API Cassandra pomocí [operace kopírování tabulky](cassandra-spark-table-copy-ops.md). 

Migrace dat pomocí úloh Spark je doporučená možnost, pokud máte data umístěná v existujícím clusteru na virtuálních počítačích Azure nebo v jakémkoli jiném cloudu. Chcete-li to provést, musíte nastavit Spark jako prostředník pro jednorázovou nebo běžnou podobu příjmu. Tuto migraci můžete zrychlit pomocí připojení Azure ExpressRoute mezi místním prostředím a Azure. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, účet Azure Cosmos DB a všechny související prostředky. Pokud to chcete udělat, vyberte skupinu prostředků pro virtuální počítač, vyberte **Odstranit** a pak potvrďte název skupiny prostředků, která se má odstranit.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak migrovat data na účet rozhraní API Cassandra v Azure Cosmos DB. Nyní se můžete dozvědět o dalších konceptech v Azure Cosmos DB:

> [!div class="nextstepaction"]
> [Nastavitelné úrovně konzistence dat v Azure Cosmos DB](../cosmos-db/consistency-levels.md)




