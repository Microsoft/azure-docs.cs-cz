---
title: Použití transakcí s více dokumenty v rozhraní Azure Cosmos DB API pro MongoDB
description: Naučte se, jak vytvořit ukázkovou aplikaci Mongo Shell, která může spouštět transakce více dokumentů (bez sémantiky) v pevné kolekci v rozhraní Azure Cosmos DB API pro MongoDB 4,0.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: gahllevy
ms.openlocfilehash: f319db76c8aee5a2a35ff8ca9670c42089350ede
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692466"
---
# <a name="use-multi-document-transactions-in-azure-cosmos-db-api-for-mongodb"></a>Použití transakcí s více dokumenty v rozhraní Azure Cosmos DB API pro MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

V tomto článku vytvoříte aplikaci prostředí Mongo, která spustí transakci více dokumentů v pevné kolekci v rozhraní Azure Cosmos DB API pro MongoDB se serverem verze 4,0.

## <a name="what-are-multi-document-transactions"></a>Co jsou transakce více dokumentů?

V rozhraní Azure Cosmos DB API pro MongoDB jsou operace v jednom dokumentu atomické. Transakce s více dokumenty umožňují aplikacím provádět atomické operace v rámci více dokumentů. Nabízí sémantiku "vše-nebo-Nothing" operací. Při potvrzení změn provedených v transakcích jsou trvalé změny a pokud transakce není úspěšná, všechny změny v transakci se zahodí.

Transakce s více dokumenty následují sémantika **kyselosti** :

* Nedělitelnost: všechny operace se zpracují jako jedna.
* Konzistence: potvrzená data jsou platná
* Izolace: izolované od jiných operací
* Odolnost: transakční data jsou trvalá, když je klient dozvědí.

## <a name="requirements"></a>Požadavky

Transakce s více dokumenty jsou podporovány v kolekci unsharded v rozhraní API verze 4,0. Transakce s více dokumenty nejsou podporovány napříč kolekcemi nebo v kolekcích horizontálně dělené v 4,0. Časový limit pro transakce je pevný 5 sekund.

Všechny ovladače, které podporují síťový protokol verze 4,0 nebo vyšší, budou podporovat rozhraní Azure Cosmos DB API pro transakce MongoDB více dokumentů.

## <a name="run-multi-document-transactions-in-mongodb-shell"></a>Spuštění transakcí s více dokumenty v prostředí MongoDB
> [!Note]
> Tento příklad nefunguje v MongoSH beta verzi (prostředí) vložené v MongoDB kompasu.

1. Otevřete příkazový řádek, v adresáři, ve kterém je nainstalovaná aplikace Mongo Shell verze 4,0 a vyšší:

   ```powershell
   cd <path_to_mongo_shell_>
   ```

2. Vytvořte skript prostředí Mongo *connect_friends.js* a přidejte následující obsah

   ```javascript
   // insert data into friends collection
   db.getMongo().getDB("users").friends.insert({name:"Tom"})
   db.getMongo().getDB("users").friends.insert({name:"Mike"})
   // start transaction
   var session = db.getMongo().startSession();
   var friendsCollection = session.getDatabase("users").friends;
   session.startTransaction();
   // operations in transaction
   try {
       friendsCollection.updateOne({ name: "Tom" }, { $set: { friendOf: "Mike" } } );
       friendsCollection.updateOne({ name: "Mike" }, { $set: { friendOf: "Tom" } } );
   } catch (error) {
       // abort transaction on error
       session.abortTransaction();
       throw error;
   }

    // commit transaction
    session.commitTransaction();

    ```

3. Spusťte následující příkaz, který provede transakci více dokumentů. Hostitel, port, uživatel a klíč najdete v Azure Portal.

   ```powershell
   mongo "<HOST>:<PORT>" -u "<USER>" -p "KEY" --ssl connect_friends.js
   ```

## <a name="next-steps"></a>Další kroky

Prozkoumejte novinky v [rozhraní Azure Cosmos DB API pro MongoDB 4,0](mongodb-feature-support-40.md)
