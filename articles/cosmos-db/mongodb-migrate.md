---
title: 'Kurz: Použití nástrojů mongoimport a mongorestore s rozhraním API služby Azure Cosmos DB pro MongoDB | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak používat věnované a mongorestore k importu dat do rozhraní API pro účet MongoDB.
keywords: mongoimport, mongorestore
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: sngun
ms.custom: mvc
Customer intent: As a developer, I want to migrate the data from my existing MongoDB workloads to a MongoDB API account in Azure Cosmos DB, so that the overhead to manage resources is handled by Azure Cosmos DB.
ms.openlocfilehash: b40ca7cf62127a95fe277e205e5a5c5d36618828
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753842"
---
# <a name="tutorial-migrate-your-data-to-a-mongodb-api-account-in-azure-cosmos-db"></a>Kurz: Migrace dat do účtu rozhraní MongoDB API ve službě Azure Cosmos DB

Jako vývojář můžete mít aplikace, které používají dat dokumentů typu NoSQL. Účet rozhraní MongoDB API ve službě Azure Cosmos DB můžete použít k ukládání a přístup k těmto datům dokumentu. Můžete také migrovat data z vašich stávajících aplikací do rozhraní API pro MongoDB. Tento kurz obsahuje pokyny o tom, jak migrovat data uložená v MongoDB do účtu rozhraní MongoDB API ve službě Azure Cosmos DB. Pokud jste umožňuje importovat data z MongoDB a plánujete používat s SQL API služby Azure Cosmos DB, měli byste použít [nástroj pro migraci dat](import-data.md) importovat data.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Připravte si plán migrace.
> * Migrate data s využitím věnované.
> * Migrate data s využitím mongorestore.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Zkontrolujte a zajistěte splnění následujících požadavků před zahájením migrace.

### <a name="plan-for-the-migration"></a>Plánování migrace

Tato část popisuje, jak naplánovat migraci data. Použijeme odhadnout náklady RU, určení latence z vašeho počítače do cloudové služby a vypočítat velikost dávky a počet pracovních procesů vkládání.


#### <a name="pre-create-and-scale-your-collections"></a>Předem vytvářet a škálovat své kolekce

