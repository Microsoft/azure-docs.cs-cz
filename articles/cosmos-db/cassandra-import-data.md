---
title: Import dat Cassandra do Azure Cosmos DB | Microsoft Docs
description: Naučte se používat příkaz CQL Copy ke kopírování dat Cassandra do Azure Cosmos DB.
services: cosmos-db
author: govindk
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 73c9f1fc26f5cb36cc475a66b67705c6177bebf8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37095668"
---
# <a name="azure-cosmos-db-import-cassandra-data"></a>Azure Cosmos DB: Import dat Cassandra

Tento kurz obsahuje pokyny pro import dat Cassandra do Azure Cosmos DB s využitím příkazu COPY jazyka CQL (Cassandra Query Language). 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Načtení připojovacího řetězce
> * Import dat pomocí příkazu cqlsh COPY
> * Import pomocí konektoru Spark 

# <a name="prerequisites"></a>Požadavky

* Instalace [Apache Cassandra](http://cassandra.apache.org/download/) a ověření, že je k dispozici *cqlsh*
* Zvýšená propustnost: Doba trvání migrace dat závisí na propustnosti, kterou pro tabulky zřídíte. V případě rozsáhlejších migrací dat nezapomeňte propustnost zvýšit. Po dokončení migrace propustnost snižte, abyste dosáhli nižších nákladů. Další informace o zvýšení propustnosti na webu [Azure Portal](https://portal.azure.com) najdete v tématu věnovaném [nastavení propustnosti pro kontejnery Azure Cosmos DB](set-throughput.md).
* Povolený protokol SSL: Azure Cosmos DB má striktní bezpečnostní požadavky a standardy. Při práci se svým účtem nezapomeňte povolit SSL. Když použijete CQL s protokolem SSH, máte možnost zadat informace SSL. 

## <a name="find-your-connection-string"></a>Vyhledání připojovacího řetězce

1. Úplně vlevo na webu [Azure Portal](https://portal.azure.com) klikněte na **Azure Cosmos DB**.

2. V podokně **Předplatná** vyberte název vašeho účtu.

3. Klikněte na **Připojovací řetězec**. Pravé podokno obsahuje všechny informace, které potřebujete pro úspěšné připojení ke svému účtu.

    ![Stránka připojovacího řetězce](./media/cassandra-import-data/keys.png)

## <a name="use-cqlsh-copy"></a>Použití příkazu cqlsh COPY

Při importu dat Cassandra do Azure Cosmos DB pro použití s rozhraním API Cassandra použijte následující pokyny:

1. Přihlaste se k cqhsh pomocí připojovacích informací z portálu.
2. Pomocí [příkazu CQL COPY](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) zkopírujte místní data do koncového bodu rozhraní API Apache Cassandra. Ujistěte se, zdroj a cíl jsou ve stejném datovém centru, abyste minimalizovali případné problémy s latencí.

### <a name="guide-for-moving-data-with-cqlsh"></a>Pokyny pro přesun dat s využitím cqlsh

1. Předem vytvořte a škálujte tabulku:
    * Ve výchozím nastavení Azure Cosmos DB zřídí novou tabulku rozhraní API Cassandra s 1 000 jednotek žádostí za sekundu (RU/s) (při vytvoření na základě CQL se zřídí s 400 RU/s). Před zahájením migrace pomocí cqlsh, předem vytvoříte všechny tabulky z prostředí [Azure Portal](https://portal.azure.com) nebo cqlsh. 

    * Na webu [Azure Portal](https://portal.azure.com) zvyšte na dobu migrace propustnost vašich tabulek, a to z výchozí hodnoty 400 nebo 1000 RU/s na 10 000 RU/s. Vyšší propustnost vám umožní zabránit omezování rychlosti a zkrátit dobu migrace. Díky fakturaci služby Azure Cosmos DB po hodinách můžete propustnost okamžitě po migraci snížit, abyste dosáhli nižších nákladů.

2. Zjistěte poplatky za RU pro operaci. Můžete k tomu využít sadu Azure Cosmos DB Cassandra API SDK podle vaší volby. Tento příklad ukazuje získání poplatků za RU pro verzi .NET. 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. Určete latenci ze svého počítače do služby Azure Cosmos DB. Pokud jste uvnitř datového centra Azure, měla by latence být malé jednociferné číslo v milisekundách. Pokud jste mimo datové centrum Azure, můžete zjistit přibližnou latenci z vašeho umístění pomocí příkazu psping nebo azurespeed.com.   

4. Vypočtěte vhodné hodnoty parametrů (NUMPROCESS, INGESTRATE, MAXBATCHSIZE nebo MINBATCHSIZE), které poskytují dobrý výkon. 

5. Spusťte finální příkaz pro migraci. Spuštění tohoto příkazu se předpokládá, jste spustili cqlsh s využitím informací připojovacího řetězce.

   ```
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="use-spark-to-import-data"></a>Použití Sparku pro import dat

Pro data umístěná v existujícím clusteru ve virtuálních počítačích Azure představuje import dat pomocí Sparku také jednu z možných variant. Vyžaduje, aby byl Spark nastavený jako prostředník pro jednorázový nebo pravidelný příjem dat. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili provádět následující úlohy:

> [!div class="checklist"]
> * Načtení připojovacího řetězce
> * Import dat pomocí příkazu cql copy
> * Import pomocí konektoru Spark 

Teď můžete přejít k části Koncepty, která obsahuje další informace o službě Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Nastavitelné úrovně konzistence dat v Azure Cosmos DB](../cosmos-db/consistency-levels.md)
