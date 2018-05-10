---
title: Použít mongoimport a mongorestore s rozhraním API pro Azure Cosmos DB pro MongoDB | Microsoft Docs
description: Další informace o použití mongoimport a mongorestore pro import dat do rozhraní API pro MongoDB účet
keywords: mongoimport mongorestore
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 36d098a76e57b65ba82c24ed81ebbe3d21489a9f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Azure Cosmos DB: Data pro Import MongoDB 

Pokud chcete migrovat data z MongoDB do účtu Azure Cosmos DB, aby je bylo možné použít pomocí rozhraní API pro MongoDB, musíte:

* Stáhněte si buď *mongoimport.exe* nebo *mongorestore.exe* z [MongoDB stažení softwaru](https://www.mongodb.com/download-center).
* Získat [připojovací řetězec API pro MongoDB](connect-mongodb-account.md).

Pokud importujete data z MongoDB a plánujete používat s Azure Cosmos DB SQL API, měli byste použít [nástroj pro migraci dat](import-data.md) k importu dat.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Získávání připojovacího řetězce
> * Import dat MongoDB pomocí mongoimport
> * Import dat MongoDB pomocí mongorestore

## <a name="prerequisites"></a>Požadavky

* Zvýšit propustnost: trvání migrace dat závisí na množství propustnosti, můžete nastavit pro jednotlivé kolekce nebo sady kolekcí. Ujistěte se, že zvýšit propustnost pro větší dat migrace. Po dokončení migrace, snížit propustnosti, abyste ušetřili náklady. Další informace o zvýšení propustnosti v [portál Azure](https://portal.azure.com), najdete v části [úrovně výkonu a cenové úrovně v Azure Cosmos DB](performance-levels.md).

* Povolte protokol SSL: Azure Cosmos DB má vysokými nároky na zabezpečení a standardy. Je nutné povolit protokol SSL, pokud při práci s vaším účtem. Postupy ve zbývající části článku zahrnují jak povolit SSL pro mongoimport a mongorestore.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Najít vaše informace o připojovacím řetězci (hostitelů, port, uživatelské jméno a heslo)

1. V [portál Azure](https://portal.azure.com), v levém podokně klikněte na tlačítko **Azure Cosmos DB** položku.
2. V **odběry** podokně, vyberte název účtu.
3. V **připojovací řetězec** okně klikněte na tlačítko **připojovací řetězec**.

   V pravém podokně obsahuje všechny informace, které potřebujete k úspěšnému připojení k vašemu účtu.

   ![Okno řetězec připojení](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>Import dat do rozhraní API pro MongoDB pomocí mongoimport

K importu dat do účtu Azure Cosmos DB, použijte následující šablonu. Vyplňte *hostitele*, *uživatelské jméno*, a *heslo* s hodnotami, které jsou specifické pro váš účet.  

Šablona:

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Příklad:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>Import dat do rozhraní API pro MongoDB pomocí mongorestore

K obnovení dat do vašeho rozhraní API pro účet MongoDB, použijte následující šablonu provést import. Vyplňte *hostitele*, *uživatelské jméno*, a *heslo* s hodnotami, které jsou specifické pro váš účet.

Šablona:

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Příklad:

    mongorestore.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>Příručka pro úspěšné migrace

1. Předem vytvořit a škálovat vaše kolekce:
        
    * Ve výchozím nastavení zřídí Azure Cosmos DB nové kolekce MongoDB s 1 000 jednotek žádosti (RUs za sekundu). Před zahájením migrace pomocí mongoimport, mongorestore nebo mongomirror, předem vytvořit všechny kolekce z [portál Azure](https://portal.azure.com) nebo z MongoDB ovladače a nástroje. Pokud vaše kolekce je větší než 10 GB, nezapomeňte vytvořit [horizontálně dělené/oddíly kolekce](partition-data.md) s klíčem odpovídající horizontálního oddílu.

    * Z [portál Azure](https://portal.azure.com), zvýšit propustnost vaší kolekce z 1 000 RUs za sekundu pro kolekce tvořené jedním oddílem a odpovídající 2500 RUs za sekundu pro horizontálně dělenou kolekci jenom pro migraci. S vyšší propustnost můžete vyhnout, omezení a migraci za kratší dobu. S každou hodinu fakturace v Azure Cosmos DB, můžete snížit propustnost ihned po migraci tak, aby ušetřili náklady.

    * Kromě zřizování RUs za sekundu na úrovni kolekce mohou také vytvářet RU za sekundu pro sadu kolekce na nadřazené úrovni databáze. To vyžaduje předběžné vytvoření databáze a kolekce, jakož i definice horizontálního oddílu klíč pro každou kolekci.

    * Můžete vytvořit horizontálně dělené kolekce prostřednictvím vaše oblíbené nástroje, ovladače nebo sady SDK. V tomto příkladu používáme k vytvoření horizontálně dělené kolekce prostředí Mongo:

        ```
        db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
        ```
    
        Výsledky:

        ```JSON
        {
            "_t" : "ShardCollectionResponse",
            "ok" : 1,
            "collectionsharded" : "admin.people"
        }
        ```

2. Přibližná RU náklady pro jednotlivý dokument zápis vypočítejte:

    a. Připojení k vaší databázi Azure Cosmos DB MongoDB z prostředí MongoDB. Můžete najít pokyny v [připojit MongoDB aplikace pro Azure Cosmos DB](connect-mongodb-account.md).
    
    b. Spuštění příkazu insert ukázka pomocí jedné z ukázkových dokumentů z prostředí MongoDB:
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. Spustit ```db.runCommand({getLastRequestStatistics: 1})``` a obdržíte odpověď takto:
     
        ```
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
        ```
        
    d. Poznamenejte si zdarma požadavku.
    
3. Určení latence z vašeho počítače ke cloudové službě Azure Cosmos DB:
    
    a. Zapnutí podrobného protokolování z prostředí MongoDB pomocí tohoto příkazu: ```setVerboseShell(true)```
    
    b. Spusťte jednoduchý dotaz pro databázi: ```db.coll.find().limit(1)```. Obdržíte odpověď takto:

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. Odeberte vloženého dokumentu před migrací zajistit, že neexistují žádné duplicitní dokumenty. Dokumenty můžete odebrat pomocí tohoto příkazu: ```db.coll.remove({})```

5. Vypočítat přibližnou *batchSize* a *numInsertionWorkers* hodnoty:

    * Pro *batchSize*, dělení celkový počet zřízení RUs podle RUs používán z vaší jednotlivý dokument zápisu v kroku 3.
    
    * Pokud počítaný *batchSize* < = 24, použijte toto číslo jako vaše *batchSize* hodnotu.
    
    * Pokud počítaný *batchSize* > 24, nastavte *batchSize* hodnotu 24.
    
    * Pro *numInsertionWorkers*, použijte tento rovnice: *numInsertionWorkers = (zřízené propustnosti * čekací doby v sekundách) / (velikost dávky * využité RUs pro jeden zápis)*.
        
    |Vlastnost|Hodnota|
    |--------|-----|
    |batchSize| 24 |
    |RUs zřídit | 10000 |
    |Latence | 0.100 s |
    |RU účtovat poplatek za 1 doc zápisu | 10 RUs |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10000 RUs x 0,1 s) / (24 × 10 RUs) = 4.1666*

6. Spusťte příkaz dokončení migrace:

   ```
   mongoimport.exe --host anhoh-mongodb.documents.azure.com:10255 -u anhoh-mongodb -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```

## <a name="next-steps"></a>Další postup

Můžete pokračovat v dalším kurzu a zjistěte, jak dotazovat MongoDB dat pomocí Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Postup dotazování MongoDB dat?](../cosmos-db/tutorial-query-mongodb.md)
