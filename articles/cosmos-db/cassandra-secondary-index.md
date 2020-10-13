---
title: Indexování v účtu Azure Cosmos DB rozhraní API Cassandra
description: Přečtěte si, jak funguje sekundární indexování v Azure Azure Cosmos DB rozhraní API Cassandram účtu.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80758024"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Sekundární indexování v Azure Cosmos DB rozhraní API Cassandra

Rozhraní API Cassandra v Azure Cosmos DB využívá základní infrastrukturu indexování k tomu, aby vystavila sílu indexování, která je podstatná pro platformu. Na rozdíl od základního rozhraní SQL API se ale rozhraní API Cassandra v Azure Cosmos DB ve výchozím nastavení neindexují všechny atributy. Místo toho podporuje sekundární indexování k vytvoření indexu pro určité atributy, které se chovají stejně jako Apache Cassandra.  

Obecně není doporučeno spouštět dotazy filtru na sloupcích, které nejsou rozděleny na oddíly. Je nutné explicitně použít syntaxi POVOLENého filtrování, což vede k operaci, která nemusí fungovat správně. V Azure Cosmos DB můžete tyto dotazy spustit u atributů s nízkou mohutnou, protože se při načítání výsledků rozcházejí mezi oddíly.

Nedoporučujeme vytvářet index u často aktualizovaného sloupce. Při definování tabulky je vhodné vytvořit index. Tím se zajistí, že data a indexy budou v konzistentním stavu. V případě, že pro existující data vytvoříte nový index, nemůžete v současné době sledovat změnu průběhu indexu pro tabulku. Potřebujete-li sledovat průběh této operace, je nutné požádat o změnu průběhu prostřednictvím [lístku podpory]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).


> [!NOTE]
> Sekundární index není podporován u následujících objektů:
> - datové typy, jako jsou typy zmrazených kolekcí, Decimal a variantní typy.
> - Statické sloupce
> - Klíče clusteringu

## <a name="indexing-example"></a>Příklad indexování

Nejprve pomocí následujících příkazů na příkazovém řádku prostředí CQL Vytvořte ukázkový prostor klíčů a tabulku:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Pak vložte ukázková uživatelská data s následujícími příkazy:

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

Pokud se pokusíte spustit následující příkaz, dojde k chybě, která vás vyzve k použití `ALLOW FILTERING` : 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

I když rozhraní API Cassandra podporuje filtrování, jak je uvedeno v předchozí části, to se nedoporučuje. Místo toho byste měli vytvořit index v, jak je znázorněno v následujícím příkladu:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Po vytvoření indexu v poli "LastName" teď můžete úspěšně spustit předchozí dotaz. U rozhraní API Cassandra v Azure Cosmos DB není nutné zadávat název indexu. Použije se výchozí index s formátem `tablename_columnname_idx` . Například ` t1_lastname_idx` je název indexu pro předchozí tabulku.

## <a name="dropping-the-index"></a>Vyřazení indexu 
Musíte znát, co má název indexu k vyřazení indexu. Spusťte `desc schema` příkaz, který získá popis tabulky. Výstup tohoto příkazu zahrnuje název indexu ve formátu `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)` . Pak můžete použít název indexu k vyřazení indexu, jak je znázorněno v následujícím příkladu:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak funguje [automatické indexování](index-overview.md) v Azure Cosmos DB
* [Funkce Apache Cassandra, které podporuje rozhraní API Cassandra pro Azure Cosmos DB](cassandra-support.md)
