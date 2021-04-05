---
title: Vytváření oddílů v Azure Cosmos DB rozhraní API Cassandra
description: Další informace o dělení Azure Cosmos DB rozhraní API Cassandra
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ba615d3e41393afe007238a0fe1e694732ad123e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087634"
---
# <a name="partitioning-in-azure-cosmos-db-cassandra-api"></a>Vytváření oddílů v Azure Cosmos DB rozhraní API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Tento článek popisuje, jak vytváření oddílů funguje v Azure Cosmos DB rozhraní API Cassandra. 

Rozhraní API Cassandra používá dělení ke škálování jednotlivých tabulek v prostoru klíčů pro splnění požadavků vaší aplikace na výkon. Oddíly se vytvoří na základě hodnoty klíče oddílu, který je spojený s každým záznamem v tabulce. Všechny záznamy v oddílu mají stejnou hodnotu klíče oddílu. Azure Cosmos DB transparentně a automaticky spravuje umístění oddílů mezi fyzickými prostředky a efektivně tak vyhovět požadavkům na škálovatelnost a výkon tabulky. V rámci zvýšení propustnosti a požadavků na úložiště se aplikace Azure Cosmos DB přesouvá a vyvažuje data na větším počtu fyzických počítačů.

V perspektivě pro vývojáře se při vytváření oddílů chová stejným způsobem jako Azure Cosmos DB rozhraní API Cassandra, jak to funguje v nativní [Apache Cassandra](https://cassandra.apache.org/). Existují však určité rozdíly na pozadí. 


## <a name="differences-between-apache-cassandra-and-azure-cosmos-db"></a>Rozdíly mezi Apache Cassandra a Azure Cosmos DB

V Azure Cosmos DB se každý počítač, na kterém jsou uložené oddíly, nazývá [fyzický oddíl](partitioning-overview.md#physical-partitions). Fyzický oddíl je podobají k virtuálnímu počítači; vyhrazenou výpočetní jednotku nebo sadu fyzických prostředků. Každý oddíl uložený v této výpočetní jednotce se v Azure Cosmos DB označuje jako [logický oddíl](partitioning-overview.md#logical-partitions) . Pokud jste už obeznámení s Apache Cassandra, můžete si logické oddíly představit stejným způsobem, jako byste si myslíte z běžných oddílů v Cassandra. 

Apache Cassandra doporučuje omezení 100 MB na velikost dat, která se dají uložit do oddílu. Rozhraní API Cassandra pro Azure Cosmos DB umožňuje až 20 GB na logický oddíl a až 30 GB dat na fyzický oddíl. V Azure Cosmos DB na rozdíl od Apache Cassandra je výpočetní kapacita dostupná ve fyzickém oddílu vyjádřená pomocí jedné metriky nazvané [jednotky žádosti](request-units.md), která vám umožní považovat zatížení za požadavky (čtení nebo zápisy) za sekundu, místo jader, paměti nebo IOPS. Díky tomu může plánování kapacity lépe rovnou, jakmile porozumíte nákladům na jednotlivé požadavky. Každý fyzický oddíl může mít k dispozici až 10000 ru výpočetní kapacity. Další informace o možnostech škálovatelnosti najdete v našem článku o [elastickém škálování](manage-scale-cassandra.md) v rozhraní API Cassandra. 

V Azure Cosmos DB se každý fyzický oddíl skládá ze sady replik, označovaných také jako sady replik, které mají alespoň 4 repliky na oddíl. To je na rozdíl od Apache Cassandra, kde je možné nastavit faktor replikace 1. To však vede k nízké dostupnosti, pokud dojde k výpadku jediného uzlu s daty. V rozhraní API Cassandra je vždy faktor replikace 4 (kvorum 3). Azure Cosmos DB automaticky spravuje sady replik, ale je potřeba je spravovat pomocí různých nástrojů v Apache Cassandra. 

Apache Cassandra má koncept tokenů, což jsou hodnoty hash klíčů oddílů. Tokeny jsou založené na bajtové hodnotě hash murmur3 64 s hodnotami od-2 ^ 63 do-2 ^ 63-1. Tento rozsah se obvykle označuje jako "token ring" v Apache Cassandra. Token Ring se distribuuje do rozsahů tokenů a tyto rozsahy se rozdělí mezi uzly přítomné v nativním clusteru Apache Cassandra. Dělení pro Azure Cosmos DB je implementováno podobným způsobem, s výjimkou toho, že používá jiný algoritmus hash a má větší interní token ring. Externě ale zveřejňujeme stejný rozsah tokenů jako Apache Cassandra, tj.-2 ^ 63 až-2 ^ 63-1.


## <a name="primary-key"></a>Primární klíč

Všechny tabulky v rozhraní API Cassandra musí mít `primary key` definované. Syntaxe pro primární klíč je uvedena níže:

```shell
column_name cql_type_definition PRIMARY KEY
```

Předpokládejme, že chceme vytvořit uživatelskou tabulku, která ukládá zprávy pro různé uživatele:

```shell
CREATE TABLE uprofile.user ( 
   id UUID PRIMARY KEY, 
   user text,  
   message text);
```

V tomto návrhu jsme definovali `id` pole jako primární klíč. Primární klíč funguje jako identifikátor záznamu v tabulce a používá se také jako klíč oddílu v Azure Cosmos DB. Pokud je primární klíč definovaný dříve popsaným způsobem, bude v každém oddílu jenom jeden záznam. Výsledkem bude naprosto horizontální a škálovatelná distribuce při zápisu dat do databáze a je ideální pro případy použití vyhledávání klíč-hodnota. Aplikace by měla při každém čtení dat z tabulky poskytnout primární klíč, aby se maximalizoval výkon při čtení. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning.png" alt-text="oddíly" border="false":::


## <a name="compound-primary-key"></a>Složený primární klíč

Apache Cassandra má také koncept  `compound keys` . Složený objekt `primary key` se skládá z více než jednoho sloupce. první sloupec je `partition key` a všechny další sloupce jsou `clustering keys` . Syntaxe pro `compound primary key` je zobrazena níže:

```shell
PRIMARY KEY (partition_key_column_name, clustering_column_name [, ...])
```

Předpokládejme, že chceme změnit výše uvedený návrh a umožnit efektivně načítat zprávy pro daného uživatele:

```shell
CREATE TABLE uprofile.user (
   user text,  
   id int, 
   message text, 
   PRIMARY KEY (user, id));
```

V tomto návrhu jsme teď definovali `user` jako klíč oddílu a `id` jako klíč clusteringu. Můžete definovat tolik klíčů clusteringu, kolik chcete, ale každá hodnota (nebo kombinace hodnot) pro clustering Key musí být jedinečná, aby bylo možné do stejného oddílu přidat několik záznamů, například:

```shell
insert into uprofile.user (user, id, message) values ('theo', 1, 'hello');
insert into uprofile.user (user, id, message) values ('theo', 2, 'hello again');
```

Po vrácení dat se data seřadí podle klíče clusteringu, jak je očekáváno v Apache Cassandra:

:::image type="content" source="./media/cassandra-partitioning/select-from-pk.png" alt-text="Snímek obrazovky zobrazující vracená data, která jsou seřazená podle klíče clusteringu":::

S datovým modelem tímto způsobem lze každému oddílu přiřadit více záznamů seskupených podle uživatele. Proto můžeme vystavit dotaz, který je efektivně směrován `partition key` (v tomto případě `user` ) a získá všechny zprávy pro daného uživatele. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning2.png" alt-text="Diagram, který ukazuje, jak lze přiřadit více záznamů každému oddílu seskupenému podle uživatele." border="false":::


## <a name="composite-partition-key"></a>Klíč složeného oddílu

Složené klíče oddílu fungují v podstatě stejným způsobem jako složené klíče, s tím rozdílem, že můžete zadat více sloupců jako složený klíč oddílu. Syntaxe složených klíčů oddílů je uvedena níže:

```shell
PRIMARY KEY (
   (partition_key_column_name[, ...]), 
    clustering_column_name [, ...]);
```
Například můžete mít následující, kde jedinečné kombinace `firstname` a `lastname` by měly tvořit klíč oddílu a `id` je klíč clusteringu:

```shell
CREATE TABLE uprofile.user ( 
   firstname text, 
   lastname text,
   id int,  
   message text, 
   PRIMARY KEY ((firstname, lastname), id) );
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [dělení a horizontálním škálování v Azure Cosmos DB](partitioning-overview.md).
* Přečtěte si o [zřízené propustnosti v Azure Cosmos DB](request-units.md).
* Přečtěte si o [globální distribuci v Azure Cosmos DB](distribute-data-globally.md).
