---
title: Indexování v účtu rozhraní API Azure Cosmos DB Cassandra
description: Zjistěte, jak sekundární indexování funguje v účtu rozhraní API Azure Cosmos DB Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758024"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Sekundární indexování v rozhraní API Azure Cosmos DB Cassandra

Rozhraní CASSAndS API v Azure Cosmos DB využívá základní infrastrukturu indexování k vystavení sílu indexování, která je vlastní platformě. Na rozdíl od základního rozhraní SQL API však rozhraní CASSANDRA API v Azure Cosmos DB ve výchozím nastavení neindexuje všechny atributy. Místo toho podporuje sekundární indexování k vytvoření indexu na určité atributy, které se chová stejným způsobem jako Apache Cassandra.  

Obecně se nedoporučuje spouštět dotazy filtru na sloupce, které nejsou rozděleny do oddílů. Syntaxi POVOLIT FILTROVÁNÍ je nutné použít explicitně, což má za následek operaci, která nemusí fungovat dobře. V Azure Cosmos DB můžete spustit tyto dotazy na nízké mohutnost atributy, protože se rozptýlit napříč oddíly načíst výsledky.

Nedoporučuje se vytvořit index v často aktualizovaném sloupci. Je rozumné vytvořit index při definování tabulky. Tím je zajištěno, že data a indexy jsou v konzistentním stavu. V případě, že vytvoříte nový index na existující data, v současné době nelze sledovat změnu průběhu indexu pro tabulku. Pokud potřebujete sledovat průběh této operace, musíte požádat o změnu průběhu prostřednictvím [lístku podpory]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).


> [!NOTE]
> Sekundární index není podporován u následujících objektů:
> - datové typy, jako jsou zmrazené typy kolekcí, desetinné číslo a typy variant.
> - Statické sloupce
> - Clustering klíče

## <a name="indexing-example"></a>Příklad indexování

Nejprve vytvořte ukázkový prostor klíčů a tabulku spuštěním následujících příkazů na řádku prostředí CQL:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Potom vložte ukázková uživatelská data s následujícími příkazy:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Pokud se pokusíte provést následující příkaz, narazíte na chybu, která vás požádá o použití `ALLOW FILTERING`: 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Přestože rozhraní CASSANDRA API podporuje povolit filtrování, jak je uvedeno v předchozí části, není doporučeno. Místo toho byste měli vytvořit index v následujícím příkladu:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Po vytvoření indexu v poli Příjmení můžete nyní úspěšně spustit předchozí dotaz. S rozhraním Cassandra API v Azure Cosmos DB, není třeba zadat název indexu. Používá se výchozí `tablename_columnname_idx` index s formátem. Je například ` t1_lastname_idx` název indexu pro předchozí tabulku.

## <a name="dropping-the-index"></a>Uvolnění indexu 
Musíte vědět, jaký je název indexu k přetažení indexu. Chcete-li získat popis tabulky, `desc schema` spusťte příkaz. Výstup tohoto příkazu obsahuje název indexu `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`ve formátu . Potom můžete použít název indexu k přetažení indexu, jak je znázorněno v následujícím příkladu:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [funguje automatické indexování](index-overview.md) v Azure Cosmos DB
* [Funkce Apache Cassandra, které podporuje rozhraní API Cassandra pro Azure Cosmos DB](cassandra-support.md)