Ve výchozím nastavení Azure Cosmos DB zřídí novou kolekci MongoDB s 1 000 jednotek žádostí za sekundu (RU/s). Před migrací s věnované nebo mongorestore předem vytvořit všechny kolekce z [webu Azure portal](https://portal.azure.com) nebo z MongoDB ovladačů a nástrojů. Pokud velikost dat je větší než 10 GB, vytvořte dělené kolekce s klíčem odpovídajících horizontálních oddílů.

Z [webu Azure portal](https://portal.azure.com), zvýšit propustnost kolekce pro migraci z 1 000 ru/s pro kolekce jednoho oddílu a 2 500 ru/s pro horizontálně dělenou kolekci. Vyšší propustnost vám umožní zabránit omezování rychlosti a zkrátit dobu migrace. Okamžitě po migraci můžete propustnost snížit, abyste dosáhli nižších nákladů.

Kromě zřízení ru za sekundu na úrovni kolekce můžete také vytvářet ru za sekundu pro sadu kolekce na nadřazené úrovni databáze. Na nadřazené úrovni musí předběžné vytvoření databáze a kolekce a nadefinujeme klíč horizontálního oddílu pro každou kolekci.

Horizontálně dělené kolekce můžete vytvořit pomocí preferovaný nástroj, ovladač nebo sady SDK. V tomto příkladu k vytvoření horizontálně dělené kolekce používáme MongoDB Shell:

```bash
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Příkaz vrátí následující výsledky:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

#### <a name="calculate-the-approximate-ru-charge-for-a-single-document-write"></a>Počítat náklady přibližné RU pro zápis do jednoho dokumentu

Z MongoDB Shell připojíte ke svému účtu rozhraní MongoDB API ve službě Azure Cosmos DB. Pokyny najdete v tématu [Připojení aplikace MongoDB ke službě Azure Cosmos DB](connect-mongodb-account.md).

Potom spusťte Ukázkový příkaz insert pomocí jednoho z ukázkových dokumentů:
   
```bash
db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
```
        
Spusťte příkaz `db.runCommand({getLastRequestStatistics: 1})`.

Obdržíte odpovědi podobně jako následující výstup:
     
```bash
globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "insert",
    "RequestCharge": 10,
    "RequestDurationInMilliSeconds": NumberLong(50)
}
```
        
Poznamenejte si zátěž požadavku.
    
#### <a name="determine-the-latency-from-your-machine-to-the-azure-cosmos-db-cloud-service"></a>Určení latence z vašeho počítače ke cloudové službě Azure Cosmos DB
    
Zapnout podrobné protokolování z MongoDB Shell pomocí příkazu `setVerboseShell(true)`.
    
Spustit základní dotaz na databázi pomocí příkazu `db.coll.find().limit(1)`.

Obdržíte odpovědi podobně jako následující výstup:

```bash
Fetched 1 record(s) in 100(ms)
```
        
Před zahájením migrace, odeberte vloženého dokumentu k zajištění, že nejsou žádné duplicitní dokumenty. Můžete odebrat dokumenty pomocí příkazu `db.coll.remove({})`.

#### <a name="calculate-the-approximate-values-for-the-batchsize-and-numinsertionworkers-properties"></a>Vypočítá přibližné hodnoty pro vlastnosti batchSize a numInsertionWorkers

Pro **batchSize** vlastnost, dělení celkové zřízené ru tak počet ru spotřebovaných z vaší zápisu jednotlivý dokument jako dokončené v části "Určení latence z vašeho počítače ke cloudové službě Azure Cosmos DB." Pokud vypočtená hodnota je menší než nebo roven 24, použijte toto číslo jako hodnotu vlastnosti. Pokud vypočtená hodnota je větší než 24, nastavte hodnotu vlastnosti na 24.
    
Pro hodnotu vlastnosti **numInsertionWorkers** vlastnost, použijte tento rovnice:

`numInsertionWorkers = (Provisioned RUs throughput * Latency in seconds) / (batchSize * Consumed RUs for a single write)`

Používáme následující hodnoty pro výpočet hodnoty pro **numInsertionWorkers** vlastnost:

| Vlastnost | Hodnota |
|--------|-----|
| **batchSize** | 24 |
| Zřízených ru | 10 000 |
| Latence | 0,100 s |
| Spotřebované jednotky ru | 10 RU |
| **numInsertionWorkers** | (10 000 ru x 0.100 s) / (24 × 10 RUs) = **4.1666** |

Spustit **monogoimport** příkaz migrace. Parametry příkazu jsou popsány dále v tomto článku.

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
```

Můžete také použít **monogorestore** příkazu. Zajistěte, aby že všechny kolekce mají propustnost nastavit dosahovalo nebo přesahovalo počet jednotek ru používané předchozí výpočty.
   
```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
```

### <a name="complete-the-prerequisites"></a>Zajistěte splnění požadavků

Poté, co budete pro migraci, proveďte následující kroky: 

* **Získání vzorku dat**: Ujistěte se, že máte nějaké ukázkové MongoDB data, před zahájením migrace. Pokud nemáte ukázkovou databázi MongoDB, můžete si stáhnout a nainstalovat [komunitní server MongoDB](https://www.mongodb.com/download-center), vytvořit ukázkovou databázi a pomocí aplikace mongoimport.exe nebo mongorestore.exe nahrát ukázková data. 

* **Zvýšení prostupnosti**: Doba trvání migrace dat závisí na množství propustnost, kterou můžete nastavit pro jednotlivé kolekce nebo sady kolekcí. V případě rozsáhlejších migrací dat nezapomeňte propustnost zvýšit. Po dokončení migrace snížení propustnosti, aby se ušetřily náklady. 

* **Povolit SSL**: Azure Cosmos DB má striktní bezpečnostní požadavky a standardy. Při práci se svým účtem nezapomeňte povolit SSL. Postupy v tomto článku zahrnují postup povolení SSL pro příkazy věnované a mongorestore.

* **Vytvořit prostředky služby Azure Cosmos DB**: Před zahájením migrace, předem vytvořte všechny kolekce z portálu Azure portal. Pokud provádíte migraci na účet služby Azure Cosmos DB, který má databáze úroveň propustnosti, ujistěte se, že jste při vytváření kolekce Azure Cosmos DB poskytují klíč oddílu.

* **Získání připojovacího řetězce**: V [webu Azure portal](https://portal.azure.com), vyberte **služby Azure Cosmos DB** položku na levé straně. V části **předplatná**, vyberte název svého účtu. V části **připojovací řetězec**vyberte **připojovací řetězec**. Pravé straně portálu zobrazí informace je nutné se připojit ke svému účtu:

    ![Informace o připojovacím řetězci](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="use-mongoimport"></a>Použití věnované

K importu dat do svého účtu služby Azure Cosmos DB použijte následující šablonu.

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Nahradit \<your_hostname >, \<your_username >, a \<your_password > Parametry s konkrétními hodnotami pro váš účet. V následujícím příkladu vytvoříme s využitím **sampleDB** hodnotu \<your_database >, a **sampleColl** hodnotu \<your_collection >:

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="use-mongorestore"></a>Použití mongorestore

K obnovení dat do svého účtu rozhraní API pro MongoDB použijte následující šablonu pro spuštění importu.

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Nahradit \<your_hostname >, \<your_username >, a \<your_password > Parametry s konkrétními hodnotami pro váš účet. V následujícím příkladu vytvoříme s využitím **./dumps/dump-2016-12-07** hodnotu \<path_to_backup >:

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --db mydatabase --collection mycollection --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud prostředky už nepotřebujete, můžete odstranit skupinu prostředků, účet služby Azure Cosmos DB a všech souvisejících prostředků. Odstranit skupinu prostředků, použijte následující kroky:

1. Přejděte do skupiny prostředků, ve které jste vytvořili účet Azure Cosmos DB.
1. Vyberte **Odstranit skupinu prostředků**.
1. Potvrďte název skupiny prostředků, které chcete odstranit a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

Pokračujte v dalším kurzu se dozvíte, jak zadávat dotazy na MongoDB data pomocí služby Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Jak provádět dotazy na data MongoDB](../cosmos-db/tutorial-query-mongodb.md)
