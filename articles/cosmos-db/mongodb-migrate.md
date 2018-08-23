---
title: Použití nástrojů mongoimport a mongorestore s rozhraním API služby Azure Cosmos DB pro MongoDB | Microsoft Docs
description: Zjistěte, jak pomocí nástrojů mongoimport a mongorestore importovat data do rozhraní API pro účet MongoDB.
keywords: mongoimport, mongorestore
services: cosmos-db
author: SnehaGunda
manager: slyons
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: sclyon
ms.custom: mvc
ms.openlocfilehash: ffb15c3a608cb7b7be275913cf9dec84e655334a
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "41917918"
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Azure Cosmos DB: Import dat MongoDB 

Pokud chcete migrovat data z MongoDB do účtu Azure Cosmos DB, aby je bylo možné použít pomocí rozhraní API pro MongoDB, musíte:

* Stáhnout a nainstalovat komunitní server z webu [MongoDB Download Center](https://www.mongodb.com/download-center).
* Použít soubor mongoimport.exe nebo mongorestore.exe nainstalovaný v adresáři složka_instalace/bin. 
* Získat [připojovací řetězec API pro MongoDB](connect-mongodb-account.md).

Pokud importujete data z MongoDB a plánujete je používat s rozhraním SQL API služby Azure Cosmos DB, měli byste k importu dat použít [nástroj pro migraci dat](import-data.md).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Načtení připojovacího řetězce
> * Import dat MongoDB pomocí nástroje mongoimport
> * Import dat MongoDB pomocí nástroje mongorestore

## <a name="prerequisites"></a>Požadavky

* Zvýšená propustnost: Doba trvání migrace dat závisí na propustnosti, kterou pro jednotlivé kolekce nebo sady kolekcí nastavíte. V případě rozsáhlejších migrací dat nezapomeňte propustnost zvýšit. Po dokončení migrace propustnost snižte, abyste dosáhli nižších nákladů. Další informace o zvýšení propustnosti na webu [Azure Portal](https://portal.azure.com) najdete v tématu [Úrovně výkonu a cenové úrovně ve službě Azure Cosmos DB](performance-levels.md).

* Povolený protokol SSL: Azure Cosmos DB má striktní bezpečnostní požadavky a standardy. Při práci se svým účtem nezapomeňte povolit SSL. Postup pro povolení SSL pro mongoimport a mongorestore najdete ve zbývající části článku.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Vyhledání informací o připojovacím řetězci (hostitel, port, uživatelské jméno a heslo)

1. V levém podokně na webu [Azure Portal](https://portal.azure.com) klikněte na položku **Azure Cosmos DB**.
1. V podokně **Předplatná** vyberte název vašeho účtu.
1. V okně **Připojovací řetězec** klikněte na **Připojovací řetězec**.

   Pravé podokno obsahuje všechny informace, které potřebujete pro úspěšné připojení ke svému účtu.

   ![Okno Připojovací řetězec](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>Import dat do rozhraní API pro MongoDB pomocí nástroje mongoimport

K importu dat do svého účtu služby Azure Cosmos DB použijte následující šablonu. Vyplňte *hostitele*, *uživatelské jméno* a *heslo* pomocí hodnot specifických pro váš účet.  

Šablona:

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Příklad:  

    mongoimport.exe --host comsosdb-mongodb-account.documents.azure.com:10255 -u comsosdb-mongodb-account -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\admin\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>Import dat do rozhraní API pro MongoDB pomocí nástroje mongorestore

K obnovení dat do svého účtu rozhraní API pro MongoDB použijte následující šablonu pro spuštění importu. Vyplňte *hostitele*, *uživatelské jméno* a *heslo* pomocí hodnot specifických pro váš účet.

Šablona:

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Příklad:

    mongorestore.exe --host comsosdb-mongodb-account.documents.azure.com:10255 -u comsosdb-mongodb-account -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>Příručka pro úspěšnou migraci

1. Předem vytvořte a škálujte kolekce:
        
    * Ve výchozím nastavení Azure Cosmos DB zřídí novou kolekci MongoDB s 1 000 jednotek žádostí za sekundu (RU/s). Před zahájením migrace pomocí nástroje mongoimport, mongorestore nebo mongomirror předem vytvořte všechny kolekce pomocí webu [Azure Portal](https://portal.azure.com) nebo ovladačů a nástrojů MongoDB. Pokud je vaše kolekce větší než 10 GB, nezapomeňte vytvořit [sdílenou/dělenou kolekci](partition-data.md) s odpovídajícím klíčem horizontálního dělení.

    * Jen pro účely migrace na webu [Azure Portal](https://portal.azure.com) zvyšte propustnost vašich kolekcí z 1 000 RU/s pro kolekce s jedním oddílem a 2 500 RU/s pro horizontálně dělené kolekce. Vyšší propustnost vám umožní zabránit omezování rychlosti a zkrátit dobu migrace. Díky fakturaci služby Azure Cosmos DB po hodinách můžete propustnost okamžitě po migraci snížit, abyste dosáhli nižších nákladů.

    * Kromě zřízení RU/s na úrovni kolekce můžete zřídit také RU/s pro sadu kolekcí na úrovni nadřazené databáze. Vyžaduje to vytvoření databáze a kolekcí předem a definování klíče horizontálního dělení pro každou kolekci.

    * Horizontálně dělené kolekce můžete vytvářet prostřednictvím svého oblíbeného nástroje, ovladače nebo sady SDK. V tomto příkladu k vytvoření horizontálně dělené kolekce používáme MongoDB Shell:

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

1. Vypočítejte průměrný poplatek za RU pro jeden zápis dokumentu:

    a. Z prostředí MongoDB Shell se připojte ke své databázi MongoDB ve službě Azure Cosmos DB. Pokyny najdete v tématu [Připojení aplikace MongoDB ke službě Azure Cosmos DB](connect-mongodb-account.md).
    
    b. Z prostředí MongoDB Shell spusťte ukázkový příkaz pro vložení s použitím některého z vašich ukázkových dokumentů:
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. Spusťte ```db.runCommand({getLastRequestStatistics: 1})``` a zobrazí se odpověď, která vypadá následovně:
     
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
        
    d. Poznamenejte si zátěž požadavku.
    
1. Určete latenci ze svého počítače do cloudové služby Azure Cosmos DB:
    
    a. Z prostředí MongoDB Shell povolte podrobné protokolování pomocí tohoto příkazu: ```setVerboseShell(true)```.
    
    b. Spusťte jednoduchý dotaz na databázi: ```db.coll.find().limit(1)```. Zobrazí se odpověď, která vypadá následovně:

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
1. Odeberte dokument vložený před migrací, abyste zajistili, že nedojde k duplicitě dokumentů. Dokumenty můžete odebrat pomocí tohoto příkazu: ```db.coll.remove({})```.

1. Vypočítejte přibližné hodnoty *batchSize* a *numInsertionWorkers*:

    * V případě hodnoty *batchSize* vydělte celkový počet zřízených RU počtem RU spotřebovaných jedním zápisem dokumentu v kroku 3.
    
    * Pokud je vypočítaná hodnota *batchSize* menší nebo rovna 24, použijte jako hodnotu *batchSize* vypočítané číslo.
    
    * Pokud je vypočítaná hodnota *batchSize* větší než 24, nastavte hodnotu *batchSize* na 24.
    
    * V případě hodnoty *numInsertionWorkers* použijte tuto rovnici: *numInsertionWorkers =  (zřízená propustnost × latence v sekundách) / (velikost dávky × počet RU spotřebovaných jedním zápisem)*.
        
    |Vlastnost|Hodnota|
    |--------|-----|
    |batchSize| 24 |
    |Počet zřízených RU | 10000 |
    |Latence | 0,100 s |
    |Poplatek za RU pro 1 zápis dokumentu | 10 RU |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10 000 RU × 0,1 s) / (24 × 10 RU) = 4,1666*

1. Spusťte dokončený příkaz pro migraci:

   ```
   mongoimport.exe --host comsosdb-mongodb-account.documents.azure.com:10255 -u comsosdb-mongodb-account -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```
   Případně můžete použít mongorestore (ujistěte se, že všechny kolekce mají nastavenou propustnost minimálně na stejný počet RU jako v předchozích výpočtech):
   
   ```
   mongorestore.exe --host comsosdb-mongodb-account.documents.azure.com:10255 -u comsosdb-mongodb-account -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
   ```

## <a name="next-steps"></a>Další kroky

Můžete pokračovat k dalšímu kurzu, kde zjistíte, jak dotazovat data MongoDB pomocí služby Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Jak dotazovat data MongoDB?](../cosmos-db/tutorial-query-mongodb.md)
