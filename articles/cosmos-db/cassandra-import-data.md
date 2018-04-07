---
title: Importovat Cassandra data do Azure Cosmos DB | Microsoft Docs
description: Další informace o použití příkazu CQL kopírovat můžete kopírovat Cassandra data do Azure Cosmos DB.
services: cosmos-db
author: govindk
manager: kfile
documentationcenter: ''
ms.assetid: eced5f6a-3f56-417a-b544-18cf000af33a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 64f60e6beb5451d8f5acd382ca8e5672a2d096f6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-import-cassandra-data"></a>Azure Cosmos DB: Data importovat Cassandra

Tento kurz obsahuje pokyny importování Cassandra dat do Azure Cosmos DB pomocí příkazu kopírování Cassandra dotazu jazyka (CQL). 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Získávání připojovacího řetězce
> * Import dat pomocí příkazu kopírování cqlsh
> * Import pomocí Spark konektoru 

# <a name="prerequisites"></a>Požadavky

* Nainstalujte [Apache Cassandra](http://cassandra.apache.org/download/) a konkrétně zkontrolujte *cqlsh* je k dispozici.
* Zvýšit propustnost: trvání migrace dat závisí na množství zřízené pro vaše tabulky propustnosti. Ujistěte se, že zvýšit propustnost pro větší dat migrace. Po dokončení migrace, snížit propustnosti, abyste ušetřili náklady. Další informace o zvýšení propustnosti v [portál Azure](https://portal.azure.com), najdete v části [sadu propustnost pro Azure Cosmos DB kontejnery](set-throughput.md).
* Povolte protokol SSL: Azure Cosmos DB má vysokými nároky na zabezpečení a standardy. Je nutné povolit protokol SSL, pokud při práci s vaším účtem. Pokud používáte CQL pomocí protokolu SSH, máte možnost zadat informace o SSL. 

## <a name="find-your-connection-string"></a>Najít připojovací řetězec

1. V [portál Azure](https://portal.azure.com), na zcela vlevo, klikněte na tlačítko **Azure Cosmos DB**.

2. V **odběry** podokně, vyberte název účtu.

3. Klikněte na tlačítko **připojovací řetězec**. V pravém podokně obsahuje všechny informace, které potřebujete k úspěšnému připojení k vašemu účtu.

    ![Stránka řetězec připojení](./media/cassandra-import-data/keys.png)

## <a name="use-cqlsh-copy"></a>Použití cqlsh kopie

K importu dat Cassandra do Azure Cosmos DB pro použití s rozhraním API Cassandra, použijte následující pokyny:

1. Přihlaste se k cqhsh pomocí informací o připojení z portálu.
2. Použití [příkaz CQL COPY](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) zkopírovat místní data na koncový bod rozhraní API Cassandra Apache. Ujistěte se, zdroje a cíle jsou ve stejném datovém centru. Chcete-li minimalizovat problémy s latencí.

### <a name="guide-for-moving-data-with-cqlsh"></a>Příručka pro přesun dat s cqlsh

1. Předem vytvořit a škálovat vaše tabulka:
    * Ve výchozím nastavení Azure Cosmos DB poskytuje nové rozhraní API Cassandra tabulky s 1 000 jednotek žádosti za sekundu (RU/s) (na základě CQL vytvoření je opatřen 400 RU/s). Před zahájením migrace pomocí cqlsh, předem vytvořit všechny tabulky z [portál Azure](https://portal.azure.com) nebo z cqlsh. 

    * Z [portál Azure](https://portal.azure.com), zvýšit propustnost vaše tabulky z výchozí propustnost (400 nebo 1000 RU/s) na 10 000 RU/s pro dobu trvání migrace. S vyšší propustnost můžete vyhnout, omezení a migraci za kratší dobu. S každou hodinu fakturace v Azure Cosmos DB, můžete snížit propustnost ihned po migraci tak, aby ušetřili náklady.

2. Určení poplatků RU operace. To provedete pomocí Cosmos DB Cassandra rozhraní API sady SDK Azure podle svého výběru. Tento příklad ukazuje získání RU poplatky verze rozhraní .NET. 

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

3. Určení latence z vašeho počítače ke službě Azure Cosmos DB. Pokud jste v rámci služby Azure datového centra, latence musí být číslo milisekundu nízkou jediná číslice. Pokud jste mimo datové centrum Azure, můžete pomocí pspingu nebo azurespeed.com získat přibližnou latence z vašeho umístění.   

4. Vypočítá správné hodnoty pro parametry (NUMPROCESS, INGESTRATE, MAXBATCHSIZE nebo MINBATCHSIZE), které poskytují dobrý výkon. 

5. Spusťte příkaz dokončení migrace. Spuštění tohoto příkazu se předpokládá, jste spustili cqlsh pomocí informace o připojovacím řetězci.

   ```
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="use-spark-to-import-data"></a>Používejte Spark pro import dat

U dat umístěných v existující cluster ve virtuálních počítačích Azure import dat pomocí Spark je také vhodná možnost. To vyžaduje Spark, aby byl nastavený jako prostředník jednou nebo regulární přijímání. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak dokončit následující úlohy:

> [!div class="checklist"]
> * Načtení připojovacího řetězce
> * Umožňuje importovat data pomocí příkazu kopírování cql
> * Import pomocí Spark konektoru 

Nyní můžete přejít k části koncepty pro další informace o Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Data přizpůsobitelné úrovně konzistence v Azure Cosmos DB](../cosmos-db/consistency-levels.md)
